---
title: Surveiller la remise des messages Azure Event Grid
description: Décrit comment surveiller la remise des messages Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824092"
---
# <a name="monitor-event-grid-message-delivery"></a>Surveiller la remise des messages Event Grid 

Cet article décrit comment utiliser le portail pour connaître l’état des remises des événements.

Event Grid assure une distribution fiable. Il distribue chaque message au moins une fois pour chaque abonnement. Les événements sont envoyés immédiatement au webhook inscrit de chaque abonnement. Si un webhook n’accuse pas réception d’un événement dans les 60 secondes suivant la première tentative de distribution, Event Grid effectue une nouvelle tentative de distribution de l’événement.

Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métriques des remises

Le portail affiche les métriques pour l’état de la remise des messages d’événement.

Pour les rubriques, les métriques sont les suivantes :

* **Publication réussie** : événement correctement envoyé à la rubrique et traité avec une réponse 2xx.
* **Échec de la publication** : événement envoyé à la rubrique mais rejeté avec un code d’erreur.
* **Sans correspondance** : événement correctement publié dans la rubrique, mais sans correspondance avec un abonnement aux événements. L’événement a été supprimé.

Pour les abonnements, les métriques sont les suivantes :

* **Remise réussie** : l’événement a été correctement remis au point de terminaison de l’abonnement et a reçu une réponse 2xx.
* **Échec de la remise** : l’événement a été envoyé au point de terminaison de l’abonnement, mais a reçu une réponse 4xx ou 5xx.
* **Événements expirés** : l’événement n’a pas été remis et toutes les nouvelles tentatives ont été envoyées. L’événement a été supprimé.
* **Événements correspondants** : l’événement dans la rubrique a été mis en correspondance par l’abonnement aux événements.

## <a name="event-subscription-status"></a>État de l’abonnement aux événements

Pour afficher les métriques pour un abonnement aux événements, vous pouvez rechercher par type d’abonnement ou par abonnement pour une ressource spécifique.

Pour effectuer une recherche par type d’abonnement aux événements, sélectionnez **Tous les services**.

![Sélectionner Tous les services](./media/monitor-event-delivery/all-services.png)

Recherchez **event grid** et sélectionnez **Abonnements Event Grid** parmi les options disponibles.

![Rechercher des abonnements aux événements](./media/monitor-event-delivery/search-and-select.png)

Filtrez par le type d’événement, l’abonnement et l’emplacement. Sélectionnez **Métriques** pour l’abonnement à afficher.

![Filtrer les abonnements aux événements](./media/monitor-event-delivery/filter-events.png)

Affichez les métriques de la rubrique d’événement et de l’abonnement.

![Afficher les métriques d’événement](./media/monitor-event-delivery/subscription-metrics.png)

Pour rechercher les métriques pour une ressource spécifique, sélectionnez cette ressource. Ensuite, sélectionnez **Événements**.

![Sélectionner les événements pour une ressource](./media/monitor-event-delivery/select-events.png)

Les métriques pour les abonnements pour cette ressource s’affichent.

## <a name="custom-event-status"></a>État d’un événement personnalisé

Si vous avez publié une rubrique personnalisée, vous pouvez afficher ses métriques. Sélectionnez le groupe de ressources pour la rubrique, puis sélectionnez la rubrique.

![Sélectionner une rubrique personnalisée](./media/monitor-event-delivery/select-custom-topic.png)

Afficher les métriques de la rubrique d’événement personnalisée.

![Afficher les métriques d’événement](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
