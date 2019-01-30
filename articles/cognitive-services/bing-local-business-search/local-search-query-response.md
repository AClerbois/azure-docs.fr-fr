---
title: Envoyer et utiliser les requêtes et les réponses de l’API Recherche d’entreprises locales Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Lisez cet article pour apprendre à envoyer et à utiliser les résultats de recherche de l’API Recherche d’entreprises locales Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: 73aa9756d7a3af4c6f7185533458e0e322b72666
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452375"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Envoyer et utiliser les requêtes et les réponses de l’API Recherche d’entreprises locales Bing

Vous pouvez obtenir des résultats locaux de l’API Recherche d’entreprises locales Bing en envoyant une requête de recherche à son point de terminaison et en incluant si nécessaire l’en-tête `Ocp-Apim-Subscription-Key`. En parallèle des [en-têtes](local-search-reference.md#headers) et [paramètres](local-search-reference.md#query-parameters) disponibles, vous pouvez personnaliser les recherches en spécifiant des [limites géographiques](specify-geographic-search.md) pour la zone de recherche ainsi que les [catégories](local-search-query-response.md) des emplacements retournés.

## <a name="creating-a-request"></a>Créer une demande

Pour envoyer une demande à l’API Recherche d’entreprises locales Bing, ajoutez un critère de recherche au paramètre `q=` avant de l’intégrer au point de terminaison API et d’inclure l’en-tête `Ocp-Apim-Subscription-Key`. Par exemple : 

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Voici la syntaxe complète de l’URL de la demande. Pour plus d’informations sur l’envoi de demandes, voir les [démarrages rapides](quickstarts/local-quickstart.md) de l’API Recherche d’entreprises locales Bing et le contenu de référence sur les [en-têtes](local-search-reference.md#headers) et les [paramètres](local-search-reference.md#query-parameters). 

Pour plus d’informations sur les catégories de recherche locale, voir [Catégories de recherche pour l’API Recherche d’entreprises locales Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Utiliser les réponses

Les réponses JSON de l’API Recherche d’entreprises locales Bing contiennent un objet `SearchResponse`. L’API retourne les résultats de recherche pertinents dans le champ `places`. Si aucun résultat n’est trouvé, le champ `places` n’est pas inclus dans la réponse.

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Attributs des résultats de recherche

Les résultats JSON retournés par l’API comportent les attributs suivants :

* _type
* address
* entityPresentationInfo
* zone géographique
* id
* Nom
* routeablePoint
* telephone
* url

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., voir la référence [API Recherche locale Bing v7](local-search-reference.md).

> [!NOTE]
> Vous, ou un tiers agissant en votre nom, ne pouvez pas utiliser, conserver, stocker, mettre en cache, partager ou distribuer les données issues de l’API Recherche locale à des fins de test, de développement, d’apprentissage, de distribution ou de mise à disposition d’un service ou d’une fonctionnalité ne faisant pas partie de la gamme Microsoft. 


## <a name="example-json-response"></a>Exemple de réponse JSON

La réponse JSON suivante comporte les résultats de recherche spécifiés par la requête `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide de la Recherche d’entreprises locales](quickstarts/local-quickstart.md)
- [Démarrage rapide Java de la Recherche d’entreprises locales](quickstarts/local-search-java-quickstart.md)
- [Démarrage rapide Node de la Recherche d’entreprises locales](quickstarts/local-search-node-quickstart.md)
- [Démarrage rapide Python de la Recherche d’entreprises locales](quickstarts/local-search-python-quickstart.md)