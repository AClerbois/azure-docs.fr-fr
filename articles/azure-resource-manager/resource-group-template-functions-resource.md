---
title: Ressources - fonctions de modèle Azure Resource Manager | Microsoft Docs
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs sur les ressources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: tomfitz
ms.openlocfilehash: 32a0263c4c8c1e85145f5d11fd44823216efdcbc
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107053"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Fonctions de ressources pour les modèles Azure Resource Manager

Resource Manager offre les fonctions ci-après pour obtenir des valeurs de ressource :

* [listAccountSas](#list)
* [listKeys](#listkeys)
* [listSecrets](#list)
* [list*](#list)
* [fournisseurs](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnement](#subscription)

Pour obtenir des valeurs de paramètres, de variables ou du déploiement actuel, consultez [Fonctions de valeur de déploiement](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listaccountsas-listkeys-listsecrets-and-list"></a>listAccountSas, listKeys, listSecrets et list*
`listAccountSas(resourceName or resourceIdentifier, apiVersion, functionValues)`

`listKeys(resourceName or resourceIdentifier, apiVersion)`

`listSecrets(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Renvoie les valeurs pour n’importe quel type de ressource qui prend en charge l’opération list. Les utilisations les plus courantes sont `listKeys` et `listSecrets`. 

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |chaîne |Identificateur unique pour la ressource. |
| apiVersion |Oui |chaîne |Version d'API de l'état d'exécution des ressources. En règle générale, au format, **aaaa-mm-jj**. |
| functionValues |Non  |objet | Objet qui contient les valeurs de la fonction. Fournissez uniquement cet objet pour les fonctions qui prennent en charge la réception d’un objet avec des valeurs de paramètre, comme **listAccountSas** sur un compte de stockage. | 

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé par listKeys a le format suivant :

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

D’autres fonctions de liste ont différents formats de retour. Pour afficher le format d’une fonction, incluez-le dans la section des sorties comme indiqué dans l’exemple de modèle. 

### <a name="remarks"></a>Remarques

Toute opération qui commence par **list** peut être utilisée en tant que fonction dans votre modèle. Les opérations disponibles incluent non seulement listKeys, mais également des opérations telles que `list`, `listAdminKeys` et `listStatus`. L’opération [Répertorier les signatures d’accès partagé de comptes](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) nécessite des paramètres de corps de requête, comme *signedExpiry*. Pour utiliser cette fonction dans un modèle, fournissez un objet avec des valeurs de paramètre de corps.

Pour déterminer les types de ressources qui ont une opération de liste, utilisez les options suivantes :

* Affichez les [opérations d’API REST](/rest/api/) pour un fournisseur de ressources et recherchez les opérations de liste. Par exemple, les comptes de stockage présentent l’[opération listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Utilisez l’applet de commande PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). L’exemple ci-dessous obtient toutes les opérations de liste pour les comptes de stockage :

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Utilisez la commande Azure CLI suivante pour filtrer uniquement les opérations de liste :

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Spécifiez la ressource en utilisant le nom de la ressource ou la [fonction resourceId](#resourceid). Lorsque vous utilisez cette fonction dans le modèle qui déploie la ressource référencée, utilisez le nom de la ressource.

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) suivant montre comment retourner les clés primaires et secondaires à partir d’un compte de stockage dans la section outputs. Il retourne également un jeton SAS pour le compte de stockage. Pour obtenir ce jeton, il passe un objet à la fonction listAccountSas. Cet exemple vise à montrer comment vous utilisez les fonctions de liste. En général, vous devez utiliser le jeton SAS dans une valeur de ressource au lieu de la retourner sous la forme d’une valeur de sortie. Les valeurs de sortie sont stockées dans l’historique de déploiement et ne sont pas sécurisées. Pour que le déploiement réussisse, vous devez spécifier une heure d’expiration dans le futur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
``` 

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storagename=<your-storage-account>
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storagename <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>fournisseurs
`providers(providerNamespace, [resourceType])`

Renvoie des informations sur un fournisseur de ressources et les types de ressources qu’il prend en charge. Si vous ne fournissez pas un type de ressource, la fonction retourne tous les types pris en charge pour le fournisseur de ressources.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| espacedenoms_fournisseur |Oui |chaîne |Espace de noms du fournisseur. |
| resourceType |Non  |chaîne |Type de ressource dans l'espace de noms spécifié. |

### <a name="return-value"></a>Valeur de retour

Chaque type pris en charge est renvoyé au format suivant : 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Le classement du tableau des valeurs retournées n’est pas garanti.

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) suivant montre comment utiliser la fonction provider :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Pour le fournisseur de ressources **Microsoft.Web** et le type de ressource **sites**, l’exemple précédent retourne un objet au format suivant :

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Renvoie un objet représentant l’état d’exécution d’une ressource.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |chaîne |Nom ou identificateur unique d’une ressource. |
| apiVersion |Non  |chaîne |Version d’API de la ressource spécifiée. Incluez ce paramètre quand la ressource n’est pas provisionnée dans le même modèle. En règle générale, au format, **aaaa-mm-jj**. |
| 'Full' |Non  |chaîne |Valeur qui spécifie si l’objet de ressource complet doit être retourné. Si vous ne spécifiez pas `'Full'`, seul l’objet properties de la ressource est retourné. L’objet complet comprend des valeurs telles que l’ID de ressource et l’emplacement. |

### <a name="return-value"></a>Valeur de retour

Chaque type de ressource retourne des propriétés différentes pour la fonction de référence. La fonction ne retourne pas un format prédéfini unique. La valeur retournée varie également selon que vous avez spécifié ou non l’objet complet. Pour afficher les propriétés d’un type de ressource, renvoyez l’objet dans la section des sorties comme indiqué dans l’exemple.

### <a name="remarks"></a>Remarques

La fonction de référence récupère l’état d’exécution d’une ressource déployée précédemment ou déployée dans le modèle actuel. Cet article montre des exemples pour les deux scénarios. Lorsque vous référencez une ressource dans le modèle actuel, indiquez uniquement le nom de la ressource en tant que paramètre. Lorsque vous référencez une ressource précédemment déployée, fournissez l’ID de ressource et une version d’API pour cette ressource. Vous pouvez déterminer les versions d’API valides pour votre ressource dans la [référence de modèle](/azure/templates/).

La fonction de référence ne peut être utilisée que dans les propriétés d’une définition de ressource et dans la section de sortie d’un modèle ou d’un déploiement.

En utilisant la fonction reference, vous déclarez de manière implicite qu’une ressource dépend d’une autre ressource si la ressource référencée est configurée dans le même modèle et vous désignez cette ressource par son nom (pas par son ID). Vous n’avez pas besoin d’utiliser également la propriété dependsOn. La fonction n’est pas évaluée tant que le déploiement de la ressource référencée n’est pas terminé.

Pour afficher les noms et les valeurs des propriétés pour un type de ressource donné, créez un modèle qui retourne l’objet dans la section outputs. Si vous disposez déjà d’une ressource de ce type, votre modèle retourne l’objet sans déployer de nouvelles ressources. 

En règle générale, vous utilisez la fonction de **référence** pour renvoyer une valeur particulière d’un objet, telle que l’URI du point de terminaison d’objet blob ou le nom de domaine complet.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Utilisez `'Full'` quand vous avez besoin de valeurs de ressource qui ne font pas partie du schéma de propriétés. Par exemple, pour définir des stratégies d’accès à des coffres de clés, obtenez les propriétés d’identité d’une machine virtuelle.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Pour voir l’exemple complet du modèle précédent, consultez [Windows et le coffre de clés](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Un exemple similaire est disponible pour [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) suivant déploie, puis référence une ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

L’exemple précédent retourne les deux objets. L’objet de propriétés retourné présente le format suivant :

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

L’objet complet retourné présente le format suivant :

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) suivant référence un compte de stockage qui n’est pas déployé dans ce modèle. Le compte de stockage existe déjà dans le même abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageResourceGroup=<rg-for-storage> storageAccountName=<your-storage-account>
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageResourceGroup <rg-for-storage> -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Renvoie un objet qui représente le groupe de ressources actuel. 

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Remarques

La fonction `resourceGroup()` ne peut pas être utilisée dans un modèle qui est [déployé au niveau abonnement](deploy-to-subscription.md). Elle n’est utilisable que dans les modèles déployés sur un groupe de ressources.

Une utilisation courante de la fonction resourceGroup consiste à créer des ressources dans le même emplacement que le groupe de ressources. L'exemple suivant utilise l'emplacement du groupe de ressources pour affecter l'emplacement d'un site web.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) suivant retourne les propriétés du groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent renvoie un objet dans le format suivant :

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>ResourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Retourne l'identificateur unique d'une ressource. Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. 

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non  |string (au format GUID) |La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement. |
| nom_groupe_ressources |Non  |chaîne |La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre groupe de ressources. |
| resourceType |Oui |chaîne |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |chaîne |Nom de la ressource. |
| nom_ressource2 |Non  |chaîne |Segment de nom de ressource suivant si la ressource est imbriquée. |

### <a name="return-value"></a>Valeur de retour

L'identificateur est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Remarques

Lorsqu’elle est utilisée avec un [déploiement au niveau abonnement](deploy-to-subscription.md), la fonction `resourceId()` ne peut récupérer que l’ID des ressources déployées à ce niveau. Ainsi, vous pouvez obtenir l’ID d’une définition de stratégie ou d’une définition de rôle, mais pas l’ID d’un compte de stockage. Pour les déploiements sur un groupe de ressources, l’inverse est vrai. Vous ne pouvez pas obtenir l’ID de ressource des ressources déployées au niveau abonnement.

Les valeurs de paramètre spécifiées varient selon que la ressource se trouve ou non dans le même abonnement et le même groupe de ressources que le déploiement actuel. Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans le même abonnement et le même groupe de ressources, utilisez :

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans le même abonnement mais dans un groupe de ressources différent, utilisez :

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans un abonnement et un groupe de ressources différents, utilisez :

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’une base de données se trouvant dans un groupe de ressources différent, utilisez :

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Pour obtenir l’ID de ressource d’une ressource de niveau abonnement lors du déploiement dans l’étendue de l’abonnement, utilisez :

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) suivant retourne l’ID de ressource pour un compte de stockage appartenant au groupe de ressources :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| sameRGOutput | Chaîne | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Chaîne | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Chaîne | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Chaîne | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>subscription
`subscription()`

Retourne des détails concernant l’abonnement pour le déploiement actuel. 

### <a name="return-value"></a>Valeur de retour

La fonction retourne les informations au format suivant :

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) suivant montre la fonction subscription qui est appelée dans la section outputs. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

