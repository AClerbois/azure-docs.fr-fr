---
title: Exemples de code Azure Active Directory B2C | Microsoft Docs
description: Exemples de code pour des applications mobiles, de bureau, web et monopages Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: d78d3193cfc01fefec7e04f751432dc053b7f8ec
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846113"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Exemples de code Azure Active Directory B2C

Les tableaux suivants fournissent des liens vers des exemples pour des applications iOS, Android, .Net et Node.js, entre autres.

## <a name="mobile-and-desktop-apps"></a>Applications de bureau et mobiles

| Exemple | Description |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Un exemple iOS dans Swift qui authentifie les utilisateurs Azure AD B2C et appelle une API à l’aide d’OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | Une application Android simple expliquant comment utiliser MSAL pour authentifier les utilisateurs via Azure Active Directory B2C et accéder à une API web avec les jetons générés. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Un exemple qui montre comment vous pouvez utiliser une bibliothèque tierce pour générer une application iOS en Objective-C qui authentifie les utilisateurs d’identités Microsoft auprès de notre service d’identité Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Un exemple qui montre comment vous pouvez utiliser une bibliothèque tierce pour générer une application Android qui authentifie les utilisateurs d’identités Microsoft auprès de notre service d’identité B2C et qui appelle une API web à l’aide de jetons d’accès OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Un exemple qui montre comment une application .NET de bureau Windows (WPF) peut connecter un utilisateur à l’aide d’Azure AD B2C, récupérer un jeton d’accès à l’aide de MSAL.NET et appeler une API. | 
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier les utilisateurs via Azure Active Directory B2C et accéder à une API web avec les jetons générés. |

## <a name="web-apps-and-apis"></a>API et applications web

| Exemple | Description |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Un exemple combiné d’application web .NET qui appelle une API web .NET, toutes deux protégées via Azure AD B2C. |
| [dotnetcore-webapp](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | Une application web ASP.NET Core qui peut connecter un utilisateur à l’aide d’Azure AD B2C, récupérer un jeton d’accès à l’aide de MSAL.NET et appeler une API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Une application Node.js qui offre un moyen simple et rapide pour configurer une application web avec Express à l’aide d’OpenID Connect. |
| [javascript-nodejs-webapp](https://github.com/AzureADQuickStarts/active-directory-b2c-javascript-nodejs-webapp) | Un serveur node.js qui offre un moyen simple et rapide pour configurer un service API REST à l’aide du protocole OAuth2. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Une petite API web node.js pour Azure AD B2C qui montre comment protéger votre API web et accepter les jetons d’accès B2C à l’aide de passport.js. |

## <a name="single-page-apps"></a>Applications à page unique

| Exemple | Description |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Une application monopage (SPA) appelant une API web. L’authentification est effectuée avec Azure AD B2C en utilisant MSAL.js. | 
| [javascript-hellojs-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp) | Une application monopage implémentée avec un serveur principal d’API web ASP.NET, qui inscrit et connecte des utilisateurs à l’aide d’Azure AD B2C et qui appelle l’API web à l’aide des jetons d’accès OAuth 2.0. |