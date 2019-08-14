---
title: 'Tutoriel : Intégration d’Azure Active Directory à Carbonite Endpoint Backup | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Carbonite Endpoint Backup.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf5bf49538823ea50bca197a78ec54d4ddcae1ff
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708442"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutoriel : Intégrer Carbonite Endpoint Backup à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Carbonite Endpoint Backup à Azure Active Directory (Azure AD). Lorsque vous intégrez Carbonite Endpoint Backup à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Carbonite Endpoint Backup
* Permettre à vos utilisateurs de se connecter automatiquement à Carbonite Endpoint Backup avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement à Carbonite Endpoint Backup pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Carbonite Endpoint Backup prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**.

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Ajout de Carbonite Endpoint Backup à partir de la galerie

Pour configurer l’intégration de Carbonite Endpoint Backup à Azure AD, à partir de la galerie, vous devez ajouter Carbonite Endpoint Backup à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Carbonite Endpoint Backup** dans la zone de recherche.
1. Sélectionnez **Carbonite Endpoint Backup** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Carbonite Endpoint Backup à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Carbonite Endpoint Backup associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Carbonite Endpoint Backup, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer Carbonite Endpoint Backup](#configure-carbonite-endpoint-backup-sso)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Carbonite Endpoint Backup](#create-carbonite-endpoint-backup-test-user)** pour avoir dans Carbonite Endpoint Backup un équivalent de B. Simon associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Carbonite Endpoint Backup**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL suivantes :

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL d’authentification**, tapez l’une des URL suivantes :

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Carbonite Endpoint Backup**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configurer l’authentification unique de Carbonite Endpoint Backup

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Carbonite Endpoint Backup en tant qu’administrateur.

1. Cliquez sur **Company** dans le volet gauche.

    ![Configuration de Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure1.png)

1. Cliquez sur **Single sign-on** (Authentification unique).

    ![Configuration de Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure2.png)

1. Cliquez sur **Enable** (Activer), puis sur **Edit settings** (Modifier les paramètres) pour configurer.

    ![Configuration de Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure3.png)

1. Dans la page des paramètres **Single Sign on** (Authentification unique), effectuez les étapes suivantes :

    ![Configuration de Carbonite Endpoint Backup ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Dans la zone de texte **Identity provider name** (Nom du fournisseur d’identité), entrez le nom du fournisseur d’identité, par exemple **Microsoft Azure AD**.

    1. Dans la zone de texte **Identity provider URL** (ID du fournisseur d’identité), collez la valeur de **Azure AD Identifier** (Identificateur Azure AD) que vous avez copiée à partir du portail Azure.

    1. Cliquez sur **Choose file** (Choisir un fichier) pour charger le fichier **Certificate (Base64)** que vous avez téléchargé depuis le portail Azure.

    1. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Carbonite Endpoint Backup.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Carbonite Endpoint Backup**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Créer un utilisateur de test Carbonite Endpoint Backup

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Carbonite Endpoint Backup en tant qu’administrateur.

1. Cliquez sur **Users** dans le volet gauche, puis sur **Add user**.

    ![Ajouter un utilisateur dans Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Dans la page **Add user**, effectuez les étapes suivantes :

    ![Ajouter un utilisateur dans Carbonite Endpoint Backup](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Entrez les informations **Email**, **First name** (Prénom) et **Last name** (Nom) de l’utilisateur, puis fournissez les autorisations exigées pour l’utilisateur en fonction des exigences de l’organisation.

    1. Cliquez sur **Add User**.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Carbonite Endpoint Backup dans le panneau d’accès doit vous connecter automatiquement à l’application Carbonite Endpoint Backup pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)