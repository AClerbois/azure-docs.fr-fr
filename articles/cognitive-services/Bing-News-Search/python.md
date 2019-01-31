---
title: 'Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Python - API REST Recherche d’actualités Bing'
titlesuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer une requête à l’API REST Recherche d’actualités Bing à l’aide de Python et recevez une réponse JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7d048287cb70f93088468e9cd477101b0fe2259f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197651"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Python et l’API REST Recherche d’actualités Bing

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’actualités Bing et recevoir une réponse JSON. Cette application JavaScript simple envoie une requête de recherche à l’API et traite les résultats. Cette application est écrite en Python, mais l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Vous pouvez exécuter cet exemple de code comme un notebook Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Le code source de cet exemple est également disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis importez le module de requête. Créez des variables pour votre clé d’abonnement, le point de terminaison et un terme de recherche. Le point de terminaison est indiqué dans le tableau de bord Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Créer les paramètres de la requête

1. Ajoutez votre clé d’abonnement à un nouveau dictionnaire en utilisant `"Ocp-Apim-Subscription-Key"` comme clé. Faites de même pour vos paramètres de recherche.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Envoyer une requête et obtenir une réponse

1. Utilisez la bibliothèque de requêtes pour appeler l’API Recherche visuelle Bing à l’aide de votre clé d’abonnement et des objets de dictionnaire créés à l’étape précédente.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` présente la réponse de l’API en tant qu’objet JSON. Accédez aux descriptions des articles contenus dans la réponse.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Affichage des résultats

Ces descriptions peuvent ensuite être restituées sous la forme d’un tableau, avec le mot clé de recherche surligné en **gras**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
[Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
