---
title: 'Tutoriel : Intégration d’Azure Active Directory à TINFOIL SECURITY | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: f5dbf831677157f660f2c0a8dd2acefe1f6a218a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809477"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutoriel : Intégration d’Azure Active Directory à TINFOIL SECURITY

Dans ce didacticiel, vous allez apprendre à intégrer TINFOIL SECURITY à Azure Active Directory (Azure AD).

L’intégration de TINFOIL SECURITY à Azure AD vous fait bénéficier des avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TINFOIL SECURITY.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à TINFOIL SECURITY (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à TINFOIL SECURITY, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TINFOIL SECURITY pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter TINFOIL SECURITY depuis la galerie
1. Configurer et tester l’authentification unique Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Ajouter TINFOIL SECURITY à partir de la galerie
Pour configurer l’intégration de TINFOIL SECURITY à Azure AD, vous devez ajouter TINFOIL SECURITY à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter TINFOIL SECURITY à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **TINFOIL SECURITY**, sélectionnez **TINFOIL SECURITY** à partir du volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![TINFOIL SECURITY à partir de la galerie](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TINFOIL SECURITY, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TINFOIL SECURITY équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur TINFOIL SECURITY associé doit être établie.

Dans TINFOIL SECURITY, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TINFOIL SECURITY, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test TINFOIL SECURITY](#create-a-tinfoil-security-test-user)** pour avoir un équivalent de Britta Simon dans TINFOIL SECURITY lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TINFOIL SECURITY.

**Pour configurer l’authentification unique Azure AD avec TINFOIL SECURITY, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **TINFOIL SECURITY**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Authentification basée sur SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Dans la section **Domaine et URL TINFOIL SECURITY**, l’utilisateur n’aura pas à effectuer les étapes que l’application a déjà intégrées préalablement à Azure.

    ![Configurer l'authentification unique](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT**.

    ![Section Certificat de signature SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Pour ajouter les mappages d’attribut requis, procédez comme suit :
    
    ![Attributs](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Attributs")
    
    | Nom de l'attribut    |   Valeur de l’attribut |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Cliquez sur **Ajouter un attribut utilisateur**.
    
    ![Ajouter un attribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "Attributs")
    
    ![Ajouter un attribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "Attributs")
    
    b. Dans la zone de texte **Nom de l’attribut**, entrez **accountid**.
    
    c. Dans la zone de texte **Valeur de l’attribut**, collez la valeur de l’ID du compte que vous obtiendrez plus tard dans le didacticiel.
    
    d. Cliquez sur **OK**.    

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de TINFOIL SECURITY**, cliquez sur **Configurer TINFOIL SECURITY** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TINFOIL SECURITY en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **My Account**.
   
    ![Tableau de bord](./media/tinfoil-security-tutorial/ic798971.png "Tableau de bord")

1. Cliquez sur **Sécurité**.
   
    ![Sécurité](./media/tinfoil-security-tutorial/ic798972.png "Sécurité")

1. Dans la page de configuration **Single Sign on** , procédez comme suit :
   
    ![Authentification unique](./media/tinfoil-security-tutorial/ic798973.png "Authentification unique")
   
    a. Sélectionnez **Enable SAML**.
   
    b. Cliquez sur **Manual Configuration**.
   
    c. Dans la zone de texte **URL de publication SAML**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.
   
    d. Dans la zone de texte **Empreinte numérique du certificat SAML**, collez la valeur de l’**empreinte** que vous avez copiée à partir de la section **Certificat de signature SAML**.
  
    e. Copiez la valeur de l’**ID de votre compte** et collez-la dans la zone de texte **Valeur de l’attribut** située sous la section **Ajouter un attribut** du portail Azure.
   
    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Utilisateurs et groupes -> Tous les utilisateurs ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Utilisateur](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Créer un utilisateur de test TINFOIL SECURITY

Pour se connecter à TINFOIL SECURITY, les utilisateurs d’Azure AD doivent être approvisionnés dans TINFOIL SECURITY. Dans le cas de TINFOIL SECURITY, l’approvisionnement est une tâche manuelle.

**Pour configurer l’approvisionnement des utilisateurs, procédez comme suit :**

1. Si l’utilisateur fait partie d’un compte d’entreprise, vous devez [contacter l’équipe de support technique TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) pour obtenir le compte d’utilisateur créé.

1. Si l’utilisateur est un utilisateur normal de SaaS TINFOIL SECURITY, il peut ajouter un collaborateur à n’importe lequel de ses sites. Cela déclenche l’envoi, à l’adresse e-mail spécifiée, d’une invitation à créer un compte d’utilisateur TINFOIL SECURITY.

> [!NOTE]
> Vous pouvez utiliser n’importe quels outils ou API de création de compte d’utilisateur, fournis par TINFOIL SECURITY, pour approvisionner des comptes d’utilisateur Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TINFOIL SECURITY.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à TINFOIL SECURITY, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste d’applications, sélectionnez **TINFOIL SECURITY**.

    ![Sélectionner TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette TINFOIL SECURITY dans le volet d’accès, vous devez être connecté automatiquement à votre application TINFOIL SECURITY. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

