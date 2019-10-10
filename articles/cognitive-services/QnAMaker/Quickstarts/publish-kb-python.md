---
title: 'Démarrage rapide : Publier une base de connaissances, REST, Python - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST Python vous aide à publier votre base de connaissances qui envoie (push) la dernière version de la base de connaissances testée à un index de Recherche Azure dédié représentant la base de connaissances publiée. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 09523bb6c9c8a58f5f7bd102d2ac30ad77f28d1c
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840188"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Démarrage rapide : Publier une base de connaissances dans QnA Maker à l'aide de Python

Ce guide de démarrage rapide basé sur REST vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.

Ce guide de démarrage rapide fait appel aux API REST QnA Maker :
* [Publier](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) : cette API ne nécessite aucune information dans le corps de la requête.

## <a name="prerequisites"></a>Prérequis

* [Python 3.7](https://www.python.org/downloads/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé et votre point de terminaison (qui incluent le nom de la ressource), sélectionnez **Démarrage rapide** pour votre ressource dans le portail Azure.
* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](../how-to/create-knowledge-base.md).

> [!NOTE] 
> Les fichiers solution complets sont disponibles dans le référentiel GitHub [**Azure-Samples/cognitive-services-qnamaker-python**](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Créer un fichier Python de base de connaissances

Créez un fichier appelé `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `publish-kb-3x.py`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires

Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez les valeurs par les vôtres.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Ajouter une requête POST pour publier la base de connaissances

Après les constantes nécessaires, ajoutez le code suivant, qui adresse une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

L’appel API renvoie un état 204 si la publication réussit sans aucun contenu dans le corps de la réponse. Le code ajoute du contenu pour les réponses 204.

Toutes les autres réponses sont renvoyées inchangées.

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Elle envoie la requête à l’API QnA Maker pour publier la base de connaissances, puis affiche 204 en cas de succès ou des erreurs.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

Une fois la base de connaissances publiée, il faut que l’[URL du point de terminaison génère une réponse](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
