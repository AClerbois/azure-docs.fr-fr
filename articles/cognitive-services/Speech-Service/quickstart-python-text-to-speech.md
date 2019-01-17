---
title: 'Démarrage rapide : Convertir du texte par synthèse vocale, Python - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à convertir du texte par synthèse vocale à l’aide de Python et de l’API REST Synthèse vocale. L’exemple de texte inclus dans ce guide est structuré en tant que langage de balisage de synthèse vocale (SSML). Cela vous permet de choisir la voix et la langue de la réponse de la reconnaissance vocale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2828330a0b00bb0695dcf91a84d0099d30c43183
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360120"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Démarrage rapide : Convertir du texte par synthèse vocale à l’aide de Python

Dans ce guide de démarrage rapide, vous allez apprendre à convertir du texte par synthèse vocale à l’aide de Python et de l’API REST Synthèse vocale. Le corps de la requête figurant dans ce guide est structuré en tant que [Langage de balisage de synthèse vocale (SSML)](speech-synthesis-markup.md), ce qui vous permet de choisir la voix et la langue de la réponse.

Ce guide de démarrage rapide exige un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource du service Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Clé d’abonnement Azure pour le service Speech

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `tts.py`.

```python
import os, requests, time
from xml.etree import ElementTree
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests`.

Ces modules sont utilisés pour écrire la réponse de la reconnaissance vocale dans un fichier avec un horodatage, construire la requête HTTP et appeler l’API Synthèse vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Définir la clé d’abonnement et créer une invite pour la conversion de texte par synthèse vocale (TTS)

Dans les sections suivantes, vous allez créer des méthodes pour gérer l’autorisation, appeler l’API Synthèse vocale et valider la réponse. Commençons par ajouter du code qui vérifie que cet exemple fonctionnera avec Python 2.7.x et 3.x.

```python
try: input = raw_input
except NameError: pass
```

Maintenant, créons une classe. C’est là que nous mettrons nos méthodes pour l’échange de jeton et l’appel de l’API Synthèse vocale.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` est votre clé unique à partir du portail Azure. `tts` invite l’utilisateur à entrer du texte qui sera converti en parole. Cette entrée est un littéral de chaîne. Il n’est donc pas nécessaire de placer les caractères dans une séquence d’échappement. Enfin, `timestr` obtient l’heure actuelle, que nous allons utiliser pour nommer votre fichier.

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

L’API REST Synthèse vocale nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cet exemple échange votre clé d’abonnement du service Speech avec un jeton accès à l’aide du point de terminaison `issueToken`.

Cet exemple suppose que votre abonnement du service Speech se situe dans la région USA Ouest. Si vous utilisez une autre région, mettez à jour la valeur de `fetch_token_url`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez le code suivant dans la classe `TextToSpeech` :

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

Ici, vous allez générer la requête et enregistrer la réponse de la reconnaissance vocale. Tout d’abord, vous devez définir les valeurs `base_url` et `path`. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `base_url`. Pour plus d’informations, consultez [Régions du service Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ensuite, vous devez ajouter les en-têtes nécessaires pour la requête. Veillez à mettre à jour `User-Agent` avec le nom de votre ressource (située dans le portail Azure), puis définissez `X-Microsoft-OutputFormat` sur la sortie audio de votre choix. Pour obtenir la liste complète des formats de sortie, consultez [Sorties audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Construisez ensuite le corps de la requête en utilisant le langage de balisage de synthèse vocale (SSML). Cet exemple définit la structure et utilise l’entrée `tts` créée précédemment.

>[!NOTE]
> Cet exemple utilise la police de la voix `ZiraRUS`. Pour obtenir la liste complète des voix/langues fournies par Microsoft, consultez [Prise en charge de la langue](language-support.md).
> Si vous êtes intéressé par la création d’une voix unique, reconnaissable pour votre marque, consultez [Création de polices de voix personnalisée](how-to-customize-voice-font.md).

Enfin, vous envoyez une requête au service. Si la requête réussit et qu’un code d’état 200 est retourné, la réponse de la reconnaissance vocale est écrite dans un fichier horodaté.

Copiez le code suivant dans la classe `TextToSpeech` :

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à instancier votre classe et à appeler vos fonctions.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application de synthèse vocale. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
python tts.py
```

Quand vous y êtes invité, tapez ce que vous souhaitez convertir par synthèse vocale. En cas de réussite, le fichier vocal se trouve dans votre dossier de projet. Vous pouvez procéder à sa lecture à l’aide du lecteur multimédia de votre choix.

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
