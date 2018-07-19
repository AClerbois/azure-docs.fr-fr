---
title: Solution Azure SQL Analytics dans Log Analytics | Microsoft Docs
description: La solution Azure SQL Analytics vous permet de gérer vos instances Azure SQL Database.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: f57a47677f752a644975a25fa746d78bced5d766
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133351"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Surveiller les instances Azure SQL Database avec Azure SQL Analytics (préversion)

![Symbole Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics est une solution de surveillance cloud pour les performances des instances Azure SQL Database à grande échelle sur plusieurs pools élastiques et abonnements. Cette solution collecte et affiche d’importantes mesures de performances pour Azure SQL Database avec des informations intégrées pour la résolution des problèmes également. 

En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes de surveillance personnalisées. La solution vous aide à identifier les problèmes sur chacune des couches de votre pile applicative. Elle utilise les données de diagnostic Azure avec les vues Log Analytics pour présenter les données relatives à l’ensemble de vos instances Azure SQL Database et de vos pools élastiques dans un espace de travail Log Analytics unique. Log Analytics prend en charge la collecte, la mise en correspondance et l’affichage des données structurées et non structurées.

Actuellement, cette solution en version préliminaire prend en charge jusqu’à 150 000 instances Azure SQL Database et 5 000 pools élastiques SQL par espace de travail.

Pour une présentation pratique sur l'utilisation de la solution Azure SQL Analytics et des scénarios d'utilisation standard, regardez la vidéo incorporée :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Sources connectées

Azure SQL Analytics est une solution de surveillance cloud prenant en charge la diffusion en continu de données de télémétrie de diagnostic pour les instances Azure SQL Database et les pools élastiques. Comme elle n’utilise pas les agents pour se connecter au service Log Analytics, la solution ne prend pas en charge la connectivité avec Windows, Linux ou les ressources SCOM. Consultez le tableau de compatibilité ci-dessous.

| Source connectée | Support | Description |
| --- | --- | --- |
| **[Azure Diagnostics](log-analytics-azure-storage.md)** | **Oui** | Les métriques Azure et les données des journaux sont envoyées à Log Analytics directement par Azure. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | Log Analytics ne lit pas les données du compte de stockage. |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | Les agents directs Windows ne sont pas utilisés par la solution. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | Les agents directs Linux ne sont pas utilisés par la solution. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne valorise aucune connexion directe entre l’agent SCOM et Log Analytics. |

## <a name="configuration"></a>Configuration

Exécutez la procédure suivante afin d’ajouter la solution Azure SQL Analytics dans votre espace de travail.

1. Ajoutez la solution Azure SQL Analytics à votre espace de travail à partir de la [marketplace Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Dans le portail Azure, cliquez sur **+ Créer une ressource**, puis recherchez **Azure SQL Analytics**.  
    ![Surveillance et gestion](./media/log-analytics-azure-sql/monitoring-management.png)
3. Sélectionnez **Azure SQL Analytics (préversion)** dans la liste.
4. Dans la zone **Azure SQL Analytics (version préliminaire)**, cliquez sur **Créer**.  
    ![Créer](./media/log-analytics-azure-sql/portal-create.png)
5. Dans la zone **Créer une nouvelle solution**, créez une solution ou sélectionnez un espace de travail existant auquel vous souhaitez ajouter la solution, puis cliquez sur **Créer**.  
    ![ajouter à l’espace de travail](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Configurer des instances Azure SQL Database et des pools élastiques pour diffuser en continu des données de télémétrie de diagnostic

Une fois que vous avez créé la solution Azure SQL Analytics dans votre espace de travail, afin de surveiller les performances des instances Azure SQL Database et/ou des pools élastiques, vous devez **configurer chaque** ressource Azure SQL Database et de pool élastique à surveiller pour diffuser ses données de télémétrie de diagnostic vers la solution.

- Activez les diagnostics Azure pour vos instances Azure SQL Database et vos pools élastiques et [configurez-les pour l’envoi de leurs données vers Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Pour configurer plusieurs abonnements Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell). Fournissez l’ID de ressource d’espace de travail en tant que paramètre lors de l’exécution du script pour transmettre les données des ressources d’un abonnement Azure vers un espace de travail d’un autre abonnement Azure.

**Exemple**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez la solution à votre espace de travail, la vignette Azure SQL Analytics est également ajoutée ; elle apparaît dans la Vue d’ensemble. La vignette indique le nombre d’instances Azure SQL Database et de pools élastiques Azure SQL auxquels la solution est associée.

![Vignette Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Affichage des données Azure SQL Analytics

Cliquez sur la vignette **Azure SQL Analytics** afin d’ouvrir le tableau de bord Azure SQL Analytics. Ce tableau de bord propose un aperçu de toutes les bases de données surveillées selon différentes perspectives. Pour que ces différentes perspectives fonctionnent, vous devez activer les métriques ou les journaux appropriés sur les ressources SQL, celles dont la diffusion doit s’effectuer en continu sur l’espace de travail Azure Log Analytics.

![Vue d’ensemble d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

En sélectionnant l’une des vignettes, vous ouvrez le rapport détaillé d’une perspective en particulier. Une fois la perspective sélectionnée, le rapport détaillé est ouvert.

![Délais d’expiration d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Chaque perspective fournit des récapitulatifs sur l’abonnement, le serveur, le pool de bases de données élastique et la base de données. En outre, sur la droite, chaque perspective présente une vision spécifique au rapport. Pour approfondir davantage, sélectionnez l’abonnement, le serveur, le pool ou la base de données dans la liste.

| Perspective | Description |
| --- | --- |
| Ressource par type | Perspective comptabilisant toutes les ressources surveillées. Ce zoom fournit un récapitulatif des métriques de DTU et Go. |
| Insights | Permet une exploration hiérarchique dans Intelligent Insights. Apprenez-en davantage sur l’intelligence artificielle intégrée. |
| Errors | Permet une exploration hiérarchique des erreurs SQL qui se sont produites dans les bases de données. |
| Délais d’expiration | Permet une exploration hiérarchique des délais d’expiration SQL qui se sont produits dans les bases de données. |
| Blocages | Permet une exploration hiérarchique des blocages SQL qui se sont produits dans les bases de données. |
| Attentes de la base de données | Permet une exploration hiérarchique des statistiques d’attente SQL au niveau des bases de données. Inclut des résumés du temps d’attente total et de la durée d’attente par type d’attente. |
| Durée de la requête | Permet une exploration hiérarchique des statistiques d’exécution de la requête, par exemple la durée de la requête, l’utilisation du processeur, l’utilisation des E/S de données, l’utilisation des E/S du journal. |
| Attentes de requête | Permet une exploration hiérarchique des statistiques d’attente de requête par catégorie d’attente. |

### <a name="intelligent-insights-report"></a>Rapport Intelligent Insights

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vous permet de suivre les performances de votre base de données. Toutes les analyses Intelligent Insights collectées peut être visualisées et sont accessibles par le biais de la perspective Insights.

![Insights Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rapports de base de données et de pool de bases de données élastique

Les pools de bases de données élastique et les bases de données ont leurs propres rapports qui affichent toutes les données collectées pour la ressource, dans le temps imparti.

![Base de données Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool de base de données élastique Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Rapports de requête

En vous aidant de la durée de la requête et des attentes de requête, vous pouvez mettre en corrélation les performances des requêtes dans le rapport de requête. Ce rapport compare les performances des requêtes entre les différentes bases de données. Il identifie également les bases de données qui effectuent la requête sélectionnée correctement et celles qui le font lentement.

![Requêtes Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analyser les données et créer des alertes

Vous pouvez facilement [créer des alertes](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) avec les données provenant de ressources Azure SQL Database. Voici quelques requêtes utiles de [recherche dans les journaux](log-analytics-log-searches.md) que vous pouvez utiliser pour créer des alertes :



*DTU élevé sur Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*DTU élevé sur un pool élastique Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Étapes suivantes

- Utilisez les [Recherches de journal](log-analytics-log-searches.md) dans Log Analytics pour afficher les données détaillées sur Azure SQL.
- [Créer un tableau de bord personnalisé](log-analytics-dashboards.md) comportant les données Azure SQL.
- [Create and manage alert rules in Log Analytics with the OMS portal](log-analytics-alerts.md) (Créer et gérer des règles d’alerte dans Log Analytics dans Log Analytics à l’aide du portail OMS).
