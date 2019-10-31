---
title: Déployer des ressources Azure inter-abonnements et groupes de ressources
description: Montre comment cibler plusieurs groupes de ressources et des abonnements Azure pendant le déploiement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528330"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En général, vous déployez toutes les ressources dans votre modèle sur un seul [groupe de ressources](resource-group-overview.md). Toutefois, il existe des scénarios dans lesquels vous pouvez souhaitez déployer un ensemble de ressources, tout en les plaçant dans différents groupes de ressources ou abonnements. Par exemple, vous voudrez peut-être déployer la machine virtuelle de sauvegarde destinée à Azure Site Recovery sur un groupe de ressources et un emplacement distincts. Resource Manager vous permet d’utiliser des modèles imbriqués pour cibler des groupes de ressources ou des abonnements différents du groupe de ressources ou de l’abonnement utilisés pour le modèle parent.

> [!NOTE]
> Vous ne pouvez pas déployer sur plus de cinq groupes de ressources dans un déploiement. En règle générale, cette limitation signifie que vous pouvez déployer sur un groupe de ressources spécifié pour le modèle parent et jusqu'à quatre groupes de ressources dans les déploiements imbriqués ou liés. Toutefois, si votre modèle parent contient uniquement des modèles imbriqués ou liés et ne déploie lui-même aucune ressource, vous pouvez inclure jusqu'à cinq groupes de ressources dans les déploiements imbriqués ou liés.

## <a name="specify-a-subscription-and-resource-group"></a>Spécifier un groupe de ressources et un abonnement

Pour cibler une autre ressource, utilisez un modèle imbriqué ou lié. Le type de ressource `Microsoft.Resources/deployments` fournit des paramètres pour `subscriptionId` et `resourceGroup`. Ces propriétés permettent de spécifier un autre groupe de ressources et un autre abonnement pour le déploiement imbriqué. Tous les groupes de ressources doivent exister avant l’exécution du déploiement. Si vous ne spécifiez ni l’ID d’abonnement ni le groupe de ressources, ce sont l’abonnement et le groupe de ressources depuis le modèle parent qui sont utilisés.

Le compte que vous utilisez pour déployer le modèle doit avoir les autorisations requises pour déployer vers l’ID d’abonnement spécifié. Si l’abonnement spécifié existe dans un autre abonné Azure Active Directory, vous devez [ajouter les utilisateurs invités à partir d’un autre répertoire](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Pour spécifier un autre groupe de ressources et un abonnement, utilisez :

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Si vos groupes de ressources se trouvent dans le même abonnement, vous pouvez supprimer la valeur **subscriptionId**.

L’exemple suivant déploie deux comptes de stockage, un dans le groupe de ressources spécifié pendant le déploiement, et l’autre dans un groupe de ressources précisé dans le paramètre `secondResourceGroup` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Si vous définissez `resourceGroup`sur le nom d’un groupe de ressources qui n’existe pas, le déploiement échoue.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Utiliser les fonctions resourceGroup() et subscription()

Pour des déploiements entre groupes de ressources, les fonctions [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) et [subscription()](resource-group-template-functions-resource.md#subscription) se résolvent différemment selon la façon dont vous spécifiez le modèle imbriqué. 

Si vous incorporez un modèle dans un autre, la résolution des fonctions dans le modèle imbriqué aboutit au groupe de ressources et à l’abonnement parent. Un modèle incorporé utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Si vous liez à un modèle distinct, la résolution des fonctions dans le modèle lié aboutit au groupe de ressources et à l’abonnement imbriqué. Un modèle lié utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Exemples de modèles

Les modèles suivants illustrent plusieurs déploiements de groupes de ressources. Les scripts permettant de déployer les modèles sont indiqués après le tableau.

|Modèle  |Description  |
|---------|---------|
|[Modèle à abonnements multiples](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Déploie un compte de stockage vers un groupe de ressources et un autre compte de stockage vers un deuxième groupe de ressources. Incluez une valeur pour l’ID d’abonnement lorsque le deuxième groupe de ressources se trouve dans un autre abonnement. |
|[Modèle à propriétés de groupe de ressources multiples](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Montre la résolution de la fonction `resourceGroup()`. Il ne déploie aucune ressource. |

### <a name="powershell"></a>PowerShell

Pour PowerShell, déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Pour PowerShell, déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Pour PowerShell, testez la résolution de **l’objet groupe de ressources** pour le modèle parent, le modèle inline et le modèle lié en utilisant :

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Dans l’exemple précédent, **parentRG** et **inlineRG** se résolvent tous les deux sur **parentGroup**. **linkedRG** se résout sur **linkedGroup**. La sortie de l’exemple précédent est :

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>D’Azure CLI

Pour Azure CLI, déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Pour Azure CLI, déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Pour Azure CLI, testez la résolution de **l’objet groupe de ressources** pour le modèle parent, le modèle inline et le modèle lié en utilisant :

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Dans l’exemple précédent, **parentRG** et **inlineRG** se résolvent tous les deux sur **parentGroup**. **linkedRG** se résout sur **linkedGroup**. La sortie de l’exemple précédent est :

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).
