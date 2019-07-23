---
title: Comprendre les affectations de refus relatives aux ressources Azure | Microsoft Docs
description: Familiarisez-vous avec les affectations de refus du contrôle d'accès en fonction du rôle (RBAC) pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165982"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Comprendre les affectations de refus relatives aux ressources Azure

À l’instar d’une attribution de rôle, une *affectation de refus* associe un ensemble d’actions de refus à un utilisateur, un groupe ou un principal de service, sur une étendue spécifique, afin de refuser l’accès. Les affectations de refus empêchent les utilisateurs d'effectuer des actions particulières sur les ressources Azure, même si une attribution de rôle leur confère un accès.

Cet article explique comment définir des attributions de refus.

## <a name="how-deny-assignments-are-created"></a>Création des affectations de refus

Les affectations de refus sont créées et gérées par Azure pour protéger les ressources. Par exemple, Azure Blueprints et les applications managées Azure utilisent les affectations de refus pour protéger les ressources managées par le système. Pour plus d’informations, consultez [Protéger les nouvelles ressources avec des verrous de ressources Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Comparer les attributions de rôles et les affectations de refus

Les affectations de refus suivent un modèle similaire à celui des affectations de rôles, mais présentent également certaines différences.

| Fonctionnalité | Attribution de rôle | Affectation de refus |
| --- | --- | --- |
| Accorder l'accès | :heavy_check_mark: |  |
| Accès refusé |  | :heavy_check_mark: |
| Peut être créé directement | :heavy_check_mark: |  |
| Appliquer à une étendue | :heavy_check_mark: | :heavy_check_mark: |
| Exclure les principaux |  | :heavy_check_mark: |
| Empêcher l’héritage pour les étendues enfants |  | :heavy_check_mark: |
| S’applique aux affectations d’[administrateurs d’abonnements classiques](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propriétés des attributions de refus

 Une attribution de refus possède les propriétés suivantes :

> [!div class="mx-tableFixed"]
> | Propriété | Obligatoire | Type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | OUI | Chaîne | Nom d'affichage de l’attribution de refus. Les noms doivent être uniques pour une étendue donnée. |
> | `Description` | Non | Chaîne | Description de l’attribution de refus. |
> | `Permissions.Actions` | Au moins un élément Actions ou DataActions | String[] | Tableau de chaînes qui spécifient les opérations de gestion auxquelles l’attribution de refus bloque l’accès. |
> | `Permissions.NotActions` | Non | String[] | Tableau de chaînes qui spécifient les opérations de gestion à exclure de l’attribution de refus. |
> | `Permissions.DataActions` | Au moins un élément Actions ou DataActions | String[] | Tableau de chaînes qui spécifient les opérations de données auxquelles l’attribution de refus bloque l’accès. |
> | `Permissions.NotDataActions` | Non | String[] | Tableau de chaînes qui spécifient les opérations de données à exclure de l’attribution de refus. |
> | `Scope` | Non | Chaîne | Chaîne qui spécifie l’étendue à laquelle l’attribution de refus s’applique. |
> | `DoNotApplyToChildScopes` | Non | Boolean | Spécifie si l’attribution de refus s’applique aux étendues enfants. La valeur par défaut est false. |
> | `Principals[i].Id` | OUI | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe, principal de service ou identité managée) auxquels s’applique l’affectation de refus. Définie sur un GUID vide `00000000-0000-0000-0000-000000000000` pour représenter tous les principaux. |
> | `Principals[i].Type` | Non | String[] | Tableau de types d’objet représentés par Principals[i].Id. Définie sur `SystemDefined` pour représenter tous les principaux. |
> | `ExcludePrincipals[i].Id` | Non | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe, principal de service ou identité managée) auxquels l’attribution de refus ne s’applique pas. |
> | `ExcludePrincipals[i].Type` | Non | String[] | Tableau de types d’objet représentés par ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Non | Boolean | Spécifie si cette attribution de refus a été créée par Azure et ne peut pas être modifiée ou supprimée. Actuellement, toutes les attributions de refus sont protégées par le système. |

## <a name="the-all-principals-principal"></a>Le principal Tous les principaux

Un principal défini par le système nommé *Tous les principaux* a été introduit pour prendre en charge les affectations de refus. Ce principal représente tous les utilisateurs, groupes, principaux de service et identités managées figurant dans un annuaire Azure AD. Si l’ID du principal est un GUID nul `00000000-0000-0000-0000-000000000000`, et le type de principal `SystemDefined`, le principal représente tous les principaux. Dans la sortie d’Azure PowerShell, Tous les principaux se présente comme suit :

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Tous les principaux peut être combiné avec `ExcludePrincipals` pour refuser tous les principaux, à l’exception de certains utilisateurs. Tous les principaux présente les contraintes suivantes :

- Il peut être utilisé uniquement dans `Principals` et ne peut pas être utilisé dans `ExcludePrincipals`.
- `Principals[i].Type` doit être défini sur `SystemDefined`.

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les affectations de refus pour les ressources Azure à l’aide du portail Azure](deny-assignments-portal.md)
* [Comprendre les définitions de rôle relatives aux ressources Azure](role-definitions.md)
