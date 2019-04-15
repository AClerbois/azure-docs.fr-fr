---
title: Journalisation des diagnostics pour Azure Analysis Services | Microsoft Docs
description: En savoir plus sur la configuration de journalisation des diagnostics pour Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2303d385d3d688050a8d82c07e78a68588f41e88
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010920"
---
# <a name="setup-diagnostic-logging"></a>Configurer la journalisation des diagnostics

Une des fonctions importantes d’une solution Analysis Services est d’analyser les performances de vos serveurs. Avec les [journaux de diagnostic des ressources Azure](../azure-monitor/platform/diagnostic-logs-overview.md), vous pouvez analyser et envoyer des journaux au [Stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et les exporter vers les [journaux Azure Monitor](../azure-monitor/azure-monitor-log-hub.md).

![Journalisation des diagnostics dans le Stockage, Event Hubs ou les journaux Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Éléments journalisés :

Vous pouvez sélectionner les catégories **Moteur**, **Service** et **Métriques**.

### <a name="engine"></a>Engine (Moteur)

L’option **Moteur** enregistre toutes les événements [xEvent](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Vous ne pouvez pas sélectionner des événements individuels. 

|Catégories de XEvent |Nom de l'événement  |
|---------|---------|
|Audit de la sécurité    |   Audit Login      |
|Audit de la sécurité    |   Audit Logout      |
|Audit de la sécurité    |   Audit Server Starts And Stops      |
|Rapports d’avancement     |   Progress Report Begin      |
|Rapports d’avancement     |   Progress Report End      |
|Rapports d’avancement     |   Progress Report Current      |
|Requêtes     |  Query Begin       |
|Requêtes     |   Query End      |
|Commandes     |  Command Begin       |
|Commandes     |  Command End       |
|Erreurs et avertissement     |   Error      |
|Découvrez     |   Discover End      |
|Notification     |    Notification     |
|session     |  Session Initialize       |
|Verrous    |  Deadlock       |
|Traitement des requêtes     |   VertiPaq SE Query Begin      |
|Traitement des requêtes     |   VertiPaq SE Query End      |
|Traitement des requêtes     |   VertiPaq SE Query Cache Match      |
|Traitement des requêtes     |   Direct Query Begin      |
|Traitement des requêtes     |  Direct Query End       |

### <a name="service"></a>de diffusion en continu

|Nom d’opération  |Survient lorsque  |
|---------|---------|
|ResumeServer     |    Reprendre un serveur     |
|SuspendServer    |   Suspendre un serveur      |
|DeleteServer     |    Supprimer un serveur     |
|RestartServer    |     L’utilisateur redémarre un serveur via SSMS ou PowerShell    |
|GetServerLogFiles    |    L’utilisateur exporte le journal du serveur via PowerShell     |
|ExportModel     |   L’utilisateur exporte un modèle dans le portail à l’aide de la commande Ouvrir dans Visual Studio     |

### <a name="all-metrics"></a>Toutes les métriques

La catégorie Métriques journalise les mêmes [métriques serveur](analysis-services-monitor.md#server-metrics) que celles affichées dans les Métriques.

## <a name="setup-diagnostics-logging"></a>Configurer la journalisation des diagnostics

### <a name="azure-portal"></a>Portail Azure

1. Dans [Portail Azure](https://portal.azure.com) > Serveur, cliquez sur **Journaux de diagnostic** dans le volet de navigation de gauche, puis sur **Activer les diagnostics**.

    ![Activer la journalisation des diagnostics pour Azure Cosmos DB dans le portail Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Dans **Paramètres de diagnostic**, spécifiez les options suivantes : 

    * **Nom**. Entrez un nom pour les journaux à créer.

    * **Archive vers un compte de stockage**. Pour utiliser cette option, vous avez besoin d’un compte de stockage existant auquel vous connecter. Voir [Créer un compte de stockage](../storage/common/storage-create-storage-account.md). Suivez les instructions pour créer un compte Resource Manager à usage général, puis sélectionnez votre compte de stockage en retournant sur cette page du portail. L’affichage des comptes de stockage nouvellement créés dans le menu déroulant peut prendre quelques minutes.
    * **Transmettre à un Event Hub**. Pour utiliser cette option, vous avez besoin d’un espace de noms Event Hub existant et d’un Event Hub auquel vous connecter. Pour plus d’informations, consultez [Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure](../event-hubs/event-hubs-create.md). Puis revenez à cette page dans le portail pour sélectionner l’espace de noms Event Hub et le nom de la stratégie.
    * **Envoyer à Azure Monitor (espace de travail Log Analytics)**. Pour utiliser cette option, servez-vous d’un espace de travail existant ou [créez une ressource d’espace de travail](../azure-monitor/learn/quick-create-workspace.md) sur le portail. Pour savoir comment afficher vos journaux d’activité, voir [Afficher les journaux d’activité dans l’espace de travail Log Analytics](#view-logs-in-log-analytics-workspace) dans cet article.

    * **Moteur**. Sélectionnez cette option pour journaliser les événements XEvent. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Service**. Sélectionnez cette option pour journaliser les événements de niveau Service. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Métriques**. Sélectionnez cette option pour stocker des données détaillées dans [Métriques](analysis-services-monitor.md#server-metrics). Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.

3. Cliquez sur **Enregistrer**.

    Si vous recevez une erreur indiquant « Failed to update diagnostics for \<workspace name>. The subscription \<subscription id> is not registered to use microsoft.insights » (Échec de la mise à jour des diagnostics pour <nom de l’espace de travail>. L’abonnement <id d’abonnement> n’est pas inscrit pour utiliser microsoft.insights), suivez les instructions de la page [Résoudre les problèmes d’Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pour inscrire le compte, puis recommencez cette procédure.

    Si vous souhaitez modifier la façon dont vos journaux de diagnostic seront enregistrés à l’avenir, vous pouvez revenir à cette page à tout moment et modifier les paramètres.

### <a name="powershell"></a>PowerShell

Voici les commandes de base qui vous seront utiles. Si vous souhaitez obtenir une aide détaillée sur la configuration de la journalisation dans un compte de stockage à l’aide de PowerShell, consultez le didacticiel plus loin dans cet article.

Pour activer la journalisation des métriques et diagnostics à l’aide de PowerShell, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage où vous souhaitez envoyer les journaux.

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

### <a name="rest-api"></a>API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Gérer vos journaux

En règle générale, les journaux sont disponibles deux heures après la configuration de la journalisation. C’est à vous de gérer vos journaux dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux en limitant l’accès à ces derniers.
* Supprimez les journaux que vous ne souhaitez plus conserver dans votre compte de stockage.
* Veillez à définir une période de rétention qui supprime les très anciens journaux de votre compte de stockage.

## <a name="view-logs-in-log-analytics-workspace"></a>Afficher les journaux d’activité dans l’espace de travail Log Analytics

Les métriques et les événements de type serveur sont intégrés aux événements XEvent dans la ressource d’espace de travail Log Analytics, ce qui permet une analyse côte à côte. Il est également possible de configurer l’espace de travail Log Analytics pour qu’il reçoive des événements provenant d’autres services Azure, afin d’obtenir une vue globale des données de journalisation des diagnostics sur l’architecture.

Pour afficher vos données de diagnostic, ouvrez **Journaux d’activité** dans le menu de gauche de l’espace de travail Log Analytics.

![Options de recherche dans les journaux dans le portail Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Dans le Générateur de requêtes, développez **LogManagement** > **AzureDiagnostics**. AzureDiagnostics inclut les événements Moteur et Service. Notez qu’une requête est créée à la volée. Le champ EventClass\_s contient des noms xEvent, qui peuvent vous paraître familiers si vous avez utilisé des xEvent pour la journalisation locale. Cliquez sur **EventClass\_s** ou sur un des noms d’événement. L’espace de travail Log Analytics poursuit la création d’une requête. Veillez à enregistrer vos requêtes pour les réutiliser ultérieurement.

### <a name="example-query"></a>Exemple de requête
Cette requête calcule et retourne le processeur de chaque événement de fin de requête/d’actualisation pour un serveur et une base de données model :

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and ServerName_s =~"MyServerName" and DatabaseName_s == "Adventure Works Localhost" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| extend Engine_CPUTime=extract(@"([^,]*)", 1,CPUTime_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g ,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs,Engine_CPUTime
| join kind=leftouter (
window
    | where OperationName == "ProgressReportEnd" or (OperationName == "VertiPaqSEQueryEnd" and EventSubclass_s  != 10) or OperationName == "DiscoverEnd" or (OperationName has "CommandEnd" and EventSubclass_s != 38)
    | summarize sum_Engine_CPUTime = sum(extract(@"([^,]*)", 1,CPUTime_s, typeof(long))) by RootActivityId_g
    ) on RootActivityId_g
| extend totalCPU = sum_Engine_CPUTime + Engine_CPUTime

```


Il existe des centaines de requêtes que vous pouvez utiliser. Pour plus d’informations sur les requêtes, voir [Bien démarrer avec les requêtes de journal Azure Monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Activer la journalisation à l’aide de PowerShell

Dans ce bref didacticiel, vous créez un compte de stockage dans le même abonnement et le même groupe de ressources que votre serveur Analysis Services. Vous montre ensuite comment utiliser Set-AzDiagnosticSetting pour activer les diagnostics de journalisation, l’envoi de la sortie vers le nouveau compte de stockage.

### <a name="prerequisites"></a>Conditions préalables
Pour suivre ce didacticiel, vous avez besoin des ressources suivantes :

* Un serveur Azure Analysis Services. Pour plus d’informations sur la création d’une ressource du serveur, consultez [Création d’un serveur Azure Analysis Services dans le portail Azure](analysis-services-create-server.md) ou [Créer un serveur Azure Analysis Services à l’aide de PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Se connecter à vos abonnements

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

```powershell
Connect-AzAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre Azure Key Vault. Tapez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzSubscription
```

Ensuite, pour spécifier l’abonnement associé au compte Azure Analysis Services que vous journalisez, tapez :

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si plusieurs abonnements sont associés à votre compte, il est important d’en spécifier un.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Créer un nouveau compte de stockage pour vos journaux

Vous pouvez utiliser un compte de stockage pour vos journaux, à condition qu’il figure dans le même abonnement que votre serveur. Ce tutoriel vise à créer un compte de stockage dédié aux journaux Analysis Services. Pour plus de commodité, vous stockez les détails du compte de stockage dans une variable nommée **sa**.

Vous utilisez également le même groupe de ressources que celui qui contient votre serveur Analysis Services. Remplacez les valeurs de `awsales_resgroup`, `awsaleslogs` et `West Central US` par les vôtres :

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifier le compte de serveur pour les journaux

Définissez le nom du compte dans une variable nommée **account**, où ResourceName est le nom du compte.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Activation de la journalisation

Pour activer la journalisation, utilisez l’applet de commande Set-AzDiagnosticSetting ainsi que les variables pour le nouveau compte de stockage, compte de serveur et la catégorie. Exécutez la commande suivante, en définissant l’indicateur **-Enabled** sur **$true** :

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Le résultat suivant doit ressembler à ce qui suit :

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Cette sortie confirme que la journalisation est maintenant activée pour le serveur et enregistre les informations dans le compte de stockage.

Vous pouvez également définir une stratégie de rétention qui supprime automatiquement vos anciens journaux. Par exemple, définissez une stratégie de rétention en attribuant à l’indicateur **-RetentionEnabled** la valeur **$true** et en réglant le paramètre **-RetentionInDays** sur **90**. Ainsi, les journaux de plus de 90 jours sont automatiquement supprimés.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [journalisation des diagnostics de ressources Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

Consultez [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) dans l’aide de PowerShell.
