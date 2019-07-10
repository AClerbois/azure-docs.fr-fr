---
title: "Didacticiel : Intégration d'Azure Active Directory à SumoLogic | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 07874988c97044ede59c18b8f85a425483679431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089532"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Didacticiel : Intégration d'Azure Active Directory à SumoLogic

Dans ce didacticiel, vous allez apprendre à intégrer SumoLogic avec Azure Active Directory (Azure AD).
L’intégration de SumoLogic dans Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SumoLogic.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SumoLogic (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SumoLogic, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement SumoLogic pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SumoLogic prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-sumologic-from-the-gallery"></a>Ajout de SumoLogic à partir de la galerie

Pour configurer l’intégration de SumoLogic dans Azure AD, vous devez ajouter SumoLogic à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SumoLogic à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **SumoLogic**, sélectionnez **SumoLogic** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![SumoLogic dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec SumoLogic, pour un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur SumoLogic associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SumoLogic, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique SumoLogic](#configure-sumologic-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test SumoLogic](#create-sumologic-test-user)** pour avoir dans SumoLogic un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec SumoLogic, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **SumoLogic**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL SumoLogic](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<tenantname>.SumoLogic.com`

   b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant :

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SumoLogic](https://www.sumologic.com/contact-us/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer SumoLogic**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-sumologic-single-sign-on"></a>Configurer l’authentification unique SumoLogic

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise SumoLogic en tant qu’administrateur.

1. Accédez à **Manage \> Security**.

    ![Gestion](./media/sumologic-tutorial/ic778556.png "Gestion")

1. Cliquez sur **SAML**.

    ![Paramètres de sécurité globaux](./media/sumologic-tutorial/ic778557.png "Paramètres de sécurité globaux")

1. Dans la liste **Select a configuration or create a new one**, sélectionnez **Azure AD**, puis cliquez sur **Configure**.

    ![Configurer SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configurer SAML 2.0")

1. Dans la boîte de dialogue **Configure SAML 2.0** , procédez comme suit :

    ![Configurer SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configurer SAML 2.0")

    a. Dans la zone de texte **Configuration Name**, entrez **Azure AD**.

    b. Sélectionnez **Debug Mode**.

    c. Dans la zone de texte **Émetteur**, collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Authn Request URL** (URL de la requête d’authentification), collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .

    f. Dans **Email Attribute**, sélectionnez **Use SAML subject**.  

    g. Sélectionnez **SP initiated Login Configuration**.

    h. Dans la zone de texte **Login Path** (Chemin d’accès de connexion), entrez **Azure**, puis cliquez sur **Save** (Enregistrer).

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SumoLogic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **SumoLogic**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **SumoLogic**.

    ![Lien SumoLogic dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-sumologic-test-user"></a>Créer un utilisateur de test SumoLogic

Pour se connecter à SumoLogic, les utilisateurs Azure AD doivent être provisionnés dans SumoLogic. Dans le cas de SumoLogic, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SumoLogic**.

1. Accédez à **Gérer \> Utilisateurs**.

    ![Utilisateurs](./media/sumologic-tutorial/ic778561.png "Utilisateurs")

1. Cliquez sur **Add**.

    ![Utilisateurs](./media/sumologic-tutorial/ic778562.png "Utilisateurs")

1. Dans la boîte de dialogue **New User** , procédez comme suit :

    ![Nouvel utilisateur](./media/sumologic-tutorial/ic778563.png "Nouvel utilisateur") 

    a. Indiquez dans les zones de texte **First Name**, **Last Name** et **Email** le prénom, le nom et l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner.
  
    b. Sélectionnez un rôle
  
    c. Pour **Status (Statut)** , sélectionnez **Active (Actif)** .
  
    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SumoLogic, pour approvisionner des comptes utilisateur AAD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SumoLogic dans le volet d’accès, vous devez être connecté automatiquement à l’application SumoLogic pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

