---
title: Tutoriel - Activer l’authentification dans une application web - Azure Active Directory B2C
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103305"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Didacticiel : Activer l’authentification dans une application web à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment utiliser Azure Active Directory B2C (Azure AD B2C) pour connecter et inscrire des utilisateurs dans une application web ASP.NET. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes de réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Créer des flux d’utilisateur](tutorial-create-user-flows.md) pour activer les expériences utilisateur dans votre application
* Installer [Visual Studio 2019](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.

## <a name="update-the-application"></a>Mettre à jour l’application

Dans le tutoriel que vous avez effectué dans le cadre des prérequis, vous avez ajouté une application web dans Azure AD B2C. Pour permettre la communication avec l’exemple de ce tutoriel, vous devez ajouter un URI de redirection à l’application dans Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis l’application *webapp1*.
1. Sous **URL de réponse**, ajoutez `https://localhost:44316`.
1. Sélectionnez **Enregistrer**.
1. Dans la page des propriétés, enregistrez l’ID d’application que vous utiliserez pour configurer l’application web.
1. Sélectionnez **Clés**, **Générer la clé**, puis **Enregistrer**. Enregistrez la clé que vous utiliserez pour configurer l’application web.

## <a name="configure-the-sample"></a>Configurer l'exemple

Dans ce tutoriel, vous allez configurer un exemple que vous pouvez télécharger à partir de GitHub. L’exemple utilise ASP.NET pour fournir une liste de tâches simple. L’exemple utilise des [composants middleware (intergiciel) Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clonez l’exemple à partir de GitHub. Assurez-vous que vous extrayez l’exemple de fichier dans un dossier où la longueur totale du chemin d’accès est inférieure à 260 caractères.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

L’exemple de solution contient les deux projets suivants :

* **TaskWebApp** : permettant de créer et de modifier une liste de tâches. L’exemple utilise le flux utilisateur d’**inscription ou de connexion** pour inscrire et connecter des utilisateurs.
* **TaskService** : prend en charge les fonctionnalités de création, de lecture, de mise à jour et de suppression des listes de tâches. L’API est protégée par Azure AD B2C et appelée par TaskWebApp.

Vous devez modifier l’exemple pour qu’il utilise l’application qui est inscrite dans votre locataire, ce qui inclut l’ID d’application et la clé que vous avez enregistrés précédemment. Vous devez également configurer les flux d’utilisateur que vous avez créés. L’exemple définit les valeurs de configuration en tant que paramètres dans le fichier *Web.config*.

Mettez à jour les paramètres dans le fichier Web. config pour qu’ils fonctionnent avec votre flux utilisateur :

1. Ouvrez la solution **B2C-WebAPI-DotNet** dans Visual Studio.
1. Dans le projet **TaskWebApp**, ouvrez le fichier **Web.config**.
    1. Mettez à jour la valeur de `ida:Tenant` et `ida:AadInstance` avec le nom du locataire Active AD B2C que vous avez créé. Par exemple, remplacez `fabrikamb2c` par `contoso`.
    1. Remplacez la valeur de `ida:ClientId` par l’ID d’application que vous avez enregistré.
    1. Remplacez la valeur de `ida:ClientSecret` avec la clé que vous avez enregistrée. Vous devez encoder le secret client au format XML avant de l’ajouter au fichier Web.config.
    1. Remplacez la valeur de `ida:SignUpSignInPolicyId` par `b2c_1_signupsignin1`.
    1. Remplacez la valeur de `ida:EditProfilePolicyId` par `b2c_1_profileediting1`.
    1. Remplacez la valeur de `ida:ResetPasswordPolicyId` par `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TaskWebApp**, puis cliquez sur **Définir en tant que projet de démarrage**.
1. Appuyez sur **F5**. Le navigateur par défaut se lance à l’adresse du site web local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Sélectionnez **S’inscrire/Se connecter** pour vous inscrire en tant qu’utilisateur de l’application. Le flux d’utilisateur **b2c_1_signupsignin1** est utilisé.
1. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne disposez pas encore d’un compte, sélectionnez **Inscrivez-vous maintenant**. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.
1. Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis.

    ![Page d’inscription affichée dans le cadre du flux de travail de connexion/inscription](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Sélectionnez **Créer** pour créer un compte local dans le locataire Azure AD B2C.

L’utilisateur de l’application peut maintenant utiliser son adresse e-mail pour se connecter à l’application web et l’utiliser.

Cependant, la fonctionnalité **Liste de tâches** ne fonctionne pas tant que vous n’avez pas terminé le tutoriel suivant de la série [Tutoriel : Utiliser Azure AD B2C pour protéger une API web ASP.NET](active-directory-b2c-tutorials-web-api.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

Passez maintenant au tutoriel suivant pour activer la fonctionnalité **Liste de tâches** de l’application web. Vous y inscrirez une application d’API web de votre propre locataire Azure AD B2C et modifierez l’exemple de code de façon à utiliser votre locataire pour l’authentification de l’API.

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser Azure Active Directory B2C pour protéger une API web ASP.NET >](active-directory-b2c-tutorials-web-api.md)
