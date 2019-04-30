---
title: Expression app() dans les requêtes de journal Azure Monitor | Microsoft Docs
description: L’expression de l’application est utilisée dans une requête de journal Azure Monitor pour récupérer des données à partir d’une application Application Insights spécifique dans le même groupe de ressources, un autre groupe de ressources ou un autre abonnement.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: a1a605bc733597430f64dceeb6c485db0abf657b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589248"
---
# <a name="app-expression-in-azure-monitor-query"></a>Expression app() dans une requête Azure Monitor

L'expression `app` est utilisée dans une requête Azure Monitor afin de récupérer des données à partir d'une application Application Insights spécifique du même groupe de ressources, d'un autre groupe de ressources ou d'un autre abonnement. Elle est particulièrement utile pour inclure des données d'application dans une requête de journal Azure Monitor et pour interroger des données de plusieurs applications dans une requête Application Insights.



## <a name="syntax"></a>Syntaxe

`app(`*Identificateur*`)`


## <a name="arguments"></a>Arguments

- *Identificateur* : Permet d’identifier l’application à l’aide de l’un des formats du tableau ci-dessous.

| Identificateur | Description | Exemples
|:---|:---|:---|
| Nom de la ressource | Nom lisible de l’application (également appelé « nom du composant ») | app("fabrikamapp") |
| Nom qualifié | Nom complet de l’application au format : « nom_abonnement/groupe_ressources/nom_composant » | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | GUID de l’application | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de la ressource Azure | Identificateur de la ressource Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notes

* Vous devez disposer d’un accès en lecture à l’application.
* L’identification d’une application par son nom suppose que celui-ci soit unique parmi tous les abonnements accessibles. Si plusieurs applications portent le nom spécifié, la requête échoue en raison de l’ambiguïté. Dans ce cas, vous devez utiliser l’un des autres identificateurs.
* Utilisez l’expression associée [workspace](workspace-expression.md) pour interroger plusieurs espaces de travail Log Analytics.
* L’expression app() n’est pas prise en charge actuellement dans la requête de recherche quand vous utilisez le portail Azure pour créer une [règle d’alerte de recherche de journal personnalisée](../platform/alerts-log.md), sauf si une application Application Insights est utilisée en tant que ressource pour la règle d’alerte.

## <a name="examples"></a>Exemples

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’[expression workspace](workspace-expression.md) pour référence à un espace de travail Log Analytics.
- Découvrez-en plus sur le stockage des [données Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).
- Accédez à la documentation complète sur le [langage de requête Kusto](/azure/kusto/query/).
