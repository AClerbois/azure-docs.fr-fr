---
title: 'Didacticiel : Intégration d’Azure Active Directory à YouEarnedIt | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et YouEarnedIt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: jeedes
ms.openlocfilehash: f860036f1a69b2d1ab6ac8de763a49380f8fe4bf
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141476"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Didacticiel : Intégration d’Azure Active Directory à YouEarnedIt

Dans ce didacticiel, vous allez apprendre à intégrer YouEarnedIt à Azure AD (Azure Active Directory).

L’intégration d’YouEarnedIt à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à YouEarnedIt.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à YouEarnedIt (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de YouEarnedIt à Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement YouEarnedIt pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’YouEarnedIt depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-youearnedit-from-the-gallery"></a>Ajout d’YouEarnedIt depuis la galerie

Pour configurer l’intégration de YouEarnedIt à Azure AD, vous devez ajouter YouEarnedIt à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter YouEarnedIt à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **YouEarnedIt**, sélectionnez **YouEarnedIt** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![YouEarnedIt dans la liste des résultats](./media/youearnedit-tutorial/tutorial_youearnedit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec YouEarnedIt avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur YouEarnedIt équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur YouEarnedIt associé doit être établie.

Dans YouEarnedIt, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec YouEarnedIt, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test YouEarnedIt](#create-a-youearnedit-test-user)** pour avoir un équivalent de Britta Simon dans YouEarnedIt lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application YouEarnedIt.

**Pour configurer l’authentification unique Azure AD avec YouEarnedIt, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **YouEarnedIt**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/youearnedit-tutorial/tutorial_youearnedit_samlbase.png)

3. Dans la section **Domaine et URL YouEarnedIt**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL YouEarnedIt](./media/youearnedit-tutorial/tutorial_youearnedit_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : 
    | Environnement  | Modèle  |
    |:--- |:--- |
    | Production | `https://<company name>.youearnedit.com/users/sign_in` |
    | Bac à sable  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | Environnement  | Modèle  |
    |:--- |:--- |
    | Production | `https://<company name>.youearnedit.com` |
    | Bac à sable  |`https://<company name>.sandbox.youearnedit.com` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez le responsable de la réussite des clients YouEarnedIt qui vous a été attribué pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/youearnedit-tutorial/tutorial_youearnedit_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/youearnedit-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de YouEarnedIt**, cliquez sur **Configurer YouEarnedIt** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de YouEarnedIt](./media/youearnedit-tutorial/tutorial_youearnedit_configure.png) 

7. Pour configurer l’authentification unique côté **YouEarnedIt**, vous devez envoyer le **Certificat (Base64)** téléchargé et **l’URL du service d’authentification unique SAML** au responsable de la réussite des clients YouEarnedIt qui vous a été attribué. Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/youearnedit-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/youearnedit-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/youearnedit-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/youearnedit-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-youearnedit-test-user"></a>Créer un utilisateur de test YouEarnedIt

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans YouEarnedIt. Collaborez avec le responsable de la réussite des clients YouEarnedIt qui vous a été attribué pour ajouter des utilisateurs à la plateforme YouEarnedIt.

>[!NOTE]
>YouEarnedIt attend du fournisseur d'identité qu’il fournisse une valeur de nom d’utilisateur ou d’adresse e-mail dans l’attribut NameID. L’authentification échoue si aucune valeur de nom d’utilisateur ou d’adresse e-mail n’est trouvée dans la base de données ou ne correspond exactement. Cette opération nécessite l’importation des comptes dans le système de YouEarnedIt avant l’intégration de l’authentification unique (en général, via l’importation CSV ou API).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à YouEarnedIt.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à YouEarnedIt, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **YouEarnedIt**.

    ![Lien YouEarnedIt dans la liste des applications](./media/youearnedit-tutorial/tutorial_youearnedit_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette YouEarnedIt dans le volet d’accès, vous devez être connecté automatiquement à votre application YouEarnedIt.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/youearnedit-tutorial/tutorial_general_04.png

[100]: ./media/youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/youearnedit-tutorial/tutorial_general_201.png
[202]: ./media/youearnedit-tutorial/tutorial_general_202.png
[203]: ./media/youearnedit-tutorial/tutorial_general_203.png