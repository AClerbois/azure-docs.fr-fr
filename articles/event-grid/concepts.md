---
title: Concept d’Azure Event Grid
description: Détaille Azure Event Grid et ses concepts. Définit plusieurs composants clés de Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 1c77d0ea9e67c8d69f3f632cace164d8a0c4d921
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617595"
---
# <a name="concepts-in-azure-event-grid"></a>Concepts utilisés dans Azure Event Grid

Cet article décrit les principaux concepts utilisés dans Azure Event Grid.

## <a name="events"></a>Événements

Un événement correspond à la plus petite quantité d’informations décrivant intégralement quelque chose qui s’est produit dans le système. Chaque événement possède des informations communes telles que la source de l’événement, l’heure à laquelle l’événement a eu lieu et un identificateur unique. Chaque événement comporte également des informations spécifiques qui se rapportent uniquement au type d’événement en question. Par exemple, un événement concernant un fichier en cours de création dans le Stockage Azure a des informations détaillées sur le fichier, notamment la valeur `lastTimeModified`. Ou bien, un événement Event Hubs contient l’URL du fichier Capture. 

Chaque événement est limité à 64 Ko de données.

Pour les propriétés qui sont envoyées dans un événement, consultez le [schéma d’événements Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Éditeurs

Un éditeur est l’utilisateur ou organisation qui décide d’envoyer des événements à Event Grid. Microsoft publie des événements pour plusieurs services Azure. Vous pouvez publier des événements à partir de votre propre application. Les organisations qui hébergent des services en dehors d’Azure peuvent publier des événements par le biais d’Event Grid.

## <a name="event-sources"></a>Sources d’événement

La source d’un événement désigne l’endroit où l’événement se produit. Chaque source de l’événement est liée à un ou plusieurs types d’événements. Par exemple, le stockage Azure est la source d’événement des événements créés pour l’objet blob. IoT Hub est la source d’événements pour les événements créés par l’appareil. Votre application est la source d’événements pour les événements personnalisés que vous définissez. Les sources d’événements sont responsables de l’envoi des événements à Event Grid.

Pour plus d’informations sur l’implémentation d’une source Event Grid prise en charge, consultez [Sources d’événements dans Azure Event Grid](event-sources.md).

## <a name="topics"></a>Rubriques

La rubrique Event Grid fournit un point de terminaison où la source envoie les événements. L’éditeur crée la rubrique Event Grid et décide si une source d’événements a besoin d’une ou plusieurs rubriques. Une rubrique est utilisée pour une collection d’événements connexes. Pour répondre à certains types d’événements, les abonnés décident des rubriques auxquelles ils souhaitent s’abonner.

Les rubriques système sont des rubriques intégrées fournies par les services Azure. Vous ne voyez pas les rubriques système dans votre abonnement Azure car l’éditeur en est propriétaire, mais vous pouvez vous y abonner. Pour vous abonner, vous fournissez des informations sur la ressource à partir de laquelle vous souhaitez recevoir des événements. Tant que vous avez accès à la ressource, vous pouvez vous abonner à ses événements.

Les rubriques personnalisées sont des rubriques tierces et applicatives. Quand vous créez une rubrique personnalisée ou que l’accès à une rubrique personnalisée vous est accordé, celle-ci est visible dans votre abonnement.

Lorsque vous concevez votre application, vous pouvez choisir le nombre de rubriques à créer. Pour les solutions volumineuses, créez une rubrique personnalisée pour chaque catégorie d’événements associés. Par exemple, envisagez une application qui envoie des événements liés à la modification de comptes d’utilisateur et au traitement de commandes. Il est peu probable qu’un gestionnaire d’événements accepte les deux catégories d’événements. Créez deux rubriques personnalisées et laissez les gestionnaires d’événements s’abonner à celle qui les intéresse. Pour les solutions de petite taille, vous pouvez à la place envoyer tous les événements à une seule rubrique. Les abonnés à des événements peuvent filtrer les types d’événements qu’ils souhaitent.

## <a name="event-subscriptions"></a>Abonnements à des événements

Un abonnement indique à Event Grid quels événements vous souhaitez recevoir sur une rubrique. Lors de la création de l’abonnement, vous spécifiez un point de terminaison pour la gestion de l’événement. Vous pouvez filtrer les événements qui sont envoyés au point de terminaison. Vous pouvez filtrer par type d’événement, ou modèle d’objet. Pour plus d’informations, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).

Pour obtenir des exemples de création d’abonnements, consultez :

* [Exemples d’interface de ligne de commande Azure pour Event Grid](cli-samples.md)
* [Exemples Azure PowerShell pour Event Grid](powershell-samples.md)
* [Modèles Azure Resource Manager pour Event Grid](template-samples.md)

Pour plus d’informations sur l’obtention de vos abonnements Event Grid actuels, consultez [Interroger les abonnements Event Grid](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Expiration de l’abonnement aux événements

[L’extension Event Grid](/cli/azure/azure-cli-extensions-list) pour Azure CLI permet de définir une date d’expiration lors de la création d’un abonnement aux événements. Si vous vous servez de l’API REST, utilisez `api-version=2018-09-15-preview`.

L’abonnement aux événements expire automatiquement après cette date. Définissez une expiration pour les abonnements aux événements qui ne sont nécessaires que pour une durée limitée et que vous ne souhaitez pas avoir à nettoyer ; par exemple, si vous créez un abonnement aux événements pour tester un scénario. 

Pour un exemple de configuration d’une expiration, voir [S’abonner avec des filtres avancés](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Gestionnaires d’événements

Pour Event Grid, un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Event Grid prend en charge plusieurs types de gestionnaires. Vous pouvez utiliser un service Azure pris en charge ou votre propre webhook comme gestionnaire. En fonction du type de gestionnaire, Event Grid suit différents mécanismes pour garantir la distribution de l’événement. Pour les gestionnaires d’événements HTTP Webhook, l’exécution de l’événement est retentée jusqu'à ce que le gestionnaire renvoie un code d’état de `200 – OK`. Dans le cas de la file d’attente de Stockage Azure, l’exécution des événements est retentée jusqu’à ce que le service de File d’attente traite correctement la transmission de type push du message dans la file d’attente.

Pour plus d’informations sur l’implémentation d’un gestionnaire Event Grid pris en charge, consultez [Gestionnaires d’événements dans Azure Event Grid](event-handlers.md).

## <a name="security"></a>Sécurité

Event Grid assure la sécurité lors de l’abonnement et de la publication de rubriques. Durant l’abonnement, vous devez disposer des autorisations appropriées sur la ressource ou la rubrique de grille d’événement. Lors de la publication, vous devez disposer d’un jeton SAS ou d’une clé d’authentification pour la rubrique. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](security-authentication.md).

## <a name="event-delivery"></a>Remise d’événement

Si Event Grid ne peut pas confirmer qu’un événement a été reçu par le point de terminaison de l’abonné, il procède à une nouvelle distribution de l’événement. Pour plus d’informations, consultez la page [Distribution et nouvelle tentative de distribution de messages avec Event Grid](delivery-and-retry.md).

## <a name="batching"></a>Traitement par lot

Quand vous utilisez une rubrique personnalisée, les événements doivent toujours être publiés dans un tableau. Il peut s’agir d’un lot de un pour les scénarios à faible débit. Toutefois, dans les cas d’utilisation à volume élevé, il est recommandé de regrouper dans un lot plusieurs événements par publication afin d’améliorer l’efficacité. La taille maximale d’un lot est de 1 Mo. Un événement ne doit pas dépasser 64 Ko.

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
