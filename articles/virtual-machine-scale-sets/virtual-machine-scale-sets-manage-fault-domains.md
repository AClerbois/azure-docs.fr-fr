---
title: Gérer les domaines d'erreur dans les groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment choisir le bon nombre de domaines d’erreur pendant la création d’un groupe de machines virtuelles identiques.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: d6207a1a77ff00ff36fe6b3b748c3f8f7279b62d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639962"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Choisir le bon nombre de domaines d’erreur pour un groupe de machines virtuelles identiques
Des groupes de machines virtuelles identiques sont créés avec cinq domaines d’erreur par défaut dans les régions Azure sans zones. Pour les régions qui prennent en charge le déploiement zonal de groupes de machines virtuelles identiques et si cette option est sélectionnée, la valeur par défaut du nombre de domaines d’erreur est de 1 pour chacune des zones. FD = 1 implique dans ce cas que les instances de machine virtuelle appartenant au groupe identique sont réparties entre plusieurs racks dans la mesure du possible.

Vous pouvez également envisager d’aligner le nombre de domaines d’erreur du groupe identique avec le nombre de domaines d’erreur de la fonctionnalité Disques managés. Cet alignement peut aider à éviter la perte de quorum si tout un domaine d’erreur de la fonctionnalité Disques managés tombe en panne. Le nombre de domaines d'erreur peut être défini sur une valeur inférieure ou égale au nombre de domaines d'erreur de la fonctionnalité Disques managés disponibles dans chacune des régions. Reportez-vous à ce [document](../virtual-machines/windows/manage-availability.md) pour en savoir plus sur le nombre de domaines d’erreur de la fonctionnalité Disques managés par région.

## <a name="rest-api"></a>API REST
Vous pouvez définir la propriété `properties.platformFaultDomainCount` sur 1, 2 ou 3 (la valeur par défaut est 5 si aucune valeur n’est spécifiée). Reportez-vous à la documentation de l’API REST [ici](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>D’Azure CLI
Vous pouvez définir le paramètre `--platform-fault-domain-count` sur 1, 2 ou 3 (la valeur par défaut est 5 si aucune valeur n’est spécifiée). Reportez-vous à la documentation d’Azure CLI [ici](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [fonctionnalités de disponibilité et de redondance](../virtual-machines/windows/availability.md) pour les environnements Azure.
