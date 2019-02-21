---
title: Passer de l'API Alerte héritée de Log Analytics à la nouvelle API Alertes Azure
description: Vue d'ensemble du retrait de l'API Alerte héritée et basée sur savedSearch de Log Analytics, et processus permettant de basculer des règles d'alerte vers la nouvelle API ScheduledQueryRules, avec des informations répondant aux préoccupations des clients.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6af1c5347a522f7e42feecb6722dfbb64439d086
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341009"
---
# <a name="switch-api-preference-for-log-alerts"></a>Changer de préférence d'API pour les alertes de journal

> [!NOTE]
> Ce contenu ne s'applique **pas** aux utilisateurs d'Azure GovCloud. Il concerne uniquement les utilisateurs du cloud Azure public.  

Récemment encore, vous gériez les règles d'alerte à partir du portail Microsoft Operations Management Suite. La nouvelle expérience d'alerte a été intégrée à différents services de Microsoft Azure, dont Log Analytics, et nous avons demandé une [extension de vos règles d'alerte du portail OMS vers Azure](alerts-extend.md). Cela dit, afin que les clients subissent le moins de perturbations possible, le processus n'a pas modifié l'interface de programmation - [API Alerte de Log Analytics](api-alerts.md) basée sur SavedSearch.

Mais aujourd'hui, vous proposez aux utilisateurs d'alertes de Log Analytics une véritable alternative de programmation Azure, l'[API ScheduledQueryRules - Azure Monitor](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), également reflétée sur votre [facturation Azure - pour les alertes de journal](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Pour en savoir plus sur la gestion des alertes de journal à l'aide de l'API, consultez [Gérer les alertes de journal à l'aide d'Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) et [Gérer les alertes de journal à l'aide de PowerShell, de l'interface CLI ou d'une API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Avantages du passage à la nouvelle API Azure

L'utilisation de l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) au détriment de l'[API Alerte héritée de Log Analytics](api-alerts.md) pour créer et gérer des alertes présente différents avantages. Ces principaux avantages sont les suivants :

- Possibilité de mener dans les règles d'alerte des [recherches croisées dans les journaux de l'espace de travail](../log-query/cross-workspace-query.md) et d'utiliser des ressources externes telles que des espaces de travail Log Analytics ou même des applications Application Insights.
- Lorsque plusieurs champs sont utilisés pour effectuer un regroupement dans une requête, l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) peut permettre à l'utilisateur de spécifier les champs à agréger sur le portail Azure.
- Les alertes de journal créées à l’aide de l’API [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) peuvent couvrir une période de 48 heures maximum et récupérer des données antérieures.
- Possibilité de créer simultanément plusieurs règles d'alerte en tant que ressource unique sans qu'il soit nécessaire de créer trois niveaux de ressources comme avec l'[API Alerte hébergée de Log Analytics](api-alerts.md).
- Interface de programmation unique pour toutes les variantes des alertes de journal basées sur des requêtes d'Azure - la nouvelle [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) peut être utilisée pour gérer les règles de Log Analytics et d'Application Insights.
- Les nouvelles fonctionnalités d'alerte de journal et les futurs développements seront uniquement disponibles via la nouvelle [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processus de basculement à partir de l'API Alertes de journal héritée

Le processus de déplacement des règles d'alerte depuis l'[API Alerte héritée de Log Analytics](api-alerts.md) n'implique aucune modification, de quelque nature que ce soit, de la définition, de la requête ou de la configuration de votre alerte. Les utilisateurs sont libres d'utiliser l'[API Alerte héritée de Log Analytics](api-alerts.md) ou la nouvelle [API scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Les règles d'alerte créées par l'une ou l'autre de ces API devront être *gérées par la même API*, ainsi que sur le portail Azure. Par défaut, Azure Monitor continuera à utiliser l'[API Alerte héritée de Log Analytics](api-alerts.md) pour créer de nouvelles règles d'alerte à partir du portail Azure.

Les conséquences du passage à l'API scheduleQueryRules sont compilées ci-dessous :

- Toutes les interactions liées à la gestion des alertes de journal via des interfaces de programmation doivent désormais s'effectuer à l'aide de l'API [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Pour plus d'informations, consultez [Exemple d'utilisation via Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) et [Exemple d'utilisation via Azure CLI et PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).
- Toute nouvelle règle d'alerte de journal créée sur le portail Azure devra utiliser l'API [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Les utilisateurs auront également la possibilité d'utiliser la [fonctionnalité supplémentaire de la nouvelle API](#Benefits-of-switching-to-new-Azure-API) via le portail Azure.

Tout client souhaitant passer volontairement à la nouvelle API [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) et bloquer l'utilisation de l'[API Alerte héritée de Log Analytics](api-alerts.md) peut le faire via un appel PUT sur l'API ci-dessous afin de modifier toutes les règles d'alerte associées à l'espace de travail Log Analytics spécifique.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Avec corps de la demande contenant le code JSON ci-dessous.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

L’API est également accessible à partir d’une ligne de commande PowerShell en utilisant [ARMClient](https://github.com/projectkudu/ARMClient), outil en ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager. Comme illustré ci-dessous, dans l’exemple d’appel PUT utilisant l’outil ARMclient pour basculer toutes les règles d’alerte associées à l’espace de travail spécifique Log Analytics.

```PowerShell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Si le passage de toutes les règles d'alerte de l'espace de travail Log Analytics à la nouvelle API [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) aboutit, la réponse suivante est fournie.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Les utilisateurs peuvent également vérifier l'état actuel de votre espace de travail Log Analytics et voir s'il a été basculé ou non vers [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Cette vérification peut être effectuée via un appel GET sur l'API ci-dessous.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pour exécuter ce qui précède en utilisant la ligne de commande PowerShell avec l’outil [ARMClient](https://github.com/projectkudu/ARMClient), consultez l’exemple ci-après.

```PowerShell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Si l'espace de travail Log Analytics spécifié a été basculé vers [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), le code JSON de la réponse se présentera comme suit.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Dans le cas contraire, si l'espace de travail Log Analytic spécifié n'a pas encore été basculé vers [scheduleQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), le code JSON de la réponse se présentera comme suit.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Monitor - Alertes de journal](alerts-unified-log.md).
- En savoir plus sur la création d’[alertes de journal dans Alertes Azure](alerts-log.md).
- En savoir plus sur l’[expérience Alertes Azure](../../azure-monitor/platform/alerts-overview.md).
