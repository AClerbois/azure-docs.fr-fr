---
title: 'Guide de démarrage rapide : créer votre premier conteneur Azure Container Instances avec PowerShell'
description: Dans le cadre de ce guide de démarrage rapide, vous allez utiliser Azure PowerShell pour déployer un conteneur Windows dans Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453564"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Guide de démarrage rapide : créer son premier conteneur dans Azure Container Instances

Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur. Dans le cadre de ce démarrage rapide, vous créez un conteneur Windows dans Azure et l’exposez sur Internet avec un nom de domaine complet. Cette opération s’effectue en une seule commande. Après quelques instants, vous pouvez voir l’application s’exécuter dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][qs-powershell-01]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.5 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez créer un conteneur en attribuant un nom, une image Docker, ainsi qu’un groupe de ressources Azure à l’applet de commande [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Si vous le souhaitez, vous pouvez exposer le conteneur sur Internet avec une étiquette de nom DNS.

Exécutez la commande suivante pour lancer un conteneur Nano Server exécutant Internet Information Services (IIS). La valeur `-DnsNameLabel` devant être unique dans la région Azure dans laquelle vous créez l’instance, il vous faudra éventuellement modifier le contenu.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Après quelques secondes, vous devriez recevoir une réponse à votre requête. Le conteneur est initialement défini sur l’état **En cours de création**, mais il doit démarrer après une ou deux minutes. Vous pouvez vérifier l’état du déploiement à l’aide de l’applet de commande [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

L’état d’approvisionnement, le nom de domaine complet et l’adresse IP du conteneur apparaissent dans la sortie de l’applet de commande :

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Une fois que le conteneur **ProvisioningState** passe à l’état `Succeeded`, accédez à son nom `Fqdn` dans votre navigateur :

![IIS déployé à l’aide d’Azure Container Instances affiché dans un navigateur][qs-powershell-01]

## <a name="clean-up-resources"></a>Supprimer les ressources

Quand vous avez fini d’utiliser le conteneur, vous pouvez le supprimer avec l’applet de commande [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image dans le registre du Hub Docker public. Si vous voulez créer une image conteneur par vous-même et la déployer dans Azure Container Instances à partir d’un registre de conteneurs Azure privé, passez au tutoriel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
