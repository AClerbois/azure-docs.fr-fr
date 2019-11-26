---
title: Utiliser des groupes de placements de proximité pour des machines virtuelles Linux
description: En savoir plus sur la création et l’utilisation de groupes de placements de proximité pour les machines virtuelles Linux dans Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171055"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Déployer des machines virtuelles dans des groupes de placements avec Azure CLI

Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un [groupe de placements de proximité](co-location.md#proximity-placement-groups).

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.


## <a name="create-the-proximity-placement-group"></a>Créer le groupe de placements de proximité
Créez un groupe de placements de proximité à l’aide de [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Liste de groupes de placements de proximité

Vous pouvez répertorier tous vos groupes de placements de proximité à l’aide de la commande [az ppg list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle dans le groupe placements de proximité avec la commande [new az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Vous pouvez voir la machine virtuelle dans le groupe de placements de proximité avec [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Groupes à haute disponibilité
Vous pouvez également créer un groupe à haute disponibilité dans votre groupe de placements de proximité. Utilisez le même paramètre `--ppg` avec la cmdlet [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) pour créer un groupe à haute disponibilité et toutes les machines virtuelles dans le groupe à haute disponibilité seront également créées dans le même groupe de placements de proximité.

## <a name="scale-sets"></a>Groupes identiques

Vous pouvez également créer un groupe identique dans votre groupe de placements de proximité. Utilisez le même paramètre `--ppg` avec [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) pour créer un jeu de mise à l’échelle et toutes les instances seront créées dans le même groupe de placements de proximité.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les commandes [Azure CLI](/cli/azure/ppg) pour les groupes de placements de proximité.
