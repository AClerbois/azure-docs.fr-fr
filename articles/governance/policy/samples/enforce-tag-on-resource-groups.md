---
title: Exemple - Forcer l’application d’une balise et d’une valeur sur les groupes de ressources
description: Cet exemple de définition de stratégie exige une étiquette et une valeur sur un groupe de ressources.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/31/2019
ms.author: dacoulte
ms.openlocfilehash: 5f4af5ee88ad491e7864e82afc337801e47f2204
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255779"
---
# <a name="sample---enforce-tag-and-its-value-on-resource-groups"></a>Exemple - Forcer l’application d’une balise et de sa valeur sur les groupes de ressources

Cette stratégie exige une étiquette et une valeur sur un groupe de ressources. Spécifiez les nom et valeur de balise nécessaires.

Vous pouvez déployer cette exemple de stratégie à l’aide des éléments suivants :

- [Portail Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interface de ligne de commande Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Exemple de stratégie

### <a name="policy-definition"></a>Définition de stratégie

Définition de stratégie JSON composée complète, utilisée par l’API REST, les boutons « Déployer sur Azure » et manuellement dans le portail.

[!code-json[main](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]

> [!NOTE]
> Si vous créez manuellement une stratégie dans le portail, utilisez les parties **properties.parameters** et **properties.policyRule** du code ci-dessus. Placez les deux sections entre accolades `{}` pour en faire du code JSON valide.

### <a name="policy-rules"></a>Règles de stratégie

Code JSON définissant les règles de la stratégie, utilisé par l’interface de ligne de commande Azure (Azure CLI) et Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Paramètres de stratégie

Code JSON définissant les paramètres de la stratégie, utilisé par l’interface de ligne de commande Azure (Azure CLI) et Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Nom |Type |Champ |Description |
|---|---|---|---|
|tagName |Chaîne |tags |Nom de la balise, par exemple costCenter|
|tagValue |Chaîne |tags |Valeur de la balise, par exemple headquarter|

Lors de la création d’une affectation via PowerShell ou l’interface de ligne de commande Azure (Azure CLI), les valeurs de paramètres peuvent être passées au format JSON dans une chaîne ou via un fichier à l’aide de `-PolicyParameter` (PowerShell) ou de `--params` (Azure CLI).
PowerShell prend également en charge `-PolicyParameterObject` qui nécessite de passer une table de hachage Nom/Valeur à l’applet de commande, où **Nom** est le nom du paramètre et **Valeur** est la valeur unique ou le tableau des valeurs transmises pendant l’affectation.

Dans cet exemple de paramètre, une _tagName_ de **costCenter** et une _tagValue_ de **headquarter** sont définies.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Portail Azure

[![Déployer l’exemple de stratégie dans Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)
[![Déployer l’exemple de stratégie dans Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'enforce-resourceGroup-tags' -DisplayName 'Enforce tag and its value on resource groups' -description 'Enforces a required tag and its value on resource groups.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyParam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'enforce-resourceGroup-tags-assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyParam
```

### <a name="remove-with-azure-powershell"></a>Supprimer avec Azure PowerShell

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Explication Azure PowerShell

Les scripts de déploiement et de suppression utilisent les commandes suivantes. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Crée une définition Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Obtient un groupe de ressources unique. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Supprime une affectation Azure Policy existante. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Supprime une définition Azure Policy existante. |

## <a name="azure-cli"></a>D’Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyParam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(
az policy assignment create --name 'enforce-resourceGroup-tags-assignment' --display-name 'Enforce tag and its value on resource groups'  --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Supprimer avec Azure CLI

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Explication Azure CLI

| Commande | Notes |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Crée une définition Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Obtient un groupe de ressources unique. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Supprime une affectation Azure Policy existante. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Supprime une définition Azure Policy existante. |

Il existe plusieurs outils permettant d’interagir avec l’API REST Resource Manager, comme [ARMClient](https://github.com/projectkudu/ARMClient) ou PowerShell. Vous trouverez un exemple d’appel à l’API REST à partir de PowerShell dans la section **Alias** de [Structure de la définition de stratégie](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>API REST

### <a name="deploy-with-rest-api"></a>Déploiement avec l’API REST

- Créer la définition de stratégie (étendue de l’abonnement). Utilisez le code JSON de [définition de stratégie](#policy-definition) pour le corps de la requête.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

- Créer l’affectation de stratégie (étendue de groupe de ressources)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

  Utilisez l’exemple de code JSON suivant pour le corps de la requête :

```json
  {
      "properties": {
          "displayName": "Enforce tag and its value Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Supprimer avec l’API REST

- Supprimer l’affectation de stratégie

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

- Supprimer la définition de stratégie

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Explication de l’API REST

| de diffusion en continu | Groupe | Opération | Notes |
|---|---|---|---|
| Gestion des ressources | Définitions de stratégies | [Créer](/rest/api/resources/policydefinitions/createorupdate) | Crée une définition Azure Policy au niveau d’un abonnement. Alternative : [Créer au niveau du groupe d’administration](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gestion des ressources | Affectations de stratégies | [Créer](/rest/api/resources/policyassignments/create) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| Gestion des ressources | Affectations de stratégies | [Supprimer](/rest/api/resources/policyassignments/delete) | Supprime une affectation Azure Policy existante. |
| Gestion des ressources | Définitions de stratégies | [Supprimer](/rest/api/resources/policydefinitions/delete) | Supprime une définition Azure Policy existante. Alternative : [Supprimer au niveau du groupe d’administration](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres [exemples Azure Policy](index.md)
- Consulter [Structure de définition Azure Policy](../concepts/definition-structure.md)