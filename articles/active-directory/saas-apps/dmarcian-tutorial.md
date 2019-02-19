---
title: 'Didacticiel : Intégration d’Azure Active Directory à dmarcian | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4768c38b2e76bfa8cdff3187c32c03fdaaf57bbf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207651"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Tutoriel : Intégration d’Azure Active Directory à dmarcian

Dans ce didacticiel, vous allez apprendre à intégrer dmarcian à Azure Active Directory (Azure AD).
L’intégration de dmarcian à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à dmarcian.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à dmarcian (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à dmarcian, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement dmarcian pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* dmarcian prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**

## <a name="adding-dmarcian-from-the-gallery"></a>Ajout de dmarcian à partir de la galerie

Pour configurer l’intégration de dmarcian à Azure AD, vous devez ajouter dmarcian à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter dmarcian à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **dmarcian**, sélectionnez **dmarcian** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![dmarcian dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec dmarcian à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur dmarcian associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec dmarcian, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique dmarcian](#configure-dmarcian-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test dmarcian](#create-dmarcian-test-user)** pour avoir un équivalent de Britta Simon dans dmarcian lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec dmarcian, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **dmarcian**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans dmarcian Domain and URLs (Domaine et URL dmarcian)](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans dmarcian Domain and URLs (Domaine et URL dmarcian)](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL d’authentification**, tapez une URL au format suivant :
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel. 

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Configurer l’authentification unique dmarcian

1. Dans une autre fenêtre de navigateur web, connectez-vous à dmarcian en tant qu’administrateur de la sécurité.

2. Cliquez sur **Profile** en haut à droite et accédez à **Preferences**.

    ![Les préférences ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Faites défiler, puis cliquez sur la section **Authentification unique** et sur **Configurer**.

    ![Authentification unique ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Sur la page **Authentification unique SAML**, définissez **État** sur **Activé** et suivez les étapes ci-après :

    ![L’authentification ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Sous la section **Add dmarcian to your Identity Provider** (Ajouter dmarcian à votre fournisseur d’identité), cliquez sur **COPIER** pour copier l’**URL du service consommateur d’assertion** de votre instance et collez-la dans la zone de texte **URL de réponse** dans la section **Configuration SAML de base** du portail Azure.

    * Sous la section **Add dmarcian to your Identity Provider** (Ajouter dmarcian à votre fournisseur d’identité), cliquez sur **COPIER** pour copier l’**ID d’entité** de votre instance et collez-la dans la zone de texte **Identificateur** dans la section **Configuration SAML de base** du portail Azure.

    * Sous la section **Set up Authentication** (Configurer l’authentification), dans la zone de texte **Identity Provider Metadata** (Métadonnées du fournisseur d’identité), collez **l’URL des métadonnées de fédération d’application**, que vous avez copiée à partir du Portail Azure.

    * Sous la section **Set up Authentication** (Configurer l’authentification), dans la zone de texte **Attribute Statements** (Instructions d’attribut), collez l’URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * Sous la section **Set up Login URL** (Configurer l’URL de connexion), copiez l’**URL de connexion** de votre instance et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

        > [!Note]
        > Vous pouvez modifier l’**URL de connexion** en fonction de votre organisation.

    * Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à dmarcian.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **dmarcian**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **dmarcian**.

    ![Lien dmarcian dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-dmarcian-test-user"></a>Créer un utilisateur de test dmarcian

Pour se connecter à dmarcian, les utilisateurs Azure AD doivent être approvisionnés dans dmarcian. Dans dmarcian, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à dmarcian en tant qu’administrateur de la sécurité.

2. Cliquez sur **Profile** en haut à droite et accédez à **Manage Users** (Gérer les utilisateurs).

    ![L’utilisateur ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Sur le côté droit de la section **SSO Users** (Utilisateurs SSO), cliquez sur **Ajouter un nouvel utilisateur**.

    ![L’utilisateur ajouté ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Dans la fenêtre contextuelle **Ajouter un nouvel utilisateur**, procédez comme suit :

    ![Le nouvel utilisateur ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Dans la zone de texte **Nouvel e-mail d’utilisateur**, entrez l’adresse de messagerie de l’utilisateur, par exemple **brittasimon@contoso.com**.

    b. Si vous souhaitez accorder des droits d’administrateur à l’utilisateur, sélectionnez **Make User an Admin** (Rendre l’utilisateur administrateur).

    c. Cliquez sur **Add User**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette dmarcian dans le volet d’accès, vous devez être connecté automatiquement à l’application dmarcian pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

