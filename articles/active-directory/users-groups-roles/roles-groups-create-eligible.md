---
title: Créer un groupe pour l’attribution de rôles dans Azure Active Directory | Microsoft Docs
description: Affichez un aperçu des rôles Azure AD personnalisés pour la délégation de la gestion des identités. Gérez les rôles Azure dans le Portail Azure, PowerShell ou l’API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5efbbd5f9f5dfe701deb50b51605ab324c081a
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401671"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Créer un groupe avec attribution de rôle dans Azure Active Directory

Vous pouvez attribuer un rôle uniquement à un groupe qui a été créé avec la propriété « isAssignableToRole » définie sur True ou qui a été créé dans le portail Azure AD avec l’option **Des rôles Azure AD peuvent être attribués au groupe** activée. Avec cet attribut, le groupe peut être attribué à un rôle dans Azure AD (Azure Active Directory). Cet article explique comment créer ce type de groupe spécial. **Remarque :** Un groupe dont la propriété isAssignableToRole a la valeur true ne peut pas être de type d’appartenance dynamique. Pour plus d’informations, consultez [Utilisation d’un groupe pour gérer des attributions de rôles Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept).

## <a name="using-azure-ad-admin-center"></a>Utilisation du Centre d’administration Azure AD

1. Connectez-vous au [Centre d’administration Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec des autorisations Administrateur de rôle privilégié ou Administrateur général dans l’organisation Azure AD.
1. Sélectionnez **Groupes** > **Tous les groupes** > **Nouveau groupe**.

    [![Ouvrez Azure Active Directory et créez un groupe.](media/roles-groups-create-eligible/new-group.png "Ouvrez Azure Active Directory et créez un groupe.")](media/roles-groups-create-eligible/new-group.png#<lightbox>)

1. Sous l’onglet **Nouveau groupe**, spécifiez un type, un nom et une description pour le groupe.
1. Activez l’option **Des rôles Azure AD peuvent être attribués au groupe**. Ce commutateur est visible uniquement pour les utilisateurs autorisés à le définir, c’est-à-dire ceux qui ont le rôle Administrateur de rôle privilégié ou Administrateur général.

    [![Rendre le nouveau groupe éligible à l’attribution de rôle](media/roles-groups-create-eligible/eligible-switch.png "Rendre le nouveau groupe éligible à l’attribution de rôle")](media/roles-groups-create-eligible/eligible-switch.png#<lightbox>)

1. Sélectionnez les membres et les propriétaires du groupe. Vous avez également la possibilité d’attribuer des rôles au groupe. Dans le cas présent, ce n’est pas nécessaire.

    [![Ajoutez des membres au groupe avec attribution de rôle et attribuez des rôles.](media/roles-groups-create-eligible/specify-members.png "Ajoutez des membres au groupe avec attribution de rôle et attribuez des rôles.")](media/roles-groups-create-eligible/specify-members.png#<lightbox>)

1. Quand vous avez spécifié les membres et les propriétaires, sélectionnez **Créer**.

    [![Le bouton Créer est disponible en bas de la page.](media/roles-groups-create-eligible/create-button.png "Le bouton Créer est disponible en bas de la page.")](media/roles-groups-create-eligible/create-button.png#<lightbox>)

Le groupe est créé avec tous les rôles que vous lui avez attribués.

## <a name="using-powershell"></a>Utilisation de PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Installer le module Azure AD en préversion

```powershell
install-module azureadpreview
import-module azureadpreview
```

Pour vérifier que le module est prêt à l’emploi, exécutez la commande suivante :

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Créer un groupe auquel un rôle peut être attribué

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Pour ce type de groupe, `isPublic` est toujours false et `isSecurityEnabled` est toujours true.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Copier les utilisateurs et les principaux de service d’un groupe dans un groupe avec attribution de rôle

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating O365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Utilisation de l’API Microsoft Graph

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Créer un groupe avec attribution de rôle dans Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

Pour ce type de groupe, `isPublic` est toujours false et `isSecurityEnabled` est toujours true.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer un rôle à un groupe cloud](roles-groups-assign-role.md)
- [Utiliser des groupes cloud pour gérer les attributions de rôles](roles-groups-concept.md)
- [Résoudre les problèmes de rôles attribués aux groupes cloud](roles-groups-faq-troubleshooting.md)
