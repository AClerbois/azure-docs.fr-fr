---
title: 'Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en utilisant Ruby'
titlesuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour envoyer une requête à l’API REST Recherche d’entités Bing à l’aide de Ruby et recevoir une réponse JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 35659a77f80bd3b3d105b9ea91513784dbca01c4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539001"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Démarrage rapide pour l’API Recherche d’entités Bing avec Ruby

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’entités Bing et voir la réponse JSON. Cette application Ruby simple envoie une requête de recherche d’actualités à l’API, puis affiche la réponse. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Alors que cette application est écrite en Ruby, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Prérequis

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou ultérieur.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Dans votre IDE ou éditeur de code favori, créez un fichier Ruby d’actualités et importez les packages suivants.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Créez des variables pour votre point de terminaison d’API, l’URL de recherche d’actualités, votre clé d’abonnement et une requête de recherche.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Mettre en forme et effectuer une requête d’API

1. Créez la chaîne de paramètres de votre requête en ajoutant la variable de votre marché au paramètre `?mkt=`. Encodez votre requête et ajoutez-la au paramètre `&q=`. Combinez l’hôte, le chemin de votre API et les paramètres de votre requête, puis castez-les en objet URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Utilisez les variables de la dernière étape pour créer la requête. Ajoutez votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Envoyer la requête et afficher la réponse

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-entities-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../search-the-web.md)
* [Informations de référence sur l’API Recherche d’entités Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
