---
title: Utiliser des disques Azure avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b790213e19b9f2aaef74a3f670c89246f54fd6d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-disks"></a>Volumes avec disques Azure

Les applications basées sur des conteneurs doivent souvent consulter et conserver des données dans un volume de données externe. Les disques Azure peuvent être utilisés comme banque de données externe. Cet article explique comment utiliser un disque Azure comme volume Kubernetes dans un cluster Azure Kubernetes Service (AKS).

Pour plus d’informations sur les volumes Kubernetes, consultez [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Créer un disque Azure

Avant de monter un disque managé Azure comme volume Kubernetes, le disque doit se trouver dans le même groupe de ressources que les ressources du cluster AKS. Pour trouver ce groupe de ressources, utilisez la commande [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Recherchez un groupe de ressources portant un nom semblable à `MC_clustername_clustername_locaton`, où « clustername » est le nom de votre cluster AKS et « location » est la région Azure où le cluster a été déployé.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Utilisez la commande [az disk create][az-disk-create] pour créer le disque Azure.

À l’aide de cet exemple, mettez à jour `--resource-group` avec le nom du groupe de ressources et `--name` avec le nom de votre choix.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Une fois que le disque a été créé, vous devriez voir une sortie similaire à celle qui suit. Cette valeur correspond à l’ID du disque, qui est utilisé lors du montage du disque sur un pod Kubernetes.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Monter le disque en tant que volume

Montez le disque Azure dans votre pod en configurant le volume dans les spécifications du conteneur.

Créez un fichier nommé `azure-disk-pod.yaml` avec le contenu suivant. Mettez à jour `diskName` avec le nom du disque nouvellement créé, et `diskURI` avec l’ID du disque. Notez également la valeur de `mountPath`. Il s’agit du chemin du disque Azure qui a été monté dans le pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Utilisez kubectl pour créer le pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution sur lequel est monté un disque Azure à l’emplacement suivant : `/mnt/azure`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment les volumes Kubernetes utilisent les disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
