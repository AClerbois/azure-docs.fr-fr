---
title: Gérer des groupes Azure Resource Manager à l'aide d'Azure PowerShell | Microsoft Docs
description: Utilisez Azure PowerShell pour gérer vos groupes Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5197358e3bd8a3052fbf71cafc2f1e3acda46b26
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721152"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gérer des groupes de ressources Azure Resource Manager à l'aide d'Azure PowerShell

Apprenez à utiliser Azure PowerShell avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos groupes de ressources Azure. Pour gérer des ressources Azure, consultez [Gérer les ressources Azure à l'aide d'Azure PowerShell](./manage-resources-powershell.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer les groupes de ressources Azure à l'aide du portail Azure](./manage-resources-portal.md)
- [Gérer les groupes de ressources Azure à l'aide d'Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Qu'est-ce qu'un groupe de ressources ?

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Quand vous spécifiez un emplacement pour le groupe de ressources, vous indiquez donc où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Le script PowerShell suivant crée un groupe de ressources, puis l'affiche.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

Le script PowerShell suivant répertorie les groupes de ressources de votre abonnement.

```azurepowershell-interactive
Get-AzResourceGroup
```

Pour obtenir un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Supprimer des groupes de ressources

Le script PowerShell suivant supprime un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Pour plus d'informations sur l'ordre dans lequel Azure Resource Manager supprime des ressources, consultez [Suppression d'un groupe de ressources par Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources sur un groupe de ressources existant

Consultez [Déployer des ressources sur un groupe de ressources existant](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Pour valider le déploiement d'un groupe de ressources, consultez [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et des ressources

Vous pouvez créer un groupe de ressources et déployer des ressources sur ce groupe à l'aide d'un modèle Resource Manager. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

Cette fonctionnalité est également appelée *Annulation en cas d'erreur*. Pour plus d'informations, consultez [Redéploiement en cas d'échec du déploiement](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Déplacer vers un autre groupe de ressources ou abonnement

Vous pouvez déplacer les ressources du groupe vers un autre groupe de ressources. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Verrouiller des groupes de ressources

Le verrouillage empêche d'autres utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources stratégiques, telles qu'une ressource, un groupe de ressources ou un abonnement Azure. 

Le script suivant verrouille un groupe de ressources pour empêcher sa suppression.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Le script suivant obtient tous les verrous relatifs à un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Baliser des groupes de ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d'informations, consultez [Organisation des ressources Azure à l'aide d'étiquettes](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exporter des groupes de ressources dans des modèles

Une fois que vous avez configuré votre groupe de ressources, vous pouvez afficher un modèle Resource Manager pour celui-ci. L’exportation du modèle offre deux avantages :

- Automatisez les futurs déploiements de la solution, car le modèle contient la totalité de l’infrastructure.
- Familiarisez-vous avec la syntaxe des modèles en consultant la notation JSON (JavaScript Object Notation) qui représente votre solution.

Pour exporter toutes les ressources d’un groupe de ressources, utilisez l’applet de commande [AzResourceGroup d’exportation](/powershell/module/az.resources/Export-AzResourceGroup) et indiquez le nom du groupe de ressources.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Il enregistre le modèle dans un fichier local.

Plutôt que d’exporter toutes les ressources dans le groupe de ressources, vous pouvez sélectionner les ressources à exporter.

Pour exporter une ressource, transmettez cet ID de ressource.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Pour exporter plusieurs ressources, transmettez les ID de ressource dans un tableau.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Lorsque vous exportez le modèle, vous pouvez spécifier si des paramètres sont utilisés dans celui-ci. Par défaut, les paramètres des noms de ressource sont inclus, mais ils n’ont pas de valeur par défaut. Vous devez transmettre cette valeur de paramètre pendant le déploiement.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

Dans la ressource, le paramètre est utilisé pour le nom.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Si vous utilisez le paramètre `-IncludeParameterDefaultValue` lors de l’exportation du modèle, le paramètre de modèle inclut une valeur par défaut qui est définie sur la valeur actuelle. Vous pouvez utiliser cette valeur par défaut ou remplacer la valeur par défaut en transmettant une valeur différente.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Si vous utilisez le paramètre `-SkipResourceNameParameterization` lors de l’exportation du modèle, les paramètres des noms de ressources ne sont pas inclus dans le modèle. Au lieu de cela, le nom de ressource est défini directement sur la ressource sur sa valeur actuelle. Vous ne pouvez pas personnaliser le nom au cours du déploiement.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Pour plus d’informations, consultez [Export d’une ressource unique ou de plusieurs ressources vers un modèle dans le portail Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gérer l'accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d'informations, consultez [Gérer l'accès avec RBAC et Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](./resource-group-overview.md).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](/azure/azure-resource-manager/).
- Pour accéder aux schémas liés aux modèles Azure Resource Manager, consultez [Informations de référence sur les modèles](/azure/templates/).