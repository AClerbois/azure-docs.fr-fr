---
title: Créer un pool d’hôtes Windows Virtual Desktop Preview pour valider les mises à jour de services - Azure
description: Comment créer un pool d’hôtes de validation pour surveiller les mises à jour de service avant de déployer les mises à jour en production.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c9b2a593a6943fe2e9577acc61b1d5a7bcd98607
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070654"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Didacticiel : Créer un pool d’hôtes pour valider les mises à jour de service

Les pools d’hôtes sont une collection d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop Preview. Avant de déployer des pools d’hôtes dans votre environnement de production, nous vous recommandons vivement de créer un pool d’hôtes de validation. Les mises à jour sont d'abord appliquées aux pools d’hôtes de validation, ce qui vous permet de surveiller les mises à jour de service avant de les déployer dans votre environnement de production. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui introduisent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement de production.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement de production. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu'au pool d’hôtes de production. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes dans le pool d’hôtes de validation à l'aide de [la fonctionnalité de diagnostic](diagnostics-role-service.md) ou des [articles de résolution des problèmes Windows Virtual Desktop](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

## <a name="create-your-host-pool"></a>Créer votre pool d'hôtes

Vous pouvez créer un pool d'hôtes en suivant les instructions de ces articles :
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace.md)
- [Créer un pool d’hôtes avec le modèle Azure Resource Manager](create-host-pools-arm-template.md)
- [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Définir votre pool d’hôtes en tant que pool d’hôtes de validation

Exécutez les cmdlets PowerShell suivantes pour définir le nouveau pool d’hôtes en tant que pool d’hôtes de validation. Remplacez les valeurs entre guillemets par les valeurs correspondant à votre session :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Exécutez la cmdlet PowerShell suivante pour vérifier que la propriété de validation a été définie. Remplacez les valeurs entre guillemets par les valeurs correspondant à votre session.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Les résultats de la cmdlet doivent se présenter comme suit :

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Mettre à jour la planification

Dans la préversion, les mises à jour de service sont publiées environ une fois par mois. En présence de problèmes majeurs, des mises à jour critiques seront publiées plus fréquemment.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un pool d’hôte de validation, vous pouvez apprendre à déployer et à vous connecter à un outil de gestion pour la gestion des ressources Microsoft Virtual Desktop.

> [!div class="nextstepaction"]
> [Didacticiel de déploiement d’un outil de gestion](./manage-resources-using-ui.md)
