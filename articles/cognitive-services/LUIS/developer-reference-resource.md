---
title: Ressources du développeur - Language Understanding
titleSuffix: Azure Cognitive Services
description: Les développeurs ont à leur disposition des API REST et des kits SDK pour implémenter Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 8accac7fe6068007180403fdab27013da161b28c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437193"
---
# <a name="developer-resources-for-language-understanding"></a>Ressources du développeur pour implémenter Language Understanding

Les développeurs peuvent utiliser des API REST et des kits SDK pour implémenter Language Understanding. 

## <a name="azure-resource-management"></a>Gestion des ressources Azure

Utilisez la couche de gestion d’Azure Cognitive Services pour créer, modifier, lister et supprimer la ressource Language Understanding ou Cognitive Services.

Recherchez de la documentation de référence basée sur l’outil :

* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Demandes de création et de prédiction Language Understanding

Le service Language Understanding est accessible à partir d’une ressource Azure que vous devez créer. Il existe deux ressources : les ressources de création et les ressources du point de terminaison de prédiction. Ces deux ressources vous permettent de contrôler vos ressources LUIS. 

Apprenez-en davantage sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>API REST

Les API de création et de point de terminaison de prédiction sont disponibles à partir d’API REST :

* [Documentation de référence](https://go.microsoft.com/fwlink/?linkid=2092087) sur la création
* [Documentation de référence](https://go.microsoft.com/fwlink/?linkid=2092356) sur le runtime de prédiction

### <a name="language-based-sdks"></a>Kits SDK basés sur le langage

|Langage |Documentation de référence|Package|Exemples|Démarrages rapides|
|--|--|--|--|--|
|C#|[Création](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Prédiction](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet - création](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet - prédiction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemples du kit SDK .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Créer et gérer une application](sdk-csharp-quickstart-authoring-app.md)<br>[Interroger le point de terminaison de prédiction](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Création et prédiction](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Kit SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Création à l’aide de REST](luis-get-started-go-add-utterance.md)<br>[Prédiction à l’aide de REST](luis-get-started-go-get-intent.md)|
|Java|[Création et prédiction](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven - création](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven - prédiction](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Création](luis-get-started-java-add-utterance.md)<br>[Prédiction](luis-get-started-java-get-intent.md)
|Node.js|[Création](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Prédiction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM - création](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM - prédiction](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Création à l’aide de REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Prédiction à l’aide de REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Création et prédiction](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Création](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Création](sdk-python-quickstart-authoring-app.md)<br>[Prédiction à l’aide de REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Autres outils et kits SDK

Bot Framework est disponible en tant que [kit SDK](https://github.com/Microsoft/botframework) dans différents langages et en tant que service avec [Azure Bot Service](https://dev.botframework.com/). 

Bot Framework fournit [plusieurs outils](https://github.com/microsoft/botbuilder-tools) pour vous aider à utiliser Language Understanding, notamment :

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) : créez des modèles de compréhension de la langue LUIS à l’aide de fichiers Markdown.
* [LUIS Cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) : créez et gérez vos applications Luis.ai.
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch) : gérez des applications parentes et enfants.
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) : générez automatiquement des classes C#/Typescript de support pour vos intentions et entités LUIS.
* [Bot emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) : application de bureau permettant aux développeurs de bots de tester et de déboguer les bots créés à l’aide du kit SDK Bot Framework.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [codes d’erreur HTTP](luis-reference-response-codes.md) courants