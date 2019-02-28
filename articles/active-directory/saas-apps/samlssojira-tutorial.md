---
title: 'Tutoriel : Intégration d’Azure Active Directory à SSO SAML pour Jira de resolution GmbH | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSO SAML pour Jira de resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434a22c56e0ac6476138ef258e7d83a2bfecb63f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871245"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutoriel : Intégration d’Azure Active Directory à SSO SAML pour Jira de resolution GmbH

Dans ce didacticiel, vous allez apprendre à intégrer SSO SAML pour Jira de resolution GmbH avec Azure Active Directory (Azure AD).
L’intégration de SSO SAML pour Jira de resolution GmbH avec Azure AD offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à SSO SAML pour Jira de resolution GmbH.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à SSO SAML pour Jira de resolution GmbH (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SSO SAML pour Jira de resolution GmbH, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement SSO SAML pour Jira de resolution GmbH pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SSO SAML pour Jira de resolution GmbH prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Ajout de SSO SAML pour Jira de resolution GmbH à partir de la galerie

Pour configurer l’intégration de SSO SAML pour Jira de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **SSO SAML pour Jira de resolution GmbH**, sélectionnez **SSO SAML pour Jira de resolution GmbH** dans le volet de résultats, puis cliquez sur **Ajouter** pour ajouter l’application.

     ![SSO SAML pour Jira de resolution GmbH dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, en tirant parti d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, un lien doit être établi entre un utilisateur Azure AD et l’utilisateur de SSO SAML pour Jira de resolution GmbH associé.

Pour configurer et tester l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique SSO SAML pour Jira de resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test de SSO SAML pour Jira de resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** pour avoir un équivalent de Britta Simon dans SSO SAML pour Jira de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **SSO SAML pour Jira de resolution GmbH**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité**, effectuez les étapes suivantes :

    ![Informations d’authentification unique pour le domaine et les URL de SSO SAML pour Jira de resolution GmbH](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    c. Cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode initié par le **fournisseur de services** :

    ![Informations d’authentification unique pour le domaine et les URL de SSO SAML pour Jira de resolution GmbH](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de SSO SAML pour Jira de resolution GmbH](https://www.resolution.de/go/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer SSO SAML pour Jira de resolution GmbH**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>Configurer l’authentification unique SSO SAML pour Jira de resolution GmbH

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration de SSO SAML pour Jira de resolution GmbH** en tant qu’administrateur.

2. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.
    
    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon1.png)

3. Vous êtes redirigé vers la page d’accès administrateur. Entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon2.png)

4. Sous l’onglet Modules complémentaires, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires). Recherchez **SAML Single Sign On (SSO) for Jira**, puis cliquez sur le bouton **Install** pour installer le nouveau plug-in SAML.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon7.png)

5. L’installation du plug-in démarre. Cliquez sur **Fermer**.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon8.png)

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon9.png)

6.  Cliquez sur **Gérer**.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon10.png)
    
8. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon11.png)

9. Dans la page **SAML SingleSignOn Plugin Configuration** (Configuration du plug-in d’authentification unique SAML), cliquez sur le bouton **Add new IdP** (Ajouter un nouveau fournisseur d’identité) pour configurer les paramètres du fournisseur d’identité.

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon4.png)

10. Dans la page **Choose your SAML Identity Provider** (Choisissez votre fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon5a.png)
 
    a. Définissez **Azure AD** comme type de fournisseurs d’identité.
    
    b. Ajoutez le **Nom** du fournisseur d’identité (p. ex. Azure AD).
    
    c. Ajoutez la **Description** du fournisseur d’identité (p. ex. Azure AD).
    
    d. Cliquez sur **Suivant**.
    
11. Dans la page **Identity provider configuration** (Configuration du fournisseur d’identité), cliquez sur le bouton **Next** (Suivant).

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon5b.png)

12. Dans la page **Import SAML IdP Metadata** (Importer les métadonnées du fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon5c.png)

    a. Cliquez sur le bouton **Load File** (Charger le fichier) et choisissez le fichier XML de métadonnées que vous avez téléchargé à l’étape 5.

    b. Cliquez sur le bouton **Import** (Importer).
    
    c. Patientez quelques instants jusqu’à la fin de l’importation.
    
    d. Cliquez sur le bouton **Next** (Suivant).
    
13. Dans la page **User ID attribute and transformation** (Attribut d’ID d’utilisateur et transformation), cliquez sur le bouton **Next** (Suivant).

    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon5d.png)
    
14. Dans la page **User creation and update** (Création et mise à jour de l’utilisateur), cliquez sur **Save & Next** (Enregistrer et suivant) pour enregistrer les paramètres.   
    
    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon6a.png)
    
15. Dans la page **Test your settings** (Tester vos paramètres), cliquez sur **Skip test & configure manually** (ignorer le test et configurer manuellement) afin d’ignorer le test de l’utilisateur pour l’instant. Cette opération, qui requiert certains paramètres dans le portail Azure, sera effectuée à la section suivante. 
    
    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon6b.png)
    
16. Dans la boîte de dialogue **Skipping the test means...** (Ignorer le test signifie...), cliquez sur **OK**.
    
    ![Configurer l'authentification unique](./media/samlssojira-tutorial/addon6c.png)

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

Dans cette section, vous activez Britta Simon pour utiliser l’authentification unique Azure en accordant l’accès à SSO SAML pour Jira de resolution GmbH.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **SSO SAML pour Jira de resolution GmbH**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **SSO SAML pour Jira de resolution GmbH**.

    ![Lien SSO SAML pour Jira de resolution GmbH dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Créer un utilisateur de test pour SSO SAML pour Jira de resolution GmbH

Pour permettre à des utilisateurs d’Azure AD de se connecter à SSO SAML pour Jira de resolution GmbH, vous devez configurer ceux-ci dans SSO SAML pour Jira de resolution GmbH.  
Dans SSO SAML pour Jira de resolution GmbH, cette configuration est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise SSO SAML pour Jira de resolution GmbH en tant qu’administrateur.

2. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Ajouter un employé](./media/samlssojira-tutorial/user1.png) 

3. Vous êtes redirigé vers la page d’accès administrateur dans laquelle vous entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Ajouter un employé](./media/samlssojira-tutorial/user2.png) 

4. Sous l’onglet **User management** (Gestion des utilisateurs), cliquez sur **Create user** (Créer un utilisateur).

    ![Ajouter un employé](./media/samlssojira-tutorial/user3.png) 

5. Dans la page de boîte de dialogue **Create New User** (Créer un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/samlssojira-tutorial/user4.png) 

    a. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    e. Cliquez sur **Create User** (Créer un utilisateur).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SSO SAML pour Jira de resolution GmbH dans le panneau d’accès, vous devez être automatiquement connecté à l’application SSO SAML pour Jira de resolution GmbH pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

