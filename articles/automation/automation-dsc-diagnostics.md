---
title: Transférer des données de rapport dans les journaux d’Azure Monitor la Configuration d’état Azure Automation
description: Cet article montre comment envoyer Desired State Configuration (DSC) données de rapports de Configuration d’état Azure Automation dans les journaux d’Azure Monitor pour fournir une gestion et obtenir des informations supplémentaires.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dad74f75fd7b73e7dab0b2dddbdfda193d5b2ec
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445796"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Transférer des données de rapport dans les journaux d’Azure Monitor la Configuration d’état Azure Automation

Configuration de l’état d’Automation Azure conserve les données d’état de nœud pendant 30 jours.
Vous pouvez envoyer des données d’état de nœud à votre espace de travail Analytique de journal si vous préférez conserver ces données pendant une période plus longue.
L’état de conformité est visible dans le portail Azure, ou avec PowerShell, pour les nœuds et les ressources DSC individuelles dans les configurations de nœud.
Avec les journaux d’Azure Monitor, vous pouvez :

- Obtenir des informations de conformité pour les nœuds gérés et les ressources individuelles
- Déclencher un e-mail ou une alerte en fonction de l’état de conformité
- Écrire des requêtes avancées dans vos nœuds gérés
- Mettre en corrélation l’état de conformité dans les comptes Automation
- Visualiser votre historique de conformité associé aux nœuds au fil du temps

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Conditions préalables

Pour commencer à envoyer vos rapports de Configuration d’état Automation dans les journaux d’Azure Monitor, vous devez :

- Version de novembre 2016 ou une version ultérieure d’[Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Un compte Azure Automation. Pour plus d’informations, consultez la page [Prise en main d’Azure Automation](automation-offering-get-started.md).
- Un espace de travail Log Analytics avec une offre de service **Automation & Control**. Pour plus d’informations, consultez [prise en main Azure Monitor journaux](../log-analytics/log-analytics-get-started.md).
- Au moins un nœud Azure Automation State Configuration. Pour plus d’informations, consultez [Intégration des machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurer l’intégration avec les journaux d’Azure Monitor

Pour commencer l’importation de données à partir d’Azure Automation DSC dans les journaux Azure Monitor, procédez comme suit :

1. Dans PowerShell, connectez-vous à votre compte Azure. Consultez [Log in with Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) (Se connecter avec Azure PowerShell).
1. Obtenez le _ResourceId_ de votre compte Automation en exécutant la commande PowerShell suivante : (si vous avez plusieurs comptes Automation, choisissez le _ResourceID_ du compte que vous souhaitez configurer).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenez le _ResourceId_ de votre espace de travail Log Analytics en exécutant la commande PowerShell suivante : (si vous avez plusieurs espaces de travail, choisissez le _ResourceID_ de l’espace de travail que vous souhaitez configurer).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Exécutez la commande PowerShell suivante, en remplaçant `<AutomationResourceId>` et `<WorkspaceResourceId>` par les valeurs _ResourceId_ de chacune des étapes précédentes :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Si vous souhaitez arrêter l’importation de données à partir de la Configuration d’état Azure Automation dans les journaux Azure Monitor, exécutez la commande PowerShell suivante :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Afficher les journaux d’activité State Configuration

Après avoir configuré l’intégration avec les journaux d’Azure Monitor pour vos données de Configuration d’état Automation, un **recherche dans les journaux** bouton s’affiche sur le **les nœuds DSC** Panneau de votre compte automation. Cliquez sur le bouton **Recherche dans les journaux d’activité** pour afficher les journaux d’activité des données de nœud DSC.

![Bouton Recherche dans les journaux](media/automation-dsc-diagnostics/log-search-button.png)

Le panneau **Recherche dans les journaux** s’ouvre et vous voyez une opération **DscNodeStatusData** pour chaque nœud State Configuration et une opération **DscResourceStatusData** pour chaque [ressource DSC](/powershell/dsc/resources) appelée dans la configuration Nœud appliquée à ce nœud.

L’opération **DscResourceStatusData** contient des informations d’erreur pour les ressources DSC ayant échoué.

Cliquez sur chaque opération dans la liste pour afficher les données correspondant à cette opération.

Vous pouvez également afficher les journaux en effectuant une recherche dans les journaux Azure Monitor.
Consultez [Recherche de données à l’aide de recherches de journal](../log-analytics/log-analytics-log-searches.md).
Tapez la requête suivante pour rechercher vos journaux d’activité State Configuration : `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Vous pouvez également affiner la requête par nom d’opération. Par exemple : `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Envoyer un e-mail si une vérification de la conformité State Configuration échoue

L’une des demandes majeures de nos clients est de pouvoir envoyer un e-mail ou un message texte lorsqu’une erreur survient avec une configuration DSC.

Pour créer une règle d’alerte, vous commencez par créer une recherche dans les journaux pour les enregistrements de rapport State Configuration qui doivent appeler l’alerte. Cliquez sur le bouton **+ Nouvelle règle d’alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de vue d’ensemble d’espace de travail Analytique de journal, cliquez sur **journaux**.
1. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`.

   Si vous avez configuré des journaux d’activité dans votre espace de travail à partir de plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation.  
   Le nom du compte Automation peut être dérivé du champ Ressource dans la recherche de DscNodeStatusData.  
1. Pour ouvrir l’écran **Créer une règle**, cliquez sur **+ Nouvelle règle d’alerte** en haut de la page. Pour plus d’informations sur les options de configuration de l’alerte, consultez [créer une règle d’alerte](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Rechercher les ressources DSC ayant échoué dans tous les nœuds

L’un des avantages de l’utilisation des journaux Azure Monitor sont que vous pouvez rechercher les vérifications ayant échoué sur les nœuds.
Pour rechercher toutes les instances de ressources DSC ayant échoué.

1. Dans la page de vue d’ensemble d’espace de travail Analytique de journal, cliquez sur **journaux**.
1. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`.

### <a name="view-historical-dsc-node-status"></a>Afficher l’état de nœud DSC historique

Enfin, vous pouvez souhaiter visualiser l’historique de l’état de votre nœud DSC au fil du temps.  
Vous pouvez utiliser cette requête pour rechercher l’état de votre nœud DSC au fil du temps.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Cela affichera un graphique de l’état du nœud au fil du temps.

## <a name="azure-monitor-logs-records"></a>Azure Monitor enregistre des enregistrements

Diagnostic d’Azure Automation crée deux catégories d’enregistrements dans les journaux Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution de la vérification de conformité. |
| OperationName |DscNodeStatusData |
| ResultType |Indique si le nœud est conforme. |
| NodeName_s |Le nom du nœud géré. |
| NodeComplianceStatus_s |Indique si le nœud est conforme. |
| DscReportStatus |Indique si la vérification de conformité a été correctement exécutée. |
| ConfigurationMode | La façon dont la configuration est appliquée au nœud. Les valeurs possibles sont __« ApplyOnly »__,__« ApplyandMonitor »__ et __« ApplyandAutoCorrect »__. <ul><li>__ApplyOnly__ : DSC applique la configuration et ne fait rien de plus, sauf si une nouvelle configuration est envoyée au nœud cible ou quand une nouvelle configuration est extraite d’un serveur. Après l’application initiale d’une nouvelle configuration, DSC ne vérifie pas l’écart par rapport à un état configuré précédemment. DSC tente d’appliquer la configuration jusqu’à ce que l’opération réussisse avant que la valeur __ApplyOnly__ prenne effet. </li><li> __ApplyAndMonitor__ : Il s’agit de la valeur par défaut. Le gestionnaire de configuration locale applique toutes les nouvelles configurations. Après l’application initiale d’une nouvelle configuration, si le nœud cible s’écarte de l’état désiré, DSC signale l’écart dans les journaux d’activité. DSC tente d’appliquer la configuration jusqu’à ce que l’opération réussisse avant que la valeur __ApplyAndMonitor__ prenne effet.</li><li>__ApplyAndAutoCorrect__ : DSC applique toutes les nouvelles configurations. Après l’application initiale d’une nouvelle configuration, si le nœud cible s’écarte de l’état désiré, DSC signale l’écart dans les journaux d’activité puis applique à nouveau la configuration actuelle.</li></ul> |
| HostName_s | Le nom du nœud géré. |
| IPAddress | L’adresse IPv4 du nœud géré. |
| Category | DscNodeStatus |
| Resource | Le nom du compte Azure Automation. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| NodeId_g |GUID qui identifie le nœud géré. |
| DscReportId_g |GUID qui identifie le rapport. |
| LastSeenTime_t |Date et heure auxquelles le rapport a été consulté pour la dernière fois. |
| ReportStartTime_t |Date et heure auxquelles le rapport a été commencé. |
| ReportEndTime_t |Date et heure auxquelles le rapport a été terminé. |
| NumberOfResources_d |Le nombre de ressources DSC appelées dans la configuration appliquée au nœud. |
| SourceSystem | Fonctionnement des journaux Azure Monitor collecte des données. Toujours *Azure* pour les diagnostics Azure. |
| ResourceId |Spécifie le compte Azure Automation. |
| resultDescription | La description de cette opération. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID représentant l’ID de corrélation du rapport de conformité. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution de la vérification de conformité. |
| OperationName |DscResourceStatusData|
| ResultType |Indique si la ressource est conforme. |
| NodeName_s |Le nom du nœud géré. |
| Category | DscNodeStatus |
| Resource | Le nom du compte Azure Automation. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| NodeId_g |GUID qui identifie le nœud géré. |
| DscReportId_g |GUID qui identifie le rapport. |
| DscResourceId_s |Le nom de l’instance de ressource DSC. |
| DscResourceName_s |Le nom de la ressource DSC. |
| DscResourceStatus_s |Indique si la ressource DSC est conforme. |
| DscModuleName_s |Le nom du module PowerShell qui contient la ressource DSC. |
| DscModuleVersion_s |La version du module PowerShell qui contient la ressource DSC. |
| DscConfigurationName_s |Le nom de la configuration appliquée au nœud. |
| ErrorCode_s | Le code d’erreur en cas d’échec de la ressource. |
| ErrorMessage_s |Le message d’erreur en cas d’échec de la ressource. |
| DscResourceDuration_d |La durée, en secondes, d’exécution de la ressource DSC. |
| SourceSystem | Fonctionnement des journaux Azure Monitor collecte des données. Toujours *Azure* pour les diagnostics Azure. |
| ResourceId |Spécifie le compte Azure Automation. |
| resultDescription | La description de cette opération. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID représentant l’ID de corrélation du rapport de conformité. |

## <a name="summary"></a>Résumé

En envoyant vos données de Configuration d’état Automation dans les journaux d’Azure Monitor, vous pouvez obtenir plus de détails sur l’état de vos nœuds de la Configuration d’état Automation par :

- Configurant des alertes pour vous avertir lorsqu’il y a un problème
- Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.  

Journaux d’Azure Monitor offre une plus grande visibilité opérationnelle à vos données de Configuration d’état Automation et peuvent aider à traiter les incidents plus rapidement.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble, consultez [Azure Automation State Configuration](automation-dsc-overview.md)
- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md)
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
- Pour en savoir plus sur la façon de construire différentes requêtes de recherche et consulter les journaux de Configuration d’état Automation avec les journaux Azure Monitor, consultez [recherches de journal dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md)
- Pour en savoir plus sur les journaux d’Azure Monitor et les sources de collecte de données, consultez [présentation des journaux de collecte stockage des données Azure dans Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md)
