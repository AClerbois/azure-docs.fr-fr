---
title: 'Démarrage rapide : Créer une base de connaissances - REST, Java - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur Java REST vous guide dans la création, par programmation, d’un exemple de base de connaissances QnA Maker qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: ddeccd34cbdc72d76d8dd26e36a533f44a9274b8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866302"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Démarrage rapide : Créer une base de connaissances dans QnA Maker à l’aide de Java

Ce démarrage rapide vous aide à créer par programmation un exemple de base de connaissances QnA Maker. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). Le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Créer un fichier de base de connaissances

Créer un fichier appelé `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `CreateKB.java`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires
Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires à la classe `CreateKB` pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker. Vous n’avez pas besoin d’ajouter la dernière accolade à la fin de la classe ; elle est déjà dans l’extrait de code final à la fin de ce guide de démarrage rapide.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Ajouter les classes de la définition du modèle de base de connaissances
Après les constantes, ajoutez les classes et fonctions suivantes dans la classe `CreateKB` pour sérialiser l’objet de définition du modèle dans JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Ajouter des fonctions de prise en charge

Ajoutez ensuite les fonctions de prise en charge suivantes dans la classe `CreateKB`.

1. Ajoutez la fonction suivante pour imprimer JSON dans un format lisible :

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Ajoutez la classe suivante pour gérer la réponse HTTP :

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Ajoutez la méthode suivante pour effectuer une requête POST dans les API QnA Maker. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Ajoutez la méthode suivante pour effectuer une requête GET dans les API QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Ajouter une méthode pour créer la base de connaissances
Ajoutez la méthode suivante pour créer la base de données en l’appelant dans la méthode POST.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération. Utilisez l’ID d’opération pour déterminer si la base de connaissances a bien été créée.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Ajouter une méthode pour obtenir le statut
Ajoutez la méthode suivante pour vérification le statut de création.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Répétez l’appel jusqu’à ce que l’opération réussisse ou échoue :

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Ajouter une méthode Main
La méthode Main crée la base de connaissances, puis demande le statut. L’**ID d’opération** create (créer) est renvoyé dans le champ **Emplacement** de l’en-tête de réponse POST. Il est ensuite utilisé dans le routage de la requête GET. ** Les boucles `while` testent à nouveau le statut s’il n’est pas terminé.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Compiler et exécuter le programme

1. Assurez-vous que la bibliothèque gson se trouve dans le répertoire `./libs`. Sur la ligne de commande, compilez le fichier `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Il envoie la requête à l’API QnA Maker pour créer la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
