---
title: Présentation du contrôle d’accès en fonction du rôle dans Azure Digital Twins | Microsoft Docs
description: Découvrez l’authentification dans Digital Twins avec le contrôle d’accès en fonction du rôle.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyhughes
ms.openlocfilehash: 33e09ad52722665e6162b18159012d69ec1463bd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849277"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Contrôle d’accès en fonction du rôle dans Azure Digital Twins

Azure Digital Twins permet un contrôle d’accès précis à des données, ressources et actions spécifiques de votre graphe spatial. En effet, il utilise une gestion granulaire des rôles et des autorisations appelée contrôle d’accès en fonction du rôle. RBAC se compose de _rôles_ et d’_attributions de rôles_. Les rôles identifient le niveau d’autorisations. Les attributions de rôles associent un rôle à un utilisateur ou un appareil.

À l’aide de RBAC, l’autorisation peut être accordée à :

- Un utilisateur.
- Un appareil.
- Un principal du service.
- Une fonction définie par l’utilisateur.
- Tous les utilisateurs qui appartiennent à un domaine.
- Un locataire.

Le degré d’accès peut également être ajusté.

RBAC est unique, car les autorisations sont héritées vers le bas du graphe spatial.

## <a name="what-can-i-do-with-rbac"></a>Que puis-je faire avec le contrôle d’accès en fonction du rôle (RBAC) ?

Un développeur peut utiliser RBAC pour effectuer les opérations suivantes :

- Accorder à un utilisateur la possibilité de gérer les appareils pour un bâtiment entier ou uniquement pour une pièce ou un étage spécifique.
- Octroyer à un administrateur l’accès global à tous les nœuds d’un graphe spatial ou uniquement à une section du graphe.
- Accorder à un spécialiste du support un accès en lecture au graphe, à l’exception des clés d’accès.
- Accorder à chaque membre d’un domaine un accès en lecture à tous les objets du graphe.

## <a name="rbac-best-practices"></a>Meilleures pratiques relatives à RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>contrôleur

### <a name="role-definitions"></a>Définitions de rôles

Une définition de rôle est une collection d’autorisations et d’autres attributs qui constituent un rôle. Une définition de rôle répertorie les opérations autorisées, notamment *CRÉER*, *LIRE*, *METTRE À JOUR*, et *SUPPRIMER* que tous les objets avec ce rôle peuvent effectuer. Elle spécifie également les types d’objets auxquels ces autorisations s’appliquent.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Pour récupérer les définitions complètes des rôles précédents, interrogez l’API système/rôles.
> Pour en savoir plus, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Types d’identificateur d’objet

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Découvrez comment accorder des autorisations au principal de service en consultant [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md#grant).

Les articles de documentation de référence suivants décrivent :

- Comment [rechercher l’ID d’objet d’un utilisateur](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Comment [obtenir l’ID d’objet d’un principal de service](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Comment [récupérer l’ID d’objet d’un locataire Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Affectations de rôles

Une attribution de rôle Azure Digital Twins associe un objet, tels qu’un utilisateur ou un locataire Azure AD, à un rôle et un espace. Les autorisations sont accordées à tous les objets qui appartiennent à cet espace. L’espace inclut la totalité du graphe spatial en dessous.

Par exemple, un utilisateur reçoit une attribution de rôle avec le rôle `DeviceInstaller` pour le nœud racine d’un graphe spatial, qui représente un bâtiment. L’utilisateur peut alors lire et mettre à jour les appareils pour ce nœud et tous les autres espaces enfants dans le bâtiment.

Pour accorder des autorisations à un destinataire, créez une attribution de rôle. Pour révoquer les autorisations, supprimez l’attribution de rôle.

>[!IMPORTANT]
> Pour en savoir plus sur les attributions de rôles, consultez [Créer et gérer des attributions de rôles](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création et la gestion des attributions de rôle Azure Digital Twins, consultez [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).
