---
title: Exemple de script Azure CLI - Gérer le trafic web | Microsoft Docs
description: 'Exemple de script Azure CLI : gérez le trafic web avec une passerelle d’application et un groupe de machines virtuelles identiques.'
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b2485b65c54bdd5dcd523226e17c180bf1eb86a6
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734196"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Gérer le trafic web avec Azure CLI

Ce script permet de créer une passerelle d’application qui utilise un groupe de machines virtuelles identiques pour les serveurs backend. La passerelle d’application peut ensuite être configurée pour gérer le trafic web. Après avoir exécuté le script, vous pouvez tester la passerelle d’application à l’aide de son adresse IP publique.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Créer un réseau virtuel. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnetwork) | Crée un sous-réseau dans un réseau virtuel. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Crée l’adresse IP publique pour la passerelle d’application. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Créer une passerelle d’application |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | Crée un groupe de machines virtuelles identiques. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Obtient l’adresse IP publique d’une passerelle d’application. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de passerelle d’application dans la [documentation relative aux machines virtuelles Windows Azure](../cli-samples.md).
