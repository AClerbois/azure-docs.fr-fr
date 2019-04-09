---
title: 'Didacticiel : Configurer BlueJeans pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour attribuer et retirer automatiquement des utilisateurs dans BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fedb372fb245b7bc35cb440bd758336ab2a68
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057623"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Didacticiel : Configurer BlueJeans pour le provisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à suivre dans BlueJeans et Azure Active Directory (Azure AD) permettant de configurer Azure AD pour l’attribution et le retrait automatiques d’utilisateurs et/ou de groupes sur BlueJeans.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* un locataire Azure AD ;
* Un locataire BlueJeans pour lequel est activé le plan [My Company](https://www.BlueJeans.com/pricing) (Ma société), ou niveau de plan plus élevé
* Un compte d’utilisateur dans BlueJeans avec des autorisations d’administration

> [!NOTE]
> L’intégration de l’attribution d’utilisateurs Azure AD repose sur [l’API BlueJeans](https://BlueJeans.github.io/developer), qui est mise à la disposition des équipes BlueJeans qui relèvent au moins du plan standard.

## <a name="adding-bluejeans-from-the-gallery"></a>Ajout de BlueJeans à partir de la galerie

Avant de configurer BlueJeans pour l’attribution automatique des utilisateurs avec Azure AD, vous devez ajouter BlueJeans à partir de la galerie d’applications Azure AD, à votre liste d’applications SaaS managées.

**Pour ajouter BlueJeans à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le  **[Azure portal](https://portal.azure.com)**, dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, sélectionnez le **nouvelle application** bouton en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **BlueJeans**, sélectionnez **BlueJeans** dans le volet de résultats, puis sélectionnez le **ajouter** pour ajouter l’application.

    ![BlueJeans dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Attribution d’utilisateurs dans BlueJeans

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à BlueJeans. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à votre application BlueJeans en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Conseils importants pour l’attribution d’utilisateurs à BlueJeans

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à BlueJeans pour tester la configuration de l’attribution automatique des utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à BlueJeans, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configuration de l’attribution automatique d’utilisateurs dans BlueJeans

Cette section vous guide tout au long des étapes de configuration du service d’attribution d’utilisateurs d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans BlueJeans en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour BlueJeans en suivant les instructions fournies dans le [tutoriel sur l’authentification unique BlueJeans](bluejeans-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour BlueJeans dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **BlueJeans**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Lien BlueJeans dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur** et le **Mot de passe d’administrateur** de votre compte BlueJeans. Voici des exemples de valeurs :

   * Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur de votre locataire BlueJeans. Exemple : admin@contoso.com.

   * Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe correspondant au nom de l’utilisateur administrateur.

6. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à BlueJeans. Si la connexion échoue, vérifiez que votre compte BlueJeans dispose des autorisations d’administrateur, puis réessayez.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec BlueJeans**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et BlueJeans. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur BlueJeans en vue de mises à jour ultérieures. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pour activer le service d’attribution d’utilisateurs Azure AD pour BlueJeans, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Définissez les utilisateurs et/ou groupes que vous aimeriez attribuer dans BlueJeans en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Attribution d’utilisateurs dans BlueJeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’attribution d’utilisateurs, qui décrivent toutes les actions effectuées par le service d’attribution d’utilisateurs Azure AD dans BlueJeans.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* BlueJeans n’autorise pas les noms d’utilisateur de plus de 30 caractères.

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
