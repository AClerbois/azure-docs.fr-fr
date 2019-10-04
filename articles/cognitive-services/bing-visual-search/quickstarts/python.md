---
title: 'Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de Python'
titleSuffix: Azure Cognitive Services
description: Découvrez comment charger une image dans l’API Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047124"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de Python

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche visuelle Bing et afficher les résultats. Cette application Python charge une image dans l’API et affiche les informations qu’elle retourne. Bien que cette application soit écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Quand vous chargez une image locale, les données de formulaire doivent inclure l’en-tête `Content-Disposition`. Vous devez définir son paramètre `name`sur « image », et vous pouvez définir le paramètre `filename` sur n’importe quelle chaîne. Le contenu du formulaire inclut les données binaires de l’image. La taille maximale de l’image que vous chargez est de 1 Mo.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Prérequis

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initialiser l’application

1. Créez un fichier Python dans votre IDE ou votre éditeur favori, puis ajoutez l’instruction `import` suivante :

    ```python
    import requests, json
    ```

2. Créez des variables pour votre clé d’abonnement, le point de terminaison et le chemin de l’image que vous chargez :

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Créez un objet de dictionnaire pour stocker les informations d’en-tête de votre demande. Liez votre clé d’abonnement à la chaîne `Ocp-Apim-Subscription-Key`, comme indiqué ci-dessous :

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Créez un autre dictionnaire destiné à contenir votre image, qui est ouverte et chargée quand vous envoyez la demande :

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyser la réponse JSON

1. Créez une méthode appelée `print_json()` pour récupérer la réponse de l’API et l’afficher au format JSON :

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Envoyer la demande

1. Utilisez `requests.post()` pour envoyer une demande à l’API Recherche visuelle Bing. Incluez la chaîne indiquant les informations relatives au point de terminaison, à l’en-tête et au fichier. Affichez `response.json()` avec `print_json()` :

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage de recherche visuelle](../tutorial-bing-visual-search-single-page-app.md)
