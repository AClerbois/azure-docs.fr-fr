---
title: 'Didacticiel : Intégration d’Azure Active Directory à Drift | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Drift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e645dd40071416a28ced475e02c47688a5759eb4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304061"
---
# <a name="tutorial-integrate-drift-with-azure-active-directory"></a>Didacticiel : Intégrer Drift à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Drift à Azure AD (Azure Active Directory). Quand vous intégrez Drift à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Drift.
* Permettre à vos utilisateurs de se connecter automatiquement à Drift avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Drift pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Drift prend en charge l’authentification unique lancée par **le fournisseur d’identité et le fournisseur de service** et le provisionnement d’utilisateurs **juste à temps**.

## <a name="adding-drift-from-the-gallery"></a>Ajout de Drift à partir de la galerie

Pour configurer l’intégration de Drift dans Azure AD, vous devez ajouter Drift, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Drift** dans la zone de recherche.
1. Sélectionnez **Drift** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Drift à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Drift associé.

Pour configurer et tester l’authentification unique Azure AD avec Drift, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer Drift](#configure-drift)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Drift](#create-drift-test-user)** pour avoir un équivalent de B. Simon dans Drift lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Drift**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer** et effectuer les étapes suivantes :

    a. Cliquez sur **Définir des URL supplémentaires**.
 
    b. Dans la zone de texte **État de relais**, tapez une URL : `https://app.drift.com` 

    c. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, effectuez les étapes suivantes :

    d. Dans la zone de texte **URL de connexion**, tapez une URL : `https://start.drift.com`

6. Votre application Drift s’attend à recevoir les assertions SAML dans un format spécifique. Vous devez donc ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ouvrir la boîte de dialogue Attributs d’utilisateur.

    ![image](common/edit-attribute.png)

7. En plus de ce qui précède, l’application Drift s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section Revendications des utilisateurs de la boîte de dialogue Attributs utilisateur, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous : 

    | Nom | Attribut source|
    | ---------------| --------------- |    
    | Nom | user.displayname |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Drift**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-drift"></a>Configurer Drift

1. Pour automatiser la configuration dans Drift, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Drift** pour être dirigé vers l’application Drift. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Drift. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 et 4.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Drift, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Drift en tant qu’administrateur et effectuez les étapes suivantes :

4. Sur le côté gauche de la barre de menus, cliquez sur l’**icône des paramètres** > **Paramètres de l’application** > **Authentification**, puis effectuez les étapes suivantes :

    ![Lien Admin](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Chargez le fichier **XML de métadonnées de fédération** que vous avez téléchargé à partir du portail Azure. Pour cela, indiquez-le dans la zone de texte **Chargez le fichier de métadonnées issu de votre fournisseur d’identité**.

    b. Après avoir chargé le fichier de métadonnées, les valeurs restantes dans la page sont remplies automatiquement.

    c. Cliquez sur **Enable SAML**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B. Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B. Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Drift.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Drift**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-drift-test-user"></a>Créer utilisateur de test Drift

Dans cette section, un utilisateur appelé Britta Simon est créé dans Drift. Drift prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Drift, il en est créé un après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support Drift](mailto:integrations@drift.com).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette Drift dans le volet d’accès, vous devez être connecté automatiquement à l’application Drift pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)