---
title: Exemples de code Azure Active Directory | Microsoft Docs
description: Fournit un index des exemples de code Azure Active Directory (point de terminaison v1.0), organisés par scénario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c199df2012e6ce3b3de5f34989cf5cbc135fdc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101021"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Exemples de code Azure Active Directory (point de terminaison v1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (Azure AD) pour ajouter l'authentification et l'autorisation à vos applications web et API web.

Cette section fournit des liens vers des exemples que vous pouvez utiliser pour en savoir plus sur le point de terminaison Azure AD v1.0. Ces exemples vous montrent comment procéder et contiennent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Le code est également accompagné de commentaires pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par des exemples de code Azure AD V2, consultez [Exemples de code v2.0 par scénario](sample-v2-code.md).

Pour comprendre le scénario de base de chaque type d’exemple, consultez [Scénarios d’authentification pour Azure AD](authentication-scenarios.md).

Vous pouvez également contribuer à nos exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Applications monopages

Cet exemple montre comment écrire une application monopage sécurisée avec Azure AD.

 Plateforme | Appelle sa propre API | Appelle une autre API web
 -------- |  --------------------- | ------------------ 
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Applications web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Applications web connectant les utilisateurs, appelant Microsoft Graph ou une API web avec l’identité de l’utilisateur

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certaines de ces applications appellent également Microsoft Graph ou votre propre API web dans le nom de l’utilisateur connecté.

 Plateforme | Connecte uniquement les utilisateurs | Appelle Microsoft Graph ou AAD Graph| Appelle une autre API web ASP.NET ou ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Php](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Applications web illustrant le contrôle d’accès en fonction du rôle (autorisation)

Les exemples suivants montrent comment implémenter le contrôle d’accès en fonction du rôle (RBAC). Le contrôle d’accès en fonction du rôle permet de restreindre les autorisations de certaines fonctionnalités dans une application web à certains utilisateurs. Les utilisateurs sont autorisés selon qu’ils appartiennent à un **groupe Azure AD** ou qu’ils ont un **rôle** d’application donné.

Plateforme | Exemple |
 -------- | ------------------- |
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Application web MVC .NET 4.5 qui utilise des **rôles** Azure AD pour l’autorisation

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Applications mobiles clientes et de bureau appelant Microsoft Graph ou une API web

Les exemples suivants illustrent des applications clientes publiques (applications de bureau/mobiles) qui accèdent à l’API web ou à Microsoft Graph pour le compte d’un utilisateur. Selon les plateformes et les appareils, les applications peuvent connecter les utilisateurs de différentes façons (flux/privilèges) : 

- de manière interactive,
- de manière silencieuse (avec l’Authentification Windows intégrée sous Windows ou une combinaison nom d’utilisateur/mot de passe), 
- voire en déléguant la connexion interactive à un autre appareil (flux de code d’appareil utilisé sur les appareils qui ne fournissent pas de contrôles web).

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET ou ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureau (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interactive | Partie de [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Cet exemple utilise [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), et non [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (application UWP utilisant ADAL.NET pour appeler une API web de locataire unique) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (application UWP utilisant ADAL.NET pour appeler une API web multilocataire)|
Mobile (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Bureau (Console)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Nom d’utilisateur / mot de passe </p>  Authentification Windows intégrée | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Bureau (Console)          | ![Console Java](media/sample-v2-code/logo_Java.png) | Nom d’utilisateur / mot de passe | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Bureau (Console)           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | Flux de code d’appareil | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Applications de démon (accédant à des API web avec l’identité de l’application)

Les exemples suivants illustrent des applications web ou de bureau qui accèdent à l’API web ou Microsoft Graph sans utilisateur (avec l’identité de l’application).

Application cliente | Plateforme | Flux/Grant | Appelle une API web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Application de démon (Console)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Informations d’identification du client avec certificat ou secret d’application | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Application de démon (Console)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Informations d’identification du client avec certificat| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Application web ASP.NET  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Informations d'identification du client | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-active-directory"></a>API web protégée par Azure Active Directory

L’exemple suivant montre comment protéger une API web node.js avec Azure AD.

Dans les sections précédentes de cet article, vous pouvez également trouver d’autres exemples qui illustrent une application cliente **appelant** une **API web** ASP.NET ou ASP.NET Core. Ces exemples ne sont pas mentionnés dans cette section, mais vous les trouverez dans la dernière colonne des tableaux ci-dessus ou ci-dessous

Plateforme | Exemple
 -------- | -------------------
![Php](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API web appelant Microsoft Graph ou une autre API web

Les exemples suivants présentent une API web qui appelle une autre API web. Le deuxième exemple montre comment gérer l’accès conditionnel.

 Plateforme |  Appelle Microsoft Graph | Appelle une autre API web ASP.NET ou ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour obtenir des exemples et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

[Guide du développeur Azure Active Directory](v1-overview.md)

[Bibliothèques d’authentification Azure Active Directory](active-directory-authentication-libraries.md)

[Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
