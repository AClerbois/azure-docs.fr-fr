---
title: Créer et gérer des groupes d’actions sur le Portail Azure
description: Découvrez comment créer et gérer des groupes d’action sur le Portail Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/22/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 52d7b84fe6210d8a4d46814ad6749bed0463478e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405649"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Créer et gérer des groupes d’actions sur le Portail Azure
Un groupe d’actions est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure. Les alertes Azure Monitor et Service Health utilisent des groupes d’actions pour avertir les utilisateurs qu’une alerte a été déclenchée. Plusieurs alertes peuvent utiliser le même groupe d’actions ou des groupes d’actions différents selon les besoins de l’utilisateur. Vous pouvez configurer jusqu'à 2 000 groupes d'actions au sein d'un abonnement.

Quand vous configurez une action pour notifier une personne par e-mail ou SMS, cette personne reçoit une confirmation indiquant qu’elle a été ajoutée au groupe d’actions.

Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Chaque action se compose des propriétés suivantes :

* **Nom** : identificateur unique au sein du groupe d’actions.  
* **Type d’action** : action effectuée. Exemples : envoi d'un appel vocal, d'un SMS ou d'un e-mail ; ou déclenchement de différents types d'actions automatisées. Reportez-vous aux types présentés plus loin dans cet article.
* **Détails** : détails correspondants qui varient selon le *type d’action*.

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager pour configurer des groupes d’actions, consultez la page [Modèles Resource Manager de groupes d’actions](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Créer un groupe d’actions à l’aide du Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Moniteur**. Le volet **Moniteur** consolide tous vos paramètres et données de supervision dans une même vue.

    ![Le service « Moniteur »](./media/action-groups/home-monitor.png)
    
1. Sélectionnez **Alertes**, puis **Gérer les actions**.

    ![Bouton Gérer les actions](./media/action-groups/manage-action-groups.png)
    
1. Sélectionnez **Ajouter un groupe d’actions** et renseignez les champs.

    ![La commande « Ajouter un groupe d’actions »](./media/action-groups/add-action-group.png)
    
1. Entrez un nom dans la zone **Nom du groupe d’actions** et un autre dans la zone **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

      ![La boîte de dialogue « Ajouter un groupe d’actions »](./media/action-groups/action-group-define.png)

1. La zone **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel est enregistré le groupe d’actions.

1. Sélectionnez le **Groupe de ressources** dans lequel le groupe d’actions est enregistré.

1. Définissez une liste d’actions. Fournissez les informations suivantes pour chaque action :

    1. **Nom** : entrez un identificateur unique pour cette action.

    1. **Type d’action** : sélectionnez E-mail, SMS, Push, Voix, application logique, Webhook, ITSM ou Runbook Automation.

    1. **Détails** : selon le type d’action, saisissez un numéro de téléphone, une adresse e-mail, un URI de webhook, une application Azure, une connexion ITSM ou un runbook Automation. Pour ITSM Action, spécifiez l’**élément de travail** et les autres champs nécessaires à votre outil ITSM.
    
    1. **Schéma d’alerte courant** : Vous pouvez également utiliser le [schéma d’alerte commun](https://aka.ms/commonAlertSchemaDocs), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor.

1. Sélectionnez **OK** pour créer le groupe d’actions.

## <a name="manage-your-action-groups"></a>Gérer des groupes d’actions

Après sa création, un groupe d’actions apparaît dans la section **Groupes d’actions** du volet **Moniteur**. Sélectionnez le groupe d’actions que vous souhaitez gérer pour :

* Ajouter, modifier ou supprimer des actions.
* Supprimer le groupe d’actions.

## <a name="action-specific-information"></a>Informations spécifiques sur l’action

> [!NOTE]
> Consultez [Subscription Service Limits for Monitoring (Limites du service d’abonnement pour la surveillance)](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) pour connaître les limites numériques sur chacun des éléments ci-dessous.  

### <a name="azure-app-push-notifications"></a>Notifications Push Azure App
Vous pouvez avoir un nombre limité d’actions d’application Azure par groupe d’actions.

### <a name="email"></a>Email
Les e-mails seront envoyés à partir des adresses e-mail suivantes. Vérifiez que le filtrage e-mail est correctement configuré
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Vous pouvez avoir un nombre limité d’actions d’e-mail par groupe d’actions. Consultez l’article [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md).

### <a name="itsm"></a>ITSM
ITSM Action requiert une connexion ITSM. Découvrez comment créer une [connexion ITSM](../../azure-monitor/platform/itsmc-overview.md).

Vous pouvez avoir un nombre limité d’actions ITSM par groupe d’actions. 

### <a name="logic-app"></a>Application logique
Vous pouvez avoir un nombre limité d’actions d’application logique par groupe d’actions.

### <a name="function"></a>Fonction
Les touches de fonction de Function Apps, qui sont configurées comme actions, sont lues par le biais de l’API Functions, ce qui nécessite actuellement l’utilisation d’applications de fonction v2 pour définir le paramètre d’application AzureWebJobsSecretStorageType sur « files » (fichiers). Consultez [Modifications apportées à la gestion de clés dans les fonctions V2]( https://aka.ms/funcsecrets) pour plus d’informations.

Vous pouvez avoir un nombre limité d’actions de fonction par groupe d’actions.

### <a name="automation-runbook"></a>Runbook Automation
Consultez l’article [Limites du service d’abonnement Azure](../../azure-subscription-service-limits.md) pour connaître les limites de charges utiles Runbook.

Vous pouvez avoir un nombre limité d’actions de runbook par groupe d’actions. 

### <a name="sms"></a>sms
Pour obtenir d’autres informations importantes, consultez [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md) et [Comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md).

Vous pouvez avoir un nombre limité d’actions SMS par groupe d’actions.  

### <a name="voice"></a>Voix
Consultez l’article [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md).

Vous pouvez avoir un nombre limité d’actions de voix par groupe d’actions.

### <a name="webhook"></a>webhook
Les Webhooks sont retentés au travers des règles suivantes. L’appel de webhook est retenté 2 fois au maximum lorsque les codes d’état HTTP suivants sont retournés : 408, 429, 503, 504 ou que le point de terminaison HTTP ne répond pas. La première nouvelle tentative se produit après 10 secondes. La deuxième nouvelle tentative se produit après 100 secondes. Après deux échecs, aucun groupe d’actions n’appellera le point de terminaison pendant 30 minutes. 

Plage d’adresses IP sources
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Pour recevoir des mises à jour sur les modifications apportées à ces adresses IP, nous vous recommandons de configurer une alerte Service Health qui surveille les notifications d’information relatives au service Groupes d’actions.

Vous pouvez avoir un nombre limité d’actions de webhook par groupe d’actions.

#### <a name="secure-webhook"></a>Webhook sécurisé
**Pour l’instant, la fonctionnalité Webhook sécurisé n’existe qu’en préversion.**

L’action de webhook de groupes d’actions vous permet de tirer parti d’Azure Active Directory pour sécuriser la connexion entre votre groupe d’actions et votre API web protégée (point de terminaison webhook). Le workflow global pour tirer parti de cette fonctionnalité est décrit ci-dessous. Pour obtenir une vue d’ensemble des applications Azure AD et des principaux de service, consultez [Présentation de la plateforme d’identités Microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Créez une application Azure AD pour votre API web protégée. Consultez https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurez votre API protégée pour qu’elle soit appelée par une application démon.
    
1. Activez les groupes d’actions pour utiliser votre application Azure AD.

    > [!NOTE]
    > Pour exécuter ce script, vous devez être membre du [rôle d’administrateur de l’application Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles).
    
    - Modifiez l’appel Connect-AzureAD du script PowerShell pour utiliser votre ID de locataire Azure AD.
    - Modifiez la variable du script PowerShell $myAzureADApplicationObjectId pour utiliser l’ID d’objet de votre application Azure AD.
    - Exécutez le script modifié.
    
1. Configurez l’action de webhook du groupe d’actions.
    - Copiez la valeur $myApp.ObjectId à partir du script et entrez-la dans le champ de l’ID d’objet d’application dans la définition de l’action de webhook.
    
    ![Action de webhook sécurisé](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Script PowerShell de webhook sécurisé

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* [Comprendre le schéma Webhook des alertes du journal d’activité](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* En savoir plus sur [ITSM Connector](../../azure-monitor/platform/itsmc-overview.md)
* En savoir plus sur la [limitation de la fréquence](../../azure-monitor/platform/alerts-rate-limiting.md) des alertes.
* Obtenir une [vue d’ensemble des alertes du journal d’activité](../../azure-monitor/platform/alerts-overview.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
