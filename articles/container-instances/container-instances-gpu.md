---
title: Déployer des instances de conteneur Azure compatibles GPU
description: Découvrez comment déployer des instances de conteneur Azure à exécuter sur les ressources GPU.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: f35b2cd8d360bd46913eaa34b91e1fd19bc1ba9b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533593"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Déployer des instances de conteneur qui utilisent des ressources GPU

Pour exécuter certaines charges de travail nécessitant beaucoup de ressources système sur Azure Container Instances, déployez vos [groupes de conteneurs](container-instances-container-groups.md) avec des *ressources GPU*. Les instances de conteneur dans le groupe peuvent accéder à un ou plusieurs GPU NVIDIA Tesla pendant l’exécution de charges de travail de conteneur comme CUDA et les applications de Deep Learning.

Comme indiqué dans cet article, vous pouvez ajouter des ressources GPU lorsque vous déployez un groupe de conteneurs avec un [fichier YAML](container-instances-multi-container-yaml.md) ou un [modèle Resource Manager](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à la condition d’accepter les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

Dans la préversion, les limites suivantes s’appliquent lors de l’utilisation de ressources GPU dans les groupes de conteneurs. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

D’autres régions seront également prises en charge au fil du temps.

**Types de systèmes d’exploitation pris en charge** : Linux uniquement

**Limites supplémentaires** : Les ressources GPU ne peuvent pas être utilisées lors du déploiement d’un groupe de conteneurs dans un [réseau virtuel](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>À propos des ressources GPU

### <a name="count-and-sku"></a>Nombre et référence SKU

Pour utiliser des GPU dans une instance de conteneur, vous devez spécifier une *ressource GPU* avec les informations suivantes :

* **Nombre** : nombre de GPU : **1**, **2** ou **4**.
* **Référence SKU** : référence SKU du GPU : **K80**, **P100** ou **V100**. Chaque référence SKU est mappée au GPU NVIDIA Tesla dans l’une des familles de machines virtuelles Azure compatibles GPU suivantes :

  | SKU | Famille de machines virtuelles |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Lors du déploiement des ressources GPU, définir des ressources processeur et mémoire appropriées pour la charge de travail, jusqu'à les valeurs maximales indiqué dans le tableau précédent. Ces valeurs sont actuellement plus de ressources de processeur et mémoire disponibles dans les groupes de conteneur sans ressources GPU.  

### <a name="things-to-know"></a>À savoir

* **Durée de déploiement** : la création d’un groupe de conteneurs comportant des ressources GPU peut prendre jusqu’à **8 à 10 minutes**. Ce délai est dû au temps supplémentaire nécessaire pour la gestion et la configuration d’une machine virtuelle GPU dans Azure. 

* **Tarification** : semblable aux groupes de conteneurs sans ressources GPU ; factures Azure pour les ressources consommées sur la *durée* d’un groupe de conteneurs avec ressources GPU. La durée est calculée à partir du début de l’extraction de l’image de votre premier conteneur jusqu’à la fin de l’exécution du groupe de conteneurs. Elle n’inclut pas le temps de déploiement du groupe de conteneurs.

  La tarification est plus élevée pour les groupes de conteneurs avec des ressources GPU que pour les groupes de conteneur sans ressources GPU. Consultez les [détails de la tarification](https://azure.microsoft.com/pricing/details/container-instances/).

* **Pilotes CUDA** : les instances de conteneur avec ressources GPU sont préconfigurées avec des pilotes NVIDIA CUDA et des runtimes de conteneur. Vous pouvez donc utiliser des images de conteneur développées pour les charges de travail CUDA.

  Nous prenons en charge CUDA 9.0 à ce stade. Par exemple, vous pouvez utiliser suivant des images de base pour votre fichier Docker :
  * [NVIDIA/CUDA:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow : 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Exemple YAML

Une façon d’ajouter des ressources GPU est de déployer un groupe de conteneurs en utilisant un [fichier YAML](container-instances-multi-container-yaml.md). Copiez le YAML suivant dans un nouveau fichier nommé *gpu-deploy-aci.yaml*, puis enregistrez le fichier. Ce YAML crée un groupe de conteneurs nommé *gpucontainergroup* et spécifie une instance de conteneur avec un GPU K80. L’instance exécute un exemple d’application d’ajout de vecteur CUDA. Les requêtes de ressources sont suffisantes pour exécuter la charge de travail.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Déployez le groupe de conteneurs avec la commande [az container create][az-container-create], en spécifiant le nom du fichier YAML dans le paramètre `--file`. Vous devez fournir le nom d’un groupe de ressources et un emplacement pour le groupe de conteneurs tel que *eastus*, qui prend en charge les ressources GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Le déploiement prend plusieurs minutes. Ensuite, le conteneur démarre et exécute une opération d’ajout de vecteur CUDA. Exécutez la commande [az container logs][az-container-logs] pour afficher la sortie du journal :

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Sortie :

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Exemple de modèle Resource Manager

Une autre façon de déployer un groupe de conteneurs avec des ressources GPU est d’utiliser un [modèle Resource Manager](container-instances-multi-container-group.md). Commencez par créer un fichier nommé `gpudeploy.json`, puis copiez-y le code JSON suivant. Cet exemple déploie une instance de conteneur avec un GPU V100 qui exécute un travail de formation [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) pour le [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/). Les requêtes de ressources sont suffisantes pour exécuter la charge de travail.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Déployez ensuite le modèle avec la commande [az group deployment create][az-group-deployment-create]. Vous devez fournir le nom d’un groupe de ressources qui a été créé dans une région telle que *eastus* et qui prend en charge les ressources GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Le déploiement prend plusieurs minutes. Ensuite, le conteneur démarre et exécute le travail TensorFlow. Exécutez la commande [az container logs][az-container-logs] pour afficher la sortie du journal :

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Sortie :

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Étant donné que l’utilisation des ressources GPU peut être onéreuse, assurez-vous que vos conteneurs ne s’exécutent pas de manière inattendue pendant de longues périodes. Surveillez vos conteneurs sur le Portail Azure ou vérifiez l’état d’un groupe de conteneurs avec la commande [az container show][az-container-show]. Par exemple : 

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Lorsque vous avez fini d’utiliser les instances de conteneur que vous avez créées, supprimez-les avec les commandes suivantes :

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le déploiement d’un groupe de conteneurs avec un [fichier YAML](container-instances-multi-container-yaml.md) ou un [modèle Resource Manager](container-instances-multi-container-group.md).
* En savoir plus sur les [tailles des machines virtuelles optimisées pour le GPU](../virtual-machines/linux/sizes-gpu.md) dans Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
