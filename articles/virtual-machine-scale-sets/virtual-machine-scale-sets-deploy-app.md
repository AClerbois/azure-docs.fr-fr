---
title: Déployez une application sur un groupe de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment déployer des applications sur des instances de machine virtuelle Linux et Windows d’un groupe identique
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 8817facc21d2a7ac86bdaf198aab3179a93c4914
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718979"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Déployer votre application sur des groupes de machines virtuelles identiques
Pour exécuter des applications sur des instances de machine virtuelle d’un groupe identique, vous devez d’abord installer les composants d’application et les fichiers requis. Cet article présente des méthodes pour créer une image de machine virtuelle personnalisée d’un groupe identique, ou pour exécuter automatiquement des scripts d’installation sur des instances de machine virtuelle existantes. Vous apprendrez également à gérer des applications ou des mises à jour du système d’exploitation sur un groupe identique.


## <a name="build-a-custom-vm-image"></a>Créer une image de machine virtuelle personnalisée
Lorsque vous utilisez une des images de plateforme Azure pour créer les instances de votre groupe identique, aucun logiciel supplémentaire n’est installé ou configuré. Vous pouvez automatiser l’installation de ces composants, mais cela augmente le temps nécessaire pour configurer des instances de machine virtuelle sur vos groupes identiques. Si vous appliquez plusieurs modifications de configuration aux instances de machine virtuelle, vous surchargez la gestion de ces scripts de configuration et tâches.

Pour simplifier la gestion de la configuration et accélérer la configuration d’une machine virtuelle, vous pouvez créer une image de machine virtuelle personnalisée prête à exécuter votre application dès qu’une instance est configurée dans le groupe identique. Pour plus d’informations sur la façon de créer et d’utiliser une image de machine virtuelle personnalisée avec un groupe identique, voir les didacticiels suivants :

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Installer une application avec l’extension de script personnalisé
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. Pour plus d’informations sur la façon de créer et d’utiliser une image de machine virtuelle personnalisée avec un groupe identique, voir les didacticiels suivants :

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modèle Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installer une application sur une machine virtuelle Windows avec PowerShell DSC
La [Configuration d’état souhaité (DSC) PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) est une plateforme de gestion qui définit la configuration des machines cibles. Les configurations d’état souhaité définissent les éléments à installer sur une machine et la procédure à suivre pour configurer l’hôte. Un moteur du Gestionnaire de configuration local (LCM) s’exécute sur chaque nœud cible qui traite les actions demandées en fonction des configurations envoyées.

L’extension PowerShell DSC vous permet de personnaliser les instances de machine virtuelle d’un groupe identique avec PowerShell. L’exemple suivant permet :

- Donne pour instruction aux instances de machine virtuelle de télécharger un package DSC à partir de GitHub – *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Définit l’extension pour exécuter un script d’installation - `configure-http.ps1`
- Obtient des informations sur un groupe identique avec [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Applique l’extension aux instances de machine virtuelle avec [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

L’extension DSC est appliquée aux instances de machine virtuelle *myScaleSet* dans le groupe de ressources nommé *myResourceGroup*. Saisissez vos propres noms, comme suit :

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Si la stratégie de mise à niveau de votre groupe identique est *manuelle*, mettez à jour vos instances de machine virtuelle avec [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Cette applet de commande applique la configuration du groupe identique mis à jour aux instances de machine virtuelle, puis installe votre application.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installer une application sur une machine virtuelle Linux avec cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) est une méthode largement utilisée pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. Comme cloud-init s’exécute pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration.

Cloud-init fonctionne aussi sur les différentes distributions. Par exemple, vous n’utilisez pas **apt-get install** ou **yum install** pour installer un package. Au lieu de cela, vous pouvez définir une liste des packages à installer, après quoi cloud-init se charge d’utiliser automatiquement l’outil de gestion de package natif correspondant à la distribution que vous sélectionnez.

Pour plus d’informations, y compris un exemple de fichier *cloud-init.txt*, consultez [Utiliser cloud-init pour personnaliser des machines virtuelles Azure](../virtual-machines/linux/using-cloud-init.md).

Pour créer un groupe identique et utiliser un fichier cloud-init, ajoutez le paramètre `--custom-data` à la commande [az vmss create](/cli/azure/vmss#az_vmss_create) et spécifiez le nom d’un fichier cloud-init. L’exemple suivant crée un groupe identique nommé *myScaleSet* dans *myResourceGroup* et configure des instances de machine virtuelle avec un fichier nommé *cloud-init.txt*. Entrez vos propres noms, comme suit :

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Installer des applications avec des mises jour du système d’exploitation
Lorsque de nouvelles versions du système d’exploitation sont disponibles, vous pouvez utiliser ou créer une nouvelle image personnalisée et [déployer des mises à niveau du système d’exploitation](virtual-machine-scale-sets-upgrade-scale-set.md) sur un groupe identique. Chaque instance de machine virtuelle est mise à niveau avec la dernière image que vous spécifiez. Vous pouvez utiliser une image personnalisée avec l’application préinstallée, l’extension de script personnalisé ou DSC PowerShell, pour que votre application soit automatiquement disponible lorsque vous effectuez la mise à niveau. Vous devrez peut-être planifier la maintenance de l’application lorsque vous effectuez cette procédure pour éviter tout problème de compatibilité de version.

Si vous utilisez une image de machine virtuelle personnalisée avec l’application préinstallée, vous pouvez intégrer les mises à jour de l’application dans un pipeline de déploiement pour créer les nouvelles images et déployer des mises à niveau du système d’exploitation sur l’ensemble du groupe identique. Avec cette approche, le pipeline peut récupérer les dernières versions de l’application, créer et valider une image de machine virtuelle, puis mettre à niveau les instances de machine virtuelle dans le groupe identique. Pour exécuter un pipeline de déploiement qui crée et déploie des mises à jour de l’application sur des images de machine virtuelle personnalisées, vous pouvez [créer et déployer une image Packer avec Visual Studio Team Services](/vsts/pipelines/apps/cd/azure/deploy-azure-scaleset) ou utiliser une autre plateforme, comme [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Étapes suivantes
Lorsque vous générez et déployez des applications sur vos groupes identiques, vous pouvez consulter [Vue d’ensemble de la conception de groupes identiques](virtual-machine-scale-sets-design-overview.md). Pour plus d’informations sur la façon de gérer votre groupe identique, consultez [Utiliser PowerShell pour gérer votre groupe identique](virtual-machine-scale-sets-windows-manage.md).
