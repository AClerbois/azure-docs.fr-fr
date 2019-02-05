---
title: 'Démarrage rapide : Détecter des visages dans une image avec l’API REST Azure et C#'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec C# pour détecter des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 6dce69b06d00ef41f4ffee46f644fb0dc3560ecc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220601"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Démarrage rapide : Détecter des visages dans une image à l’aide de l’API REST Visage et de C#

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec C# pour détecter des visages humains dans une image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- Clé d’abonnement à l’API Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Dans Visual Studio, créez un projet **Application console (.NET Framework)** et nommez-le **FaceDetection**. 
1. Si votre solution comporte d’autres projets, sélectionnez celui-ci comme projet de démarrage unique.

## <a name="add-face-detection-code"></a>Ajoutez le code de détection de visage

Ouvrez le fichier *Program.cs* du nouveau projet. Ici, vous allez ajouter le code nécessaire pour charger des images et détecter des visages.

### <a name="include-namespaces"></a>Inclure des espaces de noms

Ajoutez les instructions `using` suivantes en haut de votre fichier *Program.cs*.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Ajouter des champs essentiels

Ajoutez les champs suivants à la classe **Program** . Ces données spécifient comment se connecter au service Visage et où obtenir les données d’entrée. Vous devez mettre à jour le champ `subscriptionKey` avec la valeur de votre clé d’abonnement, et éventuellement changer la chaîne `uriBase` pour qu’elle contienne l’identificateur de région approprié. (Pour connaître la liste des points de terminaison de toutes les régions, consultez les [documents API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)).


```csharp
// Replace <Subscription Key> with your valid subscription key.
const string subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.
const string uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Recevoir l’image en entrée

Ajoutez le code suivant à la méthode **Main** de la classe **Program**. Il écrit une invite dans la console qui demande à l’utilisateur d’entrer une URL d’image. Il appelle ensuite une autre méthode, **MakeAnalysisRequest**, pour traiter l’image à cet emplacement.

```csharp
// Get the path and filename to process from the user.
Console.WriteLine("Detect faces:");
Console.Write(
    "Enter the path to an image with faces that you wish to analyze: ");
string imageFilePath = Console.ReadLine();

if (File.Exists(imageFilePath))
{
    try
    {
        MakeAnalysisRequest(imageFilePath);
        Console.WriteLine("\nWait a moment for the results to appear.\n");
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
    }
}
else
{
    Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
}
Console.ReadLine();
```

### <a name="call-the-face-detection-rest-api"></a>Appeler l’API REST de détection des visages

Ajoutez la méthode suivante à la classe **Program**. Elle construit un appel REST à l’API Visage pour détecter les informations de visage dans l’image distante (la chaîne `requestParameters` spécifie les attributs de visage à récupérer). Elle écrit ensuite les données de sortie dans une chaîne JSON.

Vous allez définir les méthodes d’assistance dans les étapes suivantes.

```csharp
// Gets the analysis of the specified image by using the Face REST API.
static async void MakeAnalysisRequest(string imageFilePath)
{
    HttpClient client = new HttpClient();

    // Request headers.
    client.DefaultRequestHeaders.Add(
        "Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request parameters. A third optional parameter is "details".
    string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
        "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
        "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    // Assemble the URI for the REST API Call.
    string uri = uriBase + "?" + requestParameters;

    HttpResponseMessage response;

    // Request body. Posts a locally stored JPEG image.
    byte[] byteData = GetImageAsByteArray(imageFilePath);

    using (ByteArrayContent content = new ByteArrayContent(byteData))
    {
        // This example uses content type "application/octet-stream".
        // The other content types you can use are "application/json"
        // and "multipart/form-data".
        content.Headers.ContentType =
            new MediaTypeHeaderValue("application/octet-stream");

        // Execute the REST API call.
        response = await client.PostAsync(uri, content);

        // Get the JSON response.
        string contentString = await response.Content.ReadAsStringAsync();

        // Display the JSON response.
        Console.WriteLine("\nResponse:\n");
        Console.WriteLine(JsonPrettyPrint(contentString));
        Console.WriteLine("\nPress Enter to exit...");
    }
}
```

### <a name="process-the-input-image-data"></a>Traiter les données de l’image en entrée

Ajoutez la méthode suivante à la classe **Program**. Elle convertit l’image située à l’URL spécifiée en tableau d’octets.

```csharp
// Returns the contents of the specified file as a byte array.
static byte[] GetImageAsByteArray(string imageFilePath)
{
    using (FileStream fileStream =
        new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
    {
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }
}
```

### <a name="parse-the-json-response"></a>Analyser la réponse JSON

Ajoutez la méthode suivante à la classe **Program**. Elle met en forme l’entrée JSON pour améliorer la lisibilité. Votre application écrit les données de cette chaîne dans la console.

```csharp
// Formats the given JSON string by adding line breaks and indents.
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    int offset = 0;
    int indentLength = 3;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\'':
                if (quote) ignore = !ignore;
                break;
        }

        if (quote)
            sb.Append(ch);
        else
        {
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', ++offset * indentLength));
                    break;
                case '}':
                case ']':
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', --offset * indentLength));
                    sb.Append(ch);
                    break;
                case ',':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', offset * indentLength));
                    break;
                case ':':
                    sb.Append(ch);
                    sb.Append(' ');
                    break;
                default:
                    if (ch != ' ') sb.Append(ch);
                    break;
            }
        }
    }

    return sb.ToString().Trim();
}
```

## <a name="run-the-app"></a>Exécution de l'application

Une réponse correcte affiche les données Visage dans un format JSON facile à lire. Par exemple : 

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une application de console .NET simple qui utilise des appels REST avec l’API Visage Azure pour détecter des visages dans une image et retourner leurs attributs. Explorez à présent la documentation de référence sur l’API Visage pour en savoir plus sur les scénarios pris en charge.

> [!div class="nextstepaction"]
> [API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
