---
title: Présentation du runtime Azure IoT Edge | Microsoft Docs
description: En savoir plus sur le runtime Azure IoT Edge et les fonctionnalités qu’il procure à vos appareils Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c9c04a8310a46605cf5733131db1418b7cb7f7a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47218782"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Présentation du runtime Azure IoT Edge et de son architecture

Le runtime IoT Edge est une collection de programmes qui doivent être installés sur un appareil pour qu’il soit considéré comme un appareil IoT Edge. Collectivement, les composants du runtime IoT Edge permettent aux appareils IoT Edge de recevoir du code à exécuter dans la périphérie et de communiquer les résultats. 

Le runtime IoT Edge exécute les fonctions suivantes sur les appareils IoT Edge :

* Il installe et met à jour des charges de travail sur l’appareil.
* Il tient à jour les normes de sécurité Azure IoT Edge sur l’appareil.
* Il garantit que les [modules IoT Edge][lnk-modules] sont toujours en cours d’exécution.
* Il envoie des rapports d’intégrité du module dans le cloud pour la surveillance à distance.
* Facilite la communication entre les appareils de nœud terminal en aval et les appareils IoT Edge.
* Il facilite la communication entre les modules et le périphérique IoT Edge.
* Il facilite la communication entre l’appareil IoT Edge et le cloud.

![Le runtime IoT Edge communique des informations et des données sur l’intégrité du module à IoT Hub.][1]

Les responsabilités du runtime IoT Edge se répartissent en deux catégories : communication et gestion des modules. Ces deux rôles sont remplis par deux composants qui composent le runtime IoT Edge. Le hub IoT Edge est responsable de la communication, tandis que l’agent IoT Edge gère le déploiement et la surveillance des modules. 

Le hub Edge et l’agent Edge sont tous deux des modules, comme n’importe quel autre module exécuté sur un appareil IoT Edge. 

## <a name="iot-edge-hub"></a>Hub IoT Edge

Le hub Edge est l’un des deux modules qui composent le runtime Azure IoT Edge. Il joue le rôle de proxy local pour IoT Hub en exposant les mêmes points de terminaison de protocole qu’IoT Hub. Cette cohérence signifie que les clients (qu’il s’agisse d’appareils ou de modules) peuvent se connecter au runtime IoT Edge comme à IoT Hub. 

>[!NOTE]
>Edge Hub prend en charge les clients qui se connectent à l’aide de MQTT ou de AMQP. Il ne prend pas en charge les clients qui utilisent HTTP. 

Le hub Edge n’est pas une version complète d’IoT Hub qui s’exécute localement. Il y a certaines choses que le hub Edge délègue de manière silencieuse à IoT Hub. Par exemple, le hub Edge transfère les demandes d’authentification à IoT Hub quand un appareil essaie de se connecter pour la première fois. Une fois la première connexion établie, les informations de sécurité sont mises en cache localement par le hub Edge. Les connexions suivantes à partir de cet appareil sont autorisées sans avoir à s’authentifier sur le cloud. 

>[!NOTE]
>Le runtime doit être connecté chaque fois qu’il essaie d’authentifier un appareil.

Pour réduire la bande passante utilisée par votre solution IoT Edge, le hub Edge optimise le nombre de connexions établies avec le cloud. Le hub Edge accepte les connexions logiques à partir de clients tels que les modules ou les appareils feuilles, et il les combine pour établir une connexion physique unique au cloud. Les détails de ce processus sont transparents pour le reste de la solution. Les clients pensent avoir leur propre connexion au cloud, alors qu’ils passent tous par la même connexion. 

![Le hub Edge joue le rôle de passerelle entre plusieurs appareils physiques et le cloud][2]

Le hub Edge peut déterminer s’il est connecté à IoT Hub. Si la connexion est perdue, le hub Edge enregistre les messages ou les mises à jour de jumeau localement. Une fois la connexion rétablie, il synchronise toutes les données. L’emplacement utilisé pour ce cache temporaire est déterminé par une propriété du jumeau de module du hub Edge. La taille du cache n’est pas limitée et augmente tant que l’appareil a une capacité de stockage. 

### <a name="module-communication"></a>Communication des modules

Le hub Edge facilite la communication entre les modules. L’utilisation du hub Edge en tant que message broker permet aux modules de rester indépendants les uns des autres. Il suffit aux modules de spécifier les entrées sur lesquelles ils acceptent des messages et les sorties vers lesquelles ils écrivent des messages. Ensuite, un développeur de solutions assemble ces entrées et sorties afin que les modules traitent les données dans l’ordre propre à cette solution. 

![Le hub Edge facilite la communication entre les modules][3]

Pour envoyer des données au hub Edge, un module appelle la méthode SendEventAsync. Le premier argument spécifie sur quelle sortie envoyer le message. Le pseudo-code suivant envoie un message sur output1 :

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Pour recevoir un message, inscrivez un rappel qui traite les messages entrant sur une entrée spécifique. Le pseudo-code suivant inscrit la fonction messageProcessor à utiliser pour le traitement de tous les messages reçus sur input1 :

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Le développeur de solution doit spécifier les règles qui déterminent comment le hub Edge transmet les messages entre les modules. Les règles de routage sont définies dans le cloud et envoyées au hub Edge dans son jumeau d’appareil. La même syntaxe pour les itinéraires IoT Hub est utilisée pour définir les itinéraires entre les modules dans Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Itinéraires entre modules][4]

## <a name="iot-edge-agent"></a>Agent IoT Edge

L’agent IoT Edge est l’autre module qui compose le runtime Azure IoT Edge. Il est responsable de l’instanciation des modules, vérifie qu’ils continuent à s’exécuter, et signale l’état des modules à IoT Hub. Tout comme n’importe quel autre module, l’agent Edge utilise son jumeau de module pour stocker ces données de configuration. 

Le [démon de sécurité IoT Edge](iot-edge-security-manager.md) démarre l’agent Edge au démarrage de l’appareil. L’agent récupère son jumeau de module à partir d’IoT Hub et inspecte le manifeste de déploiement. Le manifeste de déploiement est un fichier JSON qui déclare les modules qui doivent être démarrés. 

Chaque élément du manifeste de déploiement contient des informations sur un module, et est utilisé par l’agent Edge pour contrôler le cycle de vie du module. Voici quelques-unes des propriétés les plus intéressantes : 

* **Settings.image** : image de conteneur utilisée par l’agent Edge pour démarrer le module. L’agent Edge doit être configuré avec des informations d’identification pour le registre de conteneurs si l’image est protégée par un mot de passe. Les informations d’identification du registre de conteneurs peuvent être configurées à distance à l’aide du manifeste de déploiement, ou directement sur l’appareil Edge en mettant à jour le fichier `config.yaml` dans le dossier du programme IoT Edge.
* **settings.createOptions** : chaîne qui est transmise directement au démon Docker lors du démarrage du conteneur d’un module. L’ajout d’options Docker dans cette propriété permet de bénéficier d’options avancées telles que le transfert de port ou le montage de volumes dans le conteneur d’un module.  
* **status** : état dans lequel l’agent Edge place le module. Cette valeur est généralement définie sur *running*, car la plupart des gens souhaitent que l’agent Edge démarre immédiatement tous les modules sur l’appareil. Toutefois, vous pouvez spécifier l’arrêt comme état initial d’un module, et demander ultérieurement à l’agent Edge de démarrer le module. L’agent Edge signale l’état de chaque module au cloud dans les propriétés déclarées. Une différence entre la propriété souhaitée et la propriété rapportée est un indicateur du dysfonctionnement de l’appareil. Les états pris en charge sont :
   * Downloading
   * Exécution
   * Unhealthy
   * Échec
   * Arrêté
* **restartPolicy** : indique comment l’agent Edge redémarre un module. Les valeurs possibles incluent :
   * Never : l’agent Edge ne redémarre jamais le module.
   * onFailure : si le module se bloque, l’agent Edge le redémarre. Si le module se ferme correctement, l’agent Edge ne le redémarre pas.
   * Unhealthy : si le module se bloque ou est considéré comme défectueux, l’agent Edge le redémarre.
   * Always : si le module se bloque, est considéré comme défectueux ou s’arrête d’une façon quelconque, l’agent Edge le redémarre. 

L’agent IoT Edge envoie la réponse d’exécution à IoT Hub. Voici une liste de réponses possibles :
  * 200 - OK
  * 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
  * 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
  * 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
  * 406 - L’appareil de périphérie est hors connexion ou n’envoie pas de rapports d’état.
  * 500 - Une erreur s’est produite dans le runtime de périphérie.

### <a name="security"></a>Sécurité

L’agent IoT Edge joue un rôle essentiel dans la sécurité d’un appareil IoT Edge. Par exemple, il effectue des actions telles que la vérification de l’image d’un module avant de le démarrer. 

Pour plus d’informations sur le framework de sécurité Azure IoT Edge, consultez [Gestionnaire de sécurité IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Étapes suivantes

[Présentation des certificats Azure IoT Edge][lnk-certs]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-certs]: iot-edge-certs.md
