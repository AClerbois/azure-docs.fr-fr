---
title: Guide pratique pour gérer une identité managée affectée par l’utilisateur en utilisant le portail Azure
description: Instructions pas à pas pour créer, répertorier, supprimer et affecter un rôle à une identité managée affectée par l’utilisateur.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: a8fd6fedb83d61541746ea4204e8fcab3d3d7bab
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428747"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du Portail Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Les identités managées pour ressources Azure fournissent aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur en utilisant le Portail Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure pour créer l’identité managée affectée par l’utilisateur.
2. Dans la zone de recherche, saisissez *Identités managées*, puis sous **Services**, cliquez sur **Identités managées**.
3. Cliquez sur **Ajouter** et entrez des valeurs dans les champs suivants dans le volet d’identité **Créer une identité managée affectée par l’utilisateur** :
   - **Nom de la ressource** : Il s’agit du nom de votre identité managée affectée par l’utilisateur, par exemple UAI1.
   - **Abonnement**: Sélectionnez l’abonnement sous lequel créer l’identité managée affectée par l’utilisateur.
   - **Groupe de ressources** : Créez un groupe de ressources pour contenir votre identité managée affectée par l’utilisateur, ou choisissez **Utiliser l’existant** pour créer l’identité managée affectée par l’utilisateur dans un groupe de ressources existant.
   - **Emplacement** : Choisissez un emplacement où déployer l’identité managée affectée par l’utilisateur, par exemple **USA Ouest**.
4. Cliquez sur **Créer**.

![Créer une identité managée attribuée par l’utilisateur](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de répertorier les identités managées affectées par l’utilisateur.
2. Dans la zone de recherche, saisissez *Identités managées*, puis sous Services, cliquez sur **Identités managées**.
3. Une liste d’identités managées affectées par l’utilisateur pour votre abonnement est retournée.  Pour voir les détails d’une identité managée affectée à l’utilisateur, cliquez sur son nom.

![Afficher une identité managée affectée par l’utilisateur](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

La suppression d'une identité affectée par l'utilisateur ne la supprime pas de la machine virtuelle ou de la ressource à laquelle elle a été affectée.  Pour supprimer l'identité affectée par l'utilisateur d'une machine virtuelle, reportez-vous à [Supprimer une identité managée affectée par l'utilisateur sur une machine virtuelle](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de supprimer une identité managée affectée par l’utilisateur.
2. Sélectionnez l’identité managée affectée par l’utilisateur, puis cliquez sur **Supprimer**.
3. Sous la zone de confirmation, sélectionnez **Oui**.

![Supprimer une identité managée affectée par l’utilisateur](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Affecter un rôle à une identité managée affectée par l’utilisateur 

Pour affecter un rôle à une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte associé à l’abonnement Azure afin de répertorier les identités managées affectées par l’utilisateur.
2. Dans la zone de recherche, saisissez *Identités managées*, puis sous Services, cliquez sur **Identités managées**.
3. Une liste d’identités managées affectées par l’utilisateur pour votre abonnement est retournée.  Sélectionnez l’identité managée affectée par l’utilisateur à laquelle vous souhaitez affecter un rôle.
4. Sélectionnez **Contrôle d’accès (IAM)**, puis **Ajouter une attribution de rôle**.

   ![Début d’identité managée affectée par l’utilisateur](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Dans le panneau Ajouter une attribution de rôle, configurez les valeurs suivantes, puis cliquez sur **Enregistrer** :
   - **Rôle** : rôle à affecter
   - **Attribuer l’accès à** : ressource à laquelle affecter l’identité managée affectée par l’utilisateur
   - **Sélectionner** : membre auquel affecter l’accès
   
   ![Identité managée affectée par l’utilisateur IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  