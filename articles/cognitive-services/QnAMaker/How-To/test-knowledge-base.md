---
title: Guide pratique pour tester une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: Le test de votre base de connaissances QnA Maker est une partie importante d’un processus itératif afin d’améliorer la précision des réponses retournées. Vous pouvez tester la base de connaissances via une interface de conversation améliorée qui vous permet également d’apporter des modifications.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491200"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Tester votre base de connaissances dans QnA Maker

Le test de votre base de connaissances QnA Maker est une partie importante d’un processus itératif afin d’améliorer la précision des réponses retournées. Vous pouvez tester la base de connaissances via une interface de conversation améliorée qui vous permet également d’apporter des modifications.

## <a name="interactively-test-in-qna-maker-portal"></a>Tester de manière interactive dans le portail QnA Maker

1. Accédez à votre base de connaissances en sélectionnant son nom dans la page **Mes bases de connaissances**.
1. Pour accéder au panneau déroulant Test, sélectionnez **Test** dans le panneau supérieur de votre application.
1. Entrez une question dans la zone de texte, puis sélectionnez Entrée.
1. La réponse la plus concordante de la base de connaissances est retournée.

### <a name="clear-test-panel"></a>Effacer le panneau Test

Pour effacer toutes les questions de test entrées et leurs résultats de la console de test, sélectionnez **Recommencer** dans l’angle supérieur gauche du panneau Test.

### <a name="close-test-panel"></a>Fermer le panneau Test

Pour fermer le panneau Test, sélectionnez le bouton **Test** à nouveau. Tant que le panneau Test est ouvert, vous ne pouvez pas modifier le contenu de la base de connaissances.

### <a name="inspect-score"></a>Inspecter le score

Vous inspectez les détails du résultat du test dans le panneau Inspecter.

1.  Avec le panneau déroulant Test ouvert, sélectionnez **Inspecter** pour obtenir plus d’informations sur cette réponse.

    ![Inspecter des réponses](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Le panneau Inspection s’affiche. Le panneau inclut l’intention de score la plus élevée, ainsi que toutes les entités identifiées. Le panneau affiche le résultat de l’énoncé sélectionné.

### <a name="correct-the-top-scoring-answer"></a>Corriger la réponse avec le score le plus élevé

Si la réponse avec le score le plus élevé est incorrecte, sélectionnez la réponse correcte dans la liste et sélectionnez **Enregistrer et former**.

![Corriger la réponse avec le score le plus élevé](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Vous pouvez ajouter des formes alternatives d’une question à une réponse donnée. Tapez les réponses alternatives dans la zone de texte et cliquez sur Entrée pour les ajouter. Sélectionnez **Enregistrer et former** pour stocker les mises à jour.

![Ajouter des questions alternatives](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Ajouter une nouvelle réponse

Vous pouvez ajouter une nouvelle réponse si les réponses existantes qui ont été mises en correspondance sont incorrectes ou si la réponse n’existe pas dans la base de connaissances (aucune bonne correspondance trouvée dans la base de connaissances). 

En bas de la liste de réponses, utilisez la zone de texte pour entrer une nouvelle réponse, et appuyez sur Entrée pour l’ajouter. 

Sélectionnez **Enregistrer et former** pour conserver cette réponse. Une nouvelle paire question/réponse a maintenant été ajoutée à votre base de connaissances. 

> [!NOTE]
> Toutes les modifications apportées à votre base de connaissances ne sont enregistrées que lorsque vous appuyez sur le bouton **Enregistrer et former**.

### <a name="test-the-published-knowledge-base"></a>Tester la base de connaissances publiée

Vous pouvez tester la version publiée de la base de connaissances dans le volet de test. Une fois que vous avez publié la base de connaissances, sélectionnez la zone **Base de connaissances publiée** et envoyez une requête pour obtenir des résultats à partir de la base de connaissances publiée.

![Tester par rapport à une base de connaissances publiée](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Test par lot avec l’outil

Utilisez l’outil de test par lot lorsque vous souhaitez :

* déterminer la meilleure réponse et le meilleur score pour un ensemble de questions
* valider la réponse attendue pour l’ensemble de questions

Les tests par lots sont fournis avec l’outil de test par lot. Cet outil est disponible sous forme de [fichier exécutable compressé](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) à télécharger ou de [code source C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[La documentation de référence sur l’outil](../reference-tsv-format-batch-testing.md) comprend ce qui suit :

* exemple de ligne de commande de l’outil
* format des fichiers d’entrée et de sortie TSV 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Publier une base de connaissances](./publish-knowledge-base.md)
