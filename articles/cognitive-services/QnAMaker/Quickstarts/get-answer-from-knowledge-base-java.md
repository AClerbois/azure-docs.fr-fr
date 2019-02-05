---
title: 'Démarrage rapide : Obtenir des réponses d’une base de connaissances - REST, Java - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST Java vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 34630b2d2852da37bdcb3e5f097b2d3f6ebc6f1a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217405"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Obtenir des réponses à une question à partir d’une base de connaissance avec Java

Ce guide de démarrage rapide vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances QnA Maker publiée. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). La question, au format JSON, est envoyée dans le corps de la requête d’API. 

## <a name="prerequisites"></a>Prérequis

* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* Cet exemple utilise le [client HTTP](http://hc.apache.org/httpcomponents-client-ga/) Apache à partir de composants HTTP. Vous devez ajouter les bibliothèques clientes HTTP Apache suivantes à votre projet : 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord Azure pour votre ressource QnA Maker. 
* Paramètres de la page **Publier**. Si vous ne disposez pas d’une base de connaissances publiée, créez une base de connaissances vide, importez une base de connaissances sur la page **Paramètres**, puis publiez. Vous pouvez télécharger et utiliser [cette base de connaissances](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Les paramètres de la page de publication incluent la valeur de route POST, la valeur d’hôte et la valeur EndpointKey. 

    ![Paramètres de publication](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Le code associé à ce guide de démarrage rapide est disponible dans le dépôt [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-java-file"></a>Créer un fichier Java

Ouvrez VSCode et créez un fichier nommé `GetAnswer.java`, puis ajoutez la classe suivante :

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

Ce guide de démarrage rapide utilise des classes Apache pour les requêtes HTTP. Au-dessus de la classe GetAnswer, en haut de la fonction `GetAnswer.java`, ajoutez les dépendances nécessaires au projet :

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires

En haut de la classe `GetAnswer.java`, ajoutez les constantes nécessaires pour accéder à QnA Maker. Ces valeurs se trouvent dans la page **Publier** une fois la base de connaissances publiée.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Ajouter une requête POST pour envoyer une question

Le code suivant adresse une requête HTTPS à l’API QnA Maker afin d’envoyer la question à la base de connaissances et reçoit la réponse :

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

La valeur de l’en-tête `Authorization` inclut la chaîne `EndpointKey `. 

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Générez et exécutez le programme à partir de la ligne de commande. Il envoie automatiquement la requête à l’API QnA Maker, puis affiche la réponse dans la fenêtre de console.

1. Générez le fichier :

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Exécutez le fichier :

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
