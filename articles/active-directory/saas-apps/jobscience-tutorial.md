---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Jobscience | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8199f106c234e216a0982dc9e51413ccf30ae93a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60268704"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Didacticiel : Intégration d’Azure Active Directory à Jobscience

Dans ce didacticiel, vous allez apprendre à intégrer Jobscience à Azure Active Directory (Azure AD).

L’intégration de Jobscience dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Jobscience.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Jobscience (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Jobscience, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Jobscience pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [Offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jobscience à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Ajout de Jobscience à partir de la galerie
Pour configurer l’intégration de Jobscience à Azure AD, vous devez ajouter Jobscience à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Jobscience à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Jobscience**.

    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Dans le volet de résultats, sélectionnez **Jobscience**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jobscience avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jobscience équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Jobscience associé doit être établie.

Dans Jobscience, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Jobscience, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Jobscience](#creating-a-jobscience-test-user)** pour avoir un équivalent de Britta Simon dans Jobscience lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Jobscience.

**Pour configurer l’authentification unique Azure AD avec Jobscience, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Jobscience**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Dans la section **Domaine et URL Jobscience**, procédez comme suit :

    ![Configurer l'authentification unique](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Dans la zone de texte **URL d’authentification**, tapez une URL en utilisant le modèle suivant : `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Obtenez cette valeur auprès de l’[équipe de support Client Jobscience](https://www.jobscience.com/support) ou à partir du profil d’authentification unique que vous allez créer, qui est expliqué plus loin dans le tutoriel. 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/jobscience-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Jobscience**, cliquez sur **Configurer Jobscience** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Connectez-vous au site d’entreprise Jobscience en tant qu’administrateur.

1. Accédez à **Setup**.
   
   ![Configuration](./media/jobscience-tutorial/IC784358.png "Configuration")

1. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**. 
   
   ![Mon domaine](./media/jobscience-tutorial/ic767825.png "mon domaine")

1. Pour vérifier que votre domaine a été configuré correctement, assurez-vous qu’il figure dans **Step 4 Deployed to Users**, puis passez en revue **My Domain Settings**.

    ![Domaine déployé sur l’utilisateur](./media/jobscience-tutorial/ic784377.png "domaine déployé sur l’utilisateur")

1. Sur le site d’entreprise Jobscience, cliquez sur **Security Controls**, puis sur **Single Sign-On Settings**.
    
    ![Security Controls](./media/jobscience-tutorial/ic784364.png "Security Controls")

1. Dans la section **Single Sign-On Settings** , procédez comme suit :
    
    ![Paramètres d’authentification unique](./media/jobscience-tutorial/ic781026.png "paramètres d’authentification unique")
    
    a. Sélectionnez **SAML Enabled**.

    b. Cliquez sur **Nouveau**.

1. Dans la boîte de dialogue **SAML Single Sign-On Setting Edit** , procédez comme suit :
    
    ![Configuration de l’authentification unique SAML](./media/jobscience-tutorial/ic784365.png "Configuration de l’authentification unique SAML")
    
    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.

    b. Dans la zone de texte **Issuer (Émetteur)**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis le portail Azure.

    c. Dans la zone de texte **Entity ID** (ID identité), tapez `https://salesforce-jobscience.com`.

    d. Cliquez sur **Parcourir** pour charger votre certificat Azure AD.

    e. Pour **SAML Identity Type (Type d’identité SAML)**, sélectionnez **Assertion contains the Federation ID from the User object (L’assertion contient l’ID de fédération de l’objet utilisateur)**.

    f. Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentfier element of the Subject statement**.

    g. Dans la zone de texte **Identity Provider Login URL (URL de connexion du fournisseur d’identité)**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    h. Dans la zone de texte **Identity Provider Logout URL (URL de déconnexion du fournisseur d’identité)**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    i. Cliquez sur **Enregistrer**.

1. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**. 
    
    ![Mon domaine](./media/jobscience-tutorial/ic767825.png "mon domaine")

1. Dans la section **Login Page Branding** de la page **My Domain**, cliquez sur **Edit**.
    
    ![Personnalisation de la page de connexion](./media/jobscience-tutorial/ic767826.png "personnalisation de la page de connexion")

1. Le nom des **SAML SSO Settings** s’affiche dans la section **Authentication Service** de la page **Login Page Branding**. Sélectionnez-le, puis cliquez sur **Save**.
    
    ![Personnalisation de la page de connexion](./media/jobscience-tutorial/ic784366.png "personnalisation de la page de connexion")

1. Pour obtenir l’URL d’authentification unique initiée par le fournisseur de services , cliquez sur **Single Sign On settings** dans la section **Security Controls**.

    ![Security Controls](./media/jobscience-tutorial/ic784368.png "Security Controls")
    
    Cliquez sur le profil d’authentification unique créé à l’étape précédente. Cette page affiche l’URL d’authentification unique pour votre entreprise (par exemple, [https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid)).    

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-jobscience-test-user"></a>Création d’un utilisateur de test Jobscience

Pour permettre aux utilisateurs Azure AD de se connecter à Jobscience, vous devez les approvisionner dans Jobscience. Dans le cas de Jobscience, cet approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte utilisateur fournis par Jobscience pour approvisionner des comptes utilisateur Azure Active Directory.
>  
        
**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous au site d’entreprise **Jobscience** en tant qu’administrateur.

1. Accédez à Setup.
   
   ![Configuration](./media/jobscience-tutorial/ic784358.png "Configuration")
1. Accédez à **Manage Users \> Users**.
   
   ![Utilisateurs](./media/jobscience-tutorial/ic784369.png "Utilisateurs")
1. Cliquez sur **New User**.
   
   ![Tous les utilisateurs](./media/jobscience-tutorial/ic784370.png "Tous les utilisateurs")
1. Dans la boîte de dialogue **Edit User** , procédez comme suit :
   
   ![Modification de l’utilisateur](./media/jobscience-tutorial/ic784371.png "modification de l’utilisateur")
   
   a. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple Britta.
   
   b. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple Simon.
   
   c. Dans la zone de texte **Alias**, entrez le nom d’alias de l’utilisateur, par exemple Britta.

   d. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

   e. Dans la zone de texte **Nom d’utilisateur**, entrez le nom de l’utilisateur, par exemple Brittasimon@contoso.com.

   f. Dans la zone de texte **Surnom**, entrez le surnom, par exemple Simon.

   g. Cliquez sur **Enregistrer**.

    
> [!NOTE]
> Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jobscience.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Jobscience, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Jobscience**.

    ![Configurer l'authentification unique](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Jobscience dans le volet d’accès, vous devez vous connecter automatiquement à votre application Jobscience.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

