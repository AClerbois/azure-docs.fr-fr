---
title: Bibliothèques d’authentification Azure Active Directory | Microsoft Docs
description: La bibliothèque d'authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d'obtenir des jetons d'accès pour sécuriser les appels d'API.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 143ee99c581db052c1dbcbf46decce356ee96fa6
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258054"
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliothèques d’authentification d’Azure Active Directory

La bibliothèque d’authentification Azure Active Directory v1.0 (ADAL, Azure Active Directory Authentication Library) permet aux développeurs d’applications d’authentifier les utilisateurs dans une instance locale d’Active Directory (AD) ou dans le cloud, puis d’obtenir des jetons pour sécuriser les appels d’API. La bibliothèque ADAL facilite l’authentification pour les développeurs grâce à des fonctionnalités comme :

- Un cache de jeton configurable qui stocke les jetons d’accès et les jetons d’actualisation
- L’actualisation automatique des jetons lorsqu’un jeton d’accès expire et qu’un jeton d’actualisation est disponible
- La prise en charge des appels de méthode asynchrones

> [!NOTE]
> Vous recherchez les bibliothèques Azure AD v2.0 (MSAL) ? Consultez le [guide des bibliothèques MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Bibliothèques clientes prises en charge par Microsoft

| Plateforme | Bibliothèque | Download | Code source | Exemple | Informations de référence
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS et Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Application de bureau](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Référence](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET Client, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Application de bureau](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Application à page unique](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Application iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Référence](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Référentiel Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Application Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Application web Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)| |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Applications web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Application Web Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | [Référence](http://adal-python.readthedocs.io/en/latest/) |

## <a name="microsoft-supported-server-libraries"></a>Bibliothèques serveur prises en charge par Microsoft

| Plateforme | Bibliothèque | Download | Code source | Exemple | Informations de référence
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pour Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Application MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pour OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Application web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN pour WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Application Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensions de protocole d’identité pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestionnaire JWT pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants d’utilisation de la bibliothèque ADAL sur un client qui accède à une ressource distante :

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Authentification des utilisateurs d’une application cliente native exécutée sur un appareil

Dans ce scénario, un développeur dispose d’une application mobile cliente ou de bureau qui doit accéder à une ressource distante, par exemple une API web. L’API web n’autorise pas les appels anonymes et doit être appelée dans le contexte d’un utilisateur authentifié. L’API web est préconfigurée de manière à approuver les jetons d’accès émis par un locataire Azure AD spécifique. Azure AD est préconfiguré pour émettre des jetons d’accès pour cette ressource. Pour appeler l’API web à partir du client, le développeur utilise la bibliothèque ADAL afin de faciliter l’authentification auprès d’Azure AD. La méthode d’utilisation de la bibliothèque ADAL la plus sûre consiste à faire en sorte qu’elle affiche l’interface utilisateur pour recueillir les informations d’identification de l’utilisateur (sous forme de fenêtre de navigateur).

La bibliothèque ADAL facilite l’authentification de l’utilisateur, l’obtention d’un jeton d’accès et d’un jeton d’actualisation à partir d’Azure AD, puis l’appel de l’API web à l’aide du jeton d’accès.

Pour voir un exemple de code qui illustre ce scénario en utilisant l’authentification auprès d’Azure AD, consultez la section [Native Client WPF Application to Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Authentification d’une application cliente confidentielle exécutée sur un serveur web

Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante, par exemple une API web. L’API web n’autorisant pas les appels anonymes, elle doit être appelée à partir d’un service autorisé. L’API web est préconfigurée de manière à approuver les jetons d’accès émis par un locataire Azure AD spécifique. Azure AD est préconfiguré pour délivrer des jetons d’accès pour cette ressource à un service avec des informations d’identification client (ID client et secret). La bibliothèque ADAL facilite l’authentification du service auprès d’Azure AD en retournant un jeton d’accès qui peut être utilisé pour appeler l’API web. La bibliothèque ADAL gère également la durée de vie du jeton d'accès en le mettant en cache et en le renouvelant si nécessaire. Pour voir un exemple de code qui illustre ce scénario, consultez [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Authentification d’une application cliente confidentielle exécutée sur un serveur, pour le compte d’un utilisateur

Dans ce scénario, un développeur a une application web fonctionnant sur un serveur qui doit accéder à une ressource distante, par exemple une API web. L’API web n’autorisant pas les appels anonymes, elle doit être appelée à partir d’un service autorisé pour le compte d’un utilisateur authentifié. L’API web est préconfigurée de manière à approuver les jetons d’accès émis par un locataire Azure spécifique, et Azure AD est préconfiguré pour délivrer des jetons d’accès pour cette ressource à un service avec des informations d’identification client. Une fois l’utilisateur authentifié dans l’application web, l’application peut obtenir d’Azure AD un code d’autorisation pour l’utilisateur. L'application web peut ensuite utiliser ADAL pour obtenir d’Azure AD un jeton d'accès et un jeton d’actualisation pour le compte de l'utilisateur en utilisant le code d'autorisation et les informations d’identification client associés à l'application. Une fois l'application web en possession du jeton d'accès, elle peut appeler l'API web jusqu’à expiration du jeton. Lorsque le jeton expire, l'application web peut utiliser la bibliothèque ADAL pour obtenir un nouveau jeton d'accès en utilisant le jeton d’actualisation reçu précédemment. Pour voir un exemple de code qui illustre ce scénario, consultez [Native client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Voir aussi

- [Le guide du développeur Azure Active Directory](active-directory-developers-guide.md)
- [Scénarios d’authentification pour Azure Active Directory](active-directory-authentication-scenarios.md)
- [Exemples de code Azure Active Directory](active-directory-code-samples.md)
