---
title: Invite de consentement inattendue lors de la connexion à une application | Microsoft Docs
description: Procédure de dépannage à suivre quand un utilisateur voit une invite de consentement inattendue pour une application intégrée à Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 760efde967ca9ca65e517d01da0016ce49f1502c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477861"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Invite de consentement inattendue lors de la connexion à une application

Pour s’exécuter, de nombreuses applications qui s’intègrent à Azure Active Directory nécessitent des autorisations à plusieurs ressources. Quand ces ressources sont également intégrées à Azure Active Directory, vous devez utiliser l’infrastructure de consentement Azure AD pour demander les autorisations nécessaires pour y accéder. 

Une invite de consentement est alors affichée à la première utilisation d’une application (il s’agit généralement d’une opération unique). 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scénarios dans lesquels des invites de consentement sont présentées aux utilisateurs

Divers scénarios entraînent l’affichage d’invites supplémentaires :

* Le jeu d’autorisations exigées par l’application a changé.

* L’utilisateur ayant initialement donné son consentement à l’application n’était pas un administrateur, et un autre utilisateur (non-administrateur) utilise l’application pour la première fois.

* L’utilisateur ayant initialement donné son consentement à l’application était un administrateur, mais il n’a pas donné son consentement au nom de toute l’organisation.

* L’application utilise le [consentement incrémentiel et dynamique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) pour demander des autorisations supplémentaires après le consentement initial. Ce cas de figure se présente souvent quand les fonctionnalités facultatives d’une application nécessitent des autorisations au-delà de celles exigées pour les fonctionnalités de base.

* Le consentement, bien qu’initialement accordé, a été révoqué.

* Le développeur a configuré l’application pour exiger une invite de consentement à chaque utilisation (ce qui n’est pas recommandé).

## <a name="next-steps"></a>Étapes suivantes

-   [Applications, autorisations et consentement dans Azure Active Directory (point de terminaison v1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Étendues, autorisations et consentement dans Azure Active Directory (point de terminaison v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


