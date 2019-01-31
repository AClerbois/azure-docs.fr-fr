---
title: 'Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Node.js - API REST Recherche d’actualités Bing'
titlesuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer une requête à l’API REST Recherche d’actualités Bing à l’aide de Node.js et recevez une réponse JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9eb8869847f60d277857826e810a7e9bf51fc1fa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157158"
---
# <a name="quickstart-perform-a-news-search-using-nodejs-and-the-bing-news-search-rest-api"></a>Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Node.js et l’API REST Recherche d’actualités Bing

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’images Bing et recevoir une réponse JSON. Cette application JavaScript simple envoie une requête de recherche à l’API et affiche les résultats bruts.

Alors que cette application est écrite en JavaScript et s’exécute en Node.js, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingNewsSearchv7.js).

## <a name="prerequisites"></a>Prérequis

* La dernière version de [Node.js](https://nodejs.org/en/download/).

* La [bibliothèque de requêtes JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis définissez la sévérité et les exigences HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Créez des variables pour le point de terminaison d’API, le chemin de recherche d’API d’image, votre clé d’abonnement et le terme de recherche.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/news/search';
    let term = 'Microsoft';
    ```

## <a name="handle-and-parse-the-response"></a>Gérer et analyser la réponse

1. Définissez une fonction nommée `response_handler` qui prend un appel HTTP, `response`, comme paramètre. Dans cette fonction, effectuez les étapes suivantes :

    1. Définissez une variable pour contenir le corps de la réponse JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Stockez le corps de la réponse quand l’indicateur **data** est appelé.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Quand un indicateur **end** est signalé, la réponse JSON et ses en-têtes peuvent être affichés.

        ```javascript
        response.on('end', function () {
            console.log('\nRelevant Headers:\n');
            for (var header in response.headers)
                // header keys are lower-cased by Node.js
                if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                     console.log(header + ": " + response.headers[header]);
            body = JSON.stringify(JSON.parse(body), null, '  ');
            console.log('\nJSON Response:\n');
            console.log(body);
         });
        ```

## <a name="json-response"></a>Réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
[Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
