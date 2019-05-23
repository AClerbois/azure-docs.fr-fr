---
title: Évolution de la plateforme d’identités Microsoft - Azure
description: En savoir plus sur la plateforme d’identités Microsoft, une évolution de la plateforme de développeur et de service d’identité Azure Active Directory (Azure AD).
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 807045089f70e117d46754412d974be7fba5a77a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832495"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Évolution de la plateforme d’identités Microsoft

La plateforme d’identités Microsoft est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent les utilisateurs et obtiennent des jetons pour appeler des API comme Microsoft Graph ou des API que des développeurs ont créées. Elle se compose d’un service d’authentification, de bibliothèques open source, de fonctionnalités d’inscription et de configuration d’application (via un portail des développeurs et une API d’application), d’une documentation de développement exhaustive, d’exemples de démarrage rapide, d’exemples de code, de didacticiels, de guides pratiques et autres contenus destinés aux développeurs. La plateforme d’identités Microsoft prend en charge les protocoles standard tels qu’OAuth 2.0 et OpenID Connect.

Jusqu’à présent, la plupart des développeurs utilisaient la plateforme Azure AD v1.0 pour authentifier les comptes professionnels et scolaires (approvisionnés par Azure AD) en demandant des jetons du point de terminaison Azure AD v1.0, à l’aide de la bibliothèque d’authentification Azure AD (ADAL), du portail Azure pour l’inscription et la configuration de l’application, et de l’API Graph Azure AD pour la configuration de l’application par programme.

Avec la plateforme d’identités Microsoft (v2.0), atteignez également ces types d’utilisateurs :

- Comptes professionnels et scolaires (comptes Azure AD approvisionnés)
- Comptes personnels (comme Outlook.com ou Hotmail.com)
- Vos clients qui ont déjà leur propre adresse e-mail ou identité sociale (comme LinkedIn, Facebook, Google) via l’offre Azure AD B2C

Avec la plateforme d’identités Microsoft unifiée, vous pouvez écrire du code une seule fois et authentifier n’importe quelle identité Microsoft dans votre application. Pour plusieurs plateformes, il existe une bibliothèque open source entièrement prise en charge appelée Bibliothèque d’authentification Microsoft (MSAL). La bibliothèque MSAL est simple à utiliser, offre d’excellentes expériences d’authentification unique (SSO) pour vos utilisateurs, vous aide à atteindre des niveaux de fiabilité et de performances élevés, et est développée avec Microsoft Secure Development Lifecycle (SDL). Lorsque vous appelez des API, vous pouvez configurer votre application pour tirer parti du consentement incrémentiel, qui vous permet de retarder la demande de consentement pour les étendues plus invasives jusqu’à ce que l’utilisation de l’application garantisse cela lors de l’exécution.

Vous pouvez utiliser le portail Azure pour inscrire et configurer votre application, et utiliser l’API Microsoft Graph pour la configuration d’application par programme.

Mettez à jour votre application à votre rythme. Les applications générées avec des bibliothèques ADAL sont toujours prises en charge. Les portefeuilles d’applications mixtes, composés d’applications générées avec des bibliothèques ADAL et d’applications générées avec des bibliothèques MSAL, sont également pris en charge. Cela signifie que les applications qui utilisent les bibliothèques ADAL et MSAL les plus récentes fournissent l’authentification unique pour l’ensemble du portefeuille, au moyen du cache de jeton partagé entre ces bibliothèques. Les applications mises à jour de la bibliothèque ADAL vers la bibliothèque MSAL conserveront l’état de connexion utilisateur lors de la mise à niveau.

## <a name="microsoft-identity-platform-experience"></a>Expérience de la plateforme d’identités Microsoft

Le diagramme suivant illustre l’expérience d’identité Microsoft à un haut niveau, notamment l’expérience d’inscription d’application, les kits de développement logiciel (SDK), les points de terminaison et les identités prises en charge.

![Plateforme d’identités Microsoft actuelle](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Expérience d’inscription d’application

L’expérience d’**[inscription d’application](https://go.microsoft.com/fwlink/?linkid=2083908)** du portail Azure est la seule expérience de portail qui vous permet de gérer toutes les applications que vous avez intégrées à la plateforme d’identités Microsoft. Si vous utilisez le portail d’inscription d’application, commencez plutôt à utiliser l’expérience d’inscription d’application du portail Azure.

Pour l’intégration à Azure AD B2C (lors de l’authentification d’identités sociales ou locales), vous devrez inscrire votre application dans un locataire B2C. Cette expérience fait également partie du portail Azure.

L’**API d’application dans Microsoft Graph** est actuellement en préversion. Utilisez cette API pour configurer par programme vos applications intégrées à la plateforme d’identités Microsoft pour l’authentification de n’importe quelle identité Microsoft. Toutefois, tant que cette API n’est pas en disponibilité générale, vous devez utiliser l’API Graph 1.6 Azure AD et le manifeste d’application.

### <a name="msal-libraries"></a>Bibliothèques MSAL

Vous pouvez utiliser la bibliothèque MSAL pour générer des applications qui authentifient toutes les identités Microsoft. Les bibliothèques MSAL en .NET sont en disponibilité générale. Les bibliothèques MSAL pour JavaScript, iOS et Android sont en préversion et conviennent pour une utilisation en environnement de production. Nous offrons la même prise en charge de niveau de production pour les bibliothèques MSAL en préversion que pour les versions de MSAL et ADAL qui sont en disponibilité générale.

Vous pouvez également utiliser les bibliothèques MSAL pour intégrer votre application à Azure AD B2C.

Les bibliothèques côté serveur pour générer des applications web et des API web sont en disponibilité générale : [ASP.NET](https://docs.microsoft.com/aspnet/overview) et [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Point de terminaison de la plateforme d’identités Microsoft

Le point de terminaison de la plateforme d’identités Microsoft (v2.0) est désormais certifié OIDC. Il fonctionne avec les bibliothèques MSAL (Microsoft Authentication Libraries) ou n’importe quelle autre bibliothèque conforme aux normes. Il implémente des étendues contrôlables de visu, conformément aux normes du secteur.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les versions v1.0 et v2.0.

* [Présentation de la plateforme d’identités Microsoft (v2.0)](v2-overview.md)
* [Présentation d’Azure Active Directory pour les développeurs (v1.0)](v1-overview.md)
