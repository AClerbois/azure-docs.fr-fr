---
title: 'Collaborer avec d’autres personnes : QnA Maker'
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 080e6549579675e27486e6173d5907d92bbaad70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724919"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Collaborer avec d’autres auteurs et éditeurs

Collaborez avec d’autres auteurs et éditeurs à l’aide du contrôle d’accès en fonction du rôle (RBAC) placé sur votre ressource QnA Maker.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>L’accès est fourni sur la ressource QnA Maker

Toutes les autorisations sont contrôlées par les autorisations placées sur la ressource QnA Maker. Ces autorisations permettent la lecture, l’écriture, la publication et l’accès complet.

Cette fonctionnalité de contrôle d’accès en fonction du rôle comprend :
* Azure Active Directory (AAD) offre une compatibilité descendante de 100 % avec l’authentification basée sur les clés pour les propriétaires et les contributeurs. Les clients peuvent utiliser l’authentification basée sur les clés ou sur le contrôle d’accès en fonction du rôle pour leurs requêtes.
* Ajoutez rapidement des auteurs et des éditeurs à toutes les bases de connaissances de la ressource, car le contrôle se trouve au niveau de la ressource, et non au niveau de la base de connaissances.

## <a name="access-is-provided-by-a-defined-role"></a>L’accès est fourni par un rôle défini

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Flux d’authentification

Le diagramme suivant illustre le flux, du point de vue de l’auteur, pour la connexion au portail QnA Maker et l’utilisation des API de création.

> [!div class="mx-imgBorder"]
> ![Le diagramme suivant illustre le flux, du point de vue de l’auteur, pour la connexion au portail QnA Maker et l’utilisation des API de création.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Étapes|Description|
|--|--|
|1|Le portail acquiert un jeton pour la ressource QnA Maker.|
|2|Le portail appelle l’API de création QnA Maker (APIM) appropriée, et transmet le jeton à la place des clés.|
|3|L’API QnA Maker valide le jeton.|
|4 |L’API QnA Maker appelle le service QnAMaker.|

Si vous envisagez d’appeler les [API de création](../How-To/collaborate-knowledge-base.md), découvrez-en plus à propos de la configuration de l’authentification.

## <a name="authenticate-by-qna-maker-portal"></a>S’authentifier à l’aide du portail QnA Maker

Si vous créez et collaborez à l’aide du portail QnA Maker, après avoir [ajouté le rôle approprié à la ressource pour un collaborateur](../How-To/collaborate-knowledge-base.md), le portail QnA Maker gère toutes les autorisations d’accès.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>S’authentifier à l’aide des API et Kits de développement logiciel (SDK) QnA Maker

Si vous créez et collaborez à l’aide des API, via REST ou des Kits de développement logiciel (SDK), vous devez [créer un principal de service](../../authentication.md#assign-a-role-to-a-service-principal) afin de gérer l’authentification.

## <a name="next-step"></a>Étape suivante

* Concevoir une base de connaissances pour les [langages](design-language-culture.md) et les [applications clientes](integration-with-other-applications.md)
