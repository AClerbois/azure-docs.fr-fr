---
title: Préversion de l’accès délégué dans Windows Virtual Desktop - Azure
description: Comment déléguer des fonctionnalités d’administration lors du déploiement d’une préversion de Windows Virtual Desktop, y compris des exemples.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870504"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Préversion de l’accès délégué dans Windows Virtual Desktop

La préversion de Windows Virtual Desktop dispose d’un modèle d’accès délégué qui vous permet de définir les droits d’accès d’un utilisateur particulier en lui attribuant un rôle. Une attribution de rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue. Le modèle d’accès délégué de Windows Virtual Desktop est basé sur le modèle RBAC d’Azure. Pour en savoir plus sur les attributions de rôle spécifique et leurs composants, consultez [la vue d’ensemble du contrôle d'accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

L’accès délégué de Windows Virtual Desktop prend en charge les valeurs suivantes pour chaque élément de l’attribution de rôle :

* Principal de sécurité
    * Utilisateurs
    * Principaux de service
* Définition de rôle
    * Rôles intégrés
* Étendue
    * Groupes de clients
    * Locataires
    * Pools d’hôtes
    * Groupes d’applications

## <a name="built-in-roles"></a>Rôles intégrés

L’accès délégué dans Windows Virtual Desktop a plusieurs définitions de rôles intégrés que vous pouvez affecter aux utilisateurs et principaux de service.

* Un propriétaire RDS peut tout gérer, y compris l’accès aux ressources.
* Un contributeur RDS peut tout gérer, sauf l’accès aux ressources.
* Un lecteur RDS peut tout voir, mais ne peut faire aucun changement.
* Un opérateur RDS peut consulter les activités de diagnostic.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets Azure PowerShell pour les attributions de rôles

Vous pouvez exécuter les cmdlets suivantes pour créer, afficher et modifier des attributions de rôles :

* **Get-RdsRoleAssignment** pour afficher une liste des attributions de rôles.
* **Get-RdsRoleAssignment** pour créer une nouvelle attribution de rôle.
* **Get-RdsRoleAssignment** pour modifier des attributions de rôles.

### <a name="accepted-parameters"></a>Paramètres acceptés

Vous pouvez modifier les trois cmdlets de base avec les paramètres suivants :

* **AadTenantId** : spécifie l’ID du locataire Azure Active Directory dont le principal du service est membre.
* **AppGroupName** : nom du groupe d’application du Bureau à distance.
* **Diagnostics** : indique la portée des diagnostics. (Doit être associé aux paramètres **Infrastructure** ou **Locataire**)
* **HostPoolName** : nom du pool d’hôte du Bureau à distance.
* **Infrastructure** : indique la portée de l’infrastructure.
* **RoleDefinitionName** : nom du rôle de contrôle d’accès en fonction du rôle des Services Bureau à distance attribué à l’utilisateur, au groupe ou à l’application. (Par exemple, propriétaire des Services Bureau à distance, lecteur des Services Bureau à distance et ainsi de suite.)
* **ServerPrincipleName** : nom de l’application Azure Active Directory.
* **SignInName** : adresse e-mail de l’utilisateur ou nom d’utilisateur principal.
* **TenantName** : nom du locataire du Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste plus complète des cmdlets PowerShell que chaque rôle peut utiliser, consultez les [Informations de référence PowerShell](/powershell/windows-virtual-desktop/overview).

Pour obtenir des instructions et savoir comment configurer un environnement Bureau virtuel Windows, consultez [Environnement dans la préversion Windows Virtual Desktop](environment-setup.md).
