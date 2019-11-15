---
title: Géoreprise d’activité après sinistre et haute disponibilité Azure Functions | Microsoft Docs
description: Découvrez comment utiliser les régions géographiques pour la redondance et basculer dans Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Azure Functions, modèles, bonnes pratiques, fonctions
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: 1d75d58a6df622ffb1b277f75ceedc2c2a66369d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576238"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Géo-reprise d’activité après sinistre dans Azure Functions

Quand l’ensemble d’une région Azure ou d’un centre de données connaît un temps d’arrêt, il est essentiel que le calcul puisse se poursuivre dans une autre région.  Cet article explique certaines stratégies que vous pouvez adopter pour déployer des fonctions assurant la reprise d’activité.

## <a name="basic-concepts"></a>Concepts de base

Azure Functions s’exécute dans une région spécifique.  Pour bénéficier d’une plus haute disponibilité, vous pouvez déployer les mêmes fonctions dans plusieurs régions.  Dans ce cas, vos fonctions peuvent s’exécuter suivant le modèle *actif/actif* ou *actif/passif*.  

* Mode actif/actif. Les deux régions sont actives et reçoivent des événements (en double ou en rotation). Le modèle actif/actif est recommandé pour les fonctions HTTPS en association avec Azure Front Door.
* Modèle actif/passif. Une région est active et reçoit des événements tandis qu’une autre région, la région secondaire, est inactive.  Quand un basculement est nécessaire, la région secondaire est activée et prend en charge le traitement.  Ce modèle est recommandé pour les fonctions non-HTTP comme les fonctions Service Bus et Event Hubs.

Pour plus d’informations sur les déploiements dans plusieurs régions, consultez cette page expliquant comment [exécuter des applications dans plusieurs régions](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="activeactive-for-https-functions"></a>Modèle actif/actif pour les fonctions HTTPS

Pour déployer des fonctions suivant le modèle actif/actif, vous devez disposer de composants pouvant coordonner les événements entre les deux régions.  Pour les fonctions HTTPS, cette coordination s’effectue à l’aide d’[Azure Front Door](../frontdoor/front-door-overview.md).  Azure Front Door peut router les requêtes HTTPS et les traiter par tourniquet (round robin) entre plusieurs fonctions régionales.  Il contrôle aussi régulièrement l’intégrité de chaque point de terminaison.  Si une fonction régionale cesse de répondre aux contrôles d’intégrité, Azure Front Door la met hors service et transfère le trafic uniquement aux fonctions saines.  

![Architecture autour d’Azure Front Door et des applications de fonction](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Modèle actif/actif pour les fonctions non-HTTPS

Vous pouvez toujours déployer des fonctions non-HTTPS suivant le modèle actif/actif.  Toutefois, vous devez réfléchir à la façon dont les deux régions vont interagir ou se coordonner entre elles.  Si vous avez déployé la même application de fonction dans deux régions (chacune se déclenchant sur la même file d’attente Service Bus), celles-ci se comporteront en tant que consommateurs concurrents pour retirer les messages de cette file d’attente.  Chaque message est alors traité par l’une des instances uniquement, mais cela signifie aussi qu’il existe toujours un point de défaillance unique sur la seule file d’attente Service Bus.  Si vous déployez deux files d’attente Service Bus (l’une dans une région primaire et l’autre dans une région secondaire) et que les deux applications de fonction pointaient vers la file d’attente de leur région, la difficulté réside maintenant dans la répartition des messages des files d’attente entre les deux régions.  Cela signifie souvent que chaque serveur de publication tente de publier un message dans *les deux* régions et que chaque message est traité par les deux applications de fonction actives.  Bien que cela crée un modèle actif/actif, d’autres difficultés apparaissent concernant la duplication du calcul ainsi que la façon dont les données sont regroupées (et à quel moment).  Il est donc préférable que les déclencheurs non-HTTPS utilisent le modèle actif/passif.

## <a name="activepassive-for-non-https-functions"></a>Modèle actif/passif pour les fonctions non-HTTPS

Le modèle actif/passif permet à une fonction unique de traiter chaque message. Il offre cependant un mécanisme assurant le basculement vers une région secondaire en cas de sinistre.  Azure Functions fonctionne avec la [géo-reprise d’activité d’Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) et la [géo-reprise d’activité d’Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md).

Si nous prenons pour exemples les déclencheurs Azure Event Hubs, le modèle actif/passif implique les points suivants :

* Azure Event Hub est déployé dans une région primaire et une région secondaire.
* La géo-reprise d’activité est activée pour jumeler les hubs d’événements primaire et secondaire.  Ceci crée également un « alias » que vous pouvez utiliser pour vous connecter aux hubs d’événements et passer du hub primaire au hub secondaire sans changer les informations de connexion.
* Les applications de fonction sont déployées dans une région primaire et une région secondaire.
* Les applications de fonction sont déclenchées sur la chaîne de connexion *directe* (et non l’alias) pour le hub d’événements correspondant. 
* Les serveurs de publication pointant vers le hub d’événements doivent publier sur la chaîne de connexion d’alias. 

![Exemple d’architecture actif/passif](media/functions-geo-dr/active-passive.png)

Avant le basculement, les serveurs de publication effectuant des envois vers l’alias partagé sont routés vers le hub d’événements primaire.  L’application de fonction primaire écoute exclusivement le hub d’événements primaire.  L’application de fonction secondaire est passive et inactive.  Dès que le basculement est déclenché, les serveurs de publication qui effectuent des envois vers l’alias partagé sont routés vers le hub d’événements secondaire.  L’application de fonction secondaire devient alors active et son déclenchement commence automatiquement.  Il est possible de réaliser en intégralité un basculement efficace vers une région secondaire à partir du hub d’événements, les fonctions devenant actives uniquement quand le hub d’événements correspondant est actif.

Consultez ces articles sur le basculement avec [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) et [Event Hubs](../event-hubs/event-hubs-geo-dr.md) pour en savoir plus et pour connaître les éléments à prendre en compte.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une porte d’entrée Azure](../frontdoor/quickstart-create-front-door.md)
* [Considérations relatives au basculement avec Event Hubs](../event-hubs/event-hubs-geo-dr.md#considerations)
