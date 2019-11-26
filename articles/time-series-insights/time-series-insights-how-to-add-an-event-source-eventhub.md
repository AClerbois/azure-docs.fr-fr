---
title: Ajouter une source d’événement Event Hubs - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment ajouter une source d’événement Azure Event Hubs à votre environnement Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012620"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Ajouter une source d’événement Event Hub à votre environnement Time Series Insights

Cet article explique comment utiliser le Portail Azure pour ajouter une source d’événement qui lit des données à partir d’Azure Event Hubs dans votre environnement Azure Time Series Insights.

> [!NOTE]
> Les étapes décrites dans cet article s’appliquent à la fois aux environnements Time Series Insights - GA et Time Series Insights - Préversion.

## <a name="prerequisites"></a>Prérequis

- Créez un environnement Time Series Insights comme décrit dans [Créer un environnement Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Créez un hub d’événements. Consultez [Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure](../event-hubs/event-hubs-create.md).
- L’Event Hub doit avoir reçu des événements de message actifs. Découvrez comment [Envoyer des événements vers Azure Event Hubs à l’aide de .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Créez un groupe de consommateurs dédié dans l’Event Hub que l’environnement Time Series Insights peut utiliser. Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié, qui n’est pas partagé avec un autre consommateur. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Il existe également une limite de 20 groupes de consommateurs par Event Hub. Pour plus d’informations, consultez le [Guide de programmation Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Ajouter un groupe de consommateurs à votre Event Hub

Les applications utilisent des groupes de consommateurs pour extraire des données d’Azure Event Hubs. Indiquez un groupe de consommateurs dédiés, qui sera utilisé par cet environnement Time Series Insights uniquement, pour lire les données de manière fiable à partir de votre Event Hub.

Pour ajouter un groupe de consommateurs dans votre Event Hub :

1. Dans le [Portail Azure](https://portal.azure.com), recherchez et ouvrez votre hub d’événements à partir de l’espace de noms Event Hub.

    [![Ouvrez votre espace de noms Event Hub](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Sous **Entités**, sélectionnez **Groupes de consommateurs**, puis **Groupe de consommateurs**.

   [![Event Hub - Ajouter un groupe de consommateurs](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Sur la page **Groupes de consommateurs**, indiquez une valeur unique pour **Nom**.  Utilisez le même nom lorsque vous créez une source d’événement dans l’environnement Time Series Insights.

1. Sélectionnez **Create** (Créer).

## <a name="add-a-new-event-source"></a>Ajouter une nouvelle source d’événement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Recherchez votre environnement Time Series Insights existant. Dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez votre environnement Time Series Insights.

1. Sous **Topologie de l’environnement**, sélectionnez **Sources d’événements**, puis sélectionnez **Ajouter**.

   [![Sous Sources d’événements, sélectionnez le bouton Ajouter](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Sous **Nom de la source de l’événement**, indiquez une valeur unique pour cet environnement Time Series Insights, comme **event-stream**.

1. Pour **Source**, sélectionnez **Event Hub**.

1. Sélectionnez les valeurs appropriées sous **Option d’importation** :

   * Si un Event Hub est déjà présent dans l’un de vos abonnements, sélectionnez **Utiliser un Event Hub à partir des abonnements disponibles**. Il s’agit de l’approche la plus simple.

     [![Sélectionnez une option d’importation de source d’événement](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  Le tableau suivant décrit les propriétés requises pour l’option **Utiliser un Event Hub à partir des abonnements disponibles** :

       [![Détails sur l’abonnement et Event Hub](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Propriété | Description |
       | --- | --- |
       | Subscription | Abonnement auquel appartiennent l’espace de noms et l’instance Event Hub souhaités. |
       | Espace de noms Event Hub | Espace de noms Event Hub auquel appartient l’instance Event Hub souhaitée. |
       | Nom de l’Event Hub | Nom de l’instance Event Hub souhaitée. |
       | Valeur de stratégie Event Hub | Sélectionnez la stratégie d’accès partagé souhaitée. Vous pouvez créer la stratégie d’accès partagé dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**. |
       | Clé de stratégie Event Hub | Prérempli à partir de la valeur de stratégie de hub d’événements sélectionnée. |

    * Sélectionnez **Indiquez manuellement les paramètres Event Hub** si l’Event Hub est externe à vos abonnements ou si vous souhaitez choisir des options avancées.

       Le tableau suivant décrit les propriétés requises pour l’option **Indiquez manuellement les paramètres Event Hub** :
 
       | Propriété | Description |
       | --- | --- |
       | Identifiant d’abonnement | Abonnement auquel appartiennent l’espace de noms et l’instance Event Hub souhaités. |
       | Resource group | Groupe de ressources auquel appartiennent l’espace de noms et l’instance Event Hub souhaités. |
       | Espace de noms Event Hub | Espace de noms Event Hub auquel appartient l’instance Event Hub souhaitée. |
       | Nom de l’Event Hub | Nom de l’instance Event Hub souhaitée. |
       | Valeur de stratégie Event Hub | Sélectionnez la stratégie d’accès partagé souhaitée. Vous pouvez créer la stratégie d’accès partagé dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**. |
       | Clé de stratégie Event Hub | Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. Entrez la clé primaire ou secondaire ici. |

    * Les deux options partagent les options de configuration suivantes :

       | Propriété | Description |
       | --- | --- |
       | Groupe de consommateurs du hub d’événements | Groupe de consommateurs qui lit les événements de l’Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source de l’événement. |
       | Format de sérialisation de l’événement | Actuellement, JSON est le seul format de sérialisation disponible. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
       | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans l’Event Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, l’**heure de mise en file d’attente de l’événement** dans la source de l’événement est utilisée comme timestamp de l’événement. |

1. Ajoutez le nom du groupe de consommateurs Time Series Insights dédié que vous avez ajouté à votre Event Hub.

1. Sélectionnez **Create** (Créer).

   Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion en continu des données dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Définissez les stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser les données.

* [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.

* Accédez à votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
