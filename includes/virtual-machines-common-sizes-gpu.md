---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 402379a99be467e9db7fb692d7d532e9d39a54a6
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700787"
---
Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Ces tailles sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Cet article donne des informations sur le nombre et le type de GPU, de processeurs virtuels, de disques de données et de cartes réseau. Le débit de stockage et la bande passante réseau sont également inclus pour chacune des tailles de ce regroupement.

* Les tailles **NC, NCv2 et NCv3** sont optimisées pour les algorithmes et les applications nécessitant beaucoup de ressources réseau et de calculs. En voici quelques exemples : les applications et les simulations CUDA et OpenCL, l’intelligence artificielle et l’apprentissage profond. Équipée du GPU Tesla V100 de NVIDIA, la série NCv3 est axée sur les charges de travail informatiques à hautes performances. La série NC utilise le processeur Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) et les machines virtuelles de la série NCv2 et NCv3 sont dotées du processeur Intel Xeon E5-2690 v4 (Broadwell).

* **ND et NDv2**La série ND est destinée à l’exécution de scénarios d’apprentissage et d’inférence pour le Deep Learning. Elle utilise le GPU NVIDIA Tesla P40 et le processeur Intel Xeon E5-2690 v4 (Broadwell). La série NDv2 utilise le processeur Intel Xeon Platinum 8168 (Skylake).

* Les tailles **NV et NVv3** sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, l’encodage et les scénarios de VDI utilisant des infrastructures comme OpenGL ou DirectX.  Ces machines virtuelles reposent sur le GPU Tesla M60 de NVIDIA.

## <a name="nc-series"></a>Série NC

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les machines virtuelles de la série NC sont pilotées par la carte [Tesla K80 de NVIDIA](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) et le processeur Intel Xeon E5-2690 v3 (Haswell). Les utilisateurs peuvent exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration énergétique, les simulations de crash, le rendu de lancer de rayon, l’apprentissage profond et plus encore. La configuration NC24r fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1 440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1 440 | 4 | 48 | 64 | 4 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA

## <a name="ncv2-series"></a>Série NCv2

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de série NCv2 sont optimisées par les GPU [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/). Ces GPU peuvent fournir des performances de calcul deux fois supérieures à celles de la série NC. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. Outre les GPU, les machines virtuelles de la série NCv2 sont également dotées de processeurs Intel Xeon E5-2690 v4 (Broadwell).

La configuration NC24rs v2 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) : Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = une carte P100.

*Prenant en charge RDMA

## <a name="ncv3-series"></a>Série NCv3

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de série NCv3 sont optimisées par les GPU [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/). Ces GPU peuvent fournir des performances de calcul une fois et demie supérieures à celles de la série NCv2. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. La configuration NC24rs v3 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées. Outre les GPU, les machines virtuelles de la série NCv3 sont également pilotées par des processeurs Intel Xeon E5-2690 v4 (Broadwell).

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) : Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20 000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = une carte V100.

*Prenant en charge RDMA

## <a name="ndv2-series-preview"></a>NDv2-series (préversion)

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

InfiniBand : Non pris en charge

Les machines virtuelles de la série NDv2 rejoignent la famille de processeurs graphiques (GPU) pour répondre aux besoins des charges de travail d’intelligence artificielle, HPC et Machine Learning. Elles sont dotées de 8 GPU interconnectés Tesla V100 NVLINK de NVIDIA, de 40 cœurs Intel Xeon Platinum 8168 (Skylake) et de 672 Gio de mémoire système. L’instance NDv2 offre d’excellentes performances FP32 et FP64 pour les charges de travail HPC et d’intelligence artificielle utilisant les frameworks Cuda, TensorFlow, Pytorch, Caffe et autres.

[Inscrivez-vous et accédez à ces machines lors de la préversion](https://aka.ms/ndv2signup).
<br>

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) : Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Bande passante réseau maximale | Nombre max de cartes réseau |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40s_v2 | 40 | 672 | 2948 | 8 V100 (NVLink) | 16 | 32 | 80000/800 | 24 000 Mbits/s | 8 |

## <a name="nd-series"></a>Série ND

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de la série ND sont nouvelles dans la famille de GPU et sont conçues pour les charges de travail d’intelligence artificielle et d’apprentissage profond. Elles offrent d’excellentes performances pour l’apprentissage et l’inférence. Les instances ND sont pilotées par des GPU [Tesla P40 de NVIDIA](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) et des processeurs Intel Xeon E5-2690 v4 (Broadwell). Ces instances offrent d’excellentes performances pour les opérations à virgule flottante simple précision, et pour les charges de travail d’intelligence artificielle utilisant Microsoft Cognitive Toolkit, TensorFlow, Caffe et d’autres infrastructures. La série ND offre également une taille de mémoire GPU beaucoup plus importante (24 Go), ce qui permet d’adapter des modèles de réseaux neuronaux beaucoup plus volumineux. À l’instar de la série NC, la série ND offre une configuration avec un réseau à faible latence secondaire et à haut débit grâce à l’accès direct à la mémoire à distance (RDMA), ainsi que la connectivité InfiniBand, de sorte que vous pouvez exécuter des travaux de formation à grande échelle s’étendant sur de nombreux GPU.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) par région dans votre abonnement est défini au départ sur 0. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20 000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = une carte P40.

*Prenant en charge RDMA

## <a name="nv-series"></a>Série NV

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les machines virtuelles de la série NV sont optimisées par des GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. Les machines virtuelles de la série NV sont également dotées de processeurs Intel Xeon E5-2690 v3 (Haswell).

Chaque GPU dans les instances NV est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1 440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = une moitié de carte M60.

## <a name="nvv3-series--sup1sup"></a>Série NVv3  <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de la série NVv3 sont pilotées par des GPU [Tesla M60 de NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID avec des processeurs Intel E5-2690 v4 (Broadwell). Ces machines virtuelles sont destinées aux bureaux virtuels et aux applications graphiques avec accélération par GPU sur lesquels les clients souhaitent visualiser leurs données, simuler des résultats à afficher, travailler en CAO ou effectuer la restitution ou la diffusion du contenu. Par ailleurs, ces machines virtuelles peuvent exécuter des charges de travail de précision unique, telles que le codage et le rendu. Les machines virtuelles NVv3 prennent en charge Stockage Premium et offrent deux fois la mémoire RAM de la série NV.  

Chaque GPU dans les instances NVv3 est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20 000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1 280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = une moitié de carte M60.

<sup>1</sup> Machines virtuelles de série NVv3 dotées de la technologie Hyper-Threading d’Intel.
