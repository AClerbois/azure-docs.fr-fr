---
title: Structure des journaux Azure Monitor | Microsoft Docs
description: Pour récupérer des données de journal à partir d'Azure Monitor, vous devez exécuter une requête de journal.  Cet article explique comment les nouvelles requêtes de journal sont utilisées dans Azure Monitor et présente les concepts avec lesquels vous devez vous familiariser avant de créer une requête.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: 6f5ae426018c9e7fa2ac586a2886c8e5e609069b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813848"
---
# <a name="structure-of-azure-monitor-logs"></a>Structure des journaux Azure Monitor
La possibilité d’extraire rapidement des informations de vos données à l’aide d’une [requête de journal](log-query-overview.md) est une puissante fonctionnalité d’Azure Monitor. Pour créer des requêtes efficaces et utiles, vous devez comprendre certains concepts de base tels que l’emplacement où se trouvent les données que vous souhaitez et la manière dont elles sont structurées. Cet article présente les concepts de base que vous devez comprendre pour commencer.

## <a name="overview"></a>Vue d'ensemble
Les données contenues des journaux Azure Monitor sont stockées dans un espace de travail Log Analytics ou une application Application Insights. Les deux sont alimentés par [Azure Data Explorer](/azure/data-explorer/), ce qui signifie qu’ils tirent parti de ses puissants moteur de données et langage de requête.

Les données des espaces de travail et des applications sont organisées en tables dont chacune stocke des types de données spécifiques et a son propre ensemble de propriétés. Dans un espace de travail Log Analytics, la plupart des [sources de données](../platform/data-sources.md) écrivent dans leurs propres tables, tandis qu’Application Insights écrit dans un ensemble prédéfini de tables dans une application. Les requêtes de journal étant très flexibles, elle vous permettent de combiner facilement les données de plusieurs tables et même d’utiliser une requête inter-ressources pour combiner des données de tables figurant dans plusieurs espaces de travail ou pour écrire des requêtes combinant des données d’espace de travail et d’application.

L’illustration suivante présente des exemples de sources de données qui écrivent dans différentes tables utilisées dans les exemples de requêtes.

![Tables](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
Toutes les données collectées dans les journaux Azure Monitor à l’exception des données d’Application Insights sont stockées dans un [espace de travail Log Analytics](../platform/manage-access.md). Vous pouvez créer un ou plusieurs espaces de travail selon vos besoins spécifiques. Des [sources de données](../platform/data-sources.md) telles que des journaux d’activité et des journaux de diagnostic de ressources Azure, des agents sur des machines virtuelles et des solutions d’analyse et de surveillance écrivent les données dans un ou plusieurs espaces de travail que vous configurez dans le cadre de leur intégration. D’autres services tels qu’[Azure Security Center](/azure/security-center/) et [Azure Sentinel](/azure/sentinel/) utilisent également un espace de travail Log Analytics pour stocker leurs données afin que celles-ci puissent être analysées à l’aide de requêtes de journal en même temps que des données de surveillance provenant d’autres sources.

Les différents types de données sont stockés dans des tables distinctes au sein de l’espace de travail, et chaque table possède un ensemble unique de propriétés. Un ensemble standard de tables est ajouté à un espace de travail lors de sa création, et de nouvelles tables sont ajoutées pour différentes sources de données, solutions et services au fur et à mesure de leur intégration. Vous pouvez également créer des tables personnalisées à l’aide de l’[API Collecteur de données](../platform/data-collector-api.md).

Vous pouvez parcourir les tables d’un espace de travail et leur schéma sous l’onglet **Schéma** de Log Analytics pour cet espace de travail.

![Schéma d’espace de travail](media/scope/workspace-schema.png)

Utilisez la requête suivante pour répertorier les tables de l’espace de travail et le nombre d’enregistrements collectés au cours de la journée précédente. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consultez la documentation de chaque source de données pour plus de détails sur les tables créées. Par exemple, des articles sont disponibles sur les [sources de données d’agent](../platform/agent-data-sources.md), les [journaux de diagnostic](../platform/diagnostic-logs-schema.md) et les [solutions de surveillance](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Autorisations d’espace de travail
Pour plus d’informations sur la fourniture d’accès aux données au sein d’un espace de travail, voir [Étendue et autorisations de l’espace de travail](../platform/manage-access.md#manage-accounts-and-users). En plus de l’accès à l’espace de travail proprement dit, vous pouvez limiter l’accès à des certaines tables en appliquant un [contrôle d’accès en fonction du rôle (RBAC) au niveau des tables](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Application Insights
Lorsque vous créez une application dans Application Insights, une application correspondante est automatiquement créée dans les journaux Azure Monitor. Aucune configuration n’est requise pour collecter des données, et l’application écrit automatiquement des données de surveillance telles que les pages consultées, les demandes et les exceptions.

Contrairement à un espace de travail Log Analytics, une application Application Insights a un ensemble fixe de tables. Comme vous ne pouvez pas configurer d’autres sources de données à écrire dans l’application, aucune table supplémentaire ne peut être créée. 

| Table | Description | 
|:---|:---|
| availabilityResults | Données de synthèse de tests de disponibilité. |
| browserTimings      | Données sur les performances du client, comme le temps nécessaire pour traiter les données entrantes. |
| customEvents        | Événements personnalisés créés par votre application. |
| customMetrics       | Mesures personnalisées créées par votre application. |
| dependencies        | Appels de l’application à des composants externes. |
| exceptions          | Exceptions levées par l’exécution de l’application. |
| pageViews           | Données sur l’affichage de chaque site web avec des informations du navigateur. |
| performanceCounters | Mesures de performances des ressources de calcul sous-tendant l’application. |
| requêtes            | Détails de chaque demande de l’application.  |
| traces              | Résultats de suivi distribué. |

Vous pouvez afficher le schéma pour chaque table sous l’onglet **Schéma** dans Log Analytics pour l’application.

![Schéma de réplication](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriétés standard
Alors que chaque table dans les journaux Azure Monitor a son propre schéma, il existe des propriétés standards partagées par toutes les tables. Pour plus d’informations de chacun d’elles, voir [Propriétés standard dans les journaux Azure Monitor](../platform/log-standard-properties.md).

| Espace de travail Log Analytics | Application Application Insights | Description |
|:---|:---|:---|
| TimeGenerated | timestamp  | Date et heure de création de l’enregistrement. |
| Type          | itemType   | Nom de la table dont l’enregistrement a été extrait. |
| _ResourceId   |            | Identificateur unique de la ressource à laquelle l’enregistrement est associé. |
| _IsBillable   |            | Spécifie si les données ingérées sont facturables. |
| _BilledSize   |            | Spécifie la taille en octets des données qui seront facturées. |

## <a name="next-steps"></a>Étapes suivantes
- Apprenez à utiliser [l’analytique des journaux d’activité pour créer et modifier des recherches dans les journaux](../log-query/portals.md).
- Consultez un [didacticiel sur l’écriture de requêtes](../log-query/get-started-queries.md) à l’aide du nouveau langage de requête.
