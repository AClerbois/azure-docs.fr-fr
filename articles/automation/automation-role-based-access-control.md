---
title: Contrôle d’accès en fonction du rôle dans Azure Automation
description: Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Cet article décrit la configuration de RBAC dans Azure Automation.
keywords: rbac automation, contrôle d’accès en fonction du rôle, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bcbda2464a4607aaa0b1bb96ef8f34c8713cb5f1
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918788"
---
# <a name="role-based-access-control-in-azure-automation"></a>Contrôle d’accès en fonction du rôle dans Azure Automation

Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Avec [RBAC](../role-based-access-control/overview.md), vous pouvez répartir les tâches au sein de votre équipe et accorder aux utilisateurs, groupes et applications uniquement les accès nécessaires pour accomplir leur travail. L’accès en fonction du rôle peut être accordé aux utilisateurs à l’aide du portail Azure, des outils en ligne de commande Azure ou des API de gestion Azure.

## <a name="roles-in-automation-accounts"></a>Rôles dans les comptes Automation

Dans Azure Automation, l’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue de compte Automation. Les rôles intégrés pris en charge par un compte Automation sont présentés ci-après :

| **Rôle** | **Description** |
|:--- |:--- |
| Propriétaire |Le rôle Propriétaire autorise l’accès à l’ensemble des ressources et actions d’un compte Automation, notamment l’accès aux autres utilisateurs, groupes et applications pour gérer le compte Automation. |
| Contributeur |Le rôle Collaborateur vous permet de gérer tous les éléments excepté la modification des autorisations d’accès des autres utilisateurs à un compte Automation. |
| Lecteur |Le rôle Lecteur vous permet d’afficher toutes les ressources d’un compte Automation, mais vous ne pouvez pas y apporter de modifications. |
| Opérateur Automation |Le rôle Opérateur Automation vous permet de voir le nom et les propriétés du runbook, et de créer et gérer des travaux pour tous les runbooks dans un compte Automation. Ce rôle est utile si vous souhaitez protéger vos ressources de compte Automation telles que les ressources d’informations d’identification et les Runbooks et empêcher leur affichage ou leur modification, tout en autorisant les membres de votre organisation à exécuter ces Runbooks. |
|Opérateur de travaux Automation|Le rôle Opérateur de travaux Automation vous permet de créer et de gérer des travaux pour tous les runbooks dans un compte Automation.|
|Opérateur de runbook Automation|Le rôle Opérateur de runbook Automation vous permet de voir le nom et les propriétés d’un runbook.|
| Contributeur Log Analytics | Le rôle Contributeur Log Analytics vous permet de lire toutes les données de surveillance et de modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure.|
| Lecteur Log Analytics | Le rôle Lecteur Log Analytics vous permet d’afficher et de rechercher toutes les données de surveillance, ainsi que d’afficher les paramètres de surveillance. Vous pouvez notamment afficher la configuration des diagnostics Azure sur toutes les ressources Azure. |
| Contributeur d’analyse | Le rôle Contributeur de surveillance vous permet de lire toutes les données de surveillance et de mettre à jour les paramètres de surveillance.|
| Lecteur d’analyse | Le rôle Lecteur d’analyse vous permet de lire toutes les données de surveillance. |
| Administrateur de l'accès utilisateur |Le rôle Administrateur de l’accès utilisateur vous permet de gérer l’accès utilisateur aux comptes Azure Automation. |

## <a name="role-permissions"></a>Autorisations des rôles

Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle. Il peut s’agir d’Actions qui accordent des autorisations et de NotActions qui restreignent les autorisations.

### <a name="owner"></a>Propriétaire

Un propriétaire peut tout gérer, notamment l’accès. Le tableau suivant présente les autorisations accordées pour le rôle :

|Actions|Description|
|---|---|
|Microsoft.Automation/automationAccounts/|Créer et gérer les ressources de tous les types.|

### <a name="contributor"></a>Contributeur

Un contributeur peut tout gérer, sauf les accès. Le tableau suivant indique les autorisations accordées et refusées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Créer et gérer les ressources de tous les types|
|**Pas d’Actions**||
|Microsoft.Authorization/*/Delete| Supprimer des rôles et des attributions de rôles.       |
|Microsoft.Authorization/*/Write     |  Créer des rôles et des attributions de rôles.       |
|Microsoft.Authorization/elevateAccess/Action    | Refuse la possibilité de créer un administrateur de l’accès utilisateur.       |

### <a name="reader"></a>Lecteur

Un lecteur peut afficher toutes les ressources d’un compte Automation, mais il ne peut pas apporter de changements.

|**Actions**  |**Description**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Afficher toutes les ressources dans un compte Automation. |

### <a name="automation-operator"></a>Opérateur Automation

Un opérateur Automation est capable de créer et gérer des travaux, et de lire le nom et les propriétés du runbook pour tous les runbooks d’un compte Automation.  Remarque : Si vous voulez contrôler l’accès de l’opérateur aux différents runbooks, ne définissez pas ce rôle et utilisez conjointement les rôles « Opérateur de travaux Automation » et « Opérateur de runbook Automation » à la place. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lire l’autorisation.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Lire des ressources Runbook Worker hybrides.|
|Microsoft.Automation/automationAccounts/jobs/read|Répertorier les travaux du runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Reprendre un travail en pause.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuler un travail en cours d’exécution.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lire les flux de travail et la sortie.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Obtenir la sortie d’un travail.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Suspendre un travail en cours.|
|Microsoft.Automation/automationAccounts/jobs/write|Créer des travaux.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Obtenir une planification de travail Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Créer une planification de travail Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Obtenir l’espace de travail lié au compte Automation.|
|Microsoft.Automation/automationAccounts/read|Obtenir un compte Azure Automation.|
|Microsoft.Automation/automationAccounts/runbooks/read|Obtenir un runbook Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/read|Obtenir une ressource de planification Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/write|Créer ou mettre à jour une ressource de planification Azure Automation.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lire les rôles et les attributions de rôle.         |
|Microsoft.Resources/deployments/*      |Créer et gérer les déploiements de groupes de ressources.         |
|Microsoft.Insights/alertRules/*      | Créer et gérer les règles d’alerte.        |
|Microsoft.Support/* |Créer et gérer les tickets de support.|

### <a name="automation-job-operator"></a>Opérateur de travaux Automation

Un rôle Opérateur de travaux Automation est accordé au niveau de l’étendue du compte Automation. Les autorisations de l’opérateur permettent donc de créer et de gérer les travaux pour tous les runbooks du compte. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lire l’autorisation.|
|Microsoft.Automation/automationAccounts/jobs/read|Répertorier les travaux du runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Reprendre un travail en pause.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuler un travail en cours d’exécution.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lire les flux de travail et la sortie.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Suspendre un travail en cours.|
|Microsoft.Automation/automationAccounts/jobs/write|Créer des travaux.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Lire les rôles et les attributions de rôle.       |
|Microsoft.Resources/deployments/*      |Créer et gérer les déploiements de groupes de ressources.         |
|Microsoft.Insights/alertRules/*      | Créer et gérer les règles d’alerte.        |
|Microsoft.Support/* |Créer et gérer les tickets de support.|

### <a name="automation-runbook-operator"></a>Opérateur de runbook Automation

Un rôle Opérateur de runbook Automation est accordé au niveau de l’étendue du runbook. Un opérateur de runbook Automation peut voir le nom et les propriétés du runbook.  Ce rôle associé au rôle « Opérateur de travaux Automation » permet à l’opérateur de créer et gérer également des travaux pour le runbook. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Répertorier les runbooks.        |
|Microsoft.Authorization/*/read      | Lire l’autorisation.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lire les rôles et les attributions de rôle.         |
|Microsoft.Resources/deployments/*      | Créer et gérer les déploiements de groupes de ressources.         |
|Microsoft.Insights/alertRules/*      | Créer et gérer les règles d’alerte.        |
|Microsoft.Support/*      | Créer et gérer les tickets de support.        |

### <a name="log-analytics-contributor"></a>Contributeur Log Analytics

Un contributeur Log Analytics peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|*/read|Lire les ressources de tous les types, à l’exception des secrets.|
|Microsoft.Automation/automationAccounts/*|Gérer les comptes Automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Créer et gérer les extensions de machines virtuelles.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Répertorier les clés de compte de stockage classique.|
|Microsoft.Compute/virtualMachines/extensions/*|Créer et gérer les extensions de machines virtuelles classiques.|
|Microsoft.Insights/alertRules/*|Règles d’alerte en lecture/écriture/suppression.|
|Microsoft.Insights/diagnosticSettings/*|Paramètres de diagnostic en lecture/écriture/suppression.|
|Microsoft.OperationalInsights/*|Gérer les journaux d’Azure Monitor.|
|Microsoft.OperationsManagement/*|Gérer les solutions dans les espaces de travail.|
|Microsoft.Resources/deployments/*|Créer et gérer les déploiements de groupes de ressources.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Créer et gérer les déploiements de groupes de ressources.|
|Microsoft.Storage/storageAccounts/listKeys/action|Répertorier les clés de compte de stockage.|
|Microsoft.Support/*|Créer et gérer les tickets de support.|

### <a name="log-analytics-reader"></a>Lecteur Log Analytics

Un lecteur Log Analytics peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. Le tableau suivant indique les autorisations accordées ou refusées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|*/read|Lire les ressources de tous les types, à l’exception des secrets.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gérer les requêtes dans les journaux Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Rechercher des données de journal Azure Monitor.|
|Microsoft.Support/*|Créer et gérer les tickets de support.|
|**Pas d’Actions**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Impossible de lire les clés d’accès partagé.|

### <a name="monitoring-contributor"></a>Contributeur d’analyse

Un contributeur de surveillance peut lire toutes les données de surveillance et mettre à jour les paramètres de surveillance. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|*/read|Lire les ressources de tous les types, à l’exception des secrets.|
|Microsoft.AlertsManagement/alerts/*|Gérer les alertes.|
|Microsoft.AlertsManagement/alertsSummary/*|Gérer le tableau de bord des alertes.|
|Microsoft.Insights/AlertRules/*|Gérer les règles d’alerte.|
|Microsoft.Insights/components/*|Gérer les composants Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gérer les paramètres de diagnostic.|
|Microsoft.Insights/eventtypes/*|Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité.|
|Microsoft.Insights/LogDefinitions/*|Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité.|
|Microsoft.Insights/MetricDefinitions/*|Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource).|
|Microsoft.Insights/Metrics/*|Lire des mesures pour une ressource.|
|Microsoft.Insights/Register/Action|Inscrire le fournisseur Microsoft.Insights|
|Microsoft.Insights/webtests/*|Gérer les tests web Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gérer les packs de solution de journaux Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gérer les recherches de journaux enregistrés Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Rechercher dans les espaces de travail Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Répertorier les clés pour un espace de travail Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gérer les configurations de stockage Azure Monitor journaux.|
|Microsoft.Support/*|Créer et gérer les tickets de support.|
|Microsoft.WorkloadMonitor/workloads/*|Gérer les charges de travail.|

### <a name="monitoring-reader"></a>Lecteur d’analyse

Un lecteur de surveillance peut lire toutes les données de surveillance. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|*/read|Lire les ressources de tous les types, à l’exception des secrets.|
|Microsoft.OperationalInsights/workspaces/search/action|Rechercher dans les espaces de travail Log Analytics.|
|Microsoft.Support/*|Créer et gérer les tickets de support|

### <a name="user-access-administrator"></a>Administrateur de l'accès utilisateur

Un administrateur de l’accès utilisateur peut gérer l’accès des utilisateurs aux ressources Azure. Le tableau suivant présente les autorisations accordées pour le rôle :

|**Actions**  |**Description**  |
|---------|---------|
|*/read|Lire toutes les ressources|
|Microsoft.Authorization/*|Gérer les autorisations|
|Microsoft.Support/*|Créer et gérer les tickets de support|

## <a name="onboarding"></a>Mise en route

Les tableaux suivants indiquent les autorisations minimales nécessaires pour l’intégration de machines virtuelles pour les solutions de suivi des changements ou de gestion des mises à jour.

### <a name="onboarding-from-a-virtual-machine"></a>Intégration à partir d’une machine virtuelle

|**Action**  |**Autorisation**  |**Étendue minimale**  |
|---------|---------|---------|
|Écrire le nouveau déploiement      | Microsoft.Resources/deployments/*          |Abonnement          |
|Écrire le nouveau groupe de ressources      | Microsoft.Resources/subscriptions/resourceGroups/write        | Abonnement          |
|Créer le nouvel espace de travail par défaut      | Microsoft.OperationalInsights/workspaces/write         | Groupe de ressources         |
|Créer un compte      |  Microsoft.Automation/automationAccounts/write        |Groupe de ressources         |
|Lier l’espace de travail et le compte      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Espace de travail</br>Compte Automation
|Créer une solution      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Groupe de ressources          |
|Créer une extension MMA      | Microsoft.Compute/virtualMachines/write         | Machine virtuelle         |
|Créer une recherche enregistrée      | Microsoft.OperationalInsights/workspaces/write          | Espace de travail         |
|Créer une configuration d’étendue      | Microsoft.OperationalInsights/workspaces/write          | Espace de travail         |
|Lier la solution à la configuration d’étendue      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solution         |
|Vérification de l’état d’intégration - Lire l’espace de travail      | Microsoft.OperationalInsights/workspaces/read         | Espace de travail         |
|Vérification de l’état d’intégration - Lire la propriété de l’espace de travail lié du compte     | Microsoft.Automation/automationAccounts/read      | Compte Automation        |
|Vérification de l’état d’intégration - Lire la solution      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solution         |
|Vérification de l’état d’intégration - Lire la machine virtuelle      | Microsoft.Compute/virtualMachines/read         | Machine virtuelle         |
|Vérification de l’état d’intégration - Lire le compte      | Microsoft.Automation/automationAccounts/read  |  Compte Automation   |
| Vérification d’espace de travail d’intégration de machine virtuelle<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Abonnement         |

<sup>1</sup> cette autorisation est nécessaire pour intégrer l’expérience de portail de machine virtuelle.

### <a name="onboarding-from-automation-account"></a>Intégration à partir du compte Automation

|**Action**  |**Autorisation** |**Étendue minimale**  |
|---------|---------|---------|
|Créer un déploiement     | Microsoft.Resources/deployments/*        | Abonnement         |
|Créer un groupe de ressources     | Microsoft.Resources/subscriptions/resourceGroups/write         | Abonnement        |
|Panneau AutomationOnboarding - Créer un espace de travail     |Microsoft.OperationalInsights/workspaces/write           | Groupe de ressources        |
|Panneau AutomationOnboarding - Lire l’espace de travail lié     | Microsoft.Automation/automationAccounts/read        | Compte Automation       |
|Panneau AutomationOnboarding - Lire la solution     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solution        |
|Panneau AutomationOnboarding - Lire l’espace de travail     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Espace de travail        |
|Créer un lien pour l’espace de travail et le compte     | Microsoft.OperationalInsights/workspaces/write        | Espace de travail        |
|Écrire le compte pour shoebox      | Microsoft.Automation/automationAccounts/write        | Compte        |
|Créer une solution      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Groupe de ressources         |
|Créer/modifier la recherche enregistrée     | Microsoft.OperationalInsights/workspaces/write        | Espace de travail        |
|Créer/modifier la configuration d’étendue     | Microsoft.OperationalInsights/workspaces/write        | Espace de travail        |
|Lier la solution à la configuration d’étendue      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solution         |
|**Étape 2 : intégrer plusieurs machines virtuelles**     |         |         |
|Panneau VMOnboarding - Créer l’extension MMA     | Microsoft.Compute/virtualMachines/write           | Machine virtuelle        |
|Créer/modifier la recherche enregistrée     | Microsoft.OperationalInsights/workspaces/write           | Espace de travail        |
|Créer/modifier la configuration d’étendue  | Microsoft.OperationalInsights/workspaces/write   | Espace de travail|

## <a name="update-management"></a>Gestion des mises à jour

La gestion des mises à jour s’étend à plusieurs services pour fournir son service. Le tableau suivant présente les autorisations nécessaires pour gérer les déploiements de gestion des mises à jour :

|**Ressource**  |**Rôle**  |**Étendue**  |
|---------|---------|---------|
|Compte Automation     | Contributeur Log Analytics       | Compte Automation        |
|Compte Automation    | Contributeur de machine virtuelle        | Groupe de ressources pour le compte        |
|Espace de travail Log Analytics     | Contributeur Log Analytics| Espace de travail Log Analytics        |
|Espace de travail Log Analytics |Lecteur Log Analytics| Abonnement|
|Solution     |Contributeur Log Analytics         | Solution|
|Machine virtuelle     | Contributeur de machine virtuelle        | Machine virtuelle        |

## <a name="configure-rbac-for-your-automation-account"></a>Configurer RBAC pour votre compte Automation

La section suivante vous explique comment configurer RBAC sur votre compte Automation via le [portail](#configure-rbac-using-the-azure-portal) et [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Configurer RBAC à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez votre compte Automation depuis la page Comptes Automation.
2. Cliquez sur le contrôle **Contrôle d’accès (IAM)** en haut à gauche. Dans la page **Contrôle d’accès (IAM)** qui s’ouvre, vous pouvez ajouter de nouveaux utilisateurs, groupes et applications pour gérer votre compte Automation et afficher les rôles existants qui peuvent être configurés pour le compte Automation.
3. Cliquez sur l’onglet **Attributions de rôles**.

   ![Bouton Accéder](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Ajouter un nouvel utilisateur et affecter un rôle

1. Dans la page **Contrôle d’accès (IAM)**, cliquez sur **+ Ajouter une attribution de rôle**  pour ouvrir la page **Ajouter une attribution de rôle** dans laquelle vous pouvez ajouter un utilisateur, un groupe ou une application, puis lui attribuer un rôle.

2. Dans la liste des rôles disponibles, sélectionnez un rôle. Vous pouvez choisir l’un des rôles intégrés disponibles pris en charge par un compte Automation ou un rôle personnalisé que vous avez défini.

3. Tapez le nom d’utilisateur de l’utilisateur auquel vous souhaitez donner des autorisations dans le champ **Sélectionner**. Sélectionnez l’utilisateur dans la liste et cliquez sur **Enregistrer**.

   ![Ajouter des utilisateurs](media/automation-role-based-access-control/automation-04-add-users.png)

   L’utilisateur doit maintenant apparaître dans la page **Utilisateurs** avec le rôle sélectionné qui lui a été affecté.

   ![Répertorier les utilisateurs](media/automation-role-based-access-control/automation-05-list-users.png)

   Vous pouvez également affecter un rôle à l’utilisateur à partir de la page **Rôles** .
4. Cliquez sur **Rôles** dans la page **Contrôle d’accès (IAM)** pour ouvrir la page **Rôles**. Dans cette page, vous pouvez voir le nom du rôle, ainsi que le nombre d’utilisateurs et de groupes affectés à ce rôle.

    ![Affecter un rôle à partir de la page Utilisateurs](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Le contrôle d’accès en fonction du rôle ne peut être défini qu’au niveau de l’étendue du compte Automation, et pas au niveau d’une ressource située sous ce compte.

#### <a name="remove-a-user"></a>Supprimer un utilisateur

Vous pouvez supprimer l’autorisation d’accès d’un utilisateur qui ne gère pas le compte Automation ou qui ne travaille plus pour l’organisation. Pour supprimer un utilisateur, procédez comme suit :

1. Dans la page **Contrôle d’accès (IAM)**, sélectionnez l’utilisateur à supprimer, puis cliquez sur **Supprimer**.
2. Cliquez sur le bouton **Supprimer** dans le volet des détails de l’affectation.
3. Cliquez sur **Oui** pour confirmer la suppression.

   ![Supprimer des utilisateurs](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configurer RBAC à l’aide de PowerShell

L’accès en fonction du rôle peut également être configuré pour un compte Automation à l’aide des [applets de commande Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) ci-dessous :

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) répertorie tous les rôles RBAC qui sont disponibles dans Azure Active Directory. Vous pouvez utiliser cette commande avec la propriété **Name** pour dresser la liste de toutes les actions qui peuvent être effectuées par un rôle spécifique.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Voici l’exemple de sortie :

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) répertorie les affectations de rôle RBAC d’Azure AD dans l’étendue spécifiée. Sans paramètres, cette commande renvoie toutes les affectations de rôle effectuées dans l’abonnement. Utilisez le paramètre **ExpandPrincipalGroups** pour répertorier les affectations d’accès de l’utilisateur spécifié et des groupes dont il est membre.
    **Exemple :** Utilisez la commande suivante pour lister tous les utilisateurs et leurs rôles dans un compte Automation.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Voici l’exemple de sortie :

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Utilisez [New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) pour affecter un accès à des utilisateurs, des groupes et des applications à une étendue particulière.
    **Exemple :** Utilisez la commande suivante pour attribuer le rôle « Opérateur d’Automation » à un utilisateur dans l’étendue du compte Automation.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Voici l’exemple de sortie :

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Utilisez [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) pour supprimer l’accès d’un utilisateur, groupe ou application spécifié dans une étendue particulière.
    **Exemple :** Utilisez la commande suivante pour supprimer l’utilisateur du rôle « Opérateur d’Automation » dans l’étendue du compte Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Dans les exemples ci-dessus, remplacez **l’ID de connexion**, **l’ID d’abonnement**, le **nom du groupe de ressources** et le **nom du compte Automation** par les détails de votre compte. Choisissez **oui** lorsque vous êtes invité à confirmer avant de supprimer une affectation de rôle d’utilisateur.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Expérience utilisateur pour le rôle d’opérateur Automation - Compte Automation

Quand un utilisateur affecté au rôle Opérateur Automation sur l’étendue du compte Automation consulte le compte Automation auquel il est affecté, il peut afficher uniquement la liste des runbooks ainsi que les tâches et les planifications de runbook créées dans le compte Automation, mais il ne peut pas afficher leur définition. Il peut démarrer, arrêter, suspendre, reprendre ou planifier la tâche de Runbook. L’utilisateur n’a pas accès aux autres ressources Automation telles que les configurations, les groupes de Workers hybrides ou les nœuds DSC.

![Aucun accès aux ressources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configurer RBAC pour les runbooks

Azure Automation vous permet d’affecter RBAC aux runbooks spécifiques. Pour cela, exécutez le script suivant afin d’ajouter un utilisateur à un runbook spécifique. Le script suivant peut être exécuté par un administrateur de compte Automation ou un administrateur de locataire.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Une fois exécuté, invitez l’utilisateur à se connecter au portail Azure et à consulter **Toutes les ressources**. La liste affiche le runbook auquel il a été ajouté en tant **qu’opérateur de runbook Automation**.

![RBAC de runbook dans le portail](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Expérience utilisateur pour le rôle d’opérateur Automation - Runbook

Lorsqu’un utilisateur à qui le rôle d’opérateur Automation a été attribué sur l’étendue runbook consulte un runbook auquel il a été affecté, il peut seulement démarrer le runbook et afficher les travaux de runbook.

![Accès uniquement pour démarrer](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les différentes façons de configurer RBAC pour Azure Automation, reportez-vous à l’article [Gestion du Contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Pour plus d’informations sur les différentes façons de démarrer un Runbook, consultez l’article [Démarrage d’un Runbook](automation-starting-a-runbook.md)
* Pour en savoir plus sur les différents types de Runbook, consultez l’article [Types de Runbook Azure Automation](automation-runbook-types.md)

