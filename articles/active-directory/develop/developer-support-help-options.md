---
title: Options d'aide et de support pour les développeurs d'applications Azure AD | Microsoft Docs
description: Apprenez à vous procurer aide et support pour les questions liées au développement et les problèmes rencontrés lors de la création d'applications qui s'intègrent à des identités Microsoft (compte Azure Active Directory et Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f3f041fa3b594b4b680d794c1c013e55b97da4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235187"
---
# <a name="support-and-help-options-for-developers"></a>Options d’aide et de support pour les développeurs

Que vous implémentiez une nouvelle fonctionnalité dans votre application ou que vous commenciez à vous familiariser avec Azure Active Directory (Azure AD), les identités Microsoft ou l'API Microsoft Graph, vous pouvez être amené à rechercher de l'aide auprès de la Communauté ou à vous renseigner sur les options de support qui sont à votre disposition en tant que développeur. Cet article vous présente ces options, notamment :

> [!div class="checklist"]
> * Effectuer une recherche pour savoir si votre question ou votre problème a déjà été traité par la Communauté, ou s'il existe déjà une documentation sur la fonctionnalité que vous essayez d'implémenter
> * Dans certains cas, vous pouvez simplement souhaiter utiliser nos outils de support afin de résoudre un problème particulier
> * Si vous ne trouvez pas la solution à votre problème, vous avez la possibilité de poser une question sur *Stack Overflow*
> * Si vous rencontrez des difficultés avec l’une de nos bibliothèques d’authentification, signalez un problème *GitHub*
> * Enfin, si vous voulez vous entretenir avec une personne, ouvrez une demande de support

## <a name="search"></a>Recherche

Si vous avez une question relative au développement, la réponse peut se trouver dans la documentation, dans les [exemples GitHub](https://github.com/azure-samples) ou dans les réponses aux questions posées sur [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Recherche étendue

Pour des résultats plus rapides, élargissez votre recherche à Stack Overflow, à la documentation et aux exemples de code en utilisant la requête suivante dans votre moteur de recherche habituel :

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Sachant que *{Your Search Terms}* correspond aux mots clés de votre recherche.

## <a name="use-the-development-support-tools"></a>Utiliser les outils de support de développement

| Outil  | Description  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Collez un ID ou un jeton d'accès pour décoder les noms et les valeurs des revendications. |
| [Analyseur de code d’erreur](https://apps.dev.microsoft.com/portal/tools/errors)| Collez un code d'erreur obtenu au moment de la connexion ou dans les pages d'acceptation pour afficher les causes et les résolutions possibles. |
| [Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Cet outil vous permet de soumettre des requêtes et de voir les réponses par rapport à l'API Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Publication d’une question sur Stack Overflow

Stack Overflow est le canal de prédilection pour les questions de développement. Les membres de la communauté des développeurs et de l'équipe Microsoft y apportent directement leur contribution en vous aidant à résoudre vos problèmes.

Si vous ne trouvez pas de réponse à votre question par l'intermédiaire de la recherche, soumettez une nouvelle question sur Stack Overflow. Utilisez l'une des balises suivantes lorsque vous posez une question pour permettre à la communauté d'identifier votre question et d'y répondre plus rapidement :

|Composant/zone  | Balises |
|---------|---------|
| Bibliothèque ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Bibliothèque MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Intergiciel (middleware) OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [API Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Toute autre zone liée à des sujets sur l’authentification ou les autorisations | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Les publications suivantes de Stack Overflow contiennent des conseils sur la formulation des questions et sur l'ajout de code source. Suivez ces recommandations pour que les membres de la communauté étudient votre question et y répondent rapidement :

* [Comment poser une bonne question](https://stackoverflow.com/help/how-to-ask)
* [Comment créer un exemple minimal, complet et vérifiable](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Créer un problème GitHub

Si vous découvrez un bogue ou un problème lié à nos bibliothèques, signalez-le dans nos référentiels GitHub. Nos bibliothèques étant open source, vous pouvez également soumettre une demande de tirage (pull request).

Pour accéder à la liste des bibliothèques et de leurs référentiels GitHub, consultez les articles suivants :

* Bibliothèques et référentiels GitHub [ADAL](active-directory-authentication-libraries.md)
* Bibliothèques et référentiels GitHub [MSAL](reference-v2-libraries.md)

## <a name="open-a-support-request"></a>Ouverture d’une demande de support

Si vous avez besoin de vous entretenir avec quelqu’un, vous pouvez ouvrir une demande de support. Si vous êtes un client Azure, plusieurs options de support s’offrent à vous. Pour comparer les formules, consultez [cette page](https://azure.microsoft.com/support/plans/). Le support technique des développeurs est également disponible pour les clients Azure. Pour obtenir plus d’informations sur l’achat de formules d’assistance Developer Support, consultez [cette page](https://azure.microsoft.com/support/plans/developer/).

* Si vous disposez déjà d’une formule d’assistance Azure, [ouvrez une demande de support ici](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Si vous n’êtes pas un client Azure, vous pouvez également ouvrir une demande de support auprès de Microsoft via [notre assistance commerciale](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Vous avez aussi la possibilité de vous adresser à un [agent virtuel](https://support.microsoft.com/contactus/?ws=support) pour obtenir une assistance ou poser des questions.

### <a name="free-chat-support-for-a-limited-time"></a>Assistance gratuite par chat à durée limitée

Vous pouvez également utiliser notre assistance par messagerie instantanée ; celle-ci est gratuite pour les partenaires Microsoft sur une durée limitée. Si votre entreprise n’est pas un partenaire Microsoft, vous pouvez l’inscrire gratuitement et bénéficier d’autres avantages en vous rendant [ici](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Dès l’inscription de votre entreprise effectuée, vous pouvez démarrer la demande de conversation [ici](https://aka.ms/devchat).
