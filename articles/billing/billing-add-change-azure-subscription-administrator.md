---
title: "Ajout ou modification de rôles d’abonnement administrateur Azure | Microsoft Docs"
description: "Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 38742fd84dafc3fb68829a55ab7be0827ad0b451
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services

Vous pouvez modifier l’administrateur Azure qui gère votre abonnement Azure ou les services Azure utilisés dans votre abonnement. Pour afficher les informations de facturation Azure et gérer les abonnements, vous devez vous connecter au Centre des comptes en tant qu’administrateur de compte. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Ajouter un administrateur RBAC propriétaire pour un abonnement dans le portail Azure 

Pour ajouter un utilisateur en tant qu’administrateur d’un abonnement dans le portail Azure, nous vous recommandons de lui donner un rôle [RBAC](../active-directory/role-based-access-control-configure.md) Propriétaire. Le rôle Propriétaire a la possibilité de gérer les ressources de l’abonnement que vous avez affecté sans avoir de privilèges d’accès à d’autres abonnements. Les propriétaires ajoutés au moyen du [portail Azure](https://portal.azure.com) ne peuvent pas gérer les ressources dans le [portail Azure Classic](https://manage.windowsazure.com).

1. Connectez-vous à la [vue Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement auquel vous voulez que l’administrateur accède.
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu.
1. Sélectionnez **Ajouter** > **Rôle** > **Propriétaire**. Entrez l’adresse e-mail de l’utilisateur à ajouter en tant que propriétaire, sélectionnez l’utilisateur, puis choisissez **Enregistrer**.

    ![Capture d’écran montrant le rôle Propriétaire sélectionné](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Ajouter ou modifier un coadministrateur

Seul un propriétaire peut être ajouté en tant que coadministrateur. Les autres utilisateurs dont les rôles sont Contributeur et Lecteur ne peuvent pas être ajoutés en tant que coadministrateurs.

1. Si vous ne l’avez pas déjà fait, ajoutez un utilisateur en tant que propriétaire en suivant les instructions ci-dessus.
2. **Cliquez avec le bouton droit** sur l’utilisateur Propriétaire que vous venez d’ajouter, puis sélectionnez **Ajouter comme coadministrateur**. Si vous ne voyez pas l’option **Ajouter comme coadministrateur**, actualisez la page ou essayez avec un autre navigateur Internet. 

     ![Capture d’écran d’ajout d’un coadministrateur](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Vous devez ajouter le compte « Propriétaire » en tant que coadministrateur si l’utilisateur a besoin de gérer les services Azure via le [portail Azure Classic](https://manage.windowsazure.com/).

    Pour supprimer l’autorisation de coadministrateur, **cliquez avec le bouton droit** sur l’utilisateur « coadministrateur », puis sélectionnez **Supprimer le coadministrateur**.

    ![Capture d’écran de suppression de coadministrateur](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modifier l’administrateur de services fédérés d’un abonnement Azure

Seul l’administrateur de compte peut modifier l’administrateur de services fédérés d’un abonnement. Par défaut, au moment de l’inscription, l’administrateur de services est le même que l’administrateur de compte.

1. Vérifiez que votre scénario est pris en charge en vérifiant les [limites de modification des administrateurs de services fédérés](#limits).
1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez un abonnement.
1. Sur le côté droit, sélectionnez **Modifier les détails de l’abonnement**.

    ![Capture d’écran montrant le bouton Modifier l’abonnement dans le Centre des comptes](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de services fédérés.

    ![Capture d’écran montrant la zone de modification de l’adresse e-mail de l’administrateur de services fédérés](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limites de modification des administrateurs de services fédérés

* Chaque abonnement est associé à un annuaire Azure AD. Pour identifier l’annuaire auquel est associé l’abonnement, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/), sélectionnez **Paramètres** > **Abonnements**. Vérifiez l’ID d’abonnement pour trouver l’annuaire.
* Si vous êtes connecté avec un compte professionnel ou scolaire, vous pouvez ajouter d’autres comptes à votre organisation en tant qu’administrateur de services fédérés. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services fédérés, mais ne peut pas ajouter john@notcontoso.com, sauf si john@notcontoso.com est présent dans l’annuaire de contoso.com. Les utilisateurs connectés avec des comptes professionnels ou scolaires peuvent continuer à ajouter des utilisateurs de comptes Microsoft en tant qu’administrateur de services fédérés.

  | Méthode de connexion | Ajouter l’utilisateur de compte Microsoft comme administrateur de services fédérés ? | Ajouter le compte professionnel ou scolaire de la même organisation comme administrateur de services fédérés ? | Ajouter le compte professionnel ou scolaire d’une autre organisation comme administrateur de services fédérés ? |
  | --- | --- | --- | --- |
  |  Compte Microsoft |Oui |Non |Non |
  |  Compte professionnel ou scolaire |Oui |Oui |Non |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modifier l’administrateur de compte d’un abonnement Azure

Pour modifier l’administrateur de compte d’un abonnement, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Besoin d’identifier l’administrateur de compte ?** Procédez comme suit :

1. Connectez-vous à la [vue Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .  

## <a name="types-of-azure-admin-accounts"></a>Types de comptes d’administrateur Azure

 Les trois types de rôles Administrateur dans Microsoft Azure sont Administrateur de compte, Administrateur de services fédérés et Coadministrateur. Le tableau suivant décrit la différence entre ces trois rôles Administrateur.

| Rôle administratif | Limite | Description |
| --- | --- | --- |
| Administrateur de compte |1 par compte Azure |Il s’agit de la personne qui a souscrit ou acheté des abonnements Azure et qui est autorisée à accéder au [Centre des comptes](https://account.azure.com/Subscriptions) et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer et d’annuler des abonnements, de modifier la facturation d’un abonnement et de modifier l’administrateur de services fédérés. |
| Administrateur de services fédérés |1 par abonnement Azure |Ce rôle est autorisé à gérer les services sur le [portail Azure](https://portal.azure.com). Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services fédérés. |
| Coadministrateur (CA) dans le [portail Azure Classic](https://manage.windowsazure.com) |200 par abonnement |Ce rôle possède les mêmes privilèges d’accès que l’administrateur de services fédérés, à ceci près qu’il ne peut pas modifier la manière dont les abonnements sont associés aux répertoires Azure. |

Le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory permet d’ajouter les utilisateurs à plusieurs rôles. Pour plus d’informations, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>En savoir plus sur le contrôle d’accès aux ressources et Active Directory

* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, consultez la page[Présentation de l’accès aux ressources dans Azure](../active-directory/active-directory-understanding-resource-access.md).
* Pour plus d’informations sur Azure Active Directory, consultez les pages [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) et [Affecter des rôles Administrateur dans Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

