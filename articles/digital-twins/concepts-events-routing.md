---
title: Routage des événements et des messages avec Azure Digital Twins | Microsoft Docs
description: Vue d’ensemble du routage des événements et des messages vers des points de terminaison de service avec Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 7dfda00aca403f7f95f0c56d1db28c3c609bebd2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080658"
---
# <a name="routing-events-and-messages"></a>Routage des événements et des messages

Les solutions IoT unifient souvent plusieurs services puissants, incluant le stockage et l’analytique. Cet article explique comment connecter des applications Azure Digital Twins aux services de stockage, d’intelligence artificielle et d’analytique Azure pour leur conférer des insights et des fonctionnalités avancés.

## <a name="route-types"></a>Types de routage  

Azure Digital Twins propose deux façons de relier des événements IoT à d’autres services ou applications métier Azure :

* **Routage des événements Azure Digital Twins** : Un changement d’objet du graphe spatial, la réception de données de télémétrie ou une notification basée sur des conditions prédéfinies créée par une fonction définie par l’utilisateur peut déclencher des événements Azure Digital Twins. Les utilisateurs peuvent envoyer ces événements à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), à des [rubriques Azure Service Bus](https://azure.microsoft.com/services/service-bus/) ou à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pour les traiter ultérieurement.

* **Routage des données de télémétrie d’appareil** : En plus de router les événements, Azure Digital Twins peut router les messages de télémétrie d’appareil bruts vers Event Hubs pour obtenir des insights et une analytique supplémentaires. Ces types de messages ne sont pas traités par Azure Digital Twins. Et ils ne sont transmis qu’au hub d’événements.

Les utilisateurs peuvent spécifier un ou plusieurs points de sortie pour envoyer des événements ou transférer des messages. Les événements et les messages sont envoyés aux points de terminaison en fonction de ces préférences de routage prédéfinies. En d’autres termes, les utilisateurs peuvent spécifier un point de terminaison pour la réception des événements d’opération de graphe, un autre pour la réception des événements de télémétrie d’appareil, et ainsi de suite.

![Routage des événements Azure Digital Twins][1]

Le routage vers Event Hubs conserve l’ordre dans lequel les messages de télémétrie sont envoyés. Ainsi, ces derniers arrivent au point de terminaison dans l’ordre dans lequel ils ont été initialement reçus. Event Grid et Service Bus ne garantissent pas que les points de terminaison recevront les événements dans l’ordre dans lequel ils se sont produits. Toutefois, le schéma d’événement comprend un horodatage que vous pouvez utiliser pour identifier l’ordre une fois les événements arrivés au point de terminaison.

## <a name="route-implementation"></a>Implémentation du routage

Le service Azure Digital Twins prend en charge les types de point de terminaison (**EndpointTypes**) suivants :

* **EventHub** est le point de terminaison de chaîne de connexion Event Hub.
* **ServiceBus** est le point de terminaison de chaîne de connexion Service Bus.
* **EventGrid** est le point de terminaison de chaîne de connexion Event Grid.

Azure Digital Twins prend en charge les types d’événement (**EventTypes**) suivants qui sont envoyés au point de terminaison choisi :

* **DeviceMessages** sont les messages de télémétrie envoyés à partir des appareils des utilisateurs et transférés par le système.
* **TopologyOperation** est une opération qui change le graphe ou les métadonnées de celui-ci. Par exemple, l’ajout ou la suppression d’une entité, telle qu’un espace.
* **SpaceChange** est une modification de la valeur calculée d’un espace qui résulte d’un message de télémétrie d’appareil.
* **SensorChange** est une modification de la valeur calculée d’un capteur qui résulte d’un message de télémétrie d’appareil.
* **UdfCustom** est une notification personnalisée issue d’une fonction définie par l’utilisateur.

> [!IMPORTANT]  
> Les types de point de terminaison (**EndpointTypes**) ne prennent pas tous en charge tous les types d’événement (**EventTypes**).
> Reportez-vous au tableau suivant pour connaître les types d’événement (**EventTypes**) autorisés pour chaque type de point de terminaison (**EndpointType**).

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| Event Hub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Pour obtenir plus d’informations sur la création de points de terminaison et des exemples de schéma d’événements, consultez [Sortie et points de terminaison](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les limites de la préversion publique d’Azure Digital Twins, consultez [Limites de service de la préversion publique](concepts-service-limits.md).

- Pour tester un exemple Azure Digital Twins, consultez [Démarrage rapide : Rechercher des salles disponibles](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
