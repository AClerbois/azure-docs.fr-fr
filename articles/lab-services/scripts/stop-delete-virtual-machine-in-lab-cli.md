---
title: Exemple de script Azure CLI - Arrêter et supprimer une machine virtuelle dans un laboratoire | Microsoft Docs
description: Ce script Azure CLI arrête et supprime une machine virtuelle dans un laboratoire.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: c89a5c069186a1612161f660d985ff9144c68e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084291"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utiliser Azure CLI pour arrêter et supprimer une machine virtuelle dans un laboratoire dans Azure DevTest Labs

Ce script Azure CLI arrête et supprime une machine virtuelle dans un laboratoire. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Arrête une machine virtuelle dans un laboratoire. Cet opérateur peut prendre un certain temps. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Supprime une machine virtuelle dans un laboratoire. Cet opérateur peut prendre un certain temps. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

D’autres exemples de scripts CLI Azure Lab Services sont disponibles dans les [exemples CLI pour Azure Lab Services](../samples-cli.md).
