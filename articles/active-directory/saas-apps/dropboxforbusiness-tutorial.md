---
title: 'Tutoriel : Intégration d’Azure Active Directory à Dropbox for Business | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: cb639f04cec28596c528691066e60b087ca7b09a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808874"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Didacticiel : Intégration d’Azure Active Directory à Dropbox for Business

Dans ce didacticiel, vous allez apprendre à intégrer Dropbox for Business à Azure Active Directory (Azure AD).
L’intégration de Dropbox for Business à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Dropbox for Business.
* Vous pouvez permettre à vos utilisateurs d’être connectés automatiquement à Dropbox for Business (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Dropbox for Business, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Dropbox for Business pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Dropbox for Business prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

* Dropbox for Business prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Ajout de Dropbox for Business à partir de la galerie

Pour configurer l’intégration de Dropbox for Business à Azure AD, vous devez ajouter Dropbox for Business depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Dropbox for Business à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Dropbox for Business**, sélectionnez **Dropbox for Business** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Dropbox for Business dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Dropbox for Business au moyen d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Dropbox for Business associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Dropbox for Business, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Dropbox for Business](#configure-dropbox-for-business-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Dropbox for Business](#create-dropbox-for-business-test-user)** pour disposer, dans Dropbox for Business, d’un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Dropbox for Business, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Dropbox for Business**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Domaine et URL Dropbox for Business](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://www.dropbox.com/sso/<id>`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, tapez une valeur : `Dropbox`

    > [!NOTE]
    > La valeur URL de connexion ci-dessus n’est pas une valeur réelle. Vous mettrez à jour la valeur avec l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Dropbox for Business**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-dropbox-for-business-single-sign-on"></a>Configurer l’authentification unique Dropbox for Business

1. Pour configurer l’authentification unique du côté de **Dropbox for Business**, accédez et connectez-vous à votre locataire Dropbox for Business.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/ic769509.png "Configurer l’authentification unique")

2. Cliquez sur l’**Icône Utilisateur** et sélectionnez l’onglet **Paramètres**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure1.png "Configurer l’authentification unique")

3. Dans le volet de navigation gauche, cliquez sur **Console d’administration**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure2.png "Configurer l’authentification unique")

4. Dans la **Console d’administration**, cliquez sur **Paramètres** dans le volet de navigation gauche.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure3.png "Configurer l’authentification unique")

5. Sélectionnez **Authentification unique** sous la section **Authentification**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure4.png "Configurer l’authentification unique")

6. Dans la section **Authentification unique**, procédez comme suit :  

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure5.png "Configurer l’authentification unique")

    a. Sélectionnez **Requis** en tant qu’option dans la liste déroulante de l’**Authentification unique**.

    b. Cliquez sur **Ajouter une URL de connexion**, puis dans la zone de texte **URL de connexion du fournisseur d’identité**, collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure, et sélectionnez **Terminé**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure6.png "Configurer l’authentification unique")

    c. Cliquez sur **Charger un certificat**, puis cherchez votre **Fichier de certificat codé en Base64** que vous avez téléchargé à partir du portail Azure.

    d. Cliquez sur **Copier le lien** et collez la valeur copiée dans la zone de texte de l’**URL de connexion** de la section **Domaine et URL Dropbox for Business** du portail Azure.

    e. Cliquez sur **Enregistrer**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dropbox for Business.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Dropbox for Business**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Dropbox for Business**.

    ![Lien Dropbox for Business dans la liste Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-dropbox-for-business-test-user"></a>Créer un utilisateur de test Dropbox for Business

Dans cette section, un utilisateur appelé Britta Simon est créé dans Dropbox for Business. Dropbox for Business prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Dropbox for Business, un utilisateur est créé après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients Dropbox for Business](https://www.dropbox.com/business/contact).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Dropbox for Business dans le panneau d’accès doit vous connecter automatiquement à l’application Dropbox for Business pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

