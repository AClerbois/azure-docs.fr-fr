---
title: Suggestion automatique des termes de recherche - API Recherche Web Bing
titleSuffix: Azure Cognitive Services
description: Associez l’API Recherche Web Bing et l’API Suggestion automatique Bing pour fournir une expérience de recherche améliorée aux utilisateurs.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: aahi
ms.openlocfilehash: 5640d8ca23f0efc3e54b6ef7c986314a9b209fcf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192126"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Suggestion automatique des termes de recherche Bing dans votre application

Si vous fournissez une zone de recherche dans laquelle l’utilisateur entre son terme de recherche, utilisez [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience. À mesure que l’utilisateur tape des termes de recherche, l’API suggère des chaînes de requête.

Lorsque l’utilisateur saisit un terme de recherche, il doit être codé avec URL avant que le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) soit défini. Par exemple, si l’utilisateur entre *sailing dinghies*, définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

Si le terme de recherche contient une faute d’orthographe, la réponse à la recherche inclut un objet [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext). L’objet affiche l’orthographe d’origine et l’orthographe corrigée utilisées par Bing pour la recherche.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Vous pouvez utiliser ces informations pour signaler à l’utilisateur que vous avez modifié sa chaîne de requête lorsque vous affichez les résultats de la recherche.

![Exemple d’expérience utilisateur pour un contexte de requête](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Étapes suivantes  

* Vérifiez l’exemple avec l’article [Réponses de l’API Recherche Web Bing](search-responses.md).  

## <a name="see-also"></a>Voir aussi  

* [Référence pour l’API Recherche Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
