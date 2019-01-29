---
title: Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy
description: Cet article vous explique comment créer et gérer des stratégies par programmation pour Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: adeb963333ffc2b587d7468eb357fab8dc4d6bbe
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847048"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy

Cet article vous explique comment créer et gérer des stratégies par programmation. Les définitions de stratégie appliquent différentes règles et des différents effets sur vos ressources. Cette mise en conformité permet de garantir que les ressources restent conformes à vos normes d’entreprise et contrats de niveau de service.

Pour plus d’informations sur la conformité, consultez [Obtention de données de conformité](getting-compliance-data.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que les conditions préalables suivantes sont remplies :

1. Si ce n’est pas déjà fait, installez [ARMClient](https://github.com/projectkudu/ARMClient). C’est un outil qui envoie des requêtes HTTP aux API Azure Resource Manager.

1. Procédez à la mise à jour de votre module Azure PowerShell vers la dernière version. Pour plus d'informations, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Pour plus d’informations sur la version la plus récente, voir [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Inscrivez le fournisseur de ressources Policy Insights à l’aide d’Azure PowerShell pour vérifier que votre abonnement fonctionne avec ce fournisseur de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à exécuter l’opération d’inscription pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez la page [Fournisseurs et types de ressources](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Si ce n’est déjà fait, installez Azure CLI. Vous pouvez obtenir la dernière version sur la page [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Créer et attribuer une définition de stratégie

Pour une meilleure visibilité de vos ressources, la première chose à faire est de créer et d’attribuer des stratégies à vos ressources. L’étape suivante consiste à apprendre à créer et assigner une stratégie par programmation. L’exemple de stratégie proposé réalise l’audit des comptes de stockage ouverts à tous les réseaux publics à l’aide de PowerShell, d’Azure CLI et de requêtes HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Créer et assigner une définition de stratégie avec PowerShell

1. L’extrait de code JSON suivant permet de créer un fichier JSON nommé AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Pour plus d’informations sur la création d’une définition de stratégie, consultez la page [Structure de définition Azure Policy](../concepts/definition-structure.md).

1. Exécutez la commande suivante pour créer une définition de stratégie en utilisant le fichier AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   La commande crée une définition de stratégie nommée _Audit Storage Accounts Open to Public Networks_.
   Pour plus d'informations sur les autres paramètres que vous pouvez utiliser, consultez [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Lorsqu’il est appelé sans paramètre d’emplacement, par défaut, `New-AzPolicyDefinition` enregistre la définition de stratégie dans l’abonnement sélectionné du contexte de sessions. Pour enregistrer la définition dans un autre emplacement, utilisez les paramètres suivants :

   - **SubscriptionId** : enregistrer dans un autre abonnement. Une valeur _GUID_ est nécessaire.
   - **ManagementGroupName** : enregistrer dans un groupe d’administration. Une valeur de _chaîne_ est nécessaire.

1. Après avoir créé votre définition de stratégie, vous pouvez créer une attribution de stratégie en exécutant les commandes suivantes :

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Remplacez _ContosoRG_ par le nom de votre groupe de ressources prévu.

   Le paramètre **Étendue** sur `New-AzPolicyAssignment` fonctionne également avec les abonnements et les groupes d’administration. Le paramètre utilise un chemin d’accès de ressource complet, que la propriété **ResourceId** renvoie sur `Get-AzResourceGroup`. Pour chaque conteneur, le modèle **Étendue** est le suivant.
   Remplacez `{rgName}`, `{subId}`, et `{mgName}` par le nom de votre groupe de ressources, l’ID d’abonnement et le nom du groupe d’administration, respectivement.

   - Groupe de ressources : `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnement : `/subscriptions/{subId}/`
   - Groupe d'administration : `/providers/Microsoft.Management/managementGroups/{mgName}`

Pour plus d'informations sur la gestion des stratégies de ressources à l'aide du module Azure Resource Manager PowerShell, consultez [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Créer et assigner une définition de stratégie avec ARMClient

Pour créer une définition de stratégie, procédez comme suit.

1. Copiez l’extrait de code JSON suivant pour créer un fichier JSON. Vous appellerez le fichier à l’étape suivante.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Créez la définition de stratégie à l’aide de l’un des appels suivants :

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Remplacez {subscriptionId} précédent par l’ID de votre abonnement ou {managementGroupId} par l’ID de votre [groupe d’administration](../../management-groups/overview.md).

   Pour plus d’informations sur la structure de la requête, consultez [Définitions de stratégie – Créer ou mettre à jour](/rest/api/resources/policydefinitions/createorupdate) et [Définitions de stratégie – Créer ou mettre à jour dans le groupe d’administration](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Utilisez la procédure suivante pour créer une attribution de stratégie et assigner la définition de stratégie au niveau du groupe de ressources.

1. Copiez l’extrait de code JSON suivant pour créer un fichier d’attribution de stratégie JSON. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Créez l’attribution de stratégie à l’aide de l’appel suivant :

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

   Pour plus d’informations sur les appels HTTP à l’API REST, consultez la page [Ressources API REST Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Créer et attribuer une définition de stratégie avec Azure CLI

Pour créer une définition de stratégie, procédez comme suit :

1. Copiez l’extrait de code JSON suivant pour créer un fichier d’attribution de stratégie JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

1. Pour créer une définition de stratégie, exécutez la commande suivante :

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. Pour créer une attribution de stratégie, utilisez la commande suivante. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

Vous pouvez obtenir l’ID de définition de stratégie à l’aide de PowerShell avec la commande suivante :

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

L’ID de définition de stratégie pour la définition de stratégie que vous avez créée doit ressembler à ce qui suit :

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Pour plus d’informations sur la façon dont vous pouvez gérer les stratégies de ressources avec Azure CLI, consultez [Stratégies de ressources Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les commandes et les requêtes de cet article, consultez les articles suivants.

- [Ressources API REST Azure](/rest/api/resources/)
- [Modules Azure PowerShell](/powershell/module/az.resources/#policies)
- [Commandes de stratégie Azure CLI](/cli/azure/policy?view=azure-cli-latest)
- [Informations de référence sur l’API REST du fournisseur de ressources Policy Insights](/rest/api/policy-insights)
- [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md)