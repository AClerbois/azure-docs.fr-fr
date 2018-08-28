---
title: Vue d’ensemble d’Azure Event Grid
description: Détaille Azure Event Grid et ses concepts.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 08/17/2018
ms.author: babanisa
ms.openlocfilehash: 90e8d6a3ef093046c5ee6324f6e6590e59124da7
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42023140"
---
# <a name="an-introduction-to-azure-event-grid"></a>Présentation d’Azure Event Grid

Azure Event Grid vous permet de créer facilement des applications avec les architectures basées sur des événements. Tout d’abord, sélectionnez la ressource Azure à laquelle vous souhaitez vous abonner, puis donnez un gestionnaire d’événements ou un point de terminaison WebHook auquel envoyé l’événement. Event Grid dispose d’une prise en charge intégrée pour les événements provenant des services Azure, tels que les objets BLOB de stockage et les groupes de ressources. Event Grid dispose également d’une prise en charge pour vos propres événements, à l’aide de rubriques personnalisées. 

Vous pouvez utiliser des filtres pour acheminer des événements spécifiques à différents points de terminaison, multidiffuser vers des points de terminaison multiples et vous assurez que vos événements sont correctement livré.

Actuellement, Azure Event Grid est disponible dans toutes les régions publiques. Il n’est pas encore disponible dans les clouds Azure - Allemagne, Azure - Chine ou Azure Government.

Cet article fournit une vue d’ensemble d’Azure Event Grid. Pour bien démarrer avec Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md). 

![Modèle de grille d’événement fonctionnel](./media/overview/functional-model.png)

Veuillez noter : cette image montre comment Event Grid connecte les sources et les descripteurs, mais elle ne constitue pas une liste complète des intégrations prises en charge.

## <a name="event-sources"></a>Sources d’événement

Pour plus d’informations sur les fonctionnalités de chaque source ainsi que les articles connexes, consultez [Sources d’événement](event-sources.md). Actuellement, les services Azure suivants prennent en charge l’envoi d’événements vers Event Grid :

* Abonnements Azure (opérations de gestion)
* Container Registry
* Rubriques personnalisées
* Event Hubs
* IoT Hub
* Media Services
* Groupes de ressources (opérations de gestion)
* Service Bus
* Storage Blob
* Stockage à usage général v2 (GPv2)

## <a name="event-handlers"></a>Gestionnaires d’événements

Pour plus d’informations sur les fonctionnalités de chaque gestionnaire, ainsi que les articles connexes, consultez [Gestionnaire d'événements](event-handlers.md). Actuellement, les services Azure suivants prennent en charge la gestion d’événements depuis Event Grid : 

* Azure Automation
* Azure Functions
* Event Hubs
* les connexions hybrides
* Logic Apps
* Microsoft Flow
* Stockage File d’attente
* WebHooks

## <a name="concepts"></a>Concepts

Il existe cinq concepts dans Azure Event Grid qui vous permettent de démarrer :

* **Événements** : ce qu’il s’est passé.
* **Sources d’événements** : où l’événement a eu lieu.
* **Rubriques** : le point de terminaison où les éditeurs envoient des événements.
* **Abonnements aux événements** : le mécanisme de point de terminaison ou intégré pour acheminer des événements, parfois à plusieurs gestionnaires. Les abonnements sont également utilisés par des gestionnaires pour filtrer intelligemment les événements entrants.
* **Gestionnaires d’événements** : l’application ou le service réagissant à l’événement.

Pour plus d’informations sur ces concepts, consultez [Concepts dans Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Fonctionnalités

Voici les principales fonctionnalités d’Azure Event Grid :

* **Simplicité** : pointez et cliquez pour chercher des événements depuis votre ressource Azure vers tout gestionnaire ou point de terminaison d’événement.
* **Filtrage avancé** : filtrez le type d’événement ou le chemin de publication de l’événement pour s’assurer que les gestionnaires d’événements ne reçoivent que des événements pertinents.
* **Distribution ramifiée** : abonnez-vous à plusieurs points de terminaison pour le même événement pour envoyer des copies de l’événement à autant de places que nécessaire.
* **Fiabilité** : utiliser la nouvelle tentative de 24 heures avec interruption exponentielle pour garantir la livraison des événements.
* **Payer par événement** : payez uniquement pour le temps d’utilisation d’Event Grid.
* **Débit élevé** : générez des charges de travail élevées sur Event Grid avec prise en charge de millions d’événements par seconde.
* **Événements intégrés** : préparez et soyez rapidement opérationnel avec des événements intégrés définis par la ressource.
* **Événements personnalisés** : utilisez le routage Event Grid, le filtrage et les événements personnalisés livré de manière fiable dans votre application.

Pour comparer les services Event Grid, Event Hubs et Service Bus, consultez [Choisir entre des services Azure qui remettent des messages](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Que puis-je faire avec Event Grid ?

Azure Event Grid fournit plusieurs fonctionnalités qui améliorent considérablement l’automatisation d’opérations sans serveur et le travail d’intégration : 

### <a name="serverless-application-architectures"></a>Architectures d’application sans serveur

![Application sans serveur](./media/overview/serverless_web_app.png)

La grille d’événement connecte des sources de données et des gestionnaires d’événements. Par exemple, utilisez la grille d’événement pour déclencher instantanément une fonction sans serveur afin d’exécuter une analyse d’image chaque fois qu’une nouvelle photo est ajoutée à un conteneur de stockage d’objets blob. 

### <a name="ops-automation"></a>Automatisation des opérations

![Automatisation des opérations](./media/overview/Ops_automation.png)

Event Grid vous permet d’accélérer l’automatisation et de simplifier l’application de la stratégie. Par exemple, Event Grid peut informer Azure Automation lors de la création d’une machine virtuelle ou de l’entrée en service d’une base de données SQL. Ces événements permettent de vérifier automatiquement que les configurations de service sont conformes, placent des métadonnées dans les outils d’exploitation, étiquètent les machines virtuelles ou archivent des éléments de travail.

### <a name="application-integration"></a>Intégration d’applications

![Intégration d’applications](./media/overview/app_integration.png)

Event Grid connecte votre application à d’autres services. Par exemple, créez une rubrique d’application pour envoyer les données d’événement de votre application à Event Grid et profitez de sa livraison fiable, de son routage avancé et de son intégration directe à Azure. Vous pouvez également utiliser Event Grid avec Logic Apps pour traiter des données en tout lieu sans écrire de code. 

## <a name="how-much-does-event-grid-cost"></a>Combien coûte Event Grid ?

Azure Event Grid utilise un modèle de tarification de paie par événement, afin que vous ne payez que ce que vous utilisez. Les 100 000 premières opérations par mois sont gratuites. Les opérations sont définies en tant qu’entrées d’événement, tentatives de remise d’abonnement, appels de gestion et filtrage par suffixe d’objet. Pour plus d’informations, visitez la [page de tarification](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Étapes suivantes

* [Router les événements d’objet blob de stockage](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Répondez aux événements d’objet blob de stockage à l’aide d’Event Grid.
* [Créer des événements personnalisés et s’y abonner](custom-event-quickstart.md)  
  Démarrez et commencez à envoyer vos propres événements personnalisés vers tout point de terminaison à l’aide du guide de démarrage rapide Azure Event Grid.
* [Utilisation de Logic Apps en tant que gestionnaire d’événements](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Didacticiel sur la génération d’une application à l’aide de Logic Apps pour réagir aux événements envoyés par Event Grid.
* [Diffuser en continu des données volumineuses dans un entrepôt de données](event-grid-event-hubs-integration.md)  
  Didacticiel qui utilise Azure Functions pour diffuser en continu des données à partir d’Event Hubs vers SQL Data Warehouse.
* [Référence de l'API REST Event Grid](/rest/api/eventgrid)  
  Fournit des informations techniques supplémentaires sur Azure Event Grid et une référence pour la gestion des abonnements aux événements, le routage et le filtrage.