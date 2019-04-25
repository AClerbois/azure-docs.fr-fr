---
title: 'Didacticiel : Configurer Bonusly pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour attribuer et supprimer automatiquement des comptes d’utilisateur dans Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad0ee590572dbc92e67be9f84ffc65afc3e8473
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60282186"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Didacticiel : Configurer Bonusly pour l’attribution automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Bonusly et Azure Active Directory (Azure AD) dans le but de configurer Azure AD pour l’attribution et la suppression automatiques d’utilisateurs et/ou de groupes dans Bonusly.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* un locataire Azure AD ;
* Un [locataire Bonusly](https://bonus.ly/pricing)
* Un compte d’utilisateur Bonusly avec des autorisations d’administrateur

> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur [l’API REST Bonusly](https://bonusly.gelato.io/reference), qui est disponible pour les développeurs Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Ajout de Bonusly à partir de la galerie

Avant de configurer Bonusly pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter Bonusly à votre liste d’applications SaaS managées, à partir de la galerie d’applications Azure AD.

**Pour ajouter Bonusly à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Bonusly**, sélectionnez **Bonusly** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Bonusly dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Attribution d’utilisateurs à Bonusly

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes Azure AD ont besoin d’accéder à Bonusly. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à votre application Bonusly en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Conseils importants pour l’attribution d’utilisateurs dans Bonusly

* Dans un premier temps, il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Bonusly afin de tester la configuration de l’attribution automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à Bonusly, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configuration de l’attribution automatique d’utilisateurs dans Bonusly

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Bonusly en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Bonusly en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Bonusly](bonus-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Si vous souhaitez configurer l’attribution automatique d’utilisateurs pour Bonusly dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Bonusly**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Bonusly**.

    ![Lien Bonusly dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez le **jeton secret** de votre compte Bonusly, comme expliqué à l’étape 6.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Le **jeton secret** de votre compte se trouve dans **Admin > Company > Integrations** (Admin > Société > Intégrations). Dans la section **If you want to code** (Si vous souhaitez programmer), cliquez sur **API > Create New API Access Token** (API > Créer un jeton d’accès d’API) pour créer un jeton secret.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Dans l’écran suivant, tapez un nom pour le jeton d’accès dans la zone de texte fournie, puis appuyez sur **Create Api Key** (Créer la clé d’API). Le nouveau jeton d’accès s’affiche pendant quelques secondes dans une fenêtre contextuelle.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/Token02.png)

8. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Bonusly. Si la connexion échoue, vérifiez que votre compte Bonusly dispose des autorisations d’administrateur, puis réessayez.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Cliquez sur **Enregistrer**.

11. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Bonusly**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Bonusly. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur Bonusly en vue de mises à jour ultérieures. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pour activer le service de provisionnement Azure AD pour Bonusly, définissez le paramètre **État de la configuration** sur **Activé** dans la section **Paramètres**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Définissez les utilisateurs et/ou groupes que vous souhaitez attribuer à Bonusly en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Attribution d’utilisateurs dans Bonusly](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD dans Bonusly.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
