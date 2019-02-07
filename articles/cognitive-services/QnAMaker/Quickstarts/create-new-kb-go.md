---
title: 'Démarrage rapide : Créer une base de connaissances - REST, Go - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur GO REST vous guide dans la création, par programmation, d’un exemple de base de connaissances QnA Maker qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 248b10ea3c1b59c99d9bd45e0952dc95c2b88563
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730386"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Démarrage rapide : Créer une base de connaissances dans QnA Maker à l’aide de Go

Ce démarrage rapide vous aide à créer par programmation un exemple de base de connaissances QnA Maker. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). Le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 

Ce démarrage rapide fait appel aux API QnA Maker :
* [Créer la base de connaissances](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Prérequis

* [Go 1.10.1](https://golang.org/dl/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 

## <a name="create-a-knowledge-base-go-file"></a>Créer un fichier de base de connaissances Go

Créez un fichier appelé `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `create-new-knowledge-base.go`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires
Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Ajouter la définition du modèle de base de connaissances
Après les constantes, ajoutez la définition du modèle de base de connaissances suivante. Après la définition, le modèle est converti en une chaîne.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Ajouter les fonctions et les structures de prise en charge

Ajoutez ensuite les fonctions de prise en charge suivantes.

1. Ajoutez la structure pour une requête HTTP :

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Ajoutez la méthode suivante pour gérer une requête POST dans les API QnA Maker. Pour ce guide de démarrage rapide, la requête POST est utilisée pour envoyer la définition de la base de connaissances dans QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Ajoutez la méthode suivante pour gérer une requête GET dans les API QnA Maker. Pour ce guide de démarrage rapide, la requête GET est utilisée pour vérifier le statut de l’opération de création. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Ajouter une fonction pour créer la base de connaissances

Ajoutez les fonctions suivantes pour exécuter une requête HTTP POST afin de créer la base de connaissances. L’**ID d’opération** _create_ (créer) est renvoyé dans le champ **Emplacement** de l’en-tête de réponse POST. Il est ensuite utilisé dans le routage de la requête GET. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>Ajouter une fonction pour obtenir le statut

Ajoutez la fonction suivante pour exécuter une requête HTTP GET afin de vérifier l’état de l’opération. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Ajouter la fonction principale

La fonction suivante est la fonction principale, crée la base de connaissances et répète les vérifications de l’état. Étant donné que la création de la base de connaissances peut prendre un certain temps, vous devez répéter les appels pour vérifier l’état jusqu’à ce que l’état indique une réussite ou un échec.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Compiler le programme
Exécutez la commande suivante pour compiler le fichier. L’invite de commande ne retourne pas d’informations pour une génération réussie.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Exécuter le programme

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Il envoie la requête à l’API QnA Maker pour créer la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

```bash
go run create-new-knowledge-base
```

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
