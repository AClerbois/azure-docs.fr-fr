---
title: "Créer une capture instantanée d’un disque dur virtuel dans Azure | Microsoft Docs"
description: "Découvrez comment créer une copie d’une machine virtuelle Azure pour l’utiliser comme sauvegarde ou pour la résolution de problèmes."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Créer un instantané

Prenez une capture instantanée d’un disque dur virtuel de système d’exploitation ou de données pour la sauvegarde ou pour résoudre les problèmes de la machine virtuelle. Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utiliser le portail Azure pour prendre une capture instantanée 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Nouveau** et recherchez **Capture instantanée**.
3. Dans le panneau Capture instantanée, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. 
6. Sélectionnez un emplacement de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque géré dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Nous vous recommandons d’utiliser le type **Standard_LRS**, sauf si vous avez besoin de la stocker sur un disque hautes performances.
9. Cliquez sur **Create**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utiliser PowerShell pour prendre une capture instantanée
Les étapes suivantes vous expliquent comment obtenir la copie du disque dur virtuel, créer les configurations de capture instantanée et prendre une capture instantanée du disque avec l’applet de commande [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Vérifiez que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).


1. Définissez certains paramètres. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Obtenez le disque dur virtuel à copier.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Créez les configurations de capture instantanée. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Prenez la capture instantanée.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Si vous envisagez d’utiliser la capture instantanée pour créer un disque géré et l’attacher à une machine virtuelle qui doit être hautement performante, utilisez le paramètre `-AccountType Premium_LRS` avec la commande New-AzureRmSnapshot. Le paramètre crée la capture instantanée et la stocke en tant que disque géré Premium. Les disques gérés Premium sont plus chers que les disques gérés Standard. Vérifiez qu’il vous faut vraiment le niveau Premium avant d’utiliser ce paramètre.

## <a name="next-steps"></a>Étapes suivantes

Créez une machine virtuelle à partir d’une capture instantanée en créant un disque managé à partir d’une capture instantanée, puis en attachant le nouveau disque managé comme disque de système d’exploitation. Pour plus d’informations, consultez l’exemple [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
