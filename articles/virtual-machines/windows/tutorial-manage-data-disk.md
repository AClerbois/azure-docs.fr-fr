---
title: 'Didacticiel : gérer les disques Azure avec Azure PowerShell | Microsoft Docs'
description: Dans ce didacticiel, vous allez apprendre à utiliser Azure PowerShell afin de créer et gérer des disques Azure pour des machines virtuelles
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: 1db4dd2f1aa550ea4ccac55882f147c0585cdd0c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466725"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Didacticiel : gérer les disques Azure avec Azure PowerShell

Les machines virtuelles utilisent des disques pour stocker leur système d’exploitation, leurs applications et leurs données. Lorsque vous créez une machine virtuelle, il est important de choisir une taille de disque et une configuration appropriées à la charge de travail prévue. Ce didacticiel décrit le déploiement et la gestion des disques de machine virtuelle. Vous en apprendrez davantage sur les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="default-azure-disks"></a>Disques Azure par défaut

Lorsqu’une machine virtuelle Azure est créée, deux disques sont automatiquement attachés à celle-ci. 

**Disque de système d’exploitation** : la taille des disques de système d’exploitation peut atteindre 4 To ; ces disques hébergent le système d’exploitation des machines virtuelles.  Le disque de système d’exploitation se voit attribuer la lettre de lecteur *C:* par défaut. La configuration de la mise en cache de disque de système d’exploitation est optimisée pour les performances du système d’exploitation. Le disque de système d’exploitation **ne doit pas** héberger d’applications ou de données. Pour héberger ce type de contenu, utilisez plutôt un disque de données, qui est décrit plus loin dans cet article.

**Disque temporaire** : les disques temporaires utilisent un disque SSD qui se trouve sur le même hôte Azure que la machine virtuelle. Les disques temporaires sont extrêmement performants et peuvent être utilisés pour des opérations telles que le traitement de données temporaires. Toutefois, si la machine virtuelle est déplacée vers un nouvel hôte, toutes les données stockées sur un disque temporaire sont supprimées. La taille du disque temporaire est déterminée par la [taille de la machine virtuelle](sizes.md). Les disques temporaires se voient attribuer la lettre de lecteur *D:* par défaut.



## <a name="azure-data-disks"></a>Disques de données Azure

Des disques de données supplémentaires peuvent être ajoutés pour installer des applications et stocker des données. Les disques de données doivent être utilisés dans les cas où un stockage des données durable et réactif est nécessaire. Chaque disque de données possède une capacité maximale de 4 To. La taille de la machine virtuelle détermine le nombre de disques de données pouvant être attachés à cette machine virtuelle. Pour chaque processeur virtuel de la machine virtuelle, quatre disques de données peuvent être attachés. 


## <a name="vm-disk-types"></a>Type de disque de machine virtuelle

Azure propose deux types de disque.

**Disques Standard** : ils s’appuient sur des disques durs et offrent un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.

**Disques Premium** : ils reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS et FS. Les disques Premium sont de cinq types (P10, P20, P30, P40, P50). La taille du disque détermine le type de disque. Lorsque vous sélectionnez une taille de disque, la valeur est arrondie au type suivant. Par exemple, si la taille est inférieure à 128 Go, le disque est de type P10. Si elle est comprise entre 129 Go et 512 Go, le disque est de type P20.

### <a name="premium-disk-performance"></a>Performances du disque Premium

|Type de disque de stockage Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 | p60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Taille du disque (arrondie) | 32 Gio | 64 Gio | 128 Go | 512 Go | 1 024 Gio (1 Tio) | 2 048 Gio (2 Tio) | 4 095 Gio (4 Tio) | 8 192 Gio (8 Tio)
| Nb max. d'E/S par seconde par disque | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 | 12 500 |
Débit par disque | 25 Mo/s | 50 Mo/s | 100 Mo/s | 150 Mo/s | 200 Mo/s | 250 Mo/s | 250 Mo/s | 480 Mo/s |

Bien que le tableau ci-dessus identifie le nombre max. d’E/S par seconde par disque, un niveau de performances plus élevé est possible en entrelaçant plusieurs disques de données. Par exemple, 64 disques de données peuvent être attachés à la machine virtuelle Standard_GS5. Si chacun de ces disques est de type P30, vous pouvez atteindre un nombre maximum d’E/S par seconde de 80 000. Pour plus d’informations sur le nombre maximal d’E/S par seconde par machine virtuelle, consultez [Types et tailles des machines virtuelles](./sizes.md).

## <a name="create-and-attach-disks"></a>Créer et attacher des disques

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, créez une machine virtuelle à l’aide des commandes ci-dessous.

Définissez le nom d’utilisateur et le mot de passe pour le compte d’administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :


Créez la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Vous êtes invité à entrer un nom d’utilisateur et un mot de passe pour le compte administrateur de la machine virtuelle.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Créez la configuration initiale avec [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). L’exemple suivant configure un disque d’une taille de 128 Go.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Créez le disque de données avec la commande [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenez la machine virtuelle que vous souhaitez ajouter au disque de données avec la commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Ajoutez le disque de données à la configuration de la machine virtuelle avec la commande [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Mettez à jour la machine virtuelle avec la commande [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Préparer les disques de données

Une fois qu’un disque a été attaché à la machine virtuelle, le système d’exploitation doit être configuré pour utiliser le disque. L’exemple suivant montre comment configurer manuellement le premier disque ajouté à la machine virtuelle. Ce processus peut également être automatisé à l’aide de [l’extension de script personnalisé](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuration manuelle

Créez une connexion RDP avec la machine virtuelle. Ouvrez PowerShell et exécutez ce script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Vérifier le disque de données

Pour vérifier que le disque de données est attaché, affichez `StorageProfile` pour le `DataDisks` attaché.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Le résultat suivant doit ressembler à ce qui suit :

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances concernant les disques de machine virtuelle, notamment concernant les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données

Passez au didacticiel suivant pour en apprendre davantage sur l’automatisation de la configuration de machine virtuelle.

> [!div class="nextstepaction"]
> [How to customize a Linux virtual machine on first boot](./tutorial-automate-vm-deployment.md) (Comment personnaliser une machine virtuelle Linux au premier démarrage)
