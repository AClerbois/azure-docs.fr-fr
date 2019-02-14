---
title: Applications API web dans Azure Active Directory
description: Décrit les applications API web et les principes de base sur le flux de protocole, l’inscription et l’expiration du jeton pour ce type d’application.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b477171be0f306431b0f7c5965ebede4f4680c22
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201905"
---
# <a name="web-api"></a>API Web

Les applications API web sont des applications web qui doivent obtenir des ressources d’une API web. Dans ce scénario, il existe deux types d’identité que l’application web peut utiliser pour authentifier et appeler l’API web :

- **Identité d’application** : ce scénario utilise l’octroi d’informations d’identification client OAuth 2.0 pour l’authentification en tant qu’application et l’accès à l’API web. Avec une identité d’application, l’API web peut détecter uniquement que l’application web l’appelle, car elle ne reçoit aucune information sur l’utilisateur. Si l’application reçoit des informations sur l’utilisateur, celles-ci sont envoyées via le protocole d’application et ne sont pas signées par Azure AD. L’API web suppose que l’application web a authentifié l’utilisateur. C’est pour cette raison que ce modèle est appelé « sous-système approuvé ».
- **Identité d’utilisateur délégué**. Ce scénario peut être réalisé de deux manières : OpenID Connect et octroi du code d’autorisation OAuth 2.0 avec un client confidentiel. L’application web obtient un jeton d’accès pour l’utilisateur, ce qui prouve à l’API web que l’utilisateur s’est correctement authentifié auprès de l’application web et que l’application web a pu obtenir une identité d’utilisateur délégué pour appeler l’API web. Ce jeton d’accès est envoyé via la demande à l’API web, qui autorise l’utilisateur et renvoie la ressource souhaitée.

L’identité d’application et l’identité d’utilisateur délégué sont décrites dans le flux ci-après. La différence principale entre ces deux types d’identité est que l’identité d’utilisateur délégué doit obtenir un code d’autorisation avant que l’utilisateur puisse se connecter et accéder à l’API web.

## <a name="diagram"></a>Diagramme

![Diagramme Application web vers API web](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Flux de protocole

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identité d’application avec octroi d’informations d’identification client OAuth 2.0

1. Un utilisateur est connecté à Azure AD dans l’application web (voir la section **Applications web** pour plus d’informations).
1. L’application web doit obtenir un jeton d’accès pour pouvoir s’authentifier auprès de l’API web et extraire la ressource souhaitée. Elle envoie une requête au point de terminaison de jeton d’Azure AD, avec les informations d’identification, l’ID d’application et l’URI ID d’application de l’API web.
1. Azure AD authentifie l’application et renvoie un jeton d’accès JWT, qui est utilisé pour appeler l’API web.
1. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

### <a name="delegated-user-identity-with-openid-connect"></a>Identité d’utilisateur délégué avec OpenID Connect

1. Un utilisateur est connecté à une application web via Azure AD (voir la section Navigateur web vers application web ci-dessus). Si l’utilisateur de l’application web n’a pas encore consenti à autoriser l’application web à appeler l’API web en son nom, il doit donner son consentement. L’application affiche les autorisations nécessaires et, si certaines d’entre elles sont des autorisations administrateur, un utilisateur normal de l’annuaire ne peut pas donner le consentement. Ce processus de consentement s’applique uniquement aux applications mutualisées, pas aux applications à client unique, car l’application a déjà les autorisations nécessaires. Quand l’utilisateur s’est connecté, l’application web a reçu un jeton d’ID avec les informations sur l’utilisateur, ainsi qu’un code d’autorisation.
1. À l’aide du code d’autorisation émis par Azure AD, l’application web envoie une demande au point de terminaison de jeton d’Azure AD. Celle-ci comprend le code d’autorisation, des détails sur l’application cliente (ID d’application et URI de redirection) et la ressource souhaitée (URI ID d’application de l’API web).
1. Le code d’autorisation et les informations sur l’application web et l’API web sont validés par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.
1. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identité d’utilisateur délégué avec octroi de code d’autorisation OAuth 2.0

1. Un utilisateur est déjà connecté à une application web, dont le mécanisme d’authentification est indépendant d’Azure AD.
1. L’application web a besoin d’un code d’autorisation pour obtenir un jeton d’accès et adresse donc une demande au point de terminaison d’autorisation d’Azure AD via le navigateur, en indiquant l’ID d’application et l’URI de redirection de l’application web une fois l’authentification réussie. L’utilisateur se connecte à Azure AD.
1. Si l’utilisateur de l’application web n’a pas encore consenti à autoriser l’application web à appeler l’API web en son nom, il doit donner son consentement. L’application affiche les autorisations nécessaires et, si certaines d’entre elles sont des autorisations administrateur, un utilisateur normal de l’annuaire ne peut pas donner le consentement. Ce consentement s’applique à l’application unique et multi-locataire. Dans le cas d’un client unique, un administrateur peut effectuer le consentement d’administrateur pour consentir pour le compte de leurs utilisateurs. Cela est possible à l’aide du bouton `Grant Permissions` situé dans le [portail Azure](https://portal.azure.com). 
1. Une fois que l’utilisateur a donné son consentement, l’application web reçoit le code d’autorisation dont elle a besoin pour obtenir un jeton d’accès.
1. À l’aide du code d’autorisation émis par Azure AD, l’application web envoie une demande au point de terminaison de jeton d’Azure AD. Celle-ci comprend le code d’autorisation, des détails sur l’application cliente (ID d’application et URI de redirection) et la ressource souhaitée (URI ID d’application de l’API web).
1. Le code d’autorisation et les informations sur l’application web et l’API web sont validés par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.
1. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

## <a name="code-samples"></a>Exemples de code

Consultez les exemples de code pour les scénarios du type application web vers API web. Et consultez régulièrement cette page à laquelle nous ajoutons fréquemment de nouveaux exemples. [Application web vers API web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Inscription d'application

Pour inscrire une application auprès du point de terminaison Azure AD v1.0, consultez [Inscrire une application auprès du point de terminaison Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

* Application à locataire unique : pour les identités d’application et les identités d’utilisateur délégué, l’application web et l’API web doivent être inscrites dans le même annuaire dans Azure AD. L’API web peut être configurée pour exposer un ensemble d’autorisations utilisées pour limiter l’accès de l’application web à ses ressources. Si une identité d’utilisateur délégué est utilisée, l’application web doit sélectionner les autorisations souhaitées dans le menu déroulant **Autorisations pour d’autres applications** du portail Azure. Cette étape n’est pas requise si une identité d’application est utilisée.
* Application mutualisée : tout d’abord, l’application web est configurée pour indiquer les autorisations dont elle a besoin pour fonctionner. Cette liste d’autorisations requises s’affiche dans une boîte de dialogue quand un utilisateur ou un administrateur de l’annuaire de destination donne son consentement à l’application, ce qui la met à disposition de son organisation. Certaines applications nécessitent uniquement des autorisations au niveau utilisateur pour lesquelles tous les utilisateurs de l’organisation peuvent donner leur consentement. D’autres nécessitent des autorisations administrateur, pour lesquelles un utilisateur de l’organisation ne peut pas donner son consentement. Seul un administrateur d’annuaires peut donner son consentement aux applications qui requièrent des autorisations de ce niveau. Une fois le consentement donné par l’utilisateur ou l’administrateur, l’application web et l’API web sont inscrites dans l’annuaire de l’organisation de l’utilisateur ou de l’administrateur.

## <a name="token-expiration"></a>Expiration du jeton

Quand l’application web utilise son code d’autorisation pour obtenir un jeton d’accès JWT, elle reçoit également un jeton d’actualisation JWT. À l’expiration du jeton d’accès, le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur à nouveau sans lui demander de se connecter une nouvelle fois. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui donne lieu à la création d’un nouveau jeton d’accès et d’un nouveau jeton d’actualisation.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les autres [types d’applications et scénarios](app-types.md)
- En savoir plus sur les [principes de base de l’authentification](authentication-scenarios.md) Azure AD
