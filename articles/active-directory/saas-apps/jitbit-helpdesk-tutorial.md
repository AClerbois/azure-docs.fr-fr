---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86148d428bc526e071f095233b0692a6f3775a7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880640"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutoriel : Intégration d’Azure Active Directory avec Jitbit Helpdesk

Dans ce didacticiel, vous allez apprendre à intégrer Jitbit Helpdesk à Azure Active Directory (Azure AD).

L’intégration de Jitbit Helpdesk dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Jitbit Helpdesk.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Jitbit Helpdesk (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Jitbit Helpdesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Jitbit Helpdesk pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jitbit Helpdesk à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Ajout de Jitbit Helpdesk à partir de la galerie
Pour configurer l’intégration de Jitbit Helpdesk à Azure AD, vous devez ajouter Jitbit Helpdesk, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Jitbit Helpdesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Jitbit Helpdesk**.

    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

1. Dans le panneau de résultats, sélectionnez **Jitbit Helpdesk**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jitbit Helpdesk avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jitbit Helpdesk équivalent à l’utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Jitbit Helpdesk associé doit être établie.

Dans Jitbit Helpdesk, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Jitbit Helpdesk, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)** pour obtenir un équivalent de Britta Simon dans Jitbit Helpdesk lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Jitbit Helpdesk.

**Pour configurer l’authentification unique Azure AD avec Jitbit Helpdesk, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Jitbit Helpdesk**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

1. Dans la section **Domaine et URL Jitbit Helpdesk**, procédez comme suit :

    ![Configurer l'authentification unique](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : 
    
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support Jitbit Helpdesk](https://www.jitbit.com/support/) pour obtenir cette valeur. 
    
    b.  Dans la zone de texte **Identificateur**,saisissez une URL comme suit : `https://www.jitbit.com/web-helpdesk/`

    
 


1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/jitbit-helpdesk-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Jitbit Helpdesk**, cliquez sur **Configurer Jitbit Helpdesk** pour ouvrir la fenêtre **Configurer la connexion**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jitbit Helpdesk en tant qu’administrateur.

1. Dans la barre d’outils située dans la partie supérieure, cliquez sur **Administration**.
   
    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Cliquez sur **General settings**.
   
    ![Utilisateurs, entreprises et autorisations](./media/jitbit-helpdesk-tutorial/ic777680.png "Utilisateurs, entreprises et autorisations")

1. Dans la section de configuration **Authentication settings** , procédez comme suit :
   
    ![Authentication settings](./media/jitbit-helpdesk-tutorial/ic777683.png "Authentication settings")
    
    a. Sélectionnez **Activer l’authentification unique SAML 2.0** pour vous connecter à l’aide de l’authentification unique, avec **OneLogin**.

    b. Dans la zone de texte **URL du point de terminaison**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    c. Ouvrez votre certificat codé en **base 64** dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.

    d. Cliquez sur **Save changes**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Création d’un utilisateur de test Jitbit Helpdesk

Pour pouvoir se connecter à Jitbit Helpdesk, les utilisateurs d’Azure Active Directory doivent être approvisionnés dans Jitbit Helpdesk.  Dans le cas de Jitbit Helpdesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Jitbit Helpdesk** .

1. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.
   
    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Cliquez sur **Users, companies and permissions**.
   
    ![Utilisateurs, entreprises et autorisations](./media/jitbit-helpdesk-tutorial/ic777682.png "Utilisateurs, entreprises et autorisations")

1. Cliquez sur **Add User**.
   
    ![Ajouter un utilisateur](./media/jitbit-helpdesk-tutorial/ic777685.png "Ajouter un utilisateur")
   
1. Dans la section Créer, entrez les données du compte Azure AD que vous souhaitez configurer comme suit :

    ![Créer](./media/jitbit-helpdesk-tutorial/ic777686.png "créer")
   
   a. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**, le nom d’utilisateur du portail Azure.

   b. Dans le **E-mail** zone de texte, entrez l’e-mail de l’utilisateur comme **BrittaSimon\@contoso.com**.

   c. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

   d. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.
   
   e. Cliquez sur **Créer**.

>[!NOTE]
>Vous pouvez d’autres outils ou API de création de compte d’utilisateur fournis par Jitbit Helpdesk pour configurer des comptes d’utilisateurs Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jitbit Helpdesk.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Jitbit Helpdesk, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Jitbit Helpdesk**.

    ![Configurer l'authentification unique](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Jitbit Helpdesk dans le panneau d’accès, la page de connexion de l’application Jitbit Helpdesk doit apparaître.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/jitbit-helpdesk-tutorial/tutorial_general_203.png

