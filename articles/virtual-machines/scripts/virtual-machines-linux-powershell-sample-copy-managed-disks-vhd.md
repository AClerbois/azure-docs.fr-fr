---
title: 'Exemple de script Azure PowerShell : exporter/copier le disque dur virtuel d’un disque managé vers un compte de stockage dans une autre région | Microsoft Docs'
description: 'Exemple de script Azure PowerShell : exporter/copier le disque dur virtuel d’un disque managé vers un compte de stockage dans la même région ou dans une autre région | Microsoft Docs'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: dc1549007feed11919e25ac9dd60e88ef7f401a5
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249619"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Exporter/copier le disque dur virtuel d’un disque managé vers un compte de stockage dans une région différente avec PowerShell

Ce script permet d'exporter le disque dur virtuel d’un disque managé vers un compte de stockage situé dans une autre région. Il génère d’abord l’URI SAS du disque managé, puis l’utilise pour copier le disque dur virtuel sous-jacent vers un compte de stockage situé dans une autre région. Utilisez ce script pour copier des disques managés vers une autre région en vue d’une extension régionale.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes afin de générer l’URI SAS d’un disque managé et copie le disque dur virtuel sous-jacent vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à sa documentation spécifique.

| Commande | Notes |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Génère l’URI SAS d’un disque managé qui est utilisé pour copier le disque dur virtuel sous-jacent vers un compte de stockage. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Crée un contexte de compte de stockage avec le nom de compte et la clé. Ce contexte peut être utilisé pour effectuer des opérations de lecture/écriture sur le compte de stockage. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copie le VHD sous-jacent d’une capture instantanée vers un compte de stockage |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque managé à partir d’un VHD](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).