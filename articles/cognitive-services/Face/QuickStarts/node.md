---
title: 'Démarrage rapide : Détecter des visages dans une image avec l’API REST Azure et Node.js'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec Node.js pour détecter des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: f9ba2decf051bc21f91058e67fea8677b24714a6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322918"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Démarrage rapide : Détecter des visages dans une image à l’aide de l’API REST Visage et de Node.js

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec Node.js pour détecter des visages humains dans une image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="créez une ressource Visage"  target="_blank">créer une ressource Visage <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le Portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Visage. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
- Un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="set-up-the-node-environment"></a>Configurer l’environnement Node

Accédez au dossier où vous souhaitez créer votre projet et créez le fichier *facedetection.js*. Installez ensuite le module `axios` dans ce projet. Vos scripts peuvent ainsi effectuer des requêtes HTTP.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Écrire le script Node.js

Collez le code suivant dans *facedetection.js*. Ces champs spécifient comment se connecter au service Visage et où obtenir les données d’entrée. [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) et ajoutez-y la clé et le point de terminaison de votre abonnement Visage. Vous pouvez changer le champ `imageUrl` pour qu’il pointe vers votre propre image d’entrée.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```javascript
'use strict';

const axios = require('axios').default;

// Add a valid subscription key and endpoint to your environment variables.
let subscriptionKey = process.env['FACE_SUBSCRIPTION_KEY']
let endpoint = process.env['FACE_ENDPOINT'] + '/face/v1.0/detect'

// Optionally, replace with your own image URL (for example a .jpg or .png URL).
let imageUrl = 'https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/faces.jpg'
```

Ajoutez ensuite le code suivant pour appeler l’API Visage et obtenir des données d’attribut de visage à partir d’une image. Le champ `returnFaceAttributes` spécifie les attributs du visage à récupérer. Vous pouvez changer cette chaîne en fonction de votre utilisation prévue.


```javascript
// Send a POST request
axios({
    method: 'post',
    url: endpoint,
    params : {
        returnFaceId: true,
        returnFaceLandmarks: false,
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
    },
    data: {
        url: imageUrl,
    },
    headers: { 'Ocp-Apim-Subscription-Key': subscriptionKey }
}).then(function (response) {
    console.log('Status text: ' + response.status)
    console.log('Status text: ' + response.statusText)
    console.log()
    //console.log(response.data)
    response.data.forEach((face) => {
      console.log('Face ID: ' + face.faceId)
      console.log('Face rectangle: ' + face.faceRectangle.top + ', ' + face.faceRectangle.left + ', ' + face.faceRectangle.width + ', ' + face.faceRectangle.height)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Head pose: ' + JSON.stringify(face.faceAttributes.headPose))
      console.log('Gender: ' + face.faceAttributes.gender)
      console.log('Age: ' + face.faceAttributes.age)
      console.log('Facial hair: ' + JSON.stringify(face.faceAttributes.facialHair))
      console.log('Glasses: ' + face.faceAttributes.glasses)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Emotion: ' + JSON.stringify(face.faceAttributes.emotion))
      console.log('Blur: ' + JSON.stringify(face.faceAttributes.blur))
      console.log('Exposure: ' + JSON.stringify(face.faceAttributes.exposure))
      console.log('Noise: ' + JSON.stringify(face.faceAttributes.noise))
      console.log('Makeup: ' + JSON.stringify(face.faceAttributes.makeup))
      console.log('Accessories: ' + JSON.stringify(face.faceAttributes.accessories))
      console.log('Hair: ' + JSON.stringify(face.faceAttributes.hair))
      console.log()
    });
}).catch(function (error) {
    console.log(error)
});
```

## <a name="save-and-run-the-script"></a>Enregistrer et exécuter le script

Après avoir apporté vos changements, ouvrez une invite de commandes et exécutez le fichier avec la commande `node`.

```
node facedetection.js
```

Voici les données JSON complètes de `response.data`. Par exemple :

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez écrit un script Node.js qui appelle le service Visage Azure pour détecter des visages dans une image et retourner leurs attributs. Explorez à présent la documentation de référence sur l’API Visage pour en savoir plus.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
