---
title: Se connecter à Slack à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui supervisent les fichiers et gèrent les canaux, groupes et messages dans votre compte Slack à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 2606fdc15194264bc3d84496d2de258adfc314be
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050798"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Superviser et gérer Slack avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur Slack, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent vos fichiers Slack et gèrent vos canaux, messages, groupes Slack, etc., par exemple :

* Superviser à quel moment des fichiers sont créés.
* Créer, lister et rejoindre des canaux. 
* Publier des messages.
* Créer des groupes et définir le statut Ne pas déranger.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre compte Slack et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent des tâches avec votre compte Slack. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions Slack. Par exemple, quand un fichier est créé, vous pouvez envoyer un e-mail avec le connecteur Office 365 Outlook. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Vos informations d’identification utilisateur et votre compte [Slack](https://slack.com/)

  Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Slack.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Slack. Pour démarrer avec un déclencheur Slack, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Slack, démarrez votre application logique avec un déclencheur, par exemple un déclencheur Slack ou un autre déclencheur, tel que le déclencheur **Périodicité**.

## <a name="connect-to-slack"></a>Se connecter à Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « slack » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 
   Dans la zone de recherche, entrez « slack » comme filtre. 
   Sous la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Si vous êtes invité à vous connecter à Slack, connectez-vous à votre espace de travail Slack. 

   ![Se connecter à l’espace de travail Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorisez l’accès pour votre application logique.

   ![Autoriser l’accès à Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionné. Pour poursuivre la génération du flux de travail de votre application logique, ajoutez d’autres actions.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/slack/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
