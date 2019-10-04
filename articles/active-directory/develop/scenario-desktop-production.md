---
title: Application de bureau appelant des API web (passage en production) - plateforme d’identités Microsoft
description: Apprenez à générer une application de bureau qui appelle des API web (passage en production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268337"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Application de bureau appelant des API web : passage en production

Cet article présente des informations pour améliorer davantage votre application et la faire passer en production.

## <a name="handling-errors-in-desktop-applications"></a>Gestion des erreurs dans les applications de bureau

Dans les différents flux, vous avez appris à gérer les erreurs des flux silencieux (comme montré dans les extraits de code). Nous avons également vu qu’il y a des cas où une interaction est nécessaire (consentement incrémentiel et accès conditionnel).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Obtenir le consentement de l’utilisateur à l’avance pour plusieurs ressources

> [!NOTE]
> L’obtention d’un consentement pour plusieurs ressources fonctionne pour la plateforme d’identités Microsoft, mais pas pour Azure Active Directory (Azure AD) B2C. Azure AD B2C prend en charge le consentement de l’administrateur uniquement, et pas le consentement de l’utilisateur.

Le point de terminaison de la plateforme d’identités Microsoft (v2.0) ne vous permet pas d’obtenir un jeton pour plusieurs ressources à la fois. Par conséquent, le paramètre `scopes` ne peut contenir que les étendues d’une seule ressource. Vous pouvez veiller à ce que l’utilisateur consente d’avance plusieurs ressources via le paramètre `extraScopesToConsent`.

Par exemple, si vous avez deux ressources, ayant chacune deux étendues :

- `https://mytenant.onmicrosoft.com/customerapi` - avec 2 étendues `customer.read` et `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` - avec 2 étendues `vendor.read` et `vendor.write`

Vous devez utiliser le modificateur `.WithAdditionalPromptToConsent` ayant le paramètre `extraScopesToConsent`.

Exemple :

### <a name="in-msalnet"></a>Dans MSAL.NET

```CSharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>Dans MSAL pour iOS et macOS

Objective-C :

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];
    
NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
    
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift :

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]
        
let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
        
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Cet appel vous permet d’obtenir un jeton d’accès pour la première API web.

Lorsque vous avez besoin d’appeler la deuxième API web, vous pouvez appeler l’API `AcquireTokenSilent` :

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Le compte personnel Microsoft nécessite un nouveau consentement à chaque exécution de l’application

Pour les utilisateurs de comptes personnels Microsoft, il est de rigueur de redemander le consentement sur chaque client natif (application mobile/de bureau) pour obtenir l’autorisation. L’identité d’un client natif n’est pas sécurisée par nature (contrairement à une application cliente confidentielle qui échange un secret avec la plateforme d’identité Microsoft pour prouver son identité). La plateforme d’identité Microsoft a choisi d’atténuer ce manque de sécurité pour les services de consommateur en invitant l’utilisateur à fournir son consentement à chaque fois que l’application est autorisée.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
