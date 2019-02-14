---
title: 'Tutoriel : Intégration d’Azure Active Directory à QPrism | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 286af8b927f488acaf2877e753f6e4689c4b000f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199457"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Tutoriel : Intégration d’Azure Active Directory à QPrism

Dans ce didacticiel, vous allez apprendre à intégrer QPrism dans Azure Active Directory (Azure AD).

L’intégration de QPrism dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à QPrism.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à QPrism (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à QPrism, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement QPrism pour lequel l’authentification unique est activée

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de QPrism à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="add-qprism-from-the-gallery"></a>Ajouter QPrism à partir de la galerie
Pour configurer l’intégration de QPrism avec Azure AD, vous devez ajouter QPrism disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter QPrism à partir de la galerie :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter une application, en haut de la boîte de dialogue, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **QPrism**, puis sélectionnez **QPrism** dans le volet de résultats. Ensuite, pour ajouter l’application, cliquez sur **Ajouter**.

    ![QPrism dans la liste des résultats](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de QPrism avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur QPrism équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur QPrism associé doit être établie.

Pour établir cette relation, dans QPrism, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur**.

Pour configurer et tester l’authentification unique Azure AD avec QPrism, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
1. [Créer un utilisateur de test QPrism](#create-a-qprism-test-user) pour avoir dans QPrism un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. [Tester l’authentification unique](#test-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application QPrism.

1. Dans le portail Azure, dans la page d’intégration de l’application **QPrism**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, configurez **Mode** sur **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

1. Dans la section **Domaine et URL Absorb QPrism**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL QPrism](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<customer domain>.qmyzone.com/login`

    b. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support QPrism](mailto:qsupport-ce@quatrro.com). 

1. Dans la section  **Certificat de signature SAML** , cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

     ![Lien Téléchargement de certificat](./media/qprism-tutorial/tutorial_qprism_certificate.png)

1. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/qprism-tutorial/tutorial_general_400.png)
    
1. Pour configurer l’authentification unique côté **QPrism**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** à l’[équipe de support QPrism](mailto:qsupport-ce@quatrro.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD**

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/qprism-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/qprism-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/qprism-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/qprism-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-qprism-test-user"></a>Créer un utilisateur de test QPrism

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans QPrism. Pour ajouter des utilisateurs dans la plateforme QPrism, rapprochez-vous de [l’équipe de support de QPrism](mailto:qsupport-ce@quatrro.com) . Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à QPrism.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à QPrism :**

1. Dans le portail Azure, affichez les applications, puis accédez à la vue des répertoires. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **QPrism**.

    ![Lien QPrism dans la liste des applications](./media/qprism-tutorial/tutorial_qprism_app.png)  

1. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Sélectionnez **Ajouter**. Sous **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

1. Sous **Ajouter une attribution**, sélectionnez **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette QPrism dans le volet d’accès, vous devez être connecté automatiquement à votre application QPrism.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

