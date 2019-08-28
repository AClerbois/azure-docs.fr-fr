---
title: Didacticiel Kubernetes sur Azure - Mettre à niveau un cluster
description: Dans le cadre de ce didacticiel Azure Kubernetes Service (AKS), vous allez apprendre à mettre à niveau un cluster AKS existant vers la dernière version Kubernetes disponible.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9fe02c9b563259abb51a1a768c7facdf1bf601f7
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898842"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Didacticiel : Mettre à niveau Kubernetes dans Azure Kubernetes Service (AKS)

Durant le cycle de vie des applications et des clusters, vous pouvez choisir d’effectuer une mise à niveau vers la dernière version disponible de Kubernetes afin d’en exploiter les nouvelles fonctionnalités. Un cluster Azure Kubernetes Service (AKS) peut être mis à niveau à l’aide d’Azure CLI.

Dans ce tutoriel (le septième d’une série de sept), un cluster Kubernetes est mis à niveau. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les tutoriels précédents, une application a été empaquetée dans une image conteneur. Cette image a été chargée dans Azure Container Registry et vous avez créé un cluster AKS. L’application a ensuite été déployée sur le cluster AKS. Si vous n’avez pas effectué ces étapes et si vous souhaitez suivre cette procédure, commencez par [Tutoriel 1 : Créer des images conteneur][aks-tutorial-prepare-app].

Ce tutoriel nécessite l’exécution de l’interface de ligne de commande Azure (Azure CLI) version 2.0.53 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Obtenir les versions de cluster disponibles

Avant de mettre à niveau un cluster, utilisez la commande [az aks get-upgrades][] pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau :

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

Dans l’exemple suivant, la version actuelle est *1.13.10*, et les versions disponibles s’affichent sous la colonne *Mises à jour*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Mettre à niveau un cluster

Afin de limiter les perturbations pour les applications en cours d’exécution, les nœuds AKS sont soigneusement isolés et drainés. Dans ce processus, les étapes suivantes sont à exécuter :

1. Le planificateur Kubernetes empêche la planification de pods supplémentaires sur un nœud qui doit être mis à niveau.
1. Les pods en cours d’exécution sur le nœud sont planifiés sur d’autres nœuds du cluster.
1. Un nœud exécutant les derniers composants Kubernetes est créé.
1. Quand le nouveau nœud est prêt et est joints au cluster, le planificateur Kubernetes commence à exécuter des pods dessus.
1. L’ancien nœud est supprimé, et le nœud suivant du cluster commence le processus d’isolation et de drainage.

Utilisez la commande [az aks upgrade][] pour mettre à niveau le cluster AKS. L’exemple suivant met à niveau le cluster vers Kubernetes version *1.14.6*.

> [!NOTE]
> Vous ne pouvez mettre à niveau qu’une version mineure à la fois. Par exemple, vous pouvez effectuer une mise à niveau de la version *1.12.x* vers la version *1.13.x*, mais pas de la version *1.12* directement vers la version *1.14.x*. Pour effectuer une mise à niveau de *1.12.x* vers *1.14.x*, commencez par mettre à niveau la version *1.12.x* vers la version *1.13.x*, puis mettez à niveau la version *1.13.x* vers la version *1.14.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

L’exemple de sortie condensée suivant montre que la valeur de *kubernetesVersion* indique à présent *1.14.6* :

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.14.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Valider une mise à niveau

Assurez-vous que la mise à niveau a réussi en utilisant la commande [az aks show][] comme suit :

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie suivant indique que le cluster AKS exécute *KubernetesVersion 1.14.6* :

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Supprimer le cluster

Comme ce tutoriel est la dernière partie de la série, vous souhaiterez peut-être supprimer le cluster AKS. Les nœuds Kubernetes étant exécutés sur des machines virtuelles Azure, ils continuent d’occasionner des frais même si vous n’utilisez pas le cluster. Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service conteneur ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete].

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez mis à niveau Kubernetes dans un cluster AKS. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

Suivez ce lien pour en savoir plus sur AKS.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
