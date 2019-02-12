---
title: 'Démarrage rapide : Rechercher des vidéos à l’aide du SDK Recherche de vidéos Bing pour C#'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour envoyer des requêtes de recherche de vidéos à l’aide du SDK Recherche de vidéos Bing pour C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 820c6a57a4a8266b68c5a245c2c2cf316aa071a9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569079"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Démarrage rapide : Effectuer une recherche de vidéos avec le SDK Recherche de vidéos Bing pour C#

Utilisez ce guide de démarrage rapide pour démarrer une recherche de vidéos avec le SDK Recherche de vidéos Bing pour C#. Si l’outil Recherche de vidéos Bing dispose d’une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). Il contient d’autres fonctionnalités et annotations.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* Le framework Json.NET, disponible [sous forme de package NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Pour ajouter le SDK Recherche de vidéos Bing à votre projet, accédez à l’option `Manage NuGet Packages` à partir de l’Explorateur de solutions dans Visual Studio.  Ajoutez le package `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

L’installation du [[package NuGet du SDK Recherche de vidéos]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) installe également les dépendances suivantes :

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez une solution console en C# dans Visual Studio. Ajoutez ensuite les éléments suivants dans le fichier de code principal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instanciez le client en créant un objet `ApiKeyServiceClientCredentials` avec votre clé d’abonnement, puis en appelant le constructeur.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Envoyer une requête de recherche et traiter les résultats

1. Utilisez le client pour envoyer une requête de recherche. Utilisez « SwiftKey » pour la requête de recherche.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Si des résultats sont retournés, obtenez le premier avec `videoResults.Value[0]`. Ensuite, affichez l’ID, le titre et l’URL de la vidéo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi 

* [Qu’est-ce que l’API Recherche de vidéos Bing ?](../overview.md)
* [Exemples du Kit de développement logiciel (SDK) .NET pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
