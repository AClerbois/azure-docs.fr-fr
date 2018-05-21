---
title: Fonctions de modèle Azure Resource Manager - numérique| Microsoft Docs
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour travailler avec des nombres.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Fonctions numériques pour les modèles Azure Resource Manager

Resource Manager fournit les expressions ci-après pour travailler avec des entiers :

* [ajouter](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>ajouter
`add(operand1, operand2)`

Retourne la somme des deux entiers fournis.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- | 
|operand1 |OUI |int |Premier nombre à ajouter. |
|operand2 |OUI |int |Deuxième nombre à ajouter. |

### <a name="return-value"></a>Valeur de retour

Entier qui contient la somme des paramètres.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) suivant, vous ajoutez deux paramètres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Retourne l’index d’une boucle d’itération. 

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| loopName | Non  | chaîne | Nom de la boucle pour l’obtention de l’itération. |
| Offset |Non  |int |Le nombre à ajouter à la valeur d’itération de base zéro. |

### <a name="remarks"></a>Remarques

Cette fonction est toujours utilisée avec un objet **copy** . Si aucune valeur n’est fournie pour **offset**, la valeur d’itération actuelle est retournée. La valeur d’itération commence à zéro. Vous pouvez utiliser des boucles d’itération quand vous définissez des ressources ou des variables.

La propriété **loopName** permet d’indiquer si copyIndex fait référence à une itération de ressource ou de propriété. Si aucune valeur n’est indiquée pour **loopName**, l’itération du type de ressource actuelle est utilisée. Indiquez une valeur pour **loopName** lors de l’itération sur une propriété. 
 
Pour obtenir une description complète d’exemples d’utilisation de l’expression **copyIndex**, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Pour obtenir un exemple d’utilisation de **copyIndex** afin de définir une variable, consultez [Variables](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Exemples

L’exemple suivant montre une boucle de copie ainsi que la valeur d’index incluse dans le nom. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valeur de retour

Entier représentant l’index actuel de l’itération.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Retourne la division entière des deux entiers fournis.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |OUI |int |Le nombre à diviser. |
| operand2 |OUI |int |Le nombre utilisé pour diviser. Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la division.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) suivant, vous divisez un paramètre par un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Convertit la valeur en nombre à virgule flottante. Vous utilisez uniquement cette fonction lors de la transmission de paramètres personnalisés à une application, telle qu’une application logique.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |chaîne ou entier |Valeur à convertir en nombre à virgule flottante. |

### <a name="return-value"></a>Valeur de retour
Nombre à virgule flottante.

### <a name="example"></a>Exemples

L’exemple suivant montre comment utiliser float pour passer des paramètres à une application logique :

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Convertit la valeur spécifiée en entier.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |OUI |chaîne ou entier |La valeur à convertir en entier. |

### <a name="return-value"></a>Valeur de retour

Nombre entier de la valeur convertie.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) suivant, vous convertissez la valeur de paramètre fournie par l’utilisateur en un entier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur maximale. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la valeur maximale de la collection.

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) suivant montre comment utiliser max avec un tableau et une liste d’entiers :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 5. |
| intOutput | Int | 5. |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur minimale. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la valeur minimale de la collection.

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) suivant montre comment utiliser min avec un tableau et une liste d’entiers :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Retourne le reste de la division entière des deux entiers fournis.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |OUI |int |Le nombre à diviser. |
| operand2 |OUI |int |Le nombre utilisé pour diviser, Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour
Entier représentant le reste.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) suivant, le reste de la division d’un paramètre par un autre paramètre est retourné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Retourne la multiplication des deux entiers fournis.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |OUI |int |Premier nombre à multiplier. |
| operand2 |OUI |int |Deuxième nombre à multiplier. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la multiplication.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) suivant, vous multipliez un paramètre par un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Retourne la soustraction des deux entiers fournis.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |OUI |int |Le nombre auquel est appliquée la soustraction. |
| operand2 |OUI |int |Le nombre qui est soustrait. |

### <a name="return-value"></a>Valeur de retour
Entier représentant la multiplication.

### <a name="example"></a>Exemples

Dans [l’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) suivant, vous soustrayez un paramètre d’un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| NOM | type | Valeur |
| ---- | ---- | ----- |
| subResult | Int | 4 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

