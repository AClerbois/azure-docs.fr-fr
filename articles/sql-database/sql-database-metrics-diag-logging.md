---
title: Journalisation des diagnostics et métriques Azure SQL Database | Microsoft Docs
description: Découvrez comment activer les diagnostics dans la base de données SQL Azure pour stocker des informations sur l’utilisation des ressources et les statistiques d’exécution de requête.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: fe53dd4419c06d376a1cc46db0d2621ccbc06f23
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548631"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Journalisation des métriques et diagnostics d’Azure SQL Database

Dans cette rubrique, vous allez apprendre à configurer la journalisation de télémétrie de diagnostic pour la base de données SQL Azure via le portail Azure, PowerShell, Azure CLI, API REST Azure Monitor et modèle Azure Resource Manager. Ces outils de diagnostic peuvent être utilisés pour évaluer l’utilisation des ressources et les statistiques d’exécution de requête. 

Les bases de données uniques, les bases de données mises en pool dans les pools élastiques et les bases de données d’instance dans une instance managée peuvent diffuser en continu des métriques et des journaux de diagnostic pour faciliter la supervision des performances. Vous pouvez configurer une base de données de sorte qu’elle transmette les informations relatives à l’utilisation des ressources, aux workers et sessions ainsi qu’à la connectivité à l’une de ces ressources Azure :

- **Azure SQL Analytics** : pour bénéficier d’une supervision intelligente de vos bases de données SQL Azure avec des rapports de performances, des alertes et des suggestions d’atténuation.
- **Azure Event Hubs** : pour intégrer des données de télémétrie SQL Database à votre solution de supervision personnalisée ou à vos pipelines chauds.
- **Stockage Azure** : pour archiver des quantités importantes de données de télémétrie pour une fraction du prix.

    ![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

Pour plus d’informations sur les métriques et les catégories de journal prises en charge par les divers services Azure, consultez :

- [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Vue d’ensemble des journaux de diagnostics Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Cet article fournit des conseils qui vous aideront à activer la télémétrie de diagnostic pour les bases de données Azure SQL, les pools élastiques et les instances managées. Il peut aussi vous aider à comprendre comment configurer Azure SQL Analytics comme outil de supervision pour consulter les données de télémétrie de diagnostic de base de données.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Activer la journalisation des données de télémétrie de diagnostic

Vous pouvez activer et gérer la journalisation des métriques et des données de télémétrie de diagnostic à l’aide de l’une des méthodes suivantes :

- Portail Azure
- PowerShell
- Azure CLI
- API REST Azure Monitor
- Modèle Azure Resource Manager

Lorsque vous activez les métriques et la journalisation des diagnostics, vous devez spécifier la destination de ressources Azure pour collecter les données de télémétrie de diagnostics. Les options disponibles sont les suivantes :

- Azure SQL Analytics
- Hubs d'événements Azure
- Stockage Azure

Vous pouvez approvisionner une nouvelle ressource Azure ou sélectionner une ressource existante. Après avoir choisi une ressource à l’aide de l’option **Paramètres de diagnostic**, spécifiez les données à collecter.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Prise en charge de la journalisation des diagnostics pour les bases de données SQL Azure et instance

Activez les métriques et la journalisation des diagnostics dans les bases de données SQL ; elles ne sont pas activées par défaut.

Vous pouvez configurer les bases de données SQL Azure et instance pour collecter les données de télémétrie de diagnostic suivantes :

| Analyse des données de télémétrie pour les bases de données | Prise en charge d’une base de données unique et d’une base de données mise en pool | Support de base de données d’instance |
| :------------------- | ----- | ----- |
| [Toutes les métriques](#all-metrics) : Pourcentage DTU/CPU, Limite DTU/CPU, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP. | Oui | Non  |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics) : contient des informations sur les statistiques d’exécution de requête comme les statistiques concernant l’utilisation du processeur et la durée des requêtes. | Oui | Oui |
| [QueryStoreWaitStatistics](#query-store-wait-statistics) : Contient des informations sur les statistiques d’attente de requête (ce que vos requêtes attendue) par exemple sont des UC, de journal et de verrouillage. | Oui | Oui |
| [Erreurs](#errors-dataset) : Contient des informations sur les erreurs SQL sur une base de données. | Oui | Oui |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset) : Contient des informations sur combien de temps une base de données a passé à attendre différents types d’attente. | Oui | Non  |
| [Timeouts](#time-outs-dataset) : Contient des informations sur les délais d’attente sur une base de données. | Oui | Non  |
| [Blocks](#blockings-dataset) : Contient des informations sur le blocage des événements sur une base de données. | Oui | Non  |
| [Blocages](#deadlocks-dataset): Contient des informations sur les événements de blocage sur une base de données. | Oui | Non  |
| [AutomaticTuning](#automatic-tuning-dataset): Contient des informations sur les recommandations de réglage automatique pour une base de données. | Oui | Non  |
| [SQLInsights](#intelligent-insights-dataset) : Contient Intelligent Insights des performances pour une base de données. Pour plus d’informations, consultez [Intelligent Insights](sql-database-intelligent-insights.md). | Oui | Oui |

> [!IMPORTANT]
> Les pools élastiques et des instances managées ont leurs propres données de télémétrie de diagnostic distincts à partir de bases de données qu’ils contiennent. Ceci est important de noter que les données de télémétrie de diagnostic sont configurée séparément pour chacune de ces ressources, comme indiqué ci-dessous.

> [!NOTE]
> Journaux d’Audit de sécurité et de SQLSecurityAuditEvents ne peut pas être activés à partir des paramètres de diagnostics de base de données (bien que l’affichage sur l’écran). Pour activer la diffusion de journaux d’audit, consultez [configurer l’audit pour votre base de données](sql-database-auditing.md#subheading-2), et [journaux dans les journaux Azure Monitor et les concentrateurs d’événements d’audit](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Portail Azure

Vous pouvez utiliser la **les paramètres de diagnostic** menu pour chaque unique, regroupée, ou l’instance de base de données dans le portail Azure pour configurer la diffusion en continu de données de télémétrie de diagnostics. En outre, télémétrie de diagnostic peut également être configurée séparément pour les conteneurs de base de données : les pools élastiques et des instances gérées. Vous pouvez définir les destinations suivantes pour diffuser les données de télémétrie de diagnostic : Journaux de stockage, Azure Event Hubs et Azure Monitor Azure.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour les pools élastiques

   ![Icône de pool élastique](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Vous pouvez configurer une ressource de pool élastique de sorte qu’elle collecte les données de télémétrie de diagnostic suivantes :

| Ressource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Pool élastique** | [Toutes les métriques](sql-database-metrics-diag-logging.md#all-metrics) : pourcentage eDTU/UC, limite eDTU/UC, pourcentage de lecture de données physiques, pourcentage d’écriture du journal, pourcentage de sessions, pourcentage de Workers, stockage, pourcentage de stockage, limite de stockage, pourcentage de stockage XTP. |

Pour configurer la diffusion en continu de données de télémétrie de diagnostics pour les pools et bases de données dans des pools élastiques, vous devrez configurer séparément **à la fois** des opérations suivantes :

- Activer la diffusion en continu de données de télémétrie de diagnostic pour un pool élastique, **et**
- Activer la diffusion en continu de données de télémétrie de diagnostic pour chaque base de données dans le pool élastique

Il s’agit, car le pool élastique est un conteneur de base de données avec ses propres données de télémétrie distincte à partir d’une télémétrie de base de données individuelle.

Pour activer le streaming des données de télémétrie de diagnostic pour une ressource de pool élastique, effectuez ces étapes :

1. Accédez à la **pool élastique** ressource dans le portail Azure.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.

   ![Activer les diagnostics pour les pools élastiques](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour l’analytique de journal, sélectionnez **configurer** et créer un espace de travail en sélectionnant **+ créer le nouvel espace de travail**, ou sélectionnez un espace de travail existant.
1. Cochez la case correspondant aux données de télémétrie de diagnostic de pool élastique **AllMetrics**.
   ![Configurer les diagnostics pour les pools élastiques](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Sélectionnez **Enregistrer**.
1. En outre, configurer la diffusion en continu de données de télémétrie de diagnostic pour chaque base de données au sein du pool élastique à analyser en suivant les étapes décrites dans la section suivante.

> [!IMPORTANT]
> Outre la configuration de télémétrie de diagnostic pour un pool élastique, vous devez également configurer la télémétrie de diagnostic pour chaque base de données dans un pool élastique, comme indiqué ci-dessous. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configurer la diffusion en continu de données de télémétrie de diagnostic pour la base de données unique, ou dans le pool élastique

   ![Icône de SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Pour activer la diffusion en continu de données de télémétrie de diagnostics pour les bases de données uniques ou regroupées, procédez comme suit :

1. Accédez à Azure **base de données SQL** ressource.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
   - Vous pouvez créer jusqu’à trois connexions parallèles pour le streaming des données de télémétrie de diagnostic.
   - Sélectionnez **+Ajouter un paramètre de diagnostic** pour configurer le streaming parallèle des données de diagnostic vers plusieurs ressources.

   ![Activer les diagnostics pour les bases de données uniques, mises en pool ou d’instances](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour une expérience de supervision standard basée sur les événements, cochez les cases suivantes pour les données de télémétrie de journal de diagnostic de base de données : **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** et **Deadlocks**.
1. Pour une expérience de supervision avancée à la minute, cochez la case **AllMetrics**.
   ![Configurer les diagnostics pour unique, regroupée ou l’instance des bases de données](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Sélectionnez **Enregistrer**.
1. Répétez ces étapes pour chaque base de données que vous souhaitez analyser.

> [!NOTE]
> Journaux d’Audit de sécurité et de SQLSecurityAuditEvents ne peut pas être activés à partir des paramètres de diagnostics de base de données (bien qu’il est indiqué dans l’écran). Pour activer la diffusion de journaux d’audit, consultez [configurer l’audit pour votre base de données](sql-database-auditing.md#subheading-2), et [journaux dans les journaux Azure Monitor et les concentrateurs d’événements d’audit](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Répétez ces étapes pour chaque base de données Azure SQL Database que vous voulez superviser.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour des instances gérées

   ![Icône d’instance gérée](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Vous pouvez configurer une ressource d’instance gérée de sorte qu’elle collecte les données de télémétrie de diagnostic suivantes :

| Ressource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Instance gérée** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) contient le nombre de vCores, le pourcentage de processeur moyen, le requêtes d’E/S, les octets lus/écrits, l’espace de stockage réservé et l’espace de stockage utilisé. |

Pour configurer la diffusion en continu de données de télémétrie de diagnostic pour l’instance gérée et les bases de données instance, vous devrez configurer séparément **à la fois** des opérations suivantes :

- Activer la diffusion en continu de données de télémétrie de diagnostic pour l’instance managée, **et**
- Activer la diffusion en continu de données de télémétrie de diagnostic pour chaque base de données d’instance

Il s’agit, car managed instance est un conteneur de base de données avec ses propres données de télémétrie, distinct à partir d’une télémétrie de base de données d’instance individuelle.

Pour activer la diffusion en continu des données de télémétrie de diagnostic pour une ressource d’instance gérée, procédez comme suit :

1. Accédez à la **instance managée** ressource dans le portail Azure.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.

   ![Activer les diagnostics pour une instance gérée](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour l’analytique de journal, sélectionnez **configurer** et créer un espace de travail en sélectionnant **+ créer le nouvel espace de travail**, ou utiliser un espace de travail existant.
1. Cochez la case correspondant aux données de télémétrie de diagnostic de pool élastique **ResourceUsageStats**.
   ![Configurer les diagnostics pour l’instance managée](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Sélectionnez **Enregistrer**.
1. En outre, configurer la diffusion en continu de données de télémétrie de diagnostic pour chaque base de données d’instance au sein de l’instance gérée que vous souhaitez analyser en suivant les étapes décrites dans la section suivante.

> [!IMPORTANT]
> Outre la configuration de télémétrie de diagnostic pour une instance gérée, vous devez également configurer la télémétrie de diagnostic pour chaque base de données de l’instance, comme indiqué ci-dessous. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configurer la diffusion en continu de données de télémétrie de diagnostic par exemple bases de données

   ![Icône de base de données d’instance dans l’instance managée](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Pour activer la diffusion en continu de données de télémétrie de diagnostic par exemple bases de données, procédez comme suit :

1. Accédez à **base de données de l’instance** ressource au sein de l’instance gérée.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
   - Vous pouvez créer jusqu’à trois (3) connexions parallèles pour le streaming des données de télémétrie de diagnostic.
   - Sélectionnez **+Ajouter un paramètre de diagnostic** pour configurer le streaming parallèle des données de diagnostic vers plusieurs ressources.

   ![Activer les diagnostics pour les bases de données d’instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Cochez les cases pour les données de télémétrie de diagnostic de base de données : **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** et **Errors**.
   ![Configurer les diagnostics par exemple les bases de données](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Sélectionnez **Enregistrer**.
1. Répétez ces étapes pour chaque instance base de données à surveiller.

> [!TIP]
> Répétez ces étapes pour chaque instance base de données à surveiller.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

Vous pouvez activer les métriques et la journalisation des diagnostics à l’aide de PowerShell.

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage de destination.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L’ID de règle Azure Service Bus est une chaîne au format suivant :

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="to-configure-multiple-azure-resources"></a>Pour configurer plusieurs ressources Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell).

Indiquez l’ID de ressource d’espace de travail \<$WSID\> comme paramètre au moment d’exécuter le script `Enable-AzureRMDiagnostics.ps1` pour envoyer les données de diagnostic de plusieurs ressources vers l’espace de travail.

- Pour obtenir l’ID d’espace de travail \<$WSID\> de la destination de vos données de diagnostic, utilisez le script suivant :

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Remplacez \<subID\> par l’ID d’abonnement, \<RG_NAME\> par le nom de groupe de ressources, puis \<WS_NAME\> par le nom d’espace de travail.

### <a name="azure-cli"></a>Azure CLI

Vous pouvez activer les métriques et la journalisation des diagnostics à l’aide d’Azure CLI.

> [!NOTE]
> Scripts pour activer la journalisation des diagnostics sont pris en charge pour Azure CLI v1.0. Veuillez noter que CLI v2.0 est non pris en charge pour l’instant.

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage de destination.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L’ID de règle Service Bus est une chaîne au format suivant :

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="rest-api"></a>API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API RESTS Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Envoi vers Azure SQL Analytics

Azure SQL Analytics est une solution cloud qui supervise à grande échelle les performances des bases de données Azure SQL, des pools élastiques et des instances managées et entre plusieurs abonnements. Elle peut vous aider à collecter et visualiser des métriques de performances Azure SQL Database et intègre des fonctions d’intelligence destinées à résoudre les problèmes.

![Vue d’ensemble d’Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Les journaux de diagnostic et les métriques SQL Database peuvent être diffusés en continu vers Azure SQL Analytics en activant l’option intégrée **Envoyer à Log Analytics** sous l’onglet Paramètres de diagnostic du portail. Vous pouvez également activer l’analytique de journal à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI ou l’API REST Azure Monitor.

### <a name="installation-overview"></a>Vue d’ensemble de l’installation

Vous pouvez superviser une flotte SQL Database avec Azure SQL Analytics. Effectuez les étapes suivantes :

1. Créez une solution Azure SQL Analytics à partir de la Place de marché Azure.
2. Créez un espace de travail de supervision dans la solution.
3. Configurez les bases de données pour qu’elles diffusent en continu les données de télémétrie de diagnostic dans l’espace de travail.

Si vous utilisez des pools élastiques ou des instances gérées, vous devez aussi configurer la diffusion en continu des données de télémétrie de diagnostic à partir de ces ressources.

### <a name="create-azure-sql-analytics-resource"></a>Créer une ressource Azure SQL Analytics

1. Recherchez et sélectionnez Azure SQL Analytics dans la Place de marché Azure.

   ![Rechercher Azure SQL Analytics dans le portail](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Sélectionnez **Créer** dans l’écran de présentation de la solution.

3. Complétez le formulaire Azure SQL Analytics avec les informations supplémentaires demandées : nom de l’espace de travail, abonnement, groupe de ressources, emplacement et niveau tarifaire.

   ![Configurer Azure SQL Analytics dans le portail](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Sélectionnez **OK** pour confirmer, puis **Créer**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurer des bases de données pour enregistrer des journaux d’activité de métriques et diagnostics

Pour configurer où les bases de données enregistrent les mesures, le plus simple consiste à l’aide du portail Azure. Comme indiqué précédemment, accédez à vos ressources SQL Database sur le portail Azure, puis sélectionnez **Paramètres de diagnostic**.

Si vous utilisez des pools élastiques ou des instances gérées, vous devez aussi configurer les paramètres de diagnostic dans ces ressources pour permettre la diffusion en continu des données de télémétrie de diagnostic vers l’espace de travail.

### <a name="use-the-sql-analytics-solution"></a>Utiliser la solution SQL Analytics

Vous pouvez utiliser SQL Analytics comme un tableau de bord hiérarchique pour afficher vos ressources SQL Database. Pour découvrir comment utiliser la solution SQL Analytics, consultez [Surveiller une base de données SQL à l’aide de la solution SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmission en continu vers Event Hubs

Vous pouvez diffuser en continu les journaux de diagnostic et les métriques SQL Database dans Event Hubs à l’aide de l’option intégrée **Diffuser vers Event Hub** sur le portail. Vous pouvez également activer l’ID de règle Service Bus à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI Azure ou l’API REST d’Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Que faire des journaux d’activité de métriques et diagnostics dans Event Hubs

Après avoir sélectionné les données envoyées à Event Hub, vous vous rapprochez de l’activation de scénarios d’analyse avancée. Event Hubs fait office de porte d’entrée pour un pipeline d’événements. Les données recueillies dans un hub d’événements peuvent ensuite être transformées et stockées à l’aide d’un fournisseur d’analytique en temps réel ou d’un adaptateur de stockage. Event Hubs dissocie la production d’un flux d’événements de la consommation de ces événements. De cette façon, les consommateurs d’événements peuvent accéder aux événements sur leur propre calendrier. Pour plus d’informations sur Event Hubs, consultez :

- [Qu’est-ce qu’Azure Event Hubs ?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Vous pouvez utiliser des métriques de streaming dans Event Hubs pour :

- **Afficher l’intégrité du service par diffusion en continu des données de chemin réactif vers Power BI**. En utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de métriques et de diagnostic en informations en temps réel sur vos services Azure. Pour une vue d’ensemble de la manière de configurer un concentrateur d’événements, de traiter les données avec Stream Analytics, et d’utiliser PowerBI comme sortie, voir [Stream Analytics et Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Diffuser en continu des journaux vers des flux de journalisation et de données de télémétrie tiers**. En utilisant le streaming Event Hubs, vous pouvez intégrer vos métriques et journaux de diagnostic à diverses solutions tierces de supervision et d’analytique des journaux d’activité.

- **Créer une plateforme de télémétrie et de journalisation personnalisée**. Vous disposez déjà d’une plateforme de télémétrie personnalisée ou envisagez d’en créer une ? La nature hautement scalable d’Event Hubs et de son modèle publication-abonnement vous permet d’ingérer les journaux de diagnostic avec souplesse. Lisez le [guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmission en continu dans le stockage

Vous pouvez stocker les métriques et les journaux de diagnostics SQL Database dans Stockage Azure en utilisant l’option intégrée **Archiver dans un compte de stockage** sur le portail. Vous pouvez aussi activer le stockage à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface de ligne de commande Azure (CLI) ou l’API REST Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma des journaux d’activité de métriques et diagnostics dans le compte de stockage

Après que vous avez configuré la collecte des journaux d’activité de métriques et diagnostics, lorsque les premières lignes de données sont disponibles, un conteneur de stockage est créé dans le compte de stockage que vous avez sélectionné. La structure des objets blob est la suivante :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, plus simplement :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Par exemple, un nom d’objet blob pour toutes les métriques pourrait être :

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Le nom d’un objet blob destiné à stocker les données d’un pool élastique se présente comme suit :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Télécharger les métriques et journaux d’activité du stockage

Découvrez comment [télécharger les journaux d’activité de métriques et de diagnostics à partir du stockage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Stratégie de rétention des données et tarification

Si vous sélectionnez Event Hubs ou un compte de stockage, vous pouvez spécifier une stratégie de rétention. Cette stratégie supprime les données antérieures à un intervalle de temps sélectionné. Si vous spécifiez Log Analytics, la stratégie de rétention dépend du niveau tarifaire sélectionné. Dans ce cas, les unités gratuites fournies pour l’ingestion de données permettent de superviser gratuitement plusieurs bases de données chaque mois. Toute utilisation de données de télémétrie de diagnostic au-delà des unités gratuites peut occasionner des frais. Sachez que les bases de données actives associées à de lourdes charges de travail ingèrent davantage de données que les bases de données inactives. Pour plus d’informations, consultez [journal analytique tarification](https://azure.microsoft.com/pricing/details/monitor/).

Si vous utilisez Azure SQL Analytics, vous pouvez superviser votre ingestion de données dans la solution en sélectionnant **Espace de travail OMS** dans le menu de navigation d’Azure SQL Analytics, puis **Utilisation** et **Coûts estimés**.

## <a name="metrics-and-logs-available"></a>Métriques et journaux d’activité disponibles

Surveillance des données de télémétrie disponibles pour la base de données SQL Azure, les pools élastiques et l’instance managée est documentée ci-dessous. Données de télémétrie de surveillance collectées à l’intérieur de SQL Analytique peuvent être utilisée pour votre propre analyse personnalisée et le développement de l’application à l’aide [des requêtes de journal Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) langage.

## <a name="all-metrics"></a>Toutes les métriques

Pour plus d’informations sur toutes les métriques par ressource, consultez les tableaux suivants.

### <a name="all-metrics-for-elastic-pools"></a>Toutes les métriques des pools élastiques

|**Ressource**|**Métriques**|
|---|---|
|Pool élastique|Pourcentage DTU, eDTU utilisé, Limite eDTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Limite de stockage, Pourcentage de stockage XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Toutes les métriques des bases de données Azure SQL

|**Ressource**|**Métriques**|
|---|---|
|Base de données Azure SQL|Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP et blocages |

## <a name="all-logs"></a>Tous les journaux

Détails de la télémétrie disponible pour tous les journaux sont documentés dans les tableaux ci-dessous. Consultez [pris en charge de la journalisation des diagnostics](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) pour comprendre les journaux dans lesquels sont pris en charge pour une version particulière de la base de données - SQL Azure unique, regroupée, ou l’instance de base de données.

### <a name="resource-usage-stats-for-managed-instance"></a>Statistiques de l’utilisation des ressources pour l’instance managée

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : ResourceUsageStats |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : MANAGEDINSTANCES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom de l’instance gérée |
|ResourceId|URI de ressource |
|SKU_s|Référence SKU du produit d’instance gérée |
|virtual_core_count_s|Nombre de vCores disponibles |
|avg_cpu_percent_s|Pourcentage d’UC moyenne |
|reserved_storage_mb_s|Capacité de stockage réservée sur l’instance gérée |
|storage_space_used_mb_s|Stockage utilisé sur l’instance gérée |
|io_requests_s|Nombre d’IOPS |
|io_bytes_read_s|Octets d’IOPS lus |
|io_bytes_written_s|Octets d’IOPS écrits |

### <a name="query-store-runtime-statistics"></a>Statistiques d’exécution du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics |
|OperationName|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|query_hash_s|Hachage de requête |
|query_plan_hash_s|Hachage de plan de requête |
|statement_sql_handle_s|Descripteur sql d’instruction |
|interval_start_time_d|Démarre le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|interval_end_time_d|Termine le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|logical_io_writes_d|Nombre total d’écritures E/S logiques |
|max_logical_io_writes_d|Nombre maximal d’écritures E/S logiques par exécution |
|physical_io_reads_d|Nombre total de lectures E/S physiques |
|max_physical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution |
|logical_io_reads_d|Nombre total de lectures E/S logiques |
|max_logical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution |
|execution_type_d|Type d’exécution |
|count_executions_d|Nombre d’exécutions de la requête |
|cpu_time_d|Temps processeur total consommé par la requête, en microsecondes |
|max_cpu_time_d|Consommateur de temps processeur maximal par une exécution unique, en microsecondes |
|dop_d|Somme des degrés de parallélisme |
|max_dop_d|Degré maximal de parallélisme utilisé pour une seule exécution |
|rowcount_d|Nombre total de lignes retournées |
|max_rowcount_d|Nombre maximal de lignes retournées dans une seule exécution |
|query_max_used_memory_d|Quantité totale de mémoire utilisée en Ko |
|max_query_max_used_memory_d|Quantité maximale de mémoire utilisée par une seule exécution, en Ko |
|duration_d|Durée d’exécution totale en microsecondes |
|max_duration_d|Durée d’exécution maximale d’une seule exécution |
|num_physical_io_reads_d|Nombre total de lectures physiques |
|max_num_physical_io_reads_d|Nombre maximal de lectures physiques par exécution |
|log_bytes_used_d|Quantité totale d’octets de journal utilisés |
|max_log_bytes_used_d|Quantité maximale d’octets de journal utilisés par exécution |
|query_id_d|ID de la requête dans le magasin des requêtes |
|plan_id_d|ID du plan dans le magasin des requêtes |

En savoir plus sur les [données de statistiques d’exécution du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiques d’attente du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : QueryStoreWaitStatistics |
|OperationName|Nom de l’opération. Toujours : QueryStoreWaitStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|wait_category_s|Catégorie de l’attente |
|is_parameterizable_s|Indique si la requête est paramétrable |
|statement_type_s|Type de l’instruction |
|statement_key_hash_s|Hachage de clé d’instruction |
|exec_type_d|Type d’exécution |
|total_query_wait_time_ms_d|Temps d’attente total de la requête dans la catégorie d’attente spécifique |
|max_query_wait_time_ms_d|Temps d’attente maximal de la requête dans une exécution individuelle sur la catégorie d’attente spécifique |
|query_param_type_d|0 |
|query_hash_s|Hachage de requête dans le magasin des requêtes |
|query_plan_hash_s|Hachage de plan de requêtes dans le magasin des requêtes |
|statement_sql_handle_s|Descripteur d’instruction dans le magasin des requêtes |
|interval_start_time_d|Démarre le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|interval_end_time_d|Termine le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|count_executions_d|Comptabilisation des exécutions de la requête |
|query_id_d|ID de la requête dans le magasin des requêtes |
|plan_id_d|ID du plan dans le magasin des requêtes |

Découvrez-en davantage sur les [données des statistiques d’attente du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Jeu de données d’erreurs

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQ |
|Catégorie|Nom de la catégorie. Toujours : Errors |
|OperationName|Nom de l’opération. Toujours : ErrorEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|Message|Message d’erreur en texte brut |
|user_defined_b|Indique si l’erreur est un bit défini par l’utilisateur |
|error_number_d|Code d'erreur |
|Severity|Gravité de l’erreur |
|state_d|État de l’erreur |
|query_hash_s|Hachage de requête de la requête ayant échoué, si disponible |
|query_plan_hash_s|Hachage du plan de requête de la requête ayant échoué, si disponible |

En savoir plus sur les [messages d’erreur SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Jeu de données de statistiques d’attente de base de données

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : DatabaseWaitStatistics |
|OperationName|Nom de l’opération. Toujours : DatabaseWaitStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|wait_type_s|Nom du type d’attente |
|start_utc_date_t [UTC]|Heure de début de la période mesurée |
|end_utc_date_t [UTC]|Heure de fin de la période mesurée |
|delta_max_wait_time_ms_d|Temps d’attente maximal par exécution |
|delta_signal_wait_time_ms_d|Durée d’attente totale des signaux |
|delta_wait_time_ms_d|Durée d’attente totale dans la période |
|delta_waiting_tasks_count_d|Nombre de tâches en attente |

Apprenez-en davantage sur les [statistiques d’attente de base de données](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Jeu de données d’expirations

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Délais d’expiration |
|OperationName|Nom de l’opération. Toujours : TimeoutEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|error_state_d|Code d’état d’erreur |
|query_hash_s|Hachage de requête, si disponible |
|query_plan_hash_s|Hachage du plan de requête, si disponible |

### <a name="blockings-dataset"></a>Jeu de données de blocages

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Blocs |
|OperationName|Nom de l’opération. Toujours : BlockEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|lock_mode_s|Mode de verrouillage utilisé par la requête |
|resource_owner_type_s|Propriétaire du verrou |
|blocked_process_filtered_s|XML de rapport de processus bloqué |
|duration_d|Durée du verrou en microsecondes |

### <a name="deadlocks-dataset"></a>Jeu de données Deadlocks

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC] |Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Blocages |
|OperationName|Nom de l’opération. Toujours : DeadlockEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|deadlock_xml_s|XML de rapport de blocage |

### <a name="automatic-tuning-dataset"></a>Jeu de données AutomaticTuning

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : AutomaticTuning |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|LogicalDatabaseName_s|Nom de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|RecommendationHash_s|Hachage unique de la recommandation de réglage automatique |
|OptionName_s|Opération de paramétrage automatique |
|Schema_s|Schéma de base de données |
|Table_s|Table concernée |
|IndexName_s|Nom d’index |
|IndexColumns_s|Nom de la colonne |
|IncludedColumns_s|Colonnes incluses |
|EstimatedImpact_s|Impact estimé du JSON de recommandation de paramétrage automatique |
|Event_s|Type d’événement de paramétrage automatique |
|Timestamp_t|Horodatage de la dernière mise à jour |

### <a name="intelligent-insights-dataset"></a>Jeu de données Intelligent Insights

Apprenez-en davantage sur le [format de journal Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer la journalisation et comprendre les catégories de journaux et de métriques prises en charge par les différents services Azure, consultez :

- [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Vue d’ensemble des journaux de diagnostics Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Pour plus d’informations sur les concentrateurs d’événements, lisez :

- [Nouveautés des concentrateurs d’événements Azure ?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Pour plus d’informations sur Stockage Azure, consultez [Télécharger des métriques et des journaux de diagnostic à partir de Stockage Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
