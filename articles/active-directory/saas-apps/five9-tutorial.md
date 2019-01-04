---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Five9 Plus Adapter (CTI, agents de centre de contacts) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Five9 Plus Adapter (CTI, agents de centre de contacts).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: c8d3364f9de298d19df35846af5f421978fea960
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842167"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Didacticiel : Intégration d’Azure Active Directory avec Five9 Plus Adapter (CTI, agents de centre de contacts)

Dans ce didacticiel, vous allez apprendre à intégrer Five9 Plus Adapter (CTI, agents de centre de contacts) à Azure Active Directory (Azure AD).

L’intégration de Five9 Plus Adapter (CTI, agents de centre de contacts) à Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Five9 Plus Adapter (CTI, agents de centre de contacts)
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Five9 Plus Adapter (CTI, agents de centre de contacts) (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Five9 Plus Adapter (CTI, agents de centre de contacts), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Five9 Plus Adapter (CTI, agents de centre de contacts) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez vous inscrire pour un essai d’un mois ici : [Offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Five9 Plus Adapter (CTI, agents de centre de contacts) à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Ajout de Five9 Plus Adapter (CTI, agents de centre de contacts) à partir de la galerie
Pour configurer l’intégration de Five9 Plus Adapter (CTI, agents de centre de contacts) dans Azure AD, vous devez ajouter Five9 Plus Adapter (CTI, agents de centre de contacts) à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Five9 Plus Adapter (CTI, agents de centre de contacts) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Five9 Plus Adapter (CTI, agents de centre de contacts)**.

    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/tutorial_five9_search.png)

1. Dans le volet de résultats, sélectionnez **Five9 Plus Adapter (CTI, agents de centre de contacts)**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Five9 Plus Adapter (CTI, agents de centre de contacts), avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Five9 Plus Adapter (CTI, agents de centre de contacts) équivalent à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Five9 Plus Adapter (CTI, agents de centre de contacts) associé doit être établie.

Dans Five9 Plus Adapter (CTI, agents de centre de contacts), assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Five9 Plus Adapter (CTI, agents de centre de contacts), vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Five9 Plus Adapter (CTI, agents de centre de contacts)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)** pour avoir un équivalent de Britta Simon dans Five9 Plus Adapter (CTI, agents de centre de contacts) lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application Five9 Plus Adapter (CTI, agents de centre de contacts).

**Pour configurer l’authentification unique Azure AD avec Five9 Plus Adapter (CTI, agents de centre de contacts), effectuez les étapes suivantes :**

1. Sur le portail Azure, à la page d’intégration de l’application **Five9 Plus Adapter (CTI, agents de centre de contacts)**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. Dans la section **Domaine et URL Five9 Plus Adapter (CTI, agents de centre de contacts)**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :

    |    Environnement      |       URL      |
    | :-- | :-- |
    | Pour « Five9 Plus Adapter for Microsoft Dynamics CRM » | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Pour « Five9 Plus Adapter for Zendesk » | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Pour « Five9 Plus Adapter for Agent Desktop Toolkit » | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    |      Environnement     |      URL      |
    | :--                  | :--           |
    | Pour « Five9 Plus Adapter for Microsoft Dynamics CRM » | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Pour « Five9 Plus Adapter for Zendesk » | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Pour « Five9 Plus Adapter for Agent Desktop Toolkit » | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/five9-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Five9 Plus Adapter (CTI, agents de centre de contacts)**, cliquez sur **Configurer Five9 Plus Adapter (CTI, agents de centre de contacts)** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/five9-tutorial/tutorial_five9_configure.png) 

1. Pour configurer l’authentification unique côté **Five9 Plus Adapter (CTI, agents de centre de contacts)**, vous devez envoyer le **certificat (en base64), l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** téléchargés à l’[équipe de support Five9 Plus Adapter (CTI, agents de centre de contacts)](https://www.five9.com/about/contact). En outre, pour poursuivre la configuration de l’authentification unique, suivez les étapes ci-dessous en fonction de l’adaptateur :

    a. Guide de l’administrateur « Five9 Plus Adapter for Agent Desktop Toolkit » : [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Guide de l’administrateur « Five9 Plus Adapter for Microsoft Dynamics CRM » : [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Guide de l’administrateur « Five9 Plus Adapter for Zendesk » : [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/five9-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Création d’un utilisateur de test Five9 Plus Adapter (CTI, agents de centre de contacts)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Five9 Plus Adapter (CTI, agents de centre de contacts). Travaillez avec l’ [équipe de support Five9 Plus Adapter (CTI, agents de centre de contacts)](https://www.five9.com/about/contact)  pour ajouter les utilisateurs sur la plateforme Five9 Plus Adapter (CTI, agents de centre de contacts). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Five9 Plus Adapter (CTI, agents de centre de contacts).

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à Five9 Plus Adapter (CTI, agents de centre de contacts), effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Five9 Plus Adapter (CTI, agents de centre de contacts)**.

    ![Configurer l'authentification unique](./media/five9-tutorial/tutorial_five9_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Five9 Plus Adapter (CTI, agents de centre de contacts) dans le volet d’accès, vous devez être connecté automatiquement à votre application Five9 Plus Adapter (CTI, agents de centre de contacts).
Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

