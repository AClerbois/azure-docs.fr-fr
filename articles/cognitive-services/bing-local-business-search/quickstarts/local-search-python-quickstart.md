---
title: Démarrage rapide – Envoyer une requête à l’API Recherche d’entreprises locales Bing en Python | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour commencer à utiliser l’API Recherche d’entreprises locales Bing en Python.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 241ade7d4866186946c371aa1786bc913d045483
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335927"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Démarrage rapide : Envoyer une requête à l’API Recherche d’entreprises locales Bing en Python

Utilisez ce démarrage rapide pour commencer à envoyer des demandes à l’API Recherche d’entreprises locales Bing, un service Azure Cognitive Services. Alors que cette application simple est écrite en Python, l’API est un service web RESTful compatible avec tous les langages de programmation capables de formuler des requêtes HTTP et d’analyser du JSON.

Cet exemple d’application récupère des données de réponse locales auprès de l’API pour la requête de recherche `hotel in Bellevue`.

## <a name="prerequisites"></a>Conditions préalables

* [Python](https://www.python.org/) 2.x ou 3.x
 
Vous devrez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les API Bing. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) est suffisant pour suivre ce guide de démarrage rapide. Utilisez la clé d’accès fournie par l’essai gratuit.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Exécuter l’application complète

Le code suivant obtient des résultats localisés. Il est implémenté lors des étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez le paramètre de requête. 
3. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête Ocp-Apim-Subscription-Key.
4. Définissez l’en-tête Ocp-Apim-Subscription-Key. 
5. Établissez la connexion et envoyez la requête.
6. Imprimez les résultats JSON.

Voici le code complet pour cette démonstration :

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide Java de la Recherche d’entreprises locales](local-search-java-quickstart.md)
- [Démarrage rapide C# de la Recherche d’entreprises locales](local-quickstart.md)
- [Démarrage rapide Node de la Recherche d’entreprises locales](local-search-node-quickstart.md)
