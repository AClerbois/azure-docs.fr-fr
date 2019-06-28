---
title: Monter un volume Azure Files dans Azure Container Instances
description: Découvrir comment monter un volume Azure Files pour conserver l’état avec Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564019"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Monter un partage de fichiers Azure dans Azure Container Instances

Par défaut, les conteneurs Azure Container Instances sont sans état. Si le conteneur se bloque ou s’arrête, son état est entièrement perdu. Pour conserver l’état au-delà de la durée de vie du conteneur, vous devez monter un volume à partir d’un stockage externe. Cet article explique comment monter un partage de fichiers Azure créé avec [Azure Files](../storage/files/storage-files-introduction.md) pour une utilisation avec Azure Container Instances. Azure Files offre des partages de fichiers managés dans le cloud qui sont accessibles via le protocole SMB (Server Message Block) standard. L'utilisation d'un partage de fichiers Azure avec Azure Container Instances offre des fonctionnalités de partage de fichiers similaires à l’utilisation d’un partage de fichiers Azure avec machines virtuelles Azure.

> [!NOTE]
> Le montage d’un partage de fichiers Azure est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser un partage de fichiers Azure avec Azure Container Instances, vous devez le créer. Exécutez le script suivant pour créer un compte de stockage et y héberger le partage de fichiers, et le partage proprement dit. Comme le nom du compte de stockage doit être globalement unique, le script ajoute une valeur aléatoire à la chaîne de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obtenir des informations d’identification de stockage

Pour monter un partage de fichiers Azure en tant que volume dans Azure Container Instances, vous avez besoin de trois valeurs : le nom du compte de stockage, le nom du partage et la clé d’accès de stockage.

Si vous avez utilisé le script ci-dessus, le nom du compte de stockage a été stocké dans la variable $ACI_PERS_STORAGE_ACCOUNT_NAME. Pour afficher le nom du compte, entrez :

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Le nom du partage étant déjà connu (c’est-à-dire *acishare* dans le script ci-dessus), il ne reste que la clé du compte de stockage, qui peut être trouvée à l’aide de la commande suivante :

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Déployer le conteneur et monter le volume

Pour monter un partage de fichiers Azure en tant que volume dans un conteneur, spécifiez le point de montage du volume et le partage lorsque vous créez le conteneur avec [az container create][az-container-create]. Si vous avez accompli les étapes précédentes, vous pouvez monter le partage que vous avez créé avant à l’aide de la commande suivante pour créer un conteneur :

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

La valeur `--dns-name-label` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Mettez à jour la valeur de la commande précédente si vous recevez un message d’erreur **Étiquette de nom DNS** lorsque vous exécutez la commande.

## <a name="manage-files-in-mounted-volume"></a>Gérer les fichiers dans le volume monté

Après le démarrage du conteneur, vous pouvez utiliser l’application web simple déployée par le biais de l’image Microsoft [aci-hellofiles][aci-hellofiles] pour créer de petits fichiers texte dans le partage de fichiers Azure sur le chemin de montage que vous avez spécifié. Obtenir un nom de domaine complet de l’application web (FQDN) avec la commande [afficher de conteneur az] [ az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Vous pouvez utiliser le [portail Azure][portal] ou un outil comme [l’Explorateur Stockage Microsoft Azure][storage-explorer] pour récupérer et inspecter le fichier écrit sur le partage de fichiers.

## <a name="mount-multiple-volumes"></a>Monter plusieurs volumes

Pour monter plusieurs volumes dans une instance de conteneur, vous devez effectuer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou d'un fichier YAML.

Pour utiliser un modèle, fournissez les détails de partage et définissez les volumes en remplissant le tableau `volumes` dans la section `properties` du modèle. Par exemple, si vous avez créé deux partages de fichiers Azure nommés *share1* et *share2* dans le compte de stockage *myStorageAccount*, le tableau `volumes` ressemblerait à ceci :

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter les volumes, remplissez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur. Ainsi, nous obtenons les deux volumes montés, *myvolume1* et *myvolume2*, définis précédemment :

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer un groupe de conteneurs](container-instances-multi-container-group.md). Pour un exemple utilisant un fichier YAML, consultez [Déployer un groupe de plusieurs conteneurs avec YAML](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume gitRepo dans Azure Container Instances](container-instances-volume-gitrepo.md)
* [Monter un volume secret dans Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show