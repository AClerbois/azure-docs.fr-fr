---
title: Les liens de la page ne fonctionnent pas pour une application avec proxy d’application | Documents Microsoft
description: Comment résoudre les problèmes de liens rompus dans les applications avec proxy d’application intégrées à Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 7da9642281db07d873c4db234b8316c8248ef7f4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330901"
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

2.  Si vos applications utilisent des noms de domaine complets (FQDN), utilisez des [domaines personnalisés](manage-apps/application-proxy-configure-custom-domain.md) pour publier vos applications. Cette fonctionnalité permet à la même URL d’être utilisée à la fois en interne et en externe.

    Cette option garantit que les liens de votre application soient accessibles de l’extérieur via le proxy d’application, car les liens de l’application qui dirigent vers des URL internes sont également reconnus en externe. Tous les liens doivent appartenir à une application publiée. Toutefois, avec cette option, les liens n’ont pas besoin d’appartenir à la même application et peuvent donc appartenir à plusieurs applications.

3.  Si aucune de ces options n’est envisageable, vous pouvez utiliser la nouvelle fonctionnalité en préversion qui permet de traduire ou réécrire les URL. Avec cette fonctionnalité, les URL internes ou les liens qui se trouvent dans le corps HTML de vos applications doivent être traduits ou « mappés » vers les URL de proxy d’application externes publiées. Cela fonctionne uniquement pour les liens qui se trouvent dans du code HTML ou CSS, et non pour ceux qui sont générés par JS. 

Par conséquent, nous recommandons fortement d’utiliser les [domaines personnalisés](manage-apps/application-proxy-configure-custom-domain.md) lorsque cela est possible. Si vous souhaitez utiliser la préversion, envoyez les ID d’application par e-mail à l’adresse <aadapfeedback@microsoft.com>.

## <a name="next-steps"></a>Étapes suivantes
[Travailler avec des serveurs proxy locaux existants](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

