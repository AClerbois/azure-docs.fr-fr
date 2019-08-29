---
title: Se connecter à des flux RSS à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser des tâches et des flux de travail qui surveillent et gèrent les flux RSS à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 70b250074395977f70ac1b3eb0ce3ffdc96fced1
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050854"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gérer des flux RSS à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur RSS, vous pouvez créer des tâches et des flux de travail automatisés pour tous les flux RSS, par exemple :

* Surveiller si les éléments de flux RSS sont publiés.
* Afficher la liste de tous les éléments du flux RSS.

RSS (Rich Site Summary), également appelé Really Simple Syndication, est un format courant pour la syndication web, utilisé pour la publication de contenu fréquemment mis à jour, tel que les billets de blogs et les titres d’actualité. De nombreux éditeurs de contenu fournissent un flux RSS auquel les utilisateurs peuvent s’abonner. 

Vous pouvez utiliser un déclencheur RSS qui obtient des réponses d’un flux RSS et met la sortie à la disposition d’autres actions. Vous pouvez utiliser une action RSS dans vos applications logiques pour effectuer une tâche avec le flux RSS. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’URL d’un flux RSS

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à un flux RSS. Pour démarrer avec un déclencheur RSS, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action RSS, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-an-rss-feed"></a>Se connecter à un flux RSS

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « rss » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes, sous l’étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. Dans la zone de recherche, entrez « rss » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/rss/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)