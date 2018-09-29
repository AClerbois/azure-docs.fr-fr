---
title: Vue d’ensemble d’agent de machine virtuelle Azure | Microsoft Docs
description: Vue d’ensemble d’agent de machine virtuelle Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: ae2458b6fc650961c63da2f7644dbd54d27fc2a8
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452103"
---
# <a name="azure-virtual-machine-agent-overview"></a>Vue d’ensemble d’agent de machine virtuelle Azure
L’agent de machine virtuelle Microsoft Azure est un processus léger et sécurisé qui gère l’interaction des machines virtuelles avec le contrôleur de structure Azure. L’agent de machine virtuelle a un rôle essentiel dans l’activation et l’exécution des extensions de machine virtuelle. Les extensions de machine virtuelle permettent la configuration post-déploiement de machines virtuelles, par exemple l’installation et la configuration de logiciels. Les extensions de machine virtuelle permettent également d’utiliser des fonctionnalités de récupération, telles que la réinitialisation du mot de passe d’administration d’une machine virtuelle. Sans l’agent de machine virtuelle Azure, vous ne pouvez pas exécuter d’extensions de machine virtuelle.

Cet article détaille l’installation, la détection et la suppression de l’agent de machine virtuelle Azure.

## <a name="install-the-vm-agent"></a>Installer l'agent de machine virtuelle

### <a name="azure-marketplace-image"></a>Image Place de marché Azure

L’agent de machine virtuelle Azure est installé par défaut sur les machines virtuelles Windows qui sont déployées à partir d’une image Place de marché Azure. Lorsque vous déployez une image Place de marché Azure à partir du portail, de PowerShell, de l’interface de ligne de commande ou d’un modèle Azure Resource Manager, vous installez également l’agent de machine virtuelle Azure.

Le Package de l’agent invité de Windows est composé de deux parties :

- L’agent de provisionnement
- L’agent invité de Windows

Pour démarrer une machine virtuelle, vous devez installer l’agent de provisionnement sur la machine virtuelle, contrairement à l’agent invité qu’il n’est pas nécessaire d’installer. Au moment du déploiement de la machine virtuelle, vous pouvez choisir de ne pas installer l’agent invité de Windows. L’exemple suivant montre comment sélectionner l’option *provisionVmAgent* à l’aide d’un modèle Azure Resource Manager :

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Si les agents ne sont pas installés, vous ne pouvez pas utiliser certains services Azure, tels que la Sauvegarde Azure ou Azure Security. Ces services nécessitent l’installation d’une extension. Si vous avez déployé une machine virtuelle sans l’agent invité de Windows, vous pouvez installer la dernière version de cet agent ultérieurement.

### <a name="manual-installation"></a>Installation manuelle
L’agent de machine virtuelle Windows peut être installé manuellement avec un package Windows Installer. L’installation manuelle peut s’avérer nécessaire lorsque vous créez une image de machine virtuelle personnalisée qui est déployée sur Azure. Pour installer manuellement l’agent de machine virtuelle Windows, [téléchargez le programme d’installation de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789).

Vous pouvez installer l’agent de machine virtuelle en double-cliquant sur le fichier Windows Installer. Pour une installation automatisée ou sans assistance de l’agent de machine virtuelle, exécutez la commande suivante :

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Détecter l’agent de machine virtuelle

### <a name="powershell"></a>PowerShell

Avec le module PowerShell pour Azure Resource Manager, vous pouvez récupérer des informations sur les machines virtuelles Azure. Pour afficher des informations concernant une machine virtuelle, par exemple l’état de provisionnement de l’agent de machine virtuelle Azure, utilisez [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) :

```powershell` Get-AzureRmVM
```

The following condensed example output shows the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Vous pouvez utiliser le script suivant pour retourner une liste concise des noms de machines virtuelles et l’état de l’agent de machine virtuelle :

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Détection manuelle
Lorsque vous êtes connecté à une machine virtuelle Windows Azure, utilisez le Gestionnaire des tâches pour examiner les processus en cours d’exécution. Pour rechercher l’agent de machine virtuelle Azure, ouvrez le Gestionnaire des tâches, cliquez sur l’onglet *Détails* et recherchez le nom de processus **WindowsAzureGuestAgent.exe**. La présence de ce processus indique que l’agent de machine virtuelle est installé.


## <a name="upgrade-the-vm-agent"></a>Mettre à niveau l’agent de machine virtuelle
L’agent de machine virtuelle Azure est automatiquement mis à niveau. Lorsque de nouvelles machines virtuelles sont déployées sur Azure, elles reçoivent l’agent de machine virtuelle le plus récent au moment de leur provisionnement. Vous devez manuellement mettre à jour les images de machines virtuelles personnalisées pour inclure le nouvel agent de machine virtuelle au moment de la création de l’image.


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les extensions de machine virtuelle, consultez [Vue d’ensemble des extensions et des fonctionnalités des machines virtuelles Azure](overview.md).