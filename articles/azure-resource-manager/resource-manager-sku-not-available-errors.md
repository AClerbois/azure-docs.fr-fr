---
title: Erreurs de référence SKU Azure non disponible | Microsoft Docs
description: Décrit comment résoudre les erreurs de référence SKU non disponible quand vous déployez des ressources avec Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: fca028412052a9a1520e1178f5d182a9987a9a85
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390232"
---
# <a name="resolve-errors-for-sku-not-available"></a>Résoudre les erreurs de référence SKU non disponible

Cet article décrit comment résoudre les erreurs **SkuNotAvailable**. Si vous ne parvenez pas à trouver de référence appropriée dans cette région ou une autre qui réponde aux besoins de votre entreprise, envoyez une [demande de référence SKU](https://aka.ms/skurestriction) au support Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Cause :

Vous recevez cette erreur lorsque la ressource de référence SKU que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné.

## <a name="solution-1---powershell"></a>Solution 1 : PowerShell

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez la commande [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Filtrez les résultats par emplacement. Pour cette commande, vous devez disposer de la version la plus récente de PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Les résultats incluent une liste de références pour l’emplacement et toutes les restrictions éventuellement liées aux références concernées. Notez que la référence SKU peut être répertoriée en tant que `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Solution 2 : Azure CLI

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez la commande `az vm list-skus`. Utilisez le paramètre `--location` pour filtrer la sortie vers l’emplacement que vous utilisez. Utilisez le paramètre `--size` pour rechercher par un nom partiel.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

La commande retourne des résultats similaires à ce qui suit :

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Solution 3 : Portail Azure

Pour déterminer les références SKU qui sont disponibles dans une région, consultez le [portail](https://portal.azure.com). Connectez-vous au portail et ajoutez une ressource via l’interface. Lorsque vous définissez les valeurs, vous voyez les références (SKU) disponibles pour cette ressource. Vous n’avez pas besoin de terminer le déploiement.

Par exemple, démarrez le processus de création d’une machine virtuelle. Pour voir les autres tailles disponibles, sélectionnez **Modifier la taille**.

![Créer une machine virtuelle](./media/resource-manager-sku-not-available-errors/create-vm.png)

Vous pouvez filtrer et faire défiler les tailles disponibles.

![Références SKU disponibles](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Solution 4 : REST

Pour déterminer les références SKU disponibles dans une région, utilisez l’opération [Resource Skus - List](/rest/api/compute/resourceskus/list).

Elle renvoie les références et les régions disponibles dans le format suivant :

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

