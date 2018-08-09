---
title: 'Didacticiel : Intégration d’Azure Active Directory à IMPAC Risk Manager | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et IMPAC Risk Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: c086d58a7de4897f93699d2c22b6598896eb50e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434478"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Didacticiel : Intégration d’Azure Active Directory à IMPAC Risk Manager

L’objectif de ce didacticiel est de vous apprendre à intégrer IMPAC Risk Manager à Azure Active Directory (Azure AD).

L’intégration d’IMPAC Risk Manager à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à IMPAC Risk Manager.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à IMPAC Risk Manager (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à IMPAC Risk Manager, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement IMPAC Risk Manager pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’IMPAC Risk Manager à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Ajout d’IMPAC Risk Manager à partir de la galerie
Pour configurer l’intégration d’IMPAC Risk Manager à Azure AD, vous devez ajouter IMPAC Risk Manager à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter IMPAC Risk Manager à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **IMPAC Risk Manager**, sélectionnez **IMPAC Risk Manager** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![IMPAC Risk Manager dans la liste des résultats](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec IMPAC Risk Manager, via un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur IMPAC Risk Manager équivaut à un utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur IMPAC Risk Manager associé doit être établie.

Dans IMPAC Risk Manager, affectez la valeur de **nom d’utilisateur** d’Azure AD comme valeur de **Username**, afin d’établir la relation.

Pour configurer et tester l’authentification unique d’Azure AD avec IMPAC Risk Manager, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test IMPAC Risk Manager](#create-a-impac-risk-manager-test-user)** pour avoir un équivalent de Britta Simon dans IMPAC Risk Manager, lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application IMPAC Risk Manager.

**Pour configurer l’authentification unique Azure AD avec IMPAC Risk Manager, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **IMPAC Risk Manager**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

1. Dans la section **Domaines et URL IMPAC Risk Manager**, suivez les étapes ci-dessous si vous voulez configurer l’application en mode lancé par IDP :

    ![Informations d’authentification unique dans Domaine et URL d’IMPAC Risk Manager](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. Dans la zone de texte **Identificateur**, tapez une valeur fournie par IMPAC

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :
    | Environnement | Modèle d’URL |
    | ---------------|--------------- |    
    | Pour la production |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pour la préproduction et la formation  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pour le développement  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pour l’assurance qualité |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pour les tests |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL d’IMPAC Risk Manager](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | Environnement | Modèle d’URL |
    | ---------------|--------------- |    
    | Pour la production |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pour la préproduction et la formation  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pour le développement  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pour l’assurance qualité |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pour les tests |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/impacriskmanager-tutorial/tutorial_general_400.png)
    
1. Dans la section relative à la **configuration d’IMPAC Risk Manager**, cliquez sur le bouton permettant de **configurer IMPAC Risk Manager** pour ouvrir la fenêtre sur la **configuration de l’authentification unique**. Copiez **l’URL du service d’authentification unique SAML, l’ID d’entité SAML** et **l’URL de déconnexion** dans la section **Référence rapide**.

    ![Configurer l'authentification unique](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

1. Pour configurer l’authentification unique côté **IMPAC Risk Manager**, vous devez envoyer le **certificat (Base64)** téléchargé, **l’URL de déconnexion, l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à [l’équipe de support IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/impacriskmanager-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/impacriskmanager-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/impacriskmanager-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/impacriskmanager-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Créer un utilisateur de test IMPAC Risk Manager

Dans cette section, vous créez un utilisateur appelé Britta Simon dans IMPAC Risk Manager. Collaborez avec [l’équipe de support technique IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz) pour ajouter les utilisateurs dans la plateforme IMPAC Risk Manager. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique d’Azure en lui accordant l’accès à IMPAC Risk Manager.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à IMPAC Risk Manager, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **IMPAC Risk Manager**.

    ![Lien IMPAC Risk Manager dans la liste des applications](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette IMPAC Risk Manager dans le volet d’accès, vous devez être connecté automatiquement à votre application IMPAC Risk Manager.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/impacriskmanager-tutorial/tutorial_general_203.png

