---
title: 'Didacticiel : Intégration d’Azure Active Directory à Soonr Workplace | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Soonr Workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81ff0f1d92fcf644287fd0cf417a245581bfeb7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210932"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Tutoriel : Intégration d’Azure Active Directory à Soonr Workplace

Dans ce didacticiel, vous allez apprendre à intégrer Soonr Workplace à Azure Active Directory (Azure AD).

L’intégration de Soonr Workplace à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Soonr Workplace.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Soonr Workplace (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Soonr Workplace, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Soonr Workplace pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Soonr Workplace à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-soonr-workplace-from-the-gallery"></a>Ajout de Soonr Workplace à partir de la galerie
Pour configurer l’intégration de Soonr Workplace à Azure AD, vous devez ajouter Soonr Workplace à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Soonr Workplace à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Soonr Workplace**.

    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/tutorial_soonr_search.png)

1. Dans le volet de résultats, sélectionnez **Soonr Workplace**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Soonr Workplace, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Soonr Workplace correspondant à l’utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Soonr Workplace associé doit être établie.

Dans Soonr Workplace, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Soonr Workplace, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Soonr Workplace](#creating-a-soonr-workplace-test-user)** : pour avoir un équivalent de Britta Simon dans Soonr Workplace qui soit associé à la représentation de l’utilisateur Azure AD.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Soonr Workplace.

**Pour configurer l’authentification unique Azure AD avec Soonr Workplace, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Soonr Workplace**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_soonr_samlbase.png)

1. Dans la section **Domaine et URL Soonr Workplace**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_soonr_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<servername>.soonr.com/singlesignon/saml/SSO`

1. Dans la section **Domaine et URL Soonr Workplace**, si vous souhaitez configurer l’application en **Mode initié par SP**, procédez comme suit :
    
    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_soonr_url1.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Contactez [l’équipe de support de Soonr Workplace](https://awp.autotask.net/help/) pour obtenir ces valeurs.
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/soonr-tutorial/tutorial_soonr_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Soonr Workplace**, cliquez sur **Configurer Soonr Workplace** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_soonr_configure.png) 

1. Pour configurer l’authentification unique côté **Soonr Workplace**, vous devez envoyer le **XML de métadonnées**, **, l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** téléchargés à l’[équipe de support de Soonr Workplace](https://awp.autotask.net/help/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

    >[!Note]
    >Si vous avez besoin d’aide lors de la configuration d’Autotask Workplace, consultez [cette page](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) pour obtenir de l’aide avec votre compte Workplace.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/soonr-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Création d’un utilisateur de test Soonr Workplace

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Soonr Workplace. Collaborez avec [l’équipe de support de Soonr Workplace](https://awp.autotask.net/help/) pour créer un utilisateur dans la plateforme. Vous pouvez soumettre un ticket de support avec Soonr <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">à partir d’ici</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Soonr Workplace.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Soonr Workplace, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Soonr Workplace**.

    ![Configurer l'authentification unique](./media/soonr-tutorial/tutorial_soonr_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Quand vous cliquez sur la vignette Soonr Workplace dans le volet d’accès, vous devez être connecté automatiquement à votre application Soonr Workplace.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/soonr-tutorial/tutorial_general_01.png
[2]: ./media/soonr-tutorial/tutorial_general_02.png
[3]: ./media/soonr-tutorial/tutorial_general_03.png
[4]: ./media/soonr-tutorial/tutorial_general_04.png

[100]: ./media/soonr-tutorial/tutorial_general_100.png

[200]: ./media/soonr-tutorial/tutorial_general_200.png
[201]: ./media/soonr-tutorial/tutorial_general_201.png
[202]: ./media/soonr-tutorial/tutorial_general_202.png
[203]: ./media/soonr-tutorial/tutorial_general_203.png

