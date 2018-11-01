---
title: Se connecter à Twitter à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui supervisent et gèrent les tweets, obtenir des données sur les abonnés, les utilisateurs suivis, les autres utilisateurs, les chronologies et bien plus encore à partir de votre compte Twitter à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230656"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Superviser et gérer Twitter à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur Twitter, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent et gèrent les données qui vous intéressent dans Twitter, telles que les tweets, les abonnés, les utilisateurs, les utilisateurs suivis, les chronologies et bien plus encore, ainsi que d’autres actions, par exemple :

* Superviser, publier et rechercher des tweets.
* Obtenir des données telles que les abonnés, les utilisateurs suivis, les chronologies et bien plus encore.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre compte Twitter et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent des tâches avec votre compte Twitter. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions Twitter. Par exemple, quand un nouveau tweet avec un mot-dièse spécifique s’affiche, vous pouvez envoyer des messages avec le connecteur Slack. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Vos informations d’identification utilisateur et compte Twitter

   Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Twitter.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Twitter. Pour démarrer avec un déclencheur Twitter, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Twitter, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-twitter"></a>Se connecter à Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « twitter » comme filtre. 
   Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes : 
   
     * Sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 

       -ou-

     * Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
     Cliquez sur le signe plus (**+**) qui s’affiche, puis sélectionnez **Ajouter une action**.
     
       Dans la zone de recherche, entrez « twitter » comme filtre. 
       Sous la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à Twitter, faites-le maintenant afin de pouvoir autoriser l’accès pour votre application logique.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="examples"></a>Exemples

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Déclencheur Twitter : Lors de la publication d’un nouveau tweet

Ce déclencheur démarre un flux de travail d’application logique quand il détecte un nouveau tweet, par exemple, avec le mot-dièse #Seattle. Par exemple, quand ces tweets sont détectés, vous pouvez ajouter un fichier avec le contenu des tweets dans le stockage, tel qu’un compte Dropbox à l’aide du connecteur Dropbox. 

Si vous le souhaitez, vous pouvez inclure une condition stipulant que les tweets éligibles doivent provenir d’utilisateurs ayant un nombre minimum d’abonnés spécifié.

**Exemple en entreprise** : vous pouvez utiliser ce déclencheur pour superviser les tweets relatifs à votre entreprise et charger leur contenu vers une base de données SQL.

### <a name="twitter-action-post-a-tweet"></a>Action Twitter : Publier un tweet

Cette action publie un tweet, mais vous pouvez configurer l’action afin que le tweet contienne le contenu des tweets trouvés par le déclencheur décrit précédemment. 

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/twitterconnector/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
