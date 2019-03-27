---
title: Exemple de script avec l’interface de ligne de commande Azure CLI - Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement | Microsoft Docs
description: Exemple de script avec l’interface de ligne de commande Azure CLI - Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 3f68c4ccbcaad682303c9d67c95f8bf8a2d3041b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249611"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement avec l’interface de ligne de commande

Ce script crée un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement. Utilisez ce script pour importer un VHD spécialisé (non généralisé/préparé avec Sysprep) vers un disque de système d’exploitation managé pour créer une machine virtuelle. Ou bien, utilisez-le pour importer un VHD de données vers un disque de données managées.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque managé à partir d’un VHD. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Crée un disque managé en utilisant l’URI d’un VHD dans un compte de stockage dans le même abonnement |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
