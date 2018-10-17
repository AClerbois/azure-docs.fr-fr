---
title: 'Démarrage rapide : Rechercher des images avec le SDK Recherche d’images Bing pour C#'
description: Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application C# simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: edebd1361e39a338672b4249dd159e5c1d4078ce
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294150"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Démarrage rapide : Rechercher des images avec le SDK Recherche d’images Bing et C#

Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application C# simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) avec une gestion des erreurs supplémentaire et des annotations.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* Le [package NuGet de recherche cognitive d’images](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Pour installer le SDK pour Recherche d’images Bing, dans Visual Studio, accédez à l’option `Manage NuGet Packages` à partir de l’Explorateur de solutions dans Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

Commencez par créer une application de console C# dans Visual Studio. Ajoutez ensuite les packages suivants à votre projet.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

Dans la méthode principale de votre projet, créez des variables pour votre clé d’abonnement valide, les résultats d’image devant être retournés par Bing et un terme de recherche. Ensuite, instanciez le client de recherche d’image à l’aide de la clé.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";
//initialize the client
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Envoyer une requête de recherche avec le client

Utilisez le client pour effectuer une recherche avec un texte de requête :

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analyser et afficher le premier résultat d’image

Analysez les résultats d’image retournés par la réponse.
Si la réponse contient des résultats de recherche, stockez le premier résultat et imprimez ses détails, tels qu’une URL de miniature, l’URL d’origine, ainsi que le nombre total d’images retournées.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que la Recherche d’images Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Essayez une démonstration interactive en ligne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Exemples .NET pour le SDK Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
