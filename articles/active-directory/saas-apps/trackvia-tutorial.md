---
title: 'Didacticiel : Intégration d’Azure Active Directory à TrackVia | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TrackVia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7010023-bdda-4a19-a335-19904e75b813
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e4c90b6f9fd8b968ceb0e241649ddbcf1c2e1cb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189110"
---
# <a name="tutorial-azure-active-directory-integration-with-trackvia"></a>Didacticiel : Intégration d’Azure Active Directory à TrackVia

L’objectif de ce didacticiel est de vous apprendre à intégrer TrackVia à Azure Active Directory (Azure AD).

L’intégration de TrackVia à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TrackVia.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à TrackVia (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à TrackVia, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TrackVia pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TrackVia à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-trackvia-from-the-gallery"></a>Ajout de TrackVia à partir de la galerie
Pour configurer l’intégration de TrackVia avec Azure AD, vous devez ajouter TrackVia, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter TrackVia à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **TrackVia**, sélectionnez **TrackVia** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![TrackVia dans la liste des résultats](./media/trackvia-tutorial/tutorial_trackvia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TrackVia avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TrackVia équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur TrackVia associé doit être établie.

Dans TrackVia, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TrackVia, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer utilisateur de test TrackVia](#create-a-trackvia-test-user)** pour avoir un équivalent de Britta Simon dans TrackVia lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TrackVia.

**Pour configurer l’authentification unique Azure AD avec TrackVia, effectuez les étapes suivantes :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **TrackVia**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/trackvia-tutorial/tutorial_trackvia_samlbase.png)

1. Dans la section **Domaines et URL TrackVia**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL TrackVia](./media/trackvia-tutorial/tutorial_trackvia_url.png)

    Dans la zone de texte **Identificateur**, entrez la valeur : `TrackVia`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL TrackVia](./media/trackvia-tutorial/tutorial_trackvia_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://companyname.trackvia.com`
     
    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez [l’équipe du support client TrackVia](mailto:support@trackvia.com).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/trackvia-tutorial/tutorial_trackvia_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/trackvia-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de TrackVia**, cliquez sur **Configurer TrackVia** pour ouvrir la fenêtre **Configurer la connexion**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de TrackVia](./media/trackvia-tutorial/tutorial_trackvia_configure.png)
    
1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise TrackVia en tant qu’administrateur.

1. Cliquez sur les paramètres **Mon compte** de TrackVia, puis sélectionnez l’onglet **Authentification unique** et suivez les étapes ci-dessous :

    ![Configuration de TrackVia](./media/trackvia-tutorial/configure1.png)

    a. Dans la zone de texte **ID d’entité du fournisseur d’identité**, collez la valeur **ID d’entité SAML** copiée sur le Portail Azure.

    b. Sélectionnez **Choisir un fichier** pour charger le fichier de métadonnées que vous avez téléchargé sur le Portail Azure.

    c. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/trackvia-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/trackvia-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/trackvia-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/trackvia-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-trackvia-test-user"></a>Créer un utilisateur de test TrackVia

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans TrackVia. TrackVia prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé au moment d’une tentative d’accès à TrackVia.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support TrackVia](mailto:support@trackvia.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TrackVia.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à TrackVia, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **TrackVia**.

    ![Lien TrackVia dans la liste des applications](./media/trackvia-tutorial/tutorial_trackvia_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette TrackVia dans le volet d’accès, vous devez être connecté automatiquement à votre application TrackVia.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trackvia-tutorial/tutorial_general_01.png
[2]: ./media/trackvia-tutorial/tutorial_general_02.png
[3]: ./media/trackvia-tutorial/tutorial_general_03.png
[4]: ./media/trackvia-tutorial/tutorial_general_04.png

[100]: ./media/trackvia-tutorial/tutorial_general_100.png

[200]: ./media/trackvia-tutorial/tutorial_general_200.png
[201]: ./media/trackvia-tutorial/tutorial_general_201.png
[202]: ./media/trackvia-tutorial/tutorial_general_202.png
[203]: ./media/trackvia-tutorial/tutorial_general_203.png
