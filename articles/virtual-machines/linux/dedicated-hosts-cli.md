---
title: Déployer des hôtes dédiés Azure à l’aide de la CLI | Microsoft Docs
description: Déployez des machines virtuelles sur des hôtes dédiés à l’aide d’Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/29/2019
ms.author: cynthn
ms.openlocfilehash: 0c060e2ab94c0a57d4d4dc897702e115cfabd9a0
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827293"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Aperçu : Déployez des machines virtuelles sur des hôtes dédiés à l’aide d’Azure CLI
 

Cet article vous guide dans la création d’un [hôte dédié](dedicated-hosts.md) Azure pour héberger vos machines virtuelles. 

Vérifiez que vous avez installé Azure CLI version 2.0.70 ou version ultérieure, et que vous êtes connecté à un compte Azure avec `az login`. 

> [!IMPORTANT]
> Les hôtes dédiés Azure sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitations connues de la préversion**
> - Actuellement, les hôtes dédiés ne prennent pas en charge les groupes de machines virtuelles identiques.
> - La préversion initiale prend en charge les séries de machines virtuelles suivantes : DSv3 et ESv3. 
 

## <a name="create-resource-group"></a>Créer un groupe de ressources 
Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez le groupe de ressources avec la commande az group create. L’exemple suivant crée un groupe de ressources nommé *myDHResourceGroup* à l’emplacement *USA Est*.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Créer un groupe hôte 

Un **groupe hôte** est une ressource qui représente une collection d’hôtes dédiés. Vous créez un groupe hôte dans une région et une zone de disponibilité, et lui ajoutez des hôtes. Lors de la planification de la haute disponibilité, vous pouvez accéder à des options supplémentaires. Vous pouvez utiliser l’une des options suivantes, ou les deux, avec vos hôtes dédiés : 
- Application sur plusieurs zones de disponibilité. Dans ce cas, vous devez disposer d’un groupe hôte dans chacune des zones que vous souhaitez utiliser.
- Application sur plusieurs domaines d’erreur mappés à des racks physiques. 
 
Dans les deux cas, vous devez fournir le nombre de domaines d’erreur pour votre groupe hôte. Si vous ne souhaitez pas appliquer plusieurs domaines d’erreur dans votre groupe, utilisez un seul domaine d’erreur. 

Vous pouvez également choisir d’utiliser des zones de disponibilité et des domaines d’erreur. 

Dans cet exemple, nous allons utiliser la commande [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) pour créer un groupe hôte à l’aide de zones de disponibilité et de domaines d’erreur. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Autres exemples

Vous pouvez également utiliser [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) pour créer un groupe hôte dans la zone de disponibilité 1 (sans domaine d’erreur).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
L’exemple suivant exploite la commande [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) pour créer un groupe hôte à l’aide de domaines d’erreur uniquement (à utiliser dans les régions dans lesquels les zones de disponibilité ne sont pas prises en charge). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Créer un hôte 

À présent, créons un hôte dédié dans le groupe hôte. En plus d’un nom pour l’hôte, vous devez fournir la référence SKU pour l’hôte. La référence SKU de l’hôte capture la série de machines virtuelles prises en charge, ainsi que la génération du matériel pour l’hôte dédié.  Lors de la préversion, nous allons prendre en charge les valeurs de SKU d’hôte suivantes : DSv3_Type1 et ESv3_Type1.


Pour en savoir plus sur les références SKU et la tarification des hôtes, consultez la section relative à la [tarification des hôtes dédiés Azure](https://aka.ms/ADHPricing).

Utilisez la commande [az vm host create](/cli/azure/vm/host#az-vm-host-create) pour créer un hôte. Si vous définissez un nombre de domaines d’erreur pour votre groupe hôte, vous êtes invité à spécifier le domaine d’erreur de votre hôte.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle 
Créez une machine virtuelle dans un hôte dédié à l’aide de la commande [az vm create](/cli/azure/vm#az-vm-create). Si vous avez spécifié une zone de disponibilité lors de la création de votre groupe hôte, vous devez utiliser la même zone quand vous créez la machine virtuelle.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Toute machine virtuelle créée sur un hôte qui ne dispose pas d’un nombre de ressources suffisant présente un état ÉCHEC. 


## <a name="check-the-status-of-the-host"></a>Vérifier l’état de l’hôte

Vous pouvez vérifier l’état d’intégrité de l’hôte et le nombre de machines virtuelles que vous pouvez encore déployer sur l’hôte à l’aide de la commande [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Le résultat ressemble à ce qui suit :
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exporter en tant que modèle 
Vous pouvez exporter un modèle si vous souhaitez créer un environnement de développement supplémentaire avec les mêmes paramètres, ou un environnement de production correspondant. Resource Manager utilise des modèles JSON qui définissent tous les paramètres pour votre environnement. Vous créez des environnements entiers en faisant référence à ce modèle JSON. Vous pouvez créer manuellement des modèles JSON ou exporter un environnement existant qui créera le modèle JSON pour vous. Utilisez la commande [az group export](/cli/azure/group#az-group-export) pour exporter votre groupe de ressources.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Cette commande crée le fichier `myDHResourceGroup.json` dans votre répertoire de travail actuel. Quand vous créez un environnement à partir de ce modèle, vous êtes invité à indiquer tous les noms de ressources. Vous pouvez renseigner ces noms dans votre fichier de modèle en ajoutant le paramètre `--include-parameter-default-value` à la commande `az group export`. Modifiez votre modèle JSON pour spécifier les noms de ressources, ou créez un fichier parameters.json qui les spécifie.
 
Pour créer un environnement à partir de votre modèle, utilisez la commande [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Nettoyer 

Vous êtes facturé pour vos hôtes dédiés, même si aucune machine virtuelle n’est déployée. Supprimez tous les hôtes non utilisés pour réduire les coûts.  

Vous pouvez supprimer un hôte uniquement lorsqu’il n’est plus utilisé par aucune machine virtuelle. Supprimez les machines virtuelles avec la commande [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Cela fait, vous pouvez supprimer l’hôte, avec la commande [az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Une fois que vous avez supprimé tous vos hôtes, utilisez la commande [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete) pour supprimer le groupe hôte.  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Vous pouvez également supprimer l’intégralité du groupe de ressources via une seule commande. Cette opération supprime toutes les ressources créées dans le groupe, y compris l’ensemble des machines virtuelles, des hôtes et des groupes hôtes.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, consultez la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Le [Portail Microsoft Azure](dedicated-hosts-portal.md) permet également de créer des hôtes dédiés.

- Un exemple de modèle, trouvé [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.