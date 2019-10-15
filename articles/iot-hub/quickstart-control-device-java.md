---
title: 'Démarrage rapide : Contrôler un appareil à partir d’Azure IoT Hub avec Java'
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’applications Java. Une application est une application back-end qui peut contrôler à distance des appareils connectés à votre concentrateur. L’autre application simule un appareil connecté à votre concentrateur qui peut être contrôlé à distance.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: f59a3409d508c63f232294d8d66ade5669815b3c
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843333"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Démarrage rapide : Contrôler un appareil connecté à un hub IoT Azure avec Java

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Dans ce guide de démarrage rapide, vous utilisez une *méthode directe* pour contrôler un appareil simulé connecté à votre hub IoT Azure avec une application Java. Vous pouvez utiliser les méthodes directes pour modifier à distance le comportement d’un appareil connecté à votre IoT Hub. IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud et de gérer vos appareils à partir du cloud. 

Ce démarrage rapide utilise deux applications Java prédéfinies :

* Une application d’appareil simulé qui répond aux méthodes directes appelées à partir d’une application back-end. Pour recevoir les appels de méthode directe, cette application se connecte à un point de terminaison spécifique à l’appareil sur votre IoT Hub.

* Une application back-end qui appelle les méthodes directes sur l’appareil simulé. Pour appeler une méthode directe sur un appareil, cette application se connecte à un point de terminaison côté service sur votre IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Les deux exemples d’applications que vous exécutez dans ce guide de démarrage rapide sont écrits à l’aide de Java. Vous avez besoin de Java SE 8 sur votre machine de développement.

Vous pouvez télécharger Java SE Development Kit 8 pour plusieurs plateformes à partir de la page [Prise en charge à long terme de Java pour Azure et Azure Stack](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable). Veillez à sélectionner **Java 8** sous **Prise en charge à long terme** pour accéder aux téléchargements du kit JDK 8.

Vous pouvez vérifier la version actuelle de Java sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
java -version
```

Pour générer les exemples, vous devez installer Maven 3. Vous pouvez télécharger Maven pour plusieurs plateformes sur [Apache Maven](https://maven.apache.org/download.cgi).

Vous pouvez vérifier la version actuelle de Maven sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
mvn --version
```

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Si ce n’est déjà fait, téléchargez l’exemple de projet Java à partir de https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip et extrayez l’archive ZIP.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-java.md), vous pouvez ignorer cette étape.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-java.md), vous pouvez ignorer cette étape.

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyJavaDevice** : Nom de l’appareil que vous inscrivez. Utilisez **MyJavaDevice** comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyJavaDevice \
      --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

## <a name="retrieve-the-service-connection-string"></a>Récupérer la chaîne de connexion de service

Vous avez également besoin d’une _chaîne de connexion de service_ pour activer l’application back-end afin de vous connecter à votre IoT Hub et récupérer les messages. La commande suivante récupère la chaîne de connexion de service correspondant à votre hub IoT :

**YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

Notez la chaîne de connexion de service, qui ressemble à ce qui suit :

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Vous utiliserez cette valeur plus loin dans ce démarrage rapide. La chaîne de connexion de service est différente de la chaîne de connexion d’appareil.

## <a name="listen-for-direct-method-calls"></a>Écouter les appels de méthode directe

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub, envoie les données de télémétrie simulée et écoute les appels de méthode directe provenant de votre concentrateur. Dans ce démarrage rapide, l’appel de méthode directe à partir du concentrateur indique à l’appareil de modifier la fréquence à laquelle il envoie des données de télémétrie. L’appareil simulé renvoie un accusé de réception à votre hub après l’exécution de la méthode directe.

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Java. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device-2**.

2. Ouvrez le fichier **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** dans l’éditeur de texte de votre choix.

    Remplacez la valeur de la variable `connString` par la chaîne de connexion d’appareil que vous avez notée précédemment. Puis, enregistrez les modifications apportées au fichier **SimulatedDevice.java**.

3. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et générer l’application d’appareil simulé :

    ```cmd/sh
    mvn clean package
    ```

4. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour démarrer l’application d’appareil simulé :

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Sortie des données de télémétrie envoyées par l’appareil à votre hub IoT](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>Appeler la méthode directe

L’application back-end se connecte au point de terminaison côté service sur votre IoT Hub. L’application effectue des appels de méthode directe à un appareil via votre IoT Hub et écoute les accusés de réception. Une application back-end de l’IoT Hub s’exécute généralement dans le cloud.

1. Dans une autre fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Java. Ensuite, accédez au dossier **iot-hub\Quickstarts\back-end-application**.

2. Ouvrez le fichier **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** dans l’éditeur de texte de votre choix.

    Remplacez la valeur de la variable `iotHubConnectionString` par la chaîne de connexion de service que vous avez notée précédemment. Puis, enregistrez vos modifications dans le fichier **BackEndApplication.java**.

3. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et générer l’application back-end :

    ```cmd/sh
    mvn clean package
    ```

4. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour exécuter l’application back-end :

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    La capture d’écran suivante montre la sortie lorsque l’application effectue un appel de méthode directe sur l’appareil et reçoit un accusé de réception :

    ![Sortie quand l’application effectue un appel de méthode directe par le biais de votre hub IoT](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    Après avoir exécuté l’application back-end, un message s’affiche dans la fenêtre de la console exécutant l’appareil simulé, et la fréquence à laquelle il envoie les messages change :

    ![Le message de la console reçu de l’appareil affiche la fréquence à laquelle il change](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appelé une méthode directe sur un appareil à partir d’une application back-end et répondu à l’appel de méthode directe dans une application d’appareil simulé.

Pour savoir comment acheminer les messages appareil-à-cloud vers différentes destinations dans le cloud, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Didacticiel : acheminer les données de télémétrie vers différents points de terminaison pour traitement](tutorial-routing.md)
