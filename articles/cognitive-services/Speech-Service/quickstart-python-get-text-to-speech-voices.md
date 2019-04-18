---
title: 'Démarrage rapide : Liste voix, Python - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronaux pour une région/le point de terminaison que l’utilisation de Python. La liste est retournée au format JSON, et la disponibilité de voix varie selon la région.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: erhopf
ms.openlocfilehash: 66bda68b1313a7c172e273671bc3a03503d08e0d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876578"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Démarrage rapide : Obtenir la liste des voix de synthèse vocale à l’aide de Python

Dans ce démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronaux pour une région/le point de terminaison que l’utilisation de Python. La liste est retournée au format JSON, et la disponibilité de voix varie selon la région. Pour obtenir la liste des régions prises en charge, consultez [régions](regions.md).

Ce démarrage rapide nécessite un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource de Services de reconnaissance vocale. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Conditions préalables

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour les Services de reconnaissance vocale

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests`.

Demandes est utilisé pour les requêtes HTTP au service de synthèse vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Définir la clé d’abonnement et créer une invite pour la conversion de texte par synthèse vocale (TTS)

Dans les sections suivantes, vous allez créer des méthodes pour gérer l’autorisation, appeler l’API Synthèse vocale et valider la réponse. Nous allons commencer en créant une classe. C’est là que nous mettrons nos méthodes pour l’échange de jeton et l’appel de l’API Synthèse vocale.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` est votre clé unique à partir du portail Azure.

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Ce point de terminaison nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cet exemple échange votre clé d’abonnement de Services de reconnaissance vocale pour un jeton accès à l’aide du `issueToken` point de terminaison.

Cet exemple suppose que votre abonnement aux Services de reconnaissance vocale est dans la région ouest des États-Unis. Si vous utilisez une autre région, mettez à jour la valeur de `fetch_token_url`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez le code suivant dans la classe `GetVoices` :

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Pour plus d'informations sur l'authentification, consultez [S'authentifier avec un jeton d'accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Effectuer une requête et enregistrer la réponse

Ici, vous allez à la demande de build et enregistrer la liste des voix retourné. Tout d’abord, vous devez définir les valeurs `base_url` et `path`. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `base_url`. Pour plus d’informations, consultez [régions des Services de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ensuite, ajoutez les en-têtes requis pour la demande. Enfin, vous envoyez une requête au service. Si la demande réussit, et un code de 200 état est retourné, la réponse est écrite dans le fichier.

Copiez le code suivant dans la classe `GetVoices` :

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à instancier votre classe et à appeler vos fonctions.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter l’exemple. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
