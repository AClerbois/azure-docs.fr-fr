---
title: Référence des propriétés désirées de EdgeAgent et EdgeHub - Azure IoT Edge | Microsoft Docs
description: Passez en revue les propriétés spécifiques et les valeurs des jumeaux de module edgeAgent et edgeHub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b6eb0c5b0d52bba3d34c9853a73b1f3e07b112a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61322703"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriétés des jumeaux de module de l’agent IoT Edge et du hub IoT Edge

L’agent IoT Edge et le hub IoT Edge sont les deux modules qui constituent le runtime IoT Edge. Pour plus d’informations sur les rôles de chaque module, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md). 

Cet article fournit les propriétés souhaitées et signalées des jumeaux de module du runtime. Pour plus d’informations sur le déploiement de modules sur des appareils IoT Edge, consultez [Déploiement et supervision](module-deployment-monitoring.md).

## <a name="edgeagent-desired-properties"></a>Propriétés souhaitées pour EdgeAgent

Le jumeau de module de l’agent IoT Edge est appelé `$edgeAgent` et coordonne les communications entre l’agent IoT Edge exécuté sur un appareil et IoT Hub. Les propriétés souhaitées sont définies lors de l’application d’un manifeste de déploiement sur un appareil spécifique dans le cadre d’un déploiement d’appareil unique ou à grande échelle. 

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| schemaVersion | Doit être "1.0" | OUI |
| runtime.type | Doit être "docker" | OUI |
| runtime.settings.minDockerVersion | Définie sur la version Docker minimale requise par ce manifeste de déploiement | OUI |
| runtime.settings.loggingOptions | JSON converti en chaînes et contenant les options de journalisation du conteneur d’agent IoT Edge. [Options de journalisation Docker](https://docs.docker.com/engine/admin/logging/overview/) | Non |
| runtime.settings.registryCredentials<br>.{registryId}.username | Nom d’utilisateur du registre de conteneurs. Pour Azure Container Registry, le nom d’utilisateur est généralement le nom du registre.<br><br> Les informations d’identification du registre sont nécessaires pour toutes les images de modules qui ne sont pas publiques. | Non |
| runtime.settings.registryCredentials<br>.{registryId}.password | Mot de passe du registre de conteneurs. | Non |
| runtime.settings.registryCredentials<br>.{registryId}.address | Adresse du registre de conteneurs. Pour Azure Container Registry, l’adresse est généralement *{nom du registre}.azurecr.io*. | Non |  
| systemModules.edgeAgent.type | Doit être "docker" | OUI |
| systemModules.edgeAgent.settings.image | URI de l’image de l’agent IoT Edge. Actuellement, l’agent IoT Edge ne peut pas se mettre à jour lui-même. | OUI |
| systemModules.edgeAgent.settings<br>.createOptions | JSON converti en chaînes et contenant les options de création du conteneur d’agent IoT Edge. [Options de création Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Non |
| systemModules.edgeAgent.configuration.id | ID du déploiement ayant déployé ce module. | IoT Hub définit cette propriété quand le manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |
| systemModules.edgeHub.type | Doit être "docker" | OUI |
| systemModules.edgeHub.type | Doit être "running" | OUI |
| systemModules.edgeHub.restartPolicy | Doit être "always" | OUI |
| systemModules.edgeHub.settings.image | URI de l’image du hub IoT Edge. | OUI |
| systemModules.edgeHub.settings<br>.createOptions | JSON converti en chaînes et contenant les options de création du conteneur du hub IoT Edge. [Options de création Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Non |
| systemModules.edgeHub.configuration.id | ID du déploiement ayant déployé ce module. | IoT Hub définit cette propriété quand le manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |
| modules.{moduleId}.version | Chaîne définie par l’utilisateur représentant la version de ce module. | OUI |
| modules.{moduleId}.type | Doit être "docker" | OUI |
| modules.{moduleId}.status | {"running" \| "stopped"} | OUI |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | OUI |
| modules.{moduleId}.settings.image | URI de l’image du module. | OUI |
| modules.{moduleId}.settings.createOptions | Champ de chaîne JSON contenant les options de création du conteneur de module. [Options de création Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Non |
| modules.{moduleId}.configuration.id | ID du déploiement ayant déployé ce module. | IoT Hub définit cette propriété quand le manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |

## <a name="edgeagent-reported-properties"></a>Propriétés signalées pour EdgeAgent

Les propriétés signalées pour l’agent IoT Edge incluent trois informations principales :

1. l’état de l’application de dernières propriétés souhaitées affichées ;
2. L’état des modules en cours d’exécution sur l’appareil, comme signalé par l’agent IoT Edge
3. une copie des propriétés souhaitées en cours d’exécution sur l’appareil.

Cette dernière information est utile au cas où les dernières propriétés souhaitées ne seraient pas appliquées avec succès par le runtime, et que l’appareil exécuterait encore un manifeste de déploiement précédent.

> [!NOTE]
> Les propriétés signalées de l’agent IoT Edge sont utiles, car elles peuvent être interrogées avec le [langage de requête IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) afin de connaître l’état des déploiements à grande échelle. Pour plus d’informations sur l’utilisation des propriétés de l’agent IoT Edge pour l’état, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à grande échelle](module-deployment-monitoring.md).

Le tableau suivant n’inclut pas les informations copiées à partir des propriétés souhaitées.

| Propriété | Description |
| -------- | ----------- |
| lastDesiredVersion | Cet entier référence la dernière version des propriétés souhaitées traitées par l’agent IoT Edge. |
| lastDesiredStatus.code | Il s’agit du code d’état référençant les dernières propriétés souhaitées observées par l’agent IoT Edge. Valeurs autorisées : `200` Réussite, `400` Configuration non valide, `412` Version de schéma non valide, `417` les propriétés souhaitées sont vides, `500` Échec |
| lastDesiredStatus.description | Texte de description de l’état |
| deviceHealth | `healthy` si l’état du runtime de tous les modules est `running` ou `stopped`, sinon, `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` si l’état du runtime de tous les modules définis par le déploiement {deploymentId} est `running` ou `stopped`, sinon, `unhealthy` |
| runtime.platform.OS | Signalement du système d’exploitation exécuté sur l’appareil |
| runtime.platform.architecture | Signalement de l’architecture de l’UC sur l’appareil |
| systemModules.edgeAgent.runtimeStatus | État signalé de l’agent IoT Edge : {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Texte de description de l’état signalé de l’agent IoT Edge. |
| systemModules.edgeHub.runtimeStatus | État du hub IoT Edge : { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Texte de description de l’état du hub IoT Edge si celui-ci est non sain. |
| systemModules.edgeHub.exitCode | En cas de sortie, le code de sortie signalé par le conteneur du hub IoT Edge. |
| systemModules.edgeHub.startTimeUtc | Heure du dernier démarrage du hub IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Heure à laquelle l’utilisateur a quitté le hub IoT Edge pour la dernière fois |
| systemModules.edgeHub.lastRestartTimeUtc | Heure du dernier redémarrage du hub IoT Edge |
| systemModules.edgeHub.restartCount | Nombre de redémarrages de ce module dans le cadre de la stratégie de redémarrage. |
| modules.{moduleId}.runtimeStatus | État du module : { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Texte de description de l’état du module si celui-ci est non sain. |
| modules.{moduleId}.exitCode | En cas de sortie, le code de sortie signalé par le conteneur de module |
| modules.{moduleId}.startTimeUtc | Heure du dernier démarrage du module |
| modules.{moduleId}.lastExitTimeUtc | Heure de la dernière sortie du module |
| modules.{moduleId}.lastRestartTimeUtc | Heure du dernier redémarrage du module |
| modules.{moduleId}.restartCount | Nombre de redémarrages de ce module dans le cadre de la stratégie de redémarrage. |

## <a name="edgehub-desired-properties"></a>Propriétés souhaitées pour EdgeHub

Le jumeau de module du hub IoT Edge est appelé `$edgeHub` et coordonne les communications entre le hub IoT Edge exécuté sur un appareil et IoT Hub. Les propriétés souhaitées sont définies lors de l’application d’un manifeste de déploiement sur un appareil spécifique dans le cadre d’un déploiement d’appareil unique ou à grande échelle. 

| Propriété | Description | Requise dans le manifeste de déploiement |
| -------- | ----------- | -------- |
| schemaVersion | Doit être "1.0" | OUI |
| routes.{routeName} | Chaîne représentant la route d’un hub IoT Edge. | L’élément `routes` peut être présent mais vide. |
| storeAndForwardConfiguration.timeToLiveSecs | Durée, en secondes, pendant laquelle le hub IoT Edge conserve les messages en cas de points de terminaison de routage déconnectés (par exemple, déconnectés d’IoT Hub ou d’un module local) | OUI |

## <a name="edgehub-reported-properties"></a>Propriétés signalées pour EdgeHub

| Propriété | Description |
| -------- | ----------- |
| lastDesiredVersion | Cet entier référence la dernière version des propriétés souhaitées traitées par le hub IoT Edge. |
| lastDesiredStatus.code | Il s’agit du code d’état référençant les dernières propriétés souhaitées observées par le hub IoT Edge. Valeurs autorisées : `200` Réussite, `400` Configuration non valide, `500` Échec |
| lastDesiredStatus.description | Texte de description de l’état |
| clients.{device or moduleId}.status | État de connectivité de cet appareil ou module. Valeurs possibles {"connected" \| "disconnected"}. Seules les identités de module peuvent être à l’état déconnecté. Les appareils en aval se connectant au hub IoT Edge ne s’affichent que lorsqu’ils sont connectés. |
| clients.{device or moduleId}.lastConnectTime | Dernière connexion de l’appareil ou du module |
| clients.{device or moduleId}.lastDisconnectTime | Dernière déconnexion de l’appareil ou du module |

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser ces propriétés pour générer des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisées](module-composition.md).
