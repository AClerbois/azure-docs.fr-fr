---
title: 'Didacticiel : Intégration d’Azure Active Directory avec LoginRadius | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LoginRadius.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5715579e-598f-4d2e-970a-107b80b97be4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.openlocfilehash: 5b3028e7dc26d630ee206b212d32ca6161a00a6d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Didacticiel : Intégration d’Azure Active Directory avec LoginRadius

Dans ce didacticiel, vous allez apprendre à intégrer LoginRadius avec Azure Active Directory (Azure AD).

L’intégration de LoginRadius avec Azure AD offre les avantages suivants :

- Il est possible de contrôler dans Azure AD qui a accès à LoginRadius.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LoginRadius (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD avec LoginRadius, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LoginRadius pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LoginRadius à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-loginradius-from-the-gallery"></a>Ajout de LoginRadius à partir de la galerie
Pour configurer l’intégration de LoginRadius à Azure AD, vous devez ajouter LoginRadius à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter LoginRadius à partir de la galerie, suivez ces étapes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **LoginRadius**, sélectionnez **LoginRadius** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![LoginRadius dans la liste des résultats](./media/active-directory-saas-lr-tutorial/tutorial_LoginRadius_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LoginRadius pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quel est l’utilisateur LoginRadius équivalent à celui d’Azure AD. En d’autres termes, il faut établir une relation entre un utilisateur Azure AD et l’utilisateur LoginRadius associé.

Pour configurer et tester l’authentification unique Azure AD avec LoginRadius, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test LoginRadius](#create-a-loginradius-test-user)** pour avoir un équivalent de Britta Simon dans LoginRadius qui soit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et la configurer dans l’application LoginRadius.

**Pour configurer l’authentification unique Azure AD avec LoginRadius, suivez ces étapes :**

1. Sur la page d’intégration de l’application **LoginRadius** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-lr-tutorial/tutorial_LoginRadius_samlbase.png)

3. Dans la section **Domaine et URL LoginRadius**, suivez ces étapes :

    ![Informations sur l’authentification unique dans Domaine et URL LoginRadius](./media/active-directory-saas-lr-tutorial/tutorial_LoginRadius_url.png)

    a.  Dans la zone de texte **URL d’authentification**, tapez l’URL `https://secure.loginradius.com/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL : `https://LoginRadius.hub.loginradius.com/`
     
    > [!NOTE] 
    > Ouvrez la page URL de connexion. Cliquez sur l’onglet **Authentification unique**, entrez le **nom du plug-in** fourni par [l’équipe de support LoginRadius](mailto:support@loginradius.com), puis cliquez sur le bouton **Se connecter** pour accéder à la page de connexion Azure AD. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-lr-tutorial/tutorial_LoginRadius_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-lr-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique côté **LoginRadius**, vous devez envoyer le **XML de métadonnées** téléchargé à [l’équipe du support technique LoginRadius](mailto:support@loginradius.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-lr-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-lr-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-lr-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-lr-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-loginradius-test-user"></a>Créer un utilisateur de test LoginRadius

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans LoginRadius. Collaborez avec [l’équipe du support technique LoginRadius](mailto:support@loginradius.com) pour ajouter des utilisateurs dans la plateforme LoginRadius. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LoginRadius.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à LoginRadius, suivez ces étapes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LoginRadius**.

    ![Lien LoginRadius dans la liste des applications](./media/active-directory-saas-lr-tutorial/tutorial_LoginRadius_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette LoginRadius dans le volet d’accès, la connexion à l’application LoginRadius est établie automatiquement.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lr-tutorial/tutorial_general_203.png

