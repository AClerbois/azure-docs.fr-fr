---
title: Gérer les ressources Azure à l’aide d’Azure PowerShell | Microsoft Docs
description: Utilisez Azure PowerShell et Azure Resource Manager pour gérer vos ressources.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f61fcf7833021f23485992c7445f91d9a7b9ee45
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001522"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gérer les ressources Azure à l’aide d’Azure PowerShell

Découvrez comment utiliser Azure PowerShell avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos ressources Azure. Pour gérer des groupes de ressources, consultez [Gérer des groupes de ressources Azure à l’aide d’Azure PowerShell](./manage-resource-groups-powershell.md).

Autres articles sur la gestion des ressources :

- [Gérer les ressources Azure à l’aide du portail Azure](./manage-resources-portal.md)
- [Gérer les ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources sur un groupe de ressources existant

Vous pouvez déployer des ressources Azure directement à l’aide d’Azure PowerShell ou déployer un modèle Resource Manager pour créer des ressources Azure.

### <a name="deploy-a-resource"></a>Déployer une ressource

Le script suivant permet de créer un compte de stockage.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Déploiement d’un modèle

Le script suivant déploie un modèle de démarrage rapide pour créer un compte de stockage. Pour plus d’informations, consultez [Démarrage rapide : Créer des modèles Azure Resource Manager à l’aide de Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et des ressources

Vous pouvez créer un groupe de ressources et y déployer des ressources. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Déployer des ressources sur plusieurs abonnements ou groupes de ressources

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaitez déployer un ensemble de ressources, tout en les plaçant dans différents groupes de ressources ou abonnements. Pour plus d’informations, consultez [Déployer des ressources Azure sur plusieurs abonnements ou groupes de ressources](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Supprimer des ressources

Le script suivant montre comment supprimer un compte de stockage.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Pour plus d’informations sur l’ordre dans lequel Azure Resource Manager supprime des ressources, consultez [Suppression d’un groupe de ressources par Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Déplacer des ressources

Le script suivant montre comment déplacer un compte de stockage depuis un groupe de ressources vers un autre.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).

## <a name="lock-resources"></a>Verrouiller des ressources

Le verrouillage empêche d’autres utilisateurs de votre organisation de supprimer ou modifier accidentellement des ressources cruciales, telles qu’une ressource, un groupe de ressources ou un abonnement Azure. 

Le script suivant verrouille un compte de stockage afin d’empêcher sa suppression.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Le script suivant obtient tous les verrous pour un compte de stockage :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Le script suivant supprime un verrou d’un compte de stockage :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Baliser des ressources

L’étiquetage facilite l’organisation logique des ressources et groupe de ressources. Pour plus d’informations, consultez [Organisation des ressources Azure à l’aide d’étiquettes](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Gérer l’accès aux ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d'informations, consultez [Gérer l'accès avec RBAC et Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](./resource-group-overview.md).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](/azure/azure-resource-manager/).
- Pour accéder aux schémas liés aux modèles Azure Resource Manager, consultez [Informations de référence sur les modèles](/azure/templates/).
