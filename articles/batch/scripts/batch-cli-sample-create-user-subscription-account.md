---
title: Exemple de script Azure CLI - Créer un compte Batch - abonnement utilisateur | Documents Microsoft
description: Exemple de script Azure CLI - Créer un compte Batch en mode abonnement utilisateur
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: fe5dbfa368de400b4aad476c4aefbb70628d1145
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852151"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemple CLI : Créer un compte Batch en mode abonnement utilisateur

Ce script crée un compte Azure Batch en mode abonnement utilisateur. Un compte qui alloue des nœuds de calcul dans votre abonnement doit être authentifié via un jeton Azure Active Directory. Les nœuds de calcul alloués sont pris en compte dans le quota de processeurs virtuels (cœur) de votre abonnement. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az role assignment create](/cli/azure/role) | Crée une nouvelle attribution de rôle pour un utilisateur, un groupe ou un principal de service. |
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | Crée un coffre de clés. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | Met à jour la stratégie de sécurité du coffre de clés spécifié. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crée le compte Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
