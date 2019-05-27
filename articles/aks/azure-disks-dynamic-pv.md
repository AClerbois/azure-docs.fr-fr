---
title: Créer un volume de disque de manière dynamique pour plusieurs pods dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer un volume persistant de manière dynamique avec des disques Azure pour une utilisation simultanée avec plusieurs pods, dans Azure Kubernetes Service (ACS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 334e56db97213206d9ab7ed5ef4d1d96ab9325d6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956479"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Créer et utiliser un volume persistant de manière dynamique avec des disques Azure sur Azure Kubernetes Service (AKS)

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Cet article vous montre comment créer des volumes persistants de manière dynamique avec des disques Azure pour permettre à un pod unique de les utiliser, dans un cluster Azure Kubernetes Service (AKS).

> [!NOTE]
> Un disque Azure peut être monté uniquement avec le type de *mode d’accès* *ReadWriteOnce*, qui le rend disponible sur un seul pod dans AKS. Si vous avez besoin de partager un volume persistant entre plusieurs pods, utilisez [Azure Files][azure-files-pvc].

Pour plus d’informations sur les volumes Kubernetes, consultez [des options de stockage pour les applications dans ACS][concepts-storage].

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

Vous également besoin d’Azure CLI version 2.0.59 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="built-in-storage-classes"></a>Classes de stockage intégrées

Une classe de stockage permet de définir la création dynamique d’une unité de stockage avec un volume persistant. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Chaque cluster AKS comprend deux classes de stockage précréées, toutes deux configurées pour utiliser des disques Azure :

* La classe de stockage *par défaut* configure un disque Azure standard.
    * Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.
* La classe de stockage *Premium managée* provisionne un disque Azure Premium.
    * Les disques Premium reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Si les nœuds AKS dans votre cluster utilisent le stockage Premium, sélectionnez la classe *Premium managée*.
    
Ces classes de stockage par défaut ne vous permettent de mettre à jour de la taille du volume après sa créée. Pour activer cette fonctionnalité, ajoutez le *allowVolumeExpansion : true* trait à une des classes de stockage par défaut ou créer votre classe de stockage personnalisé propre. Vous pouvez modifier une classe de stockage existant à l’aide du `kubectl edit sc` commande. Pour plus d’informations sur les classes de stockage et de création youor propre, consultez [des options de stockage pour les applications dans ACS][storage-class-concepts].

Utilisez la commande [kubectl get sc][kubectl-get] pour afficher les classes de stockage créées au préalable. L’exemple suivant montre les classes de stockage pré-créées disponibles au sein d’un cluster AKS :

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Les revendications de volume persistant sont spécifiées dans Gio mais les disques managés Azure sont facturés par référence SKU pour une taille spécifique. Ces références (SKU) comprise 32GiB pour S4 ou P4 disques à 32TiB pour les disques S80 ou P80 (en version préliminaire). Le débit et les performances d’E/S d’un disque managé Premium dépendent à la fois de la référence SKU et de la taille d’instance des nœuds dans le cluster AKS. Pour plus d’informations, consultez [Tarification et performances de la fonctionnalité Disques managés][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant (PVC) est utilisée pour configurer automatiquement le stockage basé sur une classe de stockage. Dans ce cas, une PVC peut utiliser une des classes de stockage créées au préalable pour créer un disque géré Azure standard ou Premium.

Créez un fichier nommé `azure-premium.yaml` et copiez-y le manifeste suivant. La revendication demande un disque nommé `azure-managed-disk`, d’une taille de *5 Go* avec un accès *ReadWriteOnce*. La classe de stockage *managed-premium* est spécifiée en tant que classe de stockage.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Pour créer un disque qui utilise le stockage standard, préférez `storageClassName: default` plutôt que *managed-premium*.

Créez la revendication de volume persistant avec la commande [kubectl apply][kubectl-apply] et spécifiez votre fichier *azure-premium.yaml* :

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Utiliser le volume persistant

Une fois la revendication de volume persistant créée, et le disque provisionné convenablement, un pod peut être créé avec un accès au disque. Le manifeste suivant crée un pod NGINX de base qui utilise la revendication de volume persistant nommé *azure-managed-disk* pour monter le disque Azure à l’emplacement `/mnt/azure`. Pour Windows Server conteneurs (actuellement en version préliminaire dans ACS), spécifiez un *mountPath* à l’aide de la convention de chemin d’accès Windows, tel que *« D: »*.

Créez un fichier nommé `azure-pvc-disk.yaml` et copiez-y le manifeste suivant.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Créez le pod avec la commande [kubectl apply][kubectl-apply], comme indiqué dans l’exemple suivant :

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Vous disposez maintenant d’un pod en cours d’exécution avec le disque Azure monté dans le répertoire `/mnt/azure`. Cette configuration peut être consultée en inspectant votre pod via `kubectl describe pod mypod`, comme illustré dans l’exemple condensé suivant :

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Sauvegarder un volume persistant

Pour sauvegarder les données de votre volume persistant, prenez un instantané du disque managé pour le volume. Vous pouvez ensuite utiliser cet instantané pour créer un disque restauré et l’attacher aux pods comme moyen de restauration des données.

Tout d’abord, obtenez le nom de volume au moyen de la commande `kubectl get pvc`, par exemple pour la PVC nommée *azure-managed-disk* :

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ce nom de volume constitue le nom du disque Azure sous-jacent. Recherchez l’ID de disque à l’aide de [az disk list][az-disk-list] et fournissez votre nom de volume PVC, comme indiqué dans l’exemple suivant :

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Utilisez l’ID de disque pour créer un disque de capture instantanée avec [az snapshot create][az-snapshot-create]. L’exemple suivant crée un instantané nommé *pvcSnapshot* dans le même groupe de ressources que le cluster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Vous pouvez rencontrer des problèmes d’autorisation si vous créez des instantanés et restaurez des disques dans des groupes de ressources auxquels le cluster AKS n’a pas accès.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Selon la quantité de données présentes sur votre disque, quelques minutes peuvent être nécessaires pour créer l’instantané.

## <a name="restore-and-use-a-snapshot"></a>Restaurer et utiliser un instantané

Pour restaurer le disque et l’utiliser avec un pod Kubernetes, utilisez la capture instantanée comme source lorsque vous créez un disque avec [az disk create][az-disk-create]. Cette opération permet de conserver la ressource d’origine si vous devez ensuite accéder à l’instantané des données d’origine. L’exemple suivant crée un disque nommé *pvcRestored* à partir de l’instantané nommé *pvcSnapshot* :

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Pour utiliser le disque restauré avec un pod, spécifiez l’ID du disque dans le manifeste. Procurez-vous l’ID de disque par le biais de la commande [az disk show][az-disk-show]. L’exemple suivant récupère l’ID de disque pour *pvcRestored* qui a été créé à l’étape précédente :

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Créez un manifeste de pod nommé `azure-restored.yaml` et spécifiez l’URI de disque obtenu à l’étape précédente. L’exemple suivant crée un serveur web NGINX de base, avec le disque restauré monté comme volume à l’emplacement */mnt/azure* :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Créez le pod avec la commande [kubectl apply][kubectl-apply], comme indiqué dans l’exemple suivant :

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Vous pouvez utiliser `kubectl describe pod mypodrestored` pour voir les détails du pod, tel que dans l’exemple condensé suivant qui affiche les informations du volume :

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Étapes suivantes

Pour les recommandations associées, consultez [meilleures pratiques pour le stockage et les sauvegardes dans ACS][operator-best-practices-storage].

Découvrez plus en détail les volumes persistants Kubernetes utilisant des disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
