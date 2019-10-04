---
title: Créer une unité d’organisation (UO) dans Azure AD Domain Services | Microsoft Docs
description: Découvrez comment créer et gérer une unité d’organisation (UO) personnalisée dans un domaine managé Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: iainfou
ms.openlocfilehash: a3f9ad20e4bfba6e0bb858c82ccce73bb687a826
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613090"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Créer une unité d’organisation (UO) sur un domaine dans un domaine managé Azure AD Domain Services

Les unités d’organisation (UO) dans Active Directory Domain Services (AD DS) vous permettent de regrouper logiquement des objets tels que des comptes d’utilisateur, des comptes de service ou des comptes d’ordinateur. Vous pouvez ensuite affecter des administrateurs à des unités d’organisation spécifiques et appliquer une stratégie de groupe, donc des paramètres de configuration ciblés.

Les domaines managés Azure AD DS incluent deux unités d’organisation intégrées : *ordinateurs AADDC* et *utilisateurs AADDC*. L’unité d’organisation *Ordinateurs AADDC* contient des objets ordinateur associés à tous les ordinateurs qui sont joints au domaine managé. L’unité d’organisation *Utilisateurs AADDC* comprend les utilisateurs et les groupes qui y sont synchronisés du locataire Azure AD. Lors de la création et de l’exécution des charges de travail utilisant Azure AD DS, vous devrez peut-être créer des comptes de service pour que les applications s’authentifient elles-mêmes. Pour organiser ces comptes de service, vous créez souvent une unité d’organisation personnalisée dans le domaine managé Azure AD DS, puis des comptes de service au sein de cette unité d’organisation.

Cet article vous explique comment créer une unité d’organisation dans votre domaine managé Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Avant de commencer

Pour faire ce qui est décrit dans cet article, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, suivez le tutoriel pour [créer et configurer une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Une machine virtuelle de gestuib Windows Server jointe au domaine managé Azure AD DS.
    * Si nécessaire, suivez le tutoriel pour [créer une machine virtuelle de gestion][tutorial-create-management-vm].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considérations et limitations relatives aux unités d’organisation personnalisées

Lorsque vous créez des unités d’organisation personnalisées dans un domaine managé Azure AD DS, vous bénéficiez d’une plus grande souplesse de gestion pour la gestion des utilisateurs et l’application de la stratégie de groupe. Par rapport à un environnement AD DS local, il existe certaines limitations et considérations à prendre en compte lors de la création et de la gestion d’une structure d’unité d’organisation personnalisée dans Azure AD DS :

* Pour créer des unités d’organisation personnalisées, les utilisateurs doivent être membres du groupe *Administrateurs AAD DC*.
* Un utilisateur qui crée une unité d’organisation personnalisée se voit accorder des privilèges d’administration (contrôle total) sur cette unité d’organisation et est le propriétaire de la ressource.
    * Par défaut, le groupe *Administrateurs AAD DC* a également le contrôle total sur l’unité d’organisation personnalisée.
* Une unité d’organisation pour *Utilisateurs AADDC* est créée et contient les comptes d’utilisateur synchronisés à partir de votre locataire Azure AD.
    * Vous ne pouvez pas déplacer des utilisateurs ou des groupes de l’unité d’organisation *Utilisateurs AADDC* vers des unités d’organisation personnalisées que vous créez. Seuls les comptes d’utilisateurs ou les ressources créés dans le domaine managé Azure AD DS peuvent être déplacés dans des unités d’organisation personnalisées.
* Les comptes d’utilisateur, groupes, comptes de service et objets ordinateur que vous créez dans des unités d’organisation personnalisées ne sont pas disponibles dans votre locataire Azure AD.
    * Ces objets n’apparaissent pas à l’aide de l’API Graph Azure AD ou dans l’interface utilisateur Azure AD ; ils sont uniquement disponibles dans votre domaine managé Azure AD DS.

## <a name="create-a-custom-ou"></a>Créer une unité d’organisation personnalisée

Pour créer une unité d’organisation personnalisée, vous utilisez les outils d’administration Active Directory à partir d’une machine virtuelle jointe à un domaine. Le Centre d’administration Active Directory vous permet d’afficher, de modifier et de créer des ressources dans un domaine managé Azure AD DS, y compris des unités d’organisation.

> [!NOTE]
> Pour créer une unité d’organisation personnalisée dans un domaine managé Azure AD DS, vous devez être connecté à un compte d’utilisateur membre du groupe d *administrateurs du contrôleur de domaine AAD*.

1. Dans l’écran d’accueil, sélectionnez **Outils d’administration**. Une liste des outils de gestion disponibles est présentée dans le didacticiel pour [créer une machine virtuelle de gestion][tutorial-create-management-vm].
1. Pour créer et gérer des unités d'organisation, sélectionnez **Centre d’administration Active Directory** dans la liste des outils d’administration.
1. Dans le volet gauche, choisissez votre domaine managé Azure AD DS, par exemple *contoso.com*. Une liste des unités d’organisation et des ressources s’affiche :

    ![Sélectionnez votre domaine managé Azure AD DS dans le Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Le volet **Tâches** s’affiche sur le côté droit du Centre d’administration Active Directory. Sous le domaine, par exemple *contoso.com*, sélectionnez **Nouveau > Unité d’organisation**.

    ![Sélectionnez l’option qui vous permettra de créer une nouvelle unité d’organisation dans le Centre d'administration Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. Dans la boîte de dialogue **Créer une unité d’organisation**, indiquez un **Nom** pour la nouvelle unité d’organisation, par exemple *MyCustomOu*. Fournissez une brève description de l’unité d’organisation, telle qu’*unité d’organisation personnalisée pour les comptes de service*. Si vous le souhaitez, vous pouvez également renseigner le champ **Managée par** de l’unité d’organisation. Sélectionnez **OK**pour créer l’unité d’organisation personnalisée.

    ![Créez une unité d’organisation personnalisée à partir du Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. De retour dans le Centre d’administration Active Directory, l’unité d’organisation personnalisée est désormais listée et peut être utilisée :

    ![Unité d’organisation personnalisée disponible dans le Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des outils d’administration ou la création et l’utilisation de comptes de service, consultez les articles suivants :

* [Centre d'administration Active Directory : Prise en main](https://technet.microsoft.com/library/dd560651.aspx)
* [Guide pas à pas des comptes de service (éventuellement en anglais)](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
