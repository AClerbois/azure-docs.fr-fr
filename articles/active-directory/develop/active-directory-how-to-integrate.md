---
title: Apprendre à intégrer à Azure Active Directory | Microsoft Docs
description: Découvrez les avantages de l’intégration de votre application avec Azure Active Directory et obtenir des ressources de fonctionnalités telles que simplifiée connectez-vous, gestion des identités, l’authentification multifacteur et contrôle d’accès.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726d70c46dca712deaf4846c24976cdabcc49be9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300456"
---
# <a name="integrating-with-azure-active-directory"></a>Intégration avec Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Dans cet article, vous découvrez les avantages de l’intégration de votre application avec Azure Active Directory (Azure AD) et obtenez des ressources pour l’intégration. Azure AD offre aux organisations de niveau entreprise identity management pour les applications cloud. L’intégration d'Azure AD offre à vos utilisateurs une expérience de connexion rationalisée et permet à votre application d’être conforme à la stratégie informatique.

## <a name="how-to-integrate"></a>Apprendre à intégrer

Il existe plusieurs façons d’intégrer votre application à Azure AD. Bénéficiez d’autant de ces scénarios nécessaires à votre application.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Prise en charge Azure AD comme un moyen de se connecter à votre application

**Réduction des problèmes de connexion et réduction des coûts de prise en charge.**  En utilisant Azure AD pour se connecter à votre application, vos utilisateurs n'auront pas un nom et un mot de passe supplémentaires à mémoriser. En tant que développeur, vous aurez un mot de passe en moins à stocker et à protéger. Ne pas avoir à gérer les réinitialisations de mots de passe oubliés peut être en soi une économie considérable. Azure AD alimente les connexions pour certaines des applications cloud les plus populaires au monde, y compris Office 365 et Microsoft Azure. Avec des centaines de millions d'utilisateurs dans des millions d'organisations, il est fort probable que votre utilisateur soit déjà connecté à Azure AD. En savoir plus sur [l’ajout de la prise en charge pour la connexion Azure AD](authentication-scenarios.md).

**Simplifiez l’inscription à votre application.**   Lors de l'inscription de votre application, Azure AD peut envoyer des informations essentielles sur un utilisateur pour vous permettre de remplir au préalable votre formulaire d'inscription ou de le supprimer complètement. Les utilisateurs peuvent s’inscrire à votre application à l'aide de leur compte Azure AD via une expérience de consentement courante, similaire à celles des réseaux sociaux et des applications mobiles. N'importe quel utilisateur peut s'inscrire et se connecter à une application qui est intégrée à Azure AD sans avoir besoin de l’intervention des services informatiques. En savoir plus sur [l’inscription de votre application pour la connexion d’un compte Azure AD](../../app-service/configure-authentication-provider-aad.md) .

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Rechercher des utilisateurs, gérer l’approvisionnement des utilisateurs et contrôler l’accès à votre application

**Rechercher des utilisateurs dans le répertoire.**   Utilisez l'API Graph pour la recherche d’utilisateurs et recherchez d'autres personnes dans leur organisation lorsque vous invitez d'autres personnes ou que vous leur permettez l’accès, au lieu de leur demander de taper leur adresse e-mail. Les utilisateurs peuvent effectuer leur recherche dans une interface similaire à un carnet d’adresses, notamment en affichant les détails de la hiérarchie organisationnelle. En savoir plus sur [l’API Graph](active-directory-graph-api.md).

**Réutilisation des groupes Active Directory et des listes de distribution déjà gérées par votre client.**   Azure AD contient les groupes que votre client utilise déjà pour la distribution d’e-mail et la gestion des accès. À l'aide de l'API Graph, réutiliser ces groupes au lieu de demander à votre client de créer et de gérer un ensemble de groupes distinct dans votre application. Les informations sur les groupes peuvent également être envoyées à votre application dans les jetons de connexion. En savoir plus sur l’ [API Graph](active-directory-graph-api.md).

**Utilisation d’Azure AD pour contrôler quels utilisateurs ont accès à votre application.**   Les administrateurs et les propriétaires d’applications dans Azure AD peuvent attribuer l’accès aux applications à des utilisateurs et des groupes spécifiques. À l'aide de l'API Graph, vous pouvez lire cette liste et l'utiliser pour contrôler l’attribution de privilèges d’accès et la suppression de privilèges d’accès pour les ressources et l’accès à votre application.

**Utilisation d’Azure AD pour les rôles en fonction d’Access Control.**   Les administrateurs et les propriétaires d'applications peuvent attribuer des utilisateurs et des groupes aux rôles que vous définissez lorsque vous enregistrez votre application dans Azure AD. Les informations sur les rôles sont envoyées à votre application dans des jetons de connexion et peuvent également être lues à l'aide de l'API Graph. En savoir plus sur l’ [utilisation d'Azure AD pour les autorisations](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Accéder à l’utilisateur profil, calendrier, messagerie, contacts, fichiers et bien plus encore

**Azure AD est le serveur d'autorisation pour Office 365 et d'autres services métiers de Microsoft.**   Si vous prenez en charge Azure AD pour la connexion à votre application ou si vous prenez en charge la liaison de vos comptes d'utilisateur actuels aux comptes d'utilisateur Azure AD à l'aide d'OAuth 2.0, vous pouvez demander l'accès en lecture et en écriture à un profil utilisateur, un calendrier, des e-mails, des contacts, des fichiers et d’autres informations. Vous pouvez écrire des événements en toute transparence dans le calendrier de l'utilisateur et lire ou écrire des fichiers dans leur OneDrive. En savoir plus sur l’ [accès aux API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promouvez votre application dans Azure et des places de marché Office 365

**Promouvez votre application aux millions d'organisations qui utilisent déjà Azure AD.**   Les utilisateurs qui recherchent ces marketplace utilisent déjà un ou plusieurs services cloud, ce qui les définit en tant que clients de service cloud. En savoir plus sur la promotion de votre application dans [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Lorsque des utilisateurs s’inscrivent à votre application, cela apparaîtra dans leur panneau d'accès Azure AD et le lanceur d'applications Office 365.**   Les utilisateurs pourront rapidement et facilement retourner à votre application plus tard, ce qui améliore ainsi l’engagement utilisateur. En savoir plus sur le [panneau d'accès Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Sécuriser les communications appareil-à-service et de service à service

**L’utilisation d'Azure AD pour la gestion des identités des services et des périphériques réduit le code que vous devez écrire et permet au service informatique de gérer les accès.**   Les services et périphériques peuvent obtenir des jetons d'Azure AD à l'aide d'OAuth et utiliser ces jetons pour accéder aux API web. Grâce à Azure AD, vous pouvez éviter d'écrire du code d'authentification complexe. Étant donné que les identités des services et des périphériques sont stockées dans Azure AD, le service informatique peut gérer les clés et la révocation dans un même endroit plutôt que de faire cela séparément dans votre application.

## <a name="benefits-of-integration"></a>Avantages de l’intégration

L’intégration à Azure AD est fournie avec des avantages qui ne nécessitent pas d'écrire du code supplémentaire.

### <a name="integration-with-enterprise-identity-management"></a>Intégration avec la gestion des identités

**Aidez votre application à se conformer aux stratégies informatiques.**   Les organisations intègrent leurs systèmes de gestion des identités d’entreprise à Azure AD, donc si une personne quitte une organisation, elle perdra automatiquement accès à votre application sans que le service informatique ait besoin de prendre des mesures supplémentaires. Le service informatique peut gérer quels utilisateurs peuvent accéder à votre application et déterminer quelles sont les stratégies d'accès requises (par exemple, une authentification multifacteur), réduisant ainsi la nécessité d'écrire du code pour se conformer aux stratégies d'entreprise complexes. Azure AD fournit aux administrateurs un journal d'audit détaillé des utilisateurs qui se sont connectés à votre application, ce qui permet au service informatique d’effectuer un suivi de l’utilisation.

**Azure AD étend Active Directory au cloud afin que votre application puisse être intégrée à Active Directory.**   De nombreuses organisations dans le monde utilisent Active Directory comme leur principal système de gestion des identités et des connexions et ont besoin que leurs applications fonctionnent avec Active Directory. L’intégration avec Azure AD permet d’intégrer votre application à Active Directory.

### <a name="advanced-security-features"></a>Fonctions de sécurité avancées

**Authentification multifacteur**   Azure AD fournit une authentification multifacteur native. Les administrateurs informatiques peuvent avoir besoin d'une authentification multifacteur pour accéder à votre application afin que vous n'ayez pas à écrire le code vous-même. En savoir plus sur l’ [authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Détection de connexion anormale**   Azure AD traite plus d'un milliard de connexions par jour, tout en utilisant des algorithmes d'apprentissage automatique pour détecter des activités suspectes et signaler tout problème éventuel aux administrateurs informatiques. En prenant en charge la connexion à Azure AD, votre application bénéficie de cette protection. En savoir plus sur [l’affichage des rapports d'accès Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Accès conditionnel.**   Outre l'authentification multifacteur, les administrateurs peuvent exiger que les utilisateurs remplissent certaines conditions avant qu’ils puissent se connecter à votre application. Les conditions qui peuvent être définies incluent la plage d'adresses IP d’un périphérique client, l'appartenance à des groupes spécifiques et l'état du périphérique utilisé pour l'accès. En savoir plus sur [l’accès conditionnel d’Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Facilitent le développement

**Protocoles standard de l’industrie.**  Microsoft s'engage à prendre en charge les normes industrielles. La plateforme d’identité Microsoft prend en charge les protocoles standard OAuth 2.0 et OpenID Connect 1.0. En savoir plus sur [protocoles d’authentification Microsoft identity plateforme](active-directory-v2-protocols.md).

**Bibliothèques open source.**   Microsoft fournit les bibliothèques open source entièrement prises en charge pour les langages et plateformes courants afin d’accélérer le développement. Le code source est acquis sous licence sous Apache 2.0 et vous êtes libre de répliquer et de contribuer aux projets. En savoir plus sur [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Présence dans le monde entier et haute disponibilité

**Azure AD est déployé dans des centres de données dans le monde entier et est géré et surveillé en permanence.**  Azure AD est le système de gestion des identités de Microsoft Azure et Office 365 et est déployé dans 28 centres de données dans le monde entier. Les données d’Active Directory sont garanties pour être répliquées sur au moins trois centres de données. Les équilibrages de charge globaux garantissent aux utilisateurs un accès à la copie la plus fidèle d'Azure AD contenant leurs données. Ils retransmettent automatiquement les demandes vers d’autres centres de données si un problème est détecté.

## <a name="next-steps"></a>Étapes suivantes

[Commencer à écrire du code](v2-overview.md#getting-started).

[Connecter les utilisateurs à l’aide de la plateforme d’identité Microsoft](authentication-scenarios.md)

