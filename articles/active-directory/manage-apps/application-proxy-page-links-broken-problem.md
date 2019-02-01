---
title: Les liens de la page ne fonctionnent pas pour une application avec proxy d’application | Documents Microsoft
description: Comment résoudre les problèmes de liens rompus dans les applications avec proxy d’application intégrées à Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 585ffd966bc3ba985e2b5b93529cadba8ce65960
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151853"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Les liens de la page ne fonctionnent pas pour une application avec proxy d’application

Cet article vous aide à corriger les problèmes de liens rompus dans votre application avec proxy d’application Azure Active Directory.

## <a name="overview"></a>Vue d'ensemble 
Après la publication d’une application avec proxy d’application, les seuls liens qui fonctionnent par défaut dans l’application sont ceux qui dirigent vers des destinations contenues dans l’URL racine publiée. Si les liens ne fonctionnent pas dans l’application, il est possible que l’URL interne de l’application n’inclue pas toutes les destinations de liens de l’application.

**Pourquoi cela se produit-il ?** Lorsque vous cliquez sur un lien dans une application, le proxy d’application tente de résoudre l’URL en tant qu’URL interne au sein de la même application ou en tant qu’URL accessible depuis l’extérieur. Si le lien pointe vers une URL interne qui n’est pas dans la même application, il n’appartient à aucun de ces compartiments et provoque une erreur d’adresse introuvable.

## <a name="ways-you-can-resolve-broken-links"></a>Comment résoudre les liens rompus

Il existe trois façons de résoudre ce type de problème. Les solutions ci-dessous sont répertoriées par ordre croissant de complexité.

1.  Vérifiez que l’URL interne est une URL racine qui contient tous les liens nécessaires à l’application. Cela permet à tous les liens d’être résolus en tant que contenu publié au sein de la même application.

    Si vous modifiez l’URL interne, mais ne souhaitez pas modifier la page d’arrivée pour les utilisateurs, remplacez l’URL de la page d’accueil par l’URL interne publiée précédemment. Pour cela, accédez à « Active Directory Azure » -&gt; Inscriptions des applications -&gt; sélectionnez l’application -&gt; Propriétés. Dans l’onglet Propriétés, vous voyez le champ « URL de la page d’accueil » que vous pouvez configurer sur la page d’arrivée de votre choix.

2.  Si vos applications utilisent des noms de domaine complets (FQDN), utilisez des [domaines personnalisés](application-proxy-configure-custom-domain.md) pour publier vos applications. Cette fonctionnalité permet à la même URL d’être utilisée à la fois en interne et en externe.

    Cette option garantit que les liens de votre application soient accessibles de l’extérieur via le proxy d’application, car les liens de l’application qui dirigent vers des URL internes sont également reconnus en externe. Tous les liens doivent appartenir à une application publiée. Toutefois, avec cette option, les liens n’ont pas besoin d’appartenir à la même application et peuvent donc appartenir à plusieurs applications.

3.  Si aucune de ces options n’est envisageable, il existe plusieurs options pour activer la traduction de lien en ligne. Ces options incluent l’utilisation d’Intune Managed Browser, de l’extension Mes apps, ou du paramètre de traduction de lien sur votre application. Pour en savoir plus sur chacune de ces options et la manière de les activer, voir [Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Étapes suivantes
[Travailler avec des serveurs proxy locaux existants](application-proxy-configure-connectors-with-proxy-servers.md)

