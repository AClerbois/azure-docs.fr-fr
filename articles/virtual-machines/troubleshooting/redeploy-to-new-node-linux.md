---
title: Redéploiement des machines virtuelles Linux dans Azure | Microsoft Docs
description: Redéploiement des machines virtuelles Linux dans Azure pour atténuer les problèmes de connexion SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756188"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Redéployer une machine virtuelle vers un nouveau nœud Azure
Si vous êtes confronté à des problèmes d’accès SSH ou d’accès des applications à une machine virtuelle Linux dans Azure, vous pouvez tenter de redéployer la machine virtuelle. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise en service. Toutes les options de configuration et les ressources associées sont conservées. Cet article vous montre comment redéployer une machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du portail Azure.

> [!NOTE]
> Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour. 


## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure
Installez la dernière version [d’Azure CLI](/cli/azure/install-az-cli2) et connectez-vous à votre compte Azure avec la commande [az login](/cli/azure/reference-index).

Redéployez votre machine virtuelle avec la commande [az vm redeploy](/cli/azure/vm). L’exemple suivant permet de redéployer la machine virtuelle *myVM* dans le groupe de ressources *myResourceGroup* :

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Utiliser Azure Classic CLI
Installez la [dernière version d’Azure Classic CLI](../../cli-install-nodejs.md) et connectez-vous à votre compte Azure. Vérifiez que vous êtes en mode Resource Manager (`azure config mode arm`).

L’exemple suivant permet de redéployer la machine virtuelle *myVM* dans le groupe de ressources *myResourceGroup* :

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez rechercher une aide spécifique sur le [dépannage des connexions SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou sur les [étapes de dépannage détaillées des connexions SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous rencontrez des problèmes de connexion à votre machine virtuelle. Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.


