---
title: Fichier Include
description: Fichier Include
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550215"
---
### <a name="general-requirements"></a>Conditions générales

* Le réseau virtuel doit se trouver dans la même région et le même abonnement que le compte Batch utilisé pour créer le pool.

* Le pool utilisant le réseau virtuel peut avoir un maximum de 4096 nœuds.

* Le sous-réseau spécifié pour le pool doit avoir suffisamment d’adresses IP non attribuées pour contenir le nombre de machines virtuelles ciblées pour le pool, autrement dit, la somme des propriétés `targetDedicatedNodes` et `targetLowPriorityNodes` du pool. Si le sous-réseau ne dispose pas de suffisamment d’adresses IP non attribuées, le pool alloue partiellement les nœuds de calcul, et une erreur de redimensionnement se produit. 

* Votre point de terminaison de Stockage Azure doit être résolu par tous les serveurs DNS personnalisés qui traitent votre réseau virtuel. Plus précisément, les URL sous la forme `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` et `<account>.blob.core.windows.net` doivent être résolvables. 

Les exigences de réseau virtuel supplémentaires diffèrent si le pool Batch est dans la configuration de machine virtuelle ou dans la configuration des services cloud. Pour les nouveaux déploiements de pool dans un réseau virtuel, la configuration de machine virtuelle est recommandée.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools dans la configuration de machine virtuelle

**Prise en charge des réseaux virtuels** : uniquement les réseaux virtuels basés sur Azure Resource Manager

**ID de sous-réseau** : lorsque vous spécifiez le sous-réseau à l’aide de l’API Batch, utilisez l’*identificateur de ressource* du sous-réseau. L’identificateur du sous-réseau est au format :

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Autorisations** : vérifiez si vos stratégies ou verrous de sécurité l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations d’un utilisateur pour gérer le réseau virtuel.

**Ressources de mise en réseau supplémentaires** : Batch alloue automatiquement des ressources de mise en réseau supplémentaires dans le groupe de ressources contenant le réseau virtuel. Pour chaque 50 nœuds dédiés (ou chaque 20 nœuds de faible priorité), Batch alloue : équilibreur de charge 1 réseau groupe de sécurité (NSG), 1 adresse IP publique et 1. Ces ressources sont limitées par les [quotas de ressources](../articles/azure-subscription-service-limits.md) de l’abonnement. Pour les grands pools, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs de ces ressources.

#### <a name="network-security-groups"></a>Groupes de sécurité réseau

Le sous-réseau doit autoriser les communications entrantes issues du service Batch pour pouvoir planifier des tâches sur les nœuds de calcul, et également les communications sortantes pour communiquer avec le stockage Azure ou d’autres ressources. Pour les pools dans la configuration de machine virtuelle, Batch ajoute des groupes de sécurité réseau au niveau des interfaces réseau (NIC) attachées aux machines virtuelles. Ces groupes de sécurité réseau configurent automatiquement des règles de trafic entrant et sortant pour autoriser le trafic suivant :

* Le trafic TCP entrant sur les ports 29876 et 29877 à partir d’adresses IP du rôle de service Batch. 
* Le trafic TCP entrant sur le port 22 (nœuds Linux) ou le port 3389 (nœuds Windows) pour autoriser l’accès à distance. Pour certains types de tâches multi-instances sur Linux (par exemple, MPI), vous devez également autoriser le trafic du port 22 SSH pour les adresses IP dans le sous-réseau contenant les nœuds de calcul Batch.
* Le trafic sortant sur n’importe quel port vers le réseau virtuel.
* Le trafic sortant sur n’importe quel port vers internet.

> [!IMPORTANT]
> Soyez prudent si vous modifiez ou ajoutez des règles de trafic entrant ou sortant dans des groupes de sécurité réseau configurés par Batch. Si la communication vers les nœuds de calcul dans le sous-réseau spécifié est refusée par un groupe de sécurité réseau, alors le service Batch définit l’état des nœuds de calcul sur **inutilisable**.

Vous n’avez pas besoin spécifier de groupes de sécurité réseau au niveau du sous-réseau, car Batch configure ses propres groupes de sécurité réseau. Cependant, si le sous-réseau spécifié comporte des groupes de sécurité réseau (NSG) associés et/ou un pare-feu, configurez les règles de sécurité du trafic entrant et sortant comme indiqué dans les tableaux suivants. Configurer le trafic entrant sur le port 3389 (Windows) ou 22 (Linux) uniquement si vous avez besoin autoriser l’accès à distance aux machines virtuelles pool provenant de sources externes. Il n’est pas exigé que les machines virtuelles du pool soient utilisables. Notez que vous devrez activer le trafic de sous-réseau de réseau virtuel sur le port 22 pour Linux, si vous utilisez certains types de tâches multi-instances telles que MPI.

**Règles de sécurité entrantes**

| Adresses IP sources | Balise du service source | Ports source | Destination | Ports de destination | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement` [Balise de service](../articles/virtual-network/security-overview.md#service-tags) | * | Quelconque | 29876-29877 | TCP | AUTORISER |
| Utilisateur adresses IP sources pour accéder à distance à des nœuds de calcul et/ou de sous-réseau de nœud de calcul pour les tâches multi-instances de Linux, si nécessaire. | N/A | * | Quelconque | 3389 (Windows), 22 (Linux) | TCP | AUTORISER |

**Règles de sécurité de trafic entrant**

| Source | Ports source | Destination | Identification de destination | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Quelconque | 443 | [Balise du service](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (dans la même région que votre compte Batch et le réseau virtuel)  | Quelconque | AUTORISER |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools dans la configuration des services cloud

**Réseaux virtuels pris en charge** : réseaux virtuels classiques uniquement

**ID de sous-réseau** : lorsque vous spécifiez le sous-réseau à l’aide de l’API Batch, utilisez l’*identificateur de ressource* du sous-réseau. L’identificateur du sous-réseau est au format :

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Autorisations** : le `MicrosoftAzureBatch`principal du service doit jouer le rôle de contrôle d’accès en fonction du rôle (RBAC) `Classic Virtual Machine Contributor` pour le réseau virtuel spécifié.

#### <a name="network-security-groups"></a>Groupes de sécurité réseau

Le sous-réseau doit autoriser les communications entrantes issues du service Batch pour pouvoir planifier des tâches sur les nœuds de calcul, et également les communications sortantes pour communiquer avec le stockage Azure ou d’autres ressources.

Il est inutile de spécifier un groupe de sécurité réseau, étant donné que Batch configure les communications entrantes uniquement à partir d’adresses IP Batch pour les nœuds du pool. Cependant, si le sous-réseau spécifié comporte des groupes de sécurité réseau associés et/ou un pare-feu, configurez les règles de sécurité du trafic entrant et sortant comme indiqué dans les tableaux suivants. Si la communication vers les nœuds de calcul dans le sous-réseau spécifié est refusée par un groupe de sécurité réseau, alors le service Batch définit l’état des nœuds de calcul sur **inutilisable**.

Configurer le trafic entrant sur le port 3389 pour Windows si vous souhaitez autoriser l’accès RDP pour les nœuds du pool. Il n’est pas exigé que les nœuds du pool soient utilisables.

**Règles de sécurité entrantes**

| Adresses IP sources | Ports source | Destination | Ports de destination | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Quelconque <br /><br />Bien que cela nécessite réellement de « tout autoriser », le service Batch applique une règle ACL au niveau de chaque nœud, excluant toutes les adresses IP des services autres que Batch. | * | Quelconque | 10100, 20100, 30100 | TCP | AUTORISER |
| Facultatif, pour autoriser l’accès RDP sur les nœuds. | * | Quelconque | 3389 | TCP | AUTORISER |

**Règles de sécurité de trafic entrant**

| Source | Ports source | Destination | Ports de destination | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Quelconque | * | Quelconque | 443  | Quelconque | AUTORISER |
