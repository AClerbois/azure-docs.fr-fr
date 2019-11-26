---
title: Découvrir comment le runtime gère les appareils - Azure IoT Edge | Microsoft Docs
description: Découvrez comment runtime IoT Edge gère les modules, la sécurité, la communication et les rapports sur vos appareils
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4bdf496995e8b466f1346bfe16365b251c6853c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076050"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Présentation du runtime Azure IoT Edge et de son architecture

Le runtime IoT Edge est une collection de programmes qui transforme un appareil en appareil IoT Edge. Collectivement, les composants du runtime IoT Edge permettent aux appareils IoT Edge de recevoir du code à exécuter dans l’arête et de communiquer les résultats. 

Le runtime IoT Edge est responsable des fonctions suivantes sur les appareils IoT Edge :

* Installer et mettre à jour des charges de travail sur l’appareil.
* Tenir à jour les normes de sécurité Azure IoT Edge sur l’appareil.
* Garantir que les [modules IoT Edge](iot-edge-modules.md) sont toujours en cours d’exécution.
* Envoyer des rapports d’intégrité du module dans le cloud pour la supervision à distance.
* Gérer la communication entre les appareils en aval et les appareils IoT Edge.
* Gérer la communication entre les modules sur l’appareil IoT Edge.
* Gérer la communication entre l’appareil IoT Edge et le cloud.

![Le runtime communique des insights et des données sur l’intégrité des modules à IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Les responsabilités du runtime IoT Edge se répartissent en deux catégories : communication et gestion des modules. Ces deux rôles sont remplis par deux composants qui font partie du runtime IoT Edge. Le *hub IoT Edge* est responsable de la communication, tandis que l’*agent IoT Edge* déploie et surveille les modules. 

Le hub IoT Edge et l’agent IoT Edge sont tous deux des modules, comme n’importe quel autre module exécuté sur un appareil IoT Edge. Ils sont parfois appelés *modules runtime*. 

## <a name="iot-edge-hub"></a>Hub IoT Edge

Le hub IoT Edge est l’un des deux modules qui composent le runtime Azure IoT Edge. Il joue le rôle de proxy local pour IoT Hub en exposant les mêmes points de terminaison de protocole qu’IoT Hub. Cette cohérence signifie que les clients (qu’il s’agisse d’appareils ou de modules) peuvent se connecter au runtime IoT Edge comme à IoT Hub. 

>[!NOTE]
> Le hub IoT Edge prend en charge les clients qui se connectent à l’aide de MQTT ou de AMQP. Il ne prend pas en charge les clients qui utilisent HTTP. 

Le hub IoT Edge n’est pas une version complète d’IoT Hub qui s’exécute localement. Il y a certaines choses que le hub IoT Edge délègue de manière silencieuse à IoT Hub. Par exemple, le hub IoT Edge transfère les demandes d’authentification à IoT Hub quand un appareil essaie de se connecter pour la première fois. Une fois la première connexion établie, les informations de sécurité sont mises en cache localement par le hub IoT Edge. Les connexions suivantes à partir de cet appareil sont autorisées sans avoir à s’authentifier sur le cloud. 

Pour réduire la bande passante utilisée par votre solution IoT Edge, le hub IoT Edge optimise le nombre de connexions établies avec le cloud. Le hub IoT Edge accepte les connexions logiques à partir de clients tels que modules ou appareils en aval et il les combine pour établir une connexion physique unique au cloud. Les détails de ce processus sont transparents pour le reste de la solution. Les clients pensent avoir leur propre connexion au cloud, alors qu’ils passent tous par la même connexion. 

![Le hub IoT Edge est une passerelle entre les appareils physiques et IoT Hub](./media/iot-edge-runtime/Gateway.png)

Le hub IoT Edge peut déterminer s’il est connecté à IoT Hub. Si la connexion est perdue, le hub IoT Edge enregistre localement les messages ou les mises à jour de jumeau. Une fois la connexion rétablie, il synchronise toutes les données. L’emplacement utilisé pour ce cache temporaire est déterminé par une propriété du jumeau de module du hub IoT Edge. La taille du cache n’est pas limitée et augmente tant que l’appareil a une capacité de stockage. Pour plus d’informations, voir [Fonctionnalités hors connexion](offline-capabilities.md).

### <a name="module-communication"></a>Communication des modules

Le hub IoT Edge facilite la communication entre les modules. L’utilisation du hub IoT Edge comme un répartiteur de messages permet aux modules de rester indépendants les uns des autres. Il suffit aux modules de spécifier les entrées sur lesquelles ils acceptent des messages et les sorties vers lesquelles ils écrivent des messages. Un développeur de solutions peut assembler ces entrées et sorties afin que les modules traitent les données dans l’ordre propre à cette solution. 

![Le hub IoT Edge facilite la communication entre les modules](./media/iot-edge-runtime/module-endpoints.png)

Pour envoyer des données au hub IoT Edge, un module appelle la méthode SendEventAsync. Le premier argument spécifie sur quelle sortie envoyer le message. Le pseudo-code suivant envoie un message sur **output1** :

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Pour recevoir un message, inscrivez un rappel qui traite les messages entrant sur une entrée spécifique. Le pseudo-code suivant inscrit la fonction messageProcessor à utiliser pour le traitement de tous les messages reçus sur **input1** :

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Pour plus d’informations sur la classe ModuleClient et ses méthodes de communication, reportez-vous aux informations de référence sur l’API du langage de votre SDK préféré : [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) ou [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Le développeur de solution doit spécifier les règles qui déterminent la façon dont le hub IoT Edge transmet les messages d’un module à l’autre. Les règles d’acheminement sont définies dans le cloud et envoyées (push) vers le hub IoT Edge dans son jumeau de module. La même syntaxe pour les itinéraires IoT Hub est utilisée pour définir les itinéraires entre les modules dans Azure IoT Edge. Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).   

![Les routes entre modules passent par le hub IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent IoT Edge

L’agent IoT Edge est l’autre module qui compose le runtime Azure IoT Edge. Il est responsable de l’instanciation des modules, vérifie qu’ils continuent à s’exécuter, et signale l’état des modules à IoT Hub. Ces données de configuration sont écrites en tant que propriété du jumeau de module de l’agent IoT Edge. 

Le [démon de sécurité IoT Edge](iot-edge-security-manager.md) démarre l’agent IoT Edge au démarrage de l’appareil. L’agent récupère son jumeau de module à partir d’IoT Hub et inspecte le manifeste de déploiement. Le manifeste de déploiement est un fichier JSON qui déclare les modules qui doivent être démarrés. 

Chaque élément du manifeste de déploiement contient des informations sur un module, et est utilisé par l’agent IoT Edge pour contrôler le cycle de vie du module. Voici quelques-unes des propriétés les plus intéressantes : 

* **Settings.image** : image conteneur utilisée par l’agent IoT Edge pour démarrer le module. L’agent IoT Edge doit être configuré avec des informations d’identification pour le registre de conteneurs si l’image est protégée par un mot de passe. Les informations d’identification du registre de conteneurs peuvent être configurées à distance à l’aide du manifeste de déploiement, ou directement sur l’appareil IoT Edge en mettant à jour le fichier `config.yaml` dans le dossier du programme IoT Edge.
* **settings.createOptions** : chaîne qui est transmise directement au démon du conteneur Moby lors du démarrage du conteneur d’un module. L’ajout d’options dans cette propriété permet de bénéficier de configurations avancées telles que le transfert de port ou le montage de volumes dans le conteneur d’un module.  
* **status** : état dans lequel l’agent IoT Edge place le module. Cette valeur est généralement définie sur *running*, car la plupart des gens souhaitent que l’agent IoT Edge démarre immédiatement tous les modules sur l’appareil. Toutefois, vous pouvez spécifier l’arrêt comme état initial d’un module, et demander ultérieurement à l’agent IoT Edge de démarrer le module. L’agent IoT Edge signale l’état de chaque module au cloud dans les propriétés déclarées. Une différence entre la propriété souhaitée et la propriété rapportée est un indicateur du dysfonctionnement de l’appareil. Les états pris en charge sont :
   * Downloading
   * Exécution
   * Unhealthy
   * Échec
   * Arrêté
* **restartPolicy** : indique la façon dont l’agent IoT Edge redémarre un module. Les valeurs possibles incluent :
   * `never` : l’agent IoT Edge ne redémarre jamais le module.
   * `on-failure` : si le module se bloque, l’agent IoT Edge le redémarre. Si le module se ferme correctement, l’agent IoT Edge ne le redémarre pas.
   * `on-unhealthy` : si le module plante ou est considéré comme non sain, l’agent IoT Edge le redémarre.
   * `always` : si le module plante, est considéré comme non sain ou s’arrête d’une façon quelconque, l’agent IoT Edge le redémarre. 
* **imagePullPolicy** : indique si l’agent IoT Edge tente d’extraire automatiquement la dernière image d’un module. Si vous ne spécifiez aucune valeur, la valeur par défaut est *onCreate*. Les valeurs possibles incluent : 
   * `on-create` : lorsque vous démarrez un module ou que vous mettez à jour un module basé sur un nouveau manifeste de déploiement, l’agent IoT Edge tente d’extraire l’image du module à partir du registre de conteneurs.
   * `never` : l’agent IoT Edge ne tentera jamais d’extraire l’image du module à partir du registre de conteneurs. L’image du module devrait être mise en cache sur l’appareil et toutes les mises à jour de celle-ci devraient être effectuées manuellement ou gérées par une solution tierce. 

L’agent IoT Edge envoie la réponse d’exécution à IoT Hub. Voici une liste de réponses possibles :
  * 200 - OK
  * 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
  * 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
  * 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
  * 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
  * 500 - Une erreur s’est produite dans le runtime IoT Edge.

Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).   

### <a name="security"></a>Sécurité

L’agent IoT Edge joue un rôle essentiel dans la sécurité d’un appareil IoT Edge. Par exemple, il effectue des actions telles que la vérification de l’image d’un module avant de le démarrer. 

Pour plus d’informations sur le framework de sécurité Azure IoT Edge, consultez [Gestionnaire de sécurité IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Étapes suivantes

[Présentation des modules Azure IoT Edge](iot-edge-modules.md)
