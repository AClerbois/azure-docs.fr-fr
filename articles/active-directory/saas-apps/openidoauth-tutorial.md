---
title: Configurer une application OpenID/OAuth à partir de la galerie d’applications Azure AD | Microsoft Docs
description: Étapes pour configurer une application OpenID/OAuth à partir de la galerie d’applications Azure AD
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.openlocfilehash: 1215e0060662e0d5261d3aeac4874e5833131ba3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260837"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configurer une application OpenID/OAuth à partir de la galerie d’applications Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processus d’ajout d’une application OpenID à partir de la galerie

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory](common/select-azuread.png))

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez le nom de l’application. Dans le panneau de résultats, sélectionnez l’application souhaitée et inscrivez-vous à l’application.

    ![Openid dans la liste des résultats](common/search-new-app.png)

    > [!NOTE]
    > Pour les applications OpenID Connect et OAuth, le bouton **Ajouter** est désactivé par défaut. L’administrateur des locataires doit sélectionner le bouton S’inscrire et donner son consentement pour l’application. L’application est alors ajoutée au locataire du client, où vous pouvez effectuer les configurations. Il est inutile d’ajouter l’application explicitement.

    ![Bouton Ajouter](./media/openidoauth-tutorial/addbutton.png)

5. Lorsque vous sélectionnez le lien d’inscription, vous êtes redirigé vers la page Azure Active Directory (Azure AD) pour entrer les informations d’identification de connexion.

6. Une fois que l’authentification a été effectuée, vous acceptez le consentement dans la page de consentement. La page d’accueil de l’application s’affiche.

    > [!NOTE]
    > Vous ne pouvez ajouter qu’une instance de l’application. Si vous en avez déjà ajouté une et que vous avez tenté de fournir une nouvelle fois le consentement, l’application n’est pas ajoutée à nouveau dans le locataire. Vous ne pouvez donc utiliser en toute logique qu’une seule instance de l’application dans le locataire.

## <a name="authentication-flow-using-openid-connect"></a>Flux d’authentification à l’aide d’OpenID Connect

Le flux d’authentification le plus élémentaire comprend les étapes suivantes :

![Flux d’authentification à l’aide d’OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Application mutualisée 
Une application mutualisée est destinée à être utilisée dans plusieurs organisations, et pas dans une seule. Il s’agit généralement d’applications SaaS (software-as-a-service) écrites par un éditeur de logiciels indépendant. 

Les applications mutualisées doivent être approvisionnées dans chaque répertoire où elles seront utilisées. Leur inscription nécessite le consentement de l’utilisateur ou de l’administrateur. Ce processus de consentement démarre quand une application a été enregistrée dans l’annuaire et accède à l’API Graph ou à une autre API web. Lorsqu’un utilisateur ou un administrateur d’une autre organisation s’inscrit pour utiliser l’application, une boîte de dialogue contenant les autorisations nécessaires à l’application s’affiche. 

L’utilisateur ou l’administrateur peut alors donner son consentement pour l’application. Le consentement permet à l’application d’accéder aux données indiquées, et enregistre l’application dans le répertoire.

> [!NOTE]
> Si vous mettez votre application à la disposition des utilisateurs dans plusieurs répertoires, vous devez disposer d’un mécanisme permettant de déterminer le locataire dans lequel ils se trouvent. Une application à locataire unique ne doit regarder que dans son propre répertoire pour y rechercher un utilisateur. Une application mutualisée doit identifier un utilisateur spécifique dans tous les répertoires d’Azure AD.
> 
> À cet effet, Azure AD fournit un point de terminaison d’authentification commun vers lequel une application mutualisée peut diriger les demandes de connexion, plutôt que vers un point de terminaison propre au locataire. Ce point de terminaison est [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) pour tous les répertoires d’Azure AD. Un point de terminaison propre à un locataire peut être [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> Il est important de tenir compte du point de terminaison commun lorsque vous développez votre application. Vous avez besoin de la logique nécessaire pour gérer plusieurs locataires pendant la connexion, la déconnexion et la validation des jetons.

Par défaut, Azure AD promeut les applications mutualisées. Elles sont facilement accessibles entre les organisations et d’une utilisation simple une fois que vous en avez accepté le consentement.

## <a name="consent-framework"></a>Framework de consentement

Vous pouvez utiliser l’infrastructure de consentement d’Azure AD pour développer des applications clientes natives et web mutualisées. Ces applications autorisent la connexion au moyen de comptes d’utilisateurs d’un locataire Azure AD différent de celui où l’application a été inscrite. Elles peuvent également avoir besoin d’accéder à des API web, par exemple :
- API Microsoft Graph pour accéder à Azure AD, à Intune et aux services d’Office 365 
- API des autres services Microsoft
- Vos propres API web 

L’infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire. Cette inscription peut impliquer l’accès aux données de répertoire. Une fois le consentement donné, l’application cliente peut appeler l’API Microsoft Graph au nom de l’utilisateur et utiliser les informations en fonction des besoins.

[L’API Microsoft Graph](https://developer.microsoft.com/graph/) permet d’accéder aux données d’Office 365, par exemple :

- Calendriers et messages d’Exchange
- Sites et listes de SharePoint
- Documents de OneDrive
- Blocs-notes de OneNote
- Tâches du Planificateur
- Classeurs d’Excel

L’API Graph fournit également un accès aux utilisateurs et groupes d’Azure AD et aux autres objets de données provenant d’autres services de cloud computing Microsoft.

Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne pour le développeur d’applications et pour l’utilisateur :

1. Supposons que l’une de vos applications clientes web doive demander des autorisations spécifiques pour accéder à une ressource ou à une API. Le Portail Azure est utilisé pour déclarer les demandes d’autorisation au moment de la configuration. Comme d’autres paramètres de configuration, elles sont incluses dans l’inscription Azure AD de l’application :

    ![API Graph](./media/openidoauth-tutorial/graphapi.png)

2. Considérez que les autorisations de votre application ont été mises à jour. L’application est en cours d’exécution, et un utilisateur s’apprête à l’utiliser pour la première fois. L’application doit tout d’abord obtenir un code d’autorisation du point de terminaison /authorize d’Azure AD. Le code d’autorisation peut ensuite être utilisé pour obtenir un nouvel accès et un jeton d’actualisation.

3. Si l’utilisateur n’est pas déjà authentifié, le point de terminaison /authorize d’Azure AD l’invite à se connecter.

    ![Authentification](./media/openidoauth-tutorial/authentication.png)

4. Une fois l’utilisateur connecté, Azure AD détermine s’il est nécessaire d’afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application.

   Si le consentement n’a pas été accordé, Azure AD invite l’utilisateur à le fournir et affiche les autorisations requises pour le fonctionnement. Les autorisations qui s’affichent dans la boîte de dialogue de consentement correspondent à celles qui sont sélectionnées dans les autorisations déléguées du portail Azure.

    ![Page de consentement](./media/openidoauth-tutorial/consentpage.png)

Un utilisateur standard peut donner son consentement pour certaines autorisations. D’autres autorisations nécessitent le consentement de l’administrateur d’un locataire.

## <a name="difference-between-admin-consent-and-user-consent"></a>Différence entre le consentement administrateur et le consentement de l’utilisateur

En tant qu’administrateur, vous pouvez également donner votre consentement pour les autorisations déléguées d’une application pour le compte de tous les utilisateurs de votre client. Le consentement administrateur évite que la boîte de dialogue de consentement ne s’affiche pour tous les utilisateurs du locataire. Les utilisateurs disposant du rôle d’administrateur peuvent indiquer leur consentement dans le portail Azure. Dans la page **Paramètres** de votre application, sélectionnez **Autorisations requises** > **Accorder des autorisations**.

![Bouton Accorder des autorisations](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Pour les applications monopages (SPA) qui utilisent ADAL.js, vous devez maintenant accorder un consentement explicite à l’aide du bouton **Accorder des autorisations**. Sinon, l’application échoue lorsque le jeton d’accès est demandé.

Les autorisations application seule nécessitent toujours le consentement de l’administrateur d’un client. Si votre application demande une autorisation application seule et qu’un utilisateur tente de se connecter à l’application, un message d’erreur s’affiche. Le message indique que l’utilisateur n’est pas en mesure de donner son consentement.

Si votre application utilise des autorisations qui nécessitent un consentement administrateur, vous devez y intégrer une option comme un bouton ou un lien afin que l’administrateur puisse démarrer l’action. La requête que votre application envoie pour cette action est une demande d’autorisation OAuth2/OpenID Connect ordinaire. Cette requête inclut le paramètre de chaîne de requête *prompt=admin_consent*. 

Une fois que l’administrateur a donné son consentement et que le principal de service est créé dans le locataire du client, les demandes de connexion ultérieures n’ont plus besoin du paramètre *prompt=admin_consent*. Comme l’administrateur a décidé que les autorisations demandées sont acceptables, les autres utilisateurs du locataire n’ont plus à donner leur consentement par la suite.

L’administrateur d’un client peut empêcher les utilisateurs standard de donner son consentement aux applications. Si cette fonctionnalité est désactivée, le consentement de l’administrateur est toujours requis pour que l’application soit utilisée dans le client. Si vous souhaitez tester votre application avec le consentement de l’utilisateur final désactivé, vous trouverez le commutateur de configuration dans le [portail Azure](https://portal.azure.com/). Il se trouve dans la section [Paramètres utilisateur](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) sous **Applications d’entreprise**.

Le paramètre *prompt=admin_consent* peut également être utilisé par les applications qui demandent des autorisations ne nécessitant pas le consentement de l’administrateur. Exemple d’application qui requiert une expérience : l’administrateur des locataires « s’inscrit » une seule fois et aucun autre utilisateur n’a à donner de consentement à compter de ce moment.

Imaginez qu’une application nécessite un consentement administrateur, et qu’un administrateur se connecte sans envoyer le paramètre *prompt=admin_consent*. Si l’administrateur donne son consentement pour l’application, celui-ci s’applique uniquement pour son compte d’utilisateur. Les utilisateurs standard ne peuvent toujours pas se connecter ni donner leur consentement pour l’application. Cette fonctionnalité s’avère particulièrement utile si vous souhaitez donner à l’administrateur des locataires la possibilité d’explorer votre application avant d’autoriser l’accès à d’autres utilisateurs.