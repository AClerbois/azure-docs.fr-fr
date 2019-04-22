---
title: 'Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Python'
titlesuffix: Azure Cognitive Services
description: Commencez à utiliser l’API REST Vérification orthographique Bing pour vérifier l’orthographe et la grammaire.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 1cf46fd5ec55f0b240f6bb4adbe49c1344a4663b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547677"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Python

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API REST Vérification orthographique Bing. Cette simple application Python envoie une demande à l’API et retourne une liste de suggestions de corrections. Alors que cette application est écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Prérequis

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez l’instruction d’importation suivante.

   ```python
   import requests
   import json
   ```

2. Créez des variables pour le texte dont vous souhaitez vérifier l’orthographe, votre clé d’abonnement et votre point de terminaison Vérification orthographique Bing.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Créer les paramètres de la demande

1. Créez un dictionnaire avec `text` comme clé et votre texte comme valeur.

    ```python
    data = {'text': example_text}
    ```

2. Ajoutez les paramètres de votre demande. Ajoutez le code de votre marché après `mkt=`. Le code du marché correspond au pays depuis lequel vous effectuez la demande. De plus, ajoutez votre mode de vérification orthographique après `&mode=`. Le mode est soit `proof` (détecte la plupart des erreurs d’orthographe et de grammaire) ou `spell` (détecte la plupart des erreurs d’orthographe mais pas autant d’erreurs de grammaire).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Ajoutez un en-tête `Content-Type` et votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envoyer la demande et lire la réponse

1. Envoyez la demande POST à l’aide de la bibliothèque requests.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Obtenez la réponse JSON et imprimez-la.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorials/spellcheck.md)

- [Qu’est-ce que l’API Vérification orthographique Bing ?](../overview.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
