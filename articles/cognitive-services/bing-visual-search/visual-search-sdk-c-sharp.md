---
title: 'Démarrage rapide : Obtenir des insights sur les images à l’aide du SDK Recherche visuelle Bing pour C#'
titleSuffix: Azure Cognitive Services
description: Découvrez comment charger une image à l’aide du SDK Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: c589cc14fd429385dd47aad489e827804916d406
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741216"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide du SDK Recherche visuelle Bing pour C#

Utilisez ce guide de démarrage rapide pour commencer à obtenir des insights sur les images à partir du service Recherche visuelle Bing, à l’aide du SDK C#. Si d’un côté la Recherche visuelle Bing dispose d’une API REST compatible avec la plupart des langages de programmation, de l’autre le SDK offre un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](http://www.mono-project.com/).
* Package Recherche visuelle NuGet. 
    - Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez `Manage NuGet Packages` dans le menu. Installez le package `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. L’installation des packages NuGet installe également les éléments suivants :
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Dans Visual Studio, créez un projet. Ensuite, ajoutez les directives suivantes.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Instanciez le client avec votre clé d’abonnement.
    
    ```csharp
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Envoyer une demande de recherche 

1. Créez un `FileStream` pour vos images (dans ce cas `TestImages/image.jpg`). Ensuite, utilisez le client pour envoyer une demande de recherche à l’aide de `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Analysez les résultats de la requête précédente :

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags.First();
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions.First();
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-visual-search-single-page-app.md)
