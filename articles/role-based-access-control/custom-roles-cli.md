---
title: Créer des rôles personnalisés pour les ressources Azure à l’aide d’Azure CLI | Microsoft Docs
description: Apprenez à créer des rôles personnalisés avec contrôle d'accès en fonction du rôle (RBAC) pour les ressources Azure à l'aide d’Azure CLI. Cet article indique également comment répertorier, créer, mettre à jour et supprimer des rôles personnalisés.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118771"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Créer des rôles personnalisés pour les ressources Azure à l’aide d’Azure CLI

Si les [rôles intégrés pour les ressources Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Cet article explique comment créer et gérer des rôles personnalisés avec Azure CLI.

Pour obtenir un tutoriel pas à pas sur la création d’un rôle personnalisé, consultez [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l’aide d’Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Conditions préalables

Pour créer des rôles personnalisés, vous avez besoin des éléments suivants :

- autorisations nécessaires pour créer des rôles personnalisés, par exemple, [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ;
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Répertorier les rôles personnalisés

Pour lister les rôles personnalisés pouvant être affectés, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list). Les exemples suivants listent tous les rôles personnalisés de l’abonnement actuel.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Liste d’une définition de rôle personnalisé

Pour répertorier une définition de rôle personnalisé, utilisez [liste des définitions de rôle az](/cli/azure/role/definition#az-role-definition-list). Il s’agit de la même commande que vous utiliseriez pour un rôle intégré.

```azurecli
az role definition list --name <role_name>
```

L’exemple suivant liste les *opérateur de Machine virtuelle* définition de rôle :

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

L’exemple suivant répertorie uniquement les actions de la *opérateur de Machine virtuelle* rôle :

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Pour créer un rôle personnalisé, utilisez [az role definition create](/cli/azure/role/definition#az-role-definition-create). La définition de rôle peut être une description JSON ou le chemin d’un fichier contenant une description JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

L’exemple suivant crée un rôle personnalisé appelé *Virtual Machine Operator*. Ce rôle personnalisé attribue l’accès à toutes les opérations de lecture (« read ») des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network* et attribue l’accès pour démarrer (« start »), redémarrer (« restart ») et surveiller (« monitor ») les machines virtuelles. Ce rôle personnalisé peut être utilisé dans deux abonnements. Cet exemple utilise un fichier JSON en tant qu’entrée.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé

Pour mettre à jour un rôle personnalisé, utilisez d’abord [az role definition list](/cli/azure/role/definition#az-role-definition-list) pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez [az role definition update](/cli/azure/role/definition#az-role-definition-update) pour enregistrer la définition de rôle mise à jour.

```azurecli
az role definition update --role-definition <role_definition>
```

L’exemple suivant ajoute l’opération *Microsoft.Insights/diagnosticSettings/* aux *Actions* du rôle personnalisé *Virtual Machine Operator*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Pour spécifier le rôle à supprimer, utilisez le nom ou l’ID du rôle. Pour déterminer l’ID du rôle, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

L’exemple suivant supprime le rôle personnalisé *Virtual Machine Operator*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l’aide d’Azure CLI](tutorial-custom-role-cli.md)
- [Rôles personnalisés pour les ressources Azure](custom-roles.md)
- [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md)