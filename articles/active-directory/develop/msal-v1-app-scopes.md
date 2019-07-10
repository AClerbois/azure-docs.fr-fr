---
title: Étendues pour une application v1.0 (Microsoft Authentication Library) | Azure
description: Découvrez les étendues pour une application v1.0 à l’aide de la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514368"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Étendues pour une API web acceptant des jetons v1.0

Les autorisations OAuth2 sont des étendues d’autorisation qu’une application (ressource) de l’API web Azure AD pour développeurs (v1.0) expose aux applications clientes. Ces étendues d’autorisation peuvent être accordées aux applications clientes durant le consentement. Consultez la section sur `oauth2Permissions` dans la [référence du manifeste de l’application Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Étendues pour demander l’accès à des autorisations OAuth2 spécifiques d’une application v1.0
Pour acquérir des jetons pour des étendues spécifiques d’une application v1.0 (par exemple, Azure AD Graph, à savoir https:\//graph.windows.net), vous devez créer des étendues en concaténant un identificateur de ressource de votre choix avec une autorisation OAuth2 souhaitée pour cette ressource.

Par exemple, pour l’accès au nom de l’utilisateur, une API web v1.0 où l’URI de l’identifiant de l’application est `ResourceId` :

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Pour lire et écrire avec MSAL.NET Azure Active Directory à l’aide de l’API Azure AD Graph (https:\//graph.windows.net/), vous devez créer la liste des étendues, comme dans ce qui suit :

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Si vous voulez écrire l’étendue correspondant à l’API Azure Resource Manager (https:\//management.core.windows.net/), vous devez demander l’étendue suivante (notez les deux barres obliques) :

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Vous devez utiliser deux barres obliques, car l’API Azure Resource Manager attend une barre oblique dans sa revendication d’audience (aud) et il y a une barre oblique qui sépare le nom de l’API de l’étendue.

La logique utilisée par Azure AD est la suivante :

- Pour le point de terminaison ADAL (v1.0) avec jeton d’accès v1.0 (le seul possible), aud=resource
- Pour MSAL (point de terminaison de la plateforme d’identité Microsoft (v2.0)) qui demande un jeton d’accès pour une ressource qui accepte des jetons v2.0, aud=resource.AppId
- Pour le point de terminaison MSAL (v2.0) qui demande un jeton d’accès pour une ressource qui accepte un jeton d’accès v1.0 (ce qui correspond au cas ci-dessus), Azure AD analyse l’audience souhaitée d’après l’étendue demandée en prenant tout ce qui précède la dernière barre oblique et en l’utilisant comme identificateur de la ressource. Par conséquent, si https:\//database.windows.net attend une audience de https:\//database.windows.net/, vous devrez demander une étendue de https:\//database.windows.net//.default. Voir également problème GitHub [#747 : la barre oblique de fin est omise dans l’URL de la ressource, ce qui a entraîné un échec d’authentification sql ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Étendues pour demander l’accès à toutes les autorisations d’une application v1.0
Si vous voulez acquérir un jeton pour toutes les étendues statiques d’une application v1.0, ajoutez « .default » à l’URI d’ID de l’application de l’API :

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Étendues à demander pour le flux d’informations d’identification du client/l’application de démon
En cas de flux d’informations d’identification du client, l’étendue à passer est également `/.default`. Cela indique à Azure AD toutes les autorisations au niveau de l’application que l’administrateur a acceptées dans l’inscription de l’application.
