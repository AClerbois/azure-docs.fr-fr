---
title: Créer un cluster Azure Kubernetes Service (AKS)
description: Créez un cluster AKS avec l’interface CLI ou le portail Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Créer un cluster Azure Kubernetes Service (AKS)

Un cluster Azure Kubernetes Service (AKS) peut être créé avec l’interface Azure CLI ou le portail Azure.

## <a name="azure-cli"></a>Azure CLI

Utilisez la commande [az aks create][az-aks-create] pour créer le cluster AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Les options suivantes sont disponibles avec la commande `az aks create`.

| Argument | Description | Obligatoire |
|---|---|:---:|
| `--name``-n` | Nom de ressource pour le cluster managé. | Oui |
| `--resource-group``-g` | Nom du groupe de ressources Azure Kubernetes Service. | Oui |
| `--admin-username``-u` | Nom d’utilisateur pour les machines virtuelles Linux.  Par défaut : azureuser. | no |
| ` --client-secret` | Secret associé au principal du service. | no |
| `--dns-name-prefix``-p` | Préfixe DNS pour l’adresse IP publique des clusters. | no |
| `--generate-ssh-keys` | Génère les fichiers de clés SSH publiques et privées s’ils sont manquants. | no |
| `--kubernetes-version``-k` | Version de Kubernetes à utiliser pour la création du cluster, telle que « 1.7.9 » ou « 1.8.2 ».  Par défaut : 1.7.7. | no |
| `--no-wait` | Ne pas attendre la fin de l’opération de longue durée. | no |
| `--node-count``-c` | Nombre de nœuds pour les pools de nœud par défaut.  Par défaut : 3. | no |
| `--node-osdisk-size` | Taille osDisk en Go du pool de nœud Machine virtuelle. | no |
| `--node-vm-size``-s` | Taille de la machine virtuelle.  Par défaut : Standard_D1_v2. | no |
| `--service-principal` | Principal de service utilisé pour l’authentification du cluster. | no |
| `--ssh-key-value` | Valeur de fichier de clé ou chemin de fichier de clé SSH.  Par défaut : ~/.ssh/id_rsa.pub. | no |
| `--tags` | Balises séparées par des espaces au format 'key [= value]'. Utilisez '' pour effacer des balises existantes. | no |

## <a name="azure-portal"></a>Portail Azure

Pour obtenir des instructions sur le déploiement d’un cluster AKS avec le portail Azure, consultez le [guide de démarrage rapide du portail Azure][aks-portal-quickstart] pour Azure Kubernetes Service (AKS).

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
