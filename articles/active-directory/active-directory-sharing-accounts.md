---
title: Partage de comptes à l’aide d’Azure AD | Microsoft Docs
description: Décrit comment Azure Active Directory permet aux organisations de partager des comptes en toute sécurité pour les applications locales et les services cloud grand public.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c8d7f1f5b793fcb684e9896bb93213a23d489860
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448541"
---
# <a name="sharing-accounts-with-azure-ad"></a>Partage de comptes avec Azure AD
## <a name="overview"></a>Vue d'ensemble
Parfois, les organisations doivent utiliser les mêmes nom d’utilisateur et mot de passe pour plusieurs personnes, généralement dans deux cas :

* Au moment d’accéder à des applications qui nécessitent un nom de connexion et un mot de passe uniques pour chaque utilisateur, qu’il s’agisse d’applications locales ou de services cloud grand public (par exemple, les comptes de réseaux sociaux d’entreprise).
* Pendant la création d’environnements multi-utilisateurs. Vous pouvez avoir un compte local unique qui dispose de privilèges élevés et est utilisé pour les activités d’installation, d’administration et de récupération de base. Par exemple, le compte local « administrateur général » pour Office 365 ou le compte racine dans Salesforce.

En règle générale, ces comptes sont partagés en distribuant les informations d’identification (nom d’utilisateur et mot de passe) aux personnes appropriées ou en les stockant dans un emplacement partagé où peuvent accéder plusieurs agents de confiance.

Le modèle traditionnel de partage présente plusieurs inconvénients :

* Pour autoriser l’accès à de nouvelles applications, vous devez distribuer les informations d’identification à toutes les personnes qui ont besoin d’un accès.
* Chaque application partagée peut nécessiter son propre jeu d’informations d’identification partagées, obligeant les utilisateurs à mémoriser plusieurs jeux d’informations d’identification. Si les utilisateurs doivent mémoriser plusieurs informations d’identification, le risque augmente qu’ils recourent à des pratiques risquées, comme écrire les mots de passe.
* Vous ne pouvez pas savoir qui a accès à une application.
* Vous ne pouvez pas savoir qui a *accédé* à une application.
* Quand vous supprimez l’accès à une application, vous devez mettre à jour les informations d’identification et les redistribuer à toutes les personnes qui ont besoin d’accéder à cette application.

## <a name="azure-active-directory-account-sharing"></a>Partage de compte Azure Active Directory
Azure AD introduit une nouvelle approche de l’utilisation des comptes partagés, qui élimine ces inconvénients.

L’administrateur Azure AD configure les applications auxquelles un utilisateur peut accéder en utilisant le volet d’accès et en choisissant le type d’authentification unique le mieux adapté pour l’application. Un de ces types, l’ *authentification unique avec mot de passe*, permet à Azure AD d’agir comme un genre d’intermédiaire pendant le processus d’ouverture de session pour cette application.

Les utilisateurs se connectent une fois avec leur compte professionnel. Ce compte est le même que celui qu’ils utilisent régulièrement pour accéder à leur Bureau ou e-mail. Ils peuvent découvrir les applications auxquelles ils sont affectés et accéder uniquement à celles-ci. Grâce aux comptes partagés, cette liste d’applications peut inclure un nombre quelconque d’informations d’identification partagées. L’utilisateur final n’a pas besoin de mémoriser ou de noter les différents comptes qu’il peut être amené à utiliser.

Outre accroître la supervision et améliorer la facilité d’utilisation, les comptes partagés renforcent votre sécurité. Les utilisateurs autorisés à utiliser les informations d’identification ne voient pas le mot de passe partagé, mais plutôt obtiennent l’autorisation d’utiliser le mot de passe dans le cadre d’un flux d’authentification orchestré. De plus, certaines applications d’authentification unique par mot de passe vous permettent d’utiliser Azure AD pour substituer (mettre à jour) régulièrement les mots de passe. Le système utilise des mots de passe longs et complexes, ce qui renforce la sécurité du compte. L’administrateur peut facilement accorder ou révoquer l’accès à une application et sait qui a accès au compte et qui y a accédé dans le passé.

Azure AD prend en charge les comptes partagés pour tous les utilisateurs détenteurs d’une licence Enterprise Mobility Suite (EMS), Premium ou De base, quel que le soit le type d’applications d’authentification unique avec mot de passe. Vous pouvez partager des comptes pour des milliers d’applications déjà intégrées dans la galerie d’applications et vous pouvez ajouter votre propre application d’authentification avec mot de passe aux [applications d’authentification unique personnalisées](manage-apps/configure-single-sign-on-portal.md).

Les fonctionnalités Azure AD qui permettent le partage de compte sont les suivantes :

* [Authentification unique avec mot de passe](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Agent d’authentification unique avec mot de passe
* [Affectation de groupe](users-groups-roles/groups-self-service-management.md)
* Applications de mot de passe personnalisé
* [Tableau de bord/rapports d’utilisation des applications](active-directory-passwords-get-insights.md)
* Portails d’accès des utilisateurs finaux
* [Proxy d’application](manage-apps/application-proxy.md)
* [Marketplace Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Partage d’un compte
Pour utiliser Azure AD pour partager un compte, vous devez effectuer les opérations suivantes :

* Ajouter une application à la [galerie d’applications](https://azure.microsoft.com/marketplace/active-directory/) ou aux [applications personnalisées](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).
* Configurer l’application pour l’authentification unique avec mot de passe.
* Utiliser [l’affectation basée sur le groupe](users-groups-roles/groups-saasapps.md) et sélectionner l’option permettant d’entrer des informations d’identification partagées.
* Facultatif : dans certaines applications, telles que Facebook, Twitter ou LinkedIn, vous pouvez activer l’option de [substitution automatisée du mot de passe Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Vous pouvez également sécuriser votre compte partagé avec Multi-Factor Authentication (MFA) (en savoir plus sur la [sécurisation des applications avec Azure AD](authentication/concept-mfa-whichversion.md)) et déléguer la capacité de gérer les utilisateurs ayant accès à l’application à l’aide de la gestion de groupe [Libre-service Azure AD](users-groups-roles/groups-self-service-management.md).

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Protection des applications avec accès conditionnel](active-directory-conditional-access-azure-portal.md)
* [Gestion des groupes en libre service/accès aux applications en libre-service](users-groups-roles/groups-self-service-management.md)

