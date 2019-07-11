---
title: Bot QnA - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: Créez un bot conversationnel QnA à partir de la page Publier pour une base de connaissances existante. Ce bot utilise le SDK Bot Framework v4. Vous n’avez pas besoin d’écrire de code pour construire le bot, car tout le code vous est fourni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 1d475d988847bd4cc9e37b91a0c5d28678cb05f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446649"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Didacticiel : Créer un bot QnA avec Azure Bot Service v4

Créez un bot conversationnel QnA à partir de la page **Publier** pour une base de connaissances existante. Ce bot utilise le SDK Bot Framework v4. Vous n’avez pas besoin d’écrire de code pour construire le bot, car tout le code vous est fourni.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créez un service Azure Bot Service à partir d’une base de connaissances existante
> * Dialoguer avec le bot pour vérifier que le code fonctionne 

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’une base de connaissances publiée pour ce tutoriel. Si ce n’est pas le cas, suivez les étapes du tutoriel [Créer et répondre à partir de la base de connaissances](create-publish-query-in-portal.md) pour créer une base de connaissances QnA Maker avec des questions et des réponses.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Créer un bot QnA

Créez un bot en tant qu’application cliente pour la base de connaissances. 

1. Dans le portail QnA Maker, accédez à la page **Publish** et publiez votre base de connaissances. Sélectionnez **Create Bot**. 

    ![Dans le portail QnA Maker, accédez à la page Publish et publiez votre base de connaissances. Sélectionnez Create Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Le Portail Azure s’ouvre avec la configuration de création de bot.

1.  Entrez les paramètres pour créer le bot :

    |Paramètre|Valeur|Objectif|
    |--|--|--|
    |Nom du robot|`my-tutorial-kb-bot`|Il s’agit du nom de ressource Azure pour le bot.|
    |Abonnement|Consultez l’objectif.|Sélectionnez le même abonnement que celui utilisé pour créer les ressources QnA Maker.|
    |Groupe de ressources|`my-tutorial-rg`|Groupe de ressources utilisé pour toutes les ressources Azure relatives aux bots.|
    |Location|`west us`|Emplacement de la ressource Azure du bot.|
    |Niveau tarifaire|`F0`|Niveau gratuit du service de bot Azure.|
    |Nom de l’application|`my-tutorial-kb-bot-app`|Il s’agit d’une application web qui ne prend en charge que votre bot. Ce nom d’application doit être différent de celui que votre service QnA Maker utilise déjà. Le partage de l’application web de QnA Maker avec toute autre ressource n’est pas pris en charge.|
    |Langage du SDK|C#|Il s’agit du langage de programmation sous-jacent utilisé par le SDK du framework du bot. Vous avez le choix entre [C# ](https://github.com/Microsoft/botbuilder-dotnet) et [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |Clé d’authentification QnA|**Ne pas modifier**|Cette valeur est renseignée automatiquement.|
    |Plan App Service/Emplacement|**Ne pas modifier**|Pour ce tutoriel, l’emplacement est sans importance.|
    |Stockage Azure|**Ne pas modifier**|Les données de conversation sont stockées dans les tables Stockage Azure.|
    |Application Insights|**Ne pas modifier**|La journalisation est envoyée à Application Insights.|
    |ID d’application Microsoft|**Ne pas modifier**|L’utilisateur et le mot de passe Active Directory sont requis.|

    ![Créez le bot de base de connaissances avec ces paramètres.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Attendez quelques minutes jusqu’à ce qu’un message indique que le processus de création du bot a réussi.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Discuter avec le bot

1. Dans le Portail Azure, ouvrez la nouvelle ressource bot à partir de la notification. 

    ![Dans le Portail Azure, ouvrez la nouvelle ressource bot à partir de la notification.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Dans **Gestion du bot**, sélectionnez **Tester dans le chat web** et saisissez : `How large can my KB be?`. Le bot doit répondre avec ce qui suit : 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Testez le nouveau bot de base de connaissances.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Pour plus d’informations sur les bots Azure, consultez [Utiliser QnA Maker pour répondre aux questions](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez terminé avec le bot de ce tutoriel, supprimez le bot dans le portail Azure. 

Si vous avez créé un nouveau groupe de ressources pour les ressources du bot, supprimez-le. 

Dans le cas contraire, vous devez trouver les ressources associées au bot. La façon la plus simple est de rechercher le nom du bot et de l’application bot. Les ressources de bot incluent :

* Le plan App Service
* Le service Recherche
* Le service Cognitive
* Le service Plan
* Ils peuvent également inclure le service Application Insights ainsi que le stockage des données Application Insights


## <a name="related-to-qna-maker-bots"></a>Lié aux bots QnA Maker

* Le bot d’aide de QnA Maker, utilisé dans le portail de QnA Maker, est disponible sous forme d’[exemple de bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![L’icône du bot d’aide de QnA Maker représente un robot rouge](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Les bots médicaux](https://docs.microsoft.com/HealthBot/qna_model_howto) utilisent QnA Maker comme l’un de leurs [ modèles de langage](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concept : base de connaissances](../concepts/knowledge-base.md)

## <a name="see-also"></a>Voir aussi

- [Gérer votre base de connaissances](https://qnamaker.ai)
- [Activer votre bot dans différents canaux](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
