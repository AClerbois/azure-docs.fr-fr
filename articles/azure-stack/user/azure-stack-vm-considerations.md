---
title: Différences et considérations relatives aux machines virtuelles dans Azure Stack | Microsoft Docs
description: Découvrez les différences et les éléments à prendre en compte lors de l’utilisation de machines virtuelles dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.openlocfilehash: 83a0b8ff040425ac30cff96936f2f639fd1b5643
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076160"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Considérations relatives à l’utilisation des machines virtuelles dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les machines virtuelles Azure Stack fournissent des ressources de calcul scalables et à la demande. Avant de déployer des machines virtuelles, il est important de comprendre les différences entre les fonctionnalités de machine virtuelle disponibles dans Azure Stack et Microsoft Azure. Cet article explique ces différences et identifie les points clés à prendre en compte lors de la planification des déploiements de machines virtuelles. Pour en savoir plus sur les principales différences entre Azure Stack et Azure, consultez l’article [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-virtual-machine-differences"></a>Aide-mémoire : différences sur le plan des machines virtuelles

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
| Images de machine virtuelle | La Place de marché Azure contient des images que vous pouvez utiliser pour créer une machine virtuelle. Consultez la page [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) pour obtenir la liste des images disponibles sur la Place de marché Azure. | Par défaut, il n’y a pas d’images disponibles sur la Place de marché Azure Stack. L’administrateur du cloud Azure Stack doit publier ou télécharger des images sur la Place de marché Azure Stack pour les mettre à la disposition des utilisateurs. |
| Tailles de machines virtuelles | Azure prend en charge de nombreuses tailles de machine virtuelle. Pour connaître les options et les tailles disponibles, consultez les rubriques [Tailles des machines virtuelles Windows](../../virtual-machines/virtual-machines-windows-sizes.md) et [Tailles des machines virtuelles Linux](../../virtual-machines/linux/sizes.md). | Azure Stack prend en charge certaines des tailles de machine virtuelle qui sont disponibles dans Azure. Pour obtenir la liste des tailles prises en charge, consultez la section [Tailles de machine virtuelle](#virtual-machine-sizes) dans cet article. |
| Quotas de machine virtuelle | Les [limites de quota](../../azure-subscription-service-limits.md#service-specific-limits) sont définies par Microsoft. | L’administrateur du cloud Azure Stack doit assigner des quotas avant de mettre des machines virtuelles à la disposition des utilisateurs. |
| Extensions de machine virtuelle |Azure prend en charge de nombreuses extensions de machine virtuelle. Pour connaître les extensions disponibles, consultez l’article [Extensions et fonctionnalités de machine virtuelle](../../virtual-machines/windows/extensions-features.md).| Azure Stack prend en charge certaines des extensions disponibles dans Azure. Notez que chaque extension a des versions spécifiques. L’administrateur du cloud Azure Stack peut choisir les extensions qu’il veut mettre à la disposition des utilisateurs. Pour obtenir la liste des extensions prises en charge, consultez la section [Extensions de machine virtuelle](#virtual-machine-extensions) dans cet article. |
| Réseau de machines virtuelles | Les adresses IP publiques assignées à une machine virtuelle locataire sont accessibles via Internet.<br><br><br>Les machines virtuelles Azure ont un nom DNS fixe. | Les adresses IP publiques assignées à une machine virtuelle locataire sont accessibles uniquement au sein de l’environnement du Kit de développement Azure Stack. Un utilisateur doit pouvoir accéder au Kit de développement Azure Stack par le biais du [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou du [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) pour se connecter à une machine virtuelle créée dans Azure Stack.<br><br>Les machines virtuelles créées dans une instance Azure Stack spécifique ont un nom DNS basé sur la valeur définie par l’administrateur du cloud. |
| Stockage de machine virtuelle | Prend en charge les [disques managés](../../virtual-machines/windows/managed-disks-overview.md). | Les disques managés ne sont pas encore pris en charge dans Azure Stack. |
| Versions d’API | Azure utilise toujours les dernières versions d’API pour toutes les fonctionnalités de machine virtuelle. | Azure Stack prend en charge certains services Azure et des versions d’API spécifiques pour ces services. Pour obtenir la liste des versions d’API prises en charge, consultez la section [Versions d’API](#api-versions) dans cet article. |
|Groupes à haute disponibilité de machines virtuelles|Plusieurs domaines d’erreur (2 ou 3 par région)<br>Plusieurs domaines de mise à jour<br>Prise en charge des disques managés|Plusieurs domaines d’erreur (2 ou 3 par région)<br>Plusieurs domaines de mise à jour (20 max.)<br>Pas de prise en charge des disques managés|
|Groupes identiques de machines virtuelles |Prise en charge du dimensionnement automatique des instances|Pas de prise en charge du dimensionnement automatique des instances.<br>Pour ajouter d’autres instances à un groupe identique, utilisez le portail, les modèles Resource Manager ou PowerShell.

## <a name="virtual-machine-sizes"></a>Tailles de machines virtuelles

Azure Stack impose des limites de ressources pour éviter la consommation excessive des ressources (au niveau du service ou du serveur local). Ces limites améliorent les performances du locataire en réduisant l’impact de la consommation des ressources par d’autres locataires.

- Pour la sortie réseau de la machine virtuelle, des limites de bande passante sont en place. Les limites dans Azure Stack sont les mêmes que celles appliquées dans Azure.
- Pour les ressources de stockage, Azure Stack implémente des limites d’E/S par seconde de stockage pour éviter une consommation excessive de base des ressources par les locataires pour l’accès au stockage.
- Pour les machines virtuelles avec plusieurs disques de données joints, le débit maximal de chaque disque de données est de 500 E/S par seconde pour les disques HHD et de 2300 E/S par seconde pour les disques SSD.

Le tableau suivant répertorie les machines virtuelles prises en charge sur Azure Stack, ainsi que leur configuration :

| type           | Taille          | Plage de tailles prises en charge |
| ---------------| ------------- | ------------------------ |
|Usage général |De base A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Usage général |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Usage général |Série D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Usage général |Série Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Usage général |Série DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Usage général |Séries DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Mémoire optimisée|Série D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Mémoire optimisée|Série DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Mémoire optimisée|Série Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Mémoire optimisée|Séries DSv2 -  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Les tailles de machine virtuelle et les quantités de ressources associées sont cohérentes entre Azure Stack et Azure. Cela inclut la quantité de mémoire, le nombre de cœurs et le nombre ou la taille des disques de données qui peuvent être créés. Toutefois, les performances des machines virtuelles de même taille dépendent des caractéristiques sous-jacentes de chaque environnement Azure Stack.

## <a name="virtual-machine-extensions"></a>Extensions de machine virtuelle

 Azure Stack inclut un petit ensemble d’extensions. Des mises à jour et des extensions supplémentaires sont disponibles par le biais de la syndication de Place de marché.

Utilisez le script PowerShell suivant pour obtenir la liste des extensions de machine virtuelle qui sont disponibles dans votre environnement Azure Stack :

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versions d’API

Les fonctionnalités de machine virtuelle dans Azure Stack prennent en charge les versions d’API suivantes :

![Types de ressources de machine virtuelle](media/azure-stack-vm-considerations/vm-resoource-types.png)

Utilisez le script PowerShell suivant pour obtenir la liste des versions d’API des fonctionnalités de machine virtuelle qui sont disponibles dans votre environnement Azure Stack :

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

La liste des types de ressources et des versions d’API pris en charge peut varier si l’opérateur du cloud met à jour votre environnement Azure Stack avec une version plus récente.

## <a name="windows-activation"></a>Activation de Windows

Les produits Windows doivent être utilisés conformément aux droits d’utilisation des produits et aux termes des contrats de licence Microsoft. Azure Stack active les machines virtuelles Windows Server à l’aide de la fonctionnalité [Activation automatique des machines virtuelles](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA).

- L’hôte Azure Stack active Windows avec les clés AVMA pour Windows Server 2016. Toutes les machines virtuelles exécutant Windows Server 2012 ou une version ultérieure sont automatiquement activées.
- Celles qui exécutent Windows Server 2008 R2 doivent être activées manuellement à l’aide de [l’activation MAK](https://technet.microsoft.com/library/ff793438.aspx).

Microsoft Azure utilise l’activation KMS pour activer les machines virtuelles Windows. Si vous déplacez une machine virtuelle d’Azure Stack vers Azure et rencontrez des problèmes d’activation, consultez [Résoudre les problèmes d’activation de Windows sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Vous trouverez des informations supplémentaires dans le billet de blog [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) publié par l’équipe du support technique Azure.

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle Windows à l’aide de PowerShell dans Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)