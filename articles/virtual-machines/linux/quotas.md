---
title: Quotas de processeurs virtuels pour Azure | Microsoft Docs
description: Découvrez plus en détail les quotas de processeurs virtuels pour Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716514"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de processeurs virtuels pour les machines virtuelles

Les quotas de processeurs virtuels pour les machines virtuelles et les groupes de machines virtuelles identiques sont organisés en deux niveaux pour chaque abonnement, dans chaque région. Le premier niveau est le total des processeurs virtuels régionaux. Le deuxième niveau se compose des cœurs des différentes familles de tailles de machine virtuelle, comme les processeurs virtuels de la série D. Chaque fois qu’une nouvelle machine virtuelle est déployée, le nombre de processeurs virtuels de cette machine virtuelle ne doit pas dépasser le quota de processeurs virtuels pour la famille de tailles de machine virtuelle ou le quota du total des processeurs virtuels régionaux. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé. Il existe également un quota pour le nombre total de machines virtuelles dans la région. Pour plus d’informations sur chacun de ces quotas, consultez la section **Utilisation + quotas** de la page **Abonnement** dans le [portail Azure](https://portal.azure.com) ou lancez une requête pour obtenir les valeurs à l’aide d’Azure CLI.


## <a name="check-usage"></a>Vérifier l’utilisation

Vous pouvez vérifier l’utilisation de votre quota à l’aide de la commande [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Le résultat suivant doit ressembler à ce qui suit :


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Instances de machines virtuelles réservées
Les instances de machines virtuelles réservées, dont l’étendue se limite à un seul abonnement, ajoutent un nouvel aspect aux quotas de processeurs virtuels. Ces valeurs décrivent le nombre d’instances de la taille indiquée qui doivent être déployables dans l’abonnement. Elles jouent le rôle d’espace réservé dans le système de quota pour garantir que le quota est réservé et que les instances réservées sont déployables dans l’abonnement. Par exemple, si un abonnement spécifique a 10 instances réservées Standard_D1, la limite d’utilisation des instances réservées Standard_D1 est de 10. De cette façon, Azure veille à ce qu’il y ait toujours au moins 10 processeurs virtuels disponibles dans le quota Total des processeurs virtuels régionaux pour les instances Standard_D1 et au moins 10 processeurs virtuels disponibles dans le quota de processeurs virtuels Famille D Standard pour les instances Standard_D1.

Si une augmentation de quota est nécessaire pour acheter une instance réservée à abonnement unique, vous pouvez [demander une augmentation de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sur votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la facturation et les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
