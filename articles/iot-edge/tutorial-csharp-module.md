---
title: 'Tutoriel : Développer un module C# pour Linux - Azure IoT Edge | Microsoft Docs'
description: Ce tutoriel vous explique comment créer un module IoT Edge avec un code C# et le déployer sur un appareil IoT Edge Linux.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b9ced0b08ed26057a45959f759fb90cbd7efe2a5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239788"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Didacticiel : Développer un module C# IoT Edge pour les appareils Linux

Utilisez Visual Studio Code pour écrire du code C# et le déployer sur un appareil Linux exécutant Azure IoT Edge. 

Vous pouvez utiliser des modules Azure IoT Edge pour déployer un code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utiliserez l’appareil IoT Edge simulé que vous avez créé dans les tutoriels Déployer Azure IoT Edge sur un appareil simulé sous [Windows](quickstart.md) ou [Linux](quickstart-linux.md). Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utilisez Visual Studio Code pour créer un module IoT Edge basé sur le kit SDK .NET Core 2.1.
> * Utiliser Visual Studio Code et Docker pour créer une image Docker et la publier dans votre registre
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées

Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Étendue de solution

Ce tutoriel montre comment développer un module dans **C#** à l’aide de **Visual Studio Code**et comment le déployer sur un **appareil Linux**. Si vous développez des modules pour les appareils Windows, consultez [Développer un module IoT Edge en C# pour les appareils Windows](tutorial-csharp-module-windows.md).

Utilisez le tableau suivant afin de comprendre les options dont vous disposez pour développer et déployer des modules en C sur des appareils Linux : 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Modules C# pour LinuxAMD64 dans VS Code](./media/tutorial-c-module/green-check.png) | ![Modules C# modules pour LinuxAMD64 dans Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Modules C# pour LinuxARM32 dans VS Code](./media/tutorial-c-module/green-check.png) | ![Modules C# pour LinuxARM64 dans Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vous devez avoir effectué celui qui précède pour configurer votre environnement de développement, [Développer un module IoT Edge pour un appareil Linux](tutorial-develop-for-linux.md). Ce tutoriel vous permet d’obtenir les prérequis suivants : 

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.
* Un [appareil Linux exécutant Azure IoT Edge](quickstart-linux.md).
* Un registre de conteneurs tel qu’[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configuré avec [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configuré pour exécuter des conteneurs Linux.

Pour suivre ces tutoriels, préparez les conditions préalables supplémentaires suivantes sur votre ordinateur de développement : 

* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Créer un projet de module

Les étapes suivantes permettent de créer un projet de module IoT Edge pour C# à l’aide de Visual Studio Code et de l'extension Azure IoT Tools. Une fois le modèle de projet créé, ajoutez le nouveau code afin que le module filtre les messages selon les propriétés signalées. 

### <a name="create-a-new-project"></a>Création d'un projet

Créez un modèle de solution C# que vous pouvez personnaliser avec votre propre code. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

2. Dans la palette de commandes, entrez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

3. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module C#** . |
   | Provide a module name (Nommer le module) | Nommez votre module **CSharpModule**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. L’image conteneur est préremplie avec le nom que vous avez indiqué à la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. <br><br>Le référentiel d’images final ressemble à ceci : \<nom_registre\>.azurecr.io/csharpmodule. |
 
   ![Fourniture du référentiel d’images Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre registre de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. Utilisez les informations d’identification de la section **Clés d’accès** de votre registre de conteneurs Azure. 

1. Dans l’Explorateur VS Code, ouvrez le fichier **.env**. 
2. Mettez à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="select-your-target-architecture"></a>Sélectionner votre architecture cible

Visual Studio Code peut développer des modules C pour les appareils Linux AMD64 et Linux ARM32v7. Vous devez sélectionner l’architecture que vous ciblez avec chaque solution, car le conteneur est généré et s’exécute différemment pour chaque type d’architecture. Linux AMD64 est la valeur par défaut. 

1. Ouvrez la palette de commandes et recherchez **Azure IoT Edge: Définir la plateforme cible par défaut pour la solution Edge**, ou sélectionnez l’icône de raccourci dans la barre latérale en bas de la fenêtre. 

2. Dans la palette de commandes, sélectionnez l’architecture cible dans la liste des options. Pour ce tutoriel, comme nous utilisons une machine virtuelle Ubuntu en tant qu’appareil IoT Edge, ce dernier conservera la valeur par défaut **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

1. Dans l’Explorateur VS Code, ouvrez **modules** > **CSharpModule** > **Program.cs**.

2. En haut de l’espace de noms **CSharpModule**, ajoutez trois instructions **using** pour les types utilisés ultérieurement :

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Ajoutez la variable **temperatureThreshold** à la classe **Program**. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Ajoutez les classes **MessageBody**, **Machine**, et **Ambient** à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. Recherchez la fonction **Init**. Cette fonction crée et configurer un objet **ModuleClient** qui permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. Après avoir créé le **ModuleClient**, le code lit la valeur **temperatureThreshold** à partir des propriétés souhaitées du jumeau de module. Le code enregistre un rappel pour recevoir des messages du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode **SetInputMessageHandlerAsync** par une nouvelle, et ajoutez une méthode **SetDesiredPropertyUpdateCallbackAsync** pour les mises à jour des propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Ajoutez la méthode **onDesiredPropertiesUpdate** à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre module jumeau, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Remplacez la méthode **PipeMessage** par la méthode **FilterMessages**. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Il filtre les messages qui signalent des températures situées sous le seuil de température défini via le double de module. Il ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Enregistrez le fichier Program.cs.

9. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. 

10. Ajoutez le jumeau de module **CSharpModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section **modulesContent**, après le jumeau de module **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ajouter un jumeau de module au modèle de déploiement](./media/tutorial-csharp-module/module-twin.png)

11. Enregistrez le fichier deployment.template.json.


## <a name="build-and-push-your-module"></a>Générer et envoyer (push) votre module

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à CSharpModule pour filtrer les messages quand la température de machine signalée se situe dans les limites acceptables. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

1. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal**.

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal. Connectez-vous avec le nom d’utilisateur, le mot de passe et le serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer ces valeurs dans la section **Clés d’accès** de votre registre dans le portail Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Il se peut que vous receviez un avertissement de sécurité recommandant d’utiliser `--password-stdin`. Bien qu’il s’agisse de la bonne pratique recommandée pour les scénarios de production, elle n’est pas pertinente pour ce tutoriel. Pour plus d’informations, consultez la documentation de référence [Connexion docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge).

   La commande de génération et d’envoi (push) déclenche trois opérations. Tout d’abord, elle crée un dossier dans la solution appelé **config** contenant les manifestes de déploiement en entier. Il est généré à partir des informations dans le modèle de déploiement et d’autres fichiers de solution. Ensuite, elle exécute `docker build` pour générer l’image de conteneur basée sur le fichier docker correspondant à votre architecture cible. Puis, elle exécute `docker push` pour envoyer (push) le référentiel d’images vers votre registre de conteneurs.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Utilisez l’Explorateur de Visual Studio Code et l’extension Azure IoT Tools pour déployer le projet de module sur votre appareil IoT Edge. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario. C ’est le fichier **deployment.json** dans le dossier config. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

Vérifiez que votre appareil IoT Edge est opérationnel.

1. Dans l’Explorateur Visual Studio Code, développez la section **Appareils Azure IoT Hub** pour accéder à votre liste d’appareil IoT.

2. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil). 

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json. 

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **CSharpModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**.  

## <a name="view-generated-data"></a>Afficher les données générées

Une fois que vous appliquez le manifeste de déploiement à votre appareil IoT Edge, le runtime IoT Edge sur l’appareil collecte les nouvelles informations de déploiement et commence à s’exécuter sur celui-ci. Tous les modules en cours d’exécution sur l’appareil qui ne sont pas inclus dans le manifeste de déploiement sont arrêtés. Tous les modules manquant de l’appareil sont démarrés.

Vous pouvez afficher l’état de votre appareil IoT Edge dans la section **Appareils Azure IoT Hub** de l’explorateur de Visual Studio Code. Développez les détails de votre appareil pour afficher la liste des modules déployés et en cours d’exécution.

1. Dans l’explorateur Visual Studio Code, cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Démarrer la supervision du point de terminaison d’événements intégré**.

2. Affichez les messages reçus dans votre hub IoT. Les messages peuvent mettre un certain temps à arriver, car l’appareil IoT Edge doit recevoir son nouveau déploiement et démarrer tous les modules. Ensuite, les modifications que nous avons apportées au code du module CModule attendent que la température de la machine atteigne 25 degrés avant d’envoyer les messages. Le type de message **Alerte** est également ajouté à chaque message qui atteint ce seuil de température. 

   ![Afficher les messages reçus sur IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Modifier le jumeau de module

Nous avons utilisé le jumeau du module CSharpModule dans le manifeste de déploiement pour définir le seuil de température à 25 degrés. Vous pouvez utiliser le jumeau de module pour modifier cette fonctionnalité sans devoir mettre à jour le code du module.

1. Dans Visual Studio Code, développez les détails de votre appareil IoT Edge pour afficher les modules en cours d’exécution. 

2. Cliquez avec le bouton droit sur **CSharpModule** et sélectionnez **Modifier le jumeau de module**. 

3. Recherchez **Seuil de température** dans les propriétés souhaitées. Définissez sa valeur sur une nouvelle température supérieure de 5 à 10 degrés à la dernière température indiquée. 

4. Enregistrez le jumeau de module.

5. Cliquez avec le bouton droit sur le volet d’édition du jumeau de module et sélectionnez **Mettre à jour le jumeau de module**. 

5. Supervisez les messages appareil-à-cloud entrants. Vous devriez voir les messages s’arrêter jusqu’à ce que le nouveau seuil de température soit atteint. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez utilisées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez en apprendre plus sur le [développement de vos propres modules IoT Edge](module-development.md) et sur le [développement de modules avec Visual Studio Code](how-to-vs-code-develop-module.md). Vous pouvez passer aux tutoriels suivants afin de découvrir comment Azure IoT Edge peut vous aider à déployer des services cloud Azure pour traiter et analyser des données en périphérie.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Service Custom Vision](tutorial-deploy-custom-vision.md)
