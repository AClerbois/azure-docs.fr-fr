---
title: Analyser des fichiers vidéo et audio avec Azure Media Services | Microsoft Docs
description: Lorsque vous utilisez Azure Media Services, vous pouvez analyser vos contenus audio et vidéo à l’aide d’AudioAnalyzerPreset et de VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 247d72396d1737d568a89656c544bbe699f11e30
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342386"
---
# <a name="analyzing-video-and-audio-files"></a>Analyser des fichiers vidéo et audio

Azure Media Services v3 vous permet d’extraire les insights de vos fichiers vidéo et audio avec Video Indexer via les présélections de l’analyseur d’AMS v3 (décrites dans cet article). Si vous souhaitez des informations plus détaillées, utilisez directement Video Indexer. Pour comprendre à quel moment utiliser Video Indexer plutôt que les présélections de l’analyseur de Media Services, consultez le [document de comparaison](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Pour analyser votre contenu à l’aide des préréglages Media Services v3, vous créez une **transformation** et envoyez un **travail** qui utilise l’un de ces préréglages : [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. L’article suivant montre comment utiliser **VideoAnalyzerPreset** : [Tutoriel : Analyser des vidéos avec Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Lorsque vous utilisez des présélections pour l’analyseur vidéo ou audio, utilisez le Portail Azure pour paramétrer votre compte de sorte à ce qu’il dispose de 10 unités réservées Multimédia S3. Pour plus d’informations, consultez [Vue d’ensemble de la mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md).

## <a name="built-in-presets"></a>Préréglages intégrés

Actuellement, Media Services prend en charge les préréglages d’analyseur intégrés suivants :  

|**Nom du préréglage**|**Scénario**|**Détails**|
|---|---|---|
|**AudioAnalyzerPreset**|Analyse de contenu audio|Ce préréglage applique un ensemble prédéfini d’opérations d’analyse basée sur l’IA, notamment la transcription de la parole. Actuellement, le préréglage prend en charge le traitement du contenu avec une seule piste audio qui inclut la reconnaissance vocale dans une seule langue. Vous pouvez spécifier la langue de la charge utile audio de l’entrée en utilisant le format BCP-47 « balise de langue-région ». Les langues prises en charge sont l’anglais (« en-US » et « en-GB »), l’espagnol (« es-ES » et « es-MX »), le français (« fr-FR »), l’italien (« it-IT »), le japonais (« ja-JP »), le portugais (« pt-BR »), le chinois (« zh-CN »), l’allemand (« de-DE »), l’arabe (« ar-EG »), le russe (« ru-RU »), l’hindi (« hi-IN ») et le coréen (« ko-KR »).<br/><br/> Si la langue n’est pas spécifiée ou a la valeur Null, la fonctionnalité de détection automatique de la langue choisit la première langue détectée qu’elle utilise pendant la durée de traitement du fichier. Cette fonctionnalité prend actuellement en charge les langues suivantes : allemand, anglais, chinois, espagnol, français, italien, japonais, portugais et russe. Actuellement, elle ne prend pas en charge le basculement dynamique d’une langue à l’autre après la détection de la première langue. La fonctionnalité de détection automatique de la langue fonctionne mieux sur des enregistrements audio avec des voix clairement identifiables. Si la détection automatique de la langue ne parvient pas à trouver la langue, la transcription utilise l’anglais.|
|**VideoAnalyzerPreset**|Analyse de contenu audio et vidéo|Extrait des insights (métadonnées enrichies) des contenus audio et vidéo, et génère en sortie un fichier au format JSON. Vous pouvez spécifier si vous voulez extraire seulement des insights audio lors du traitement d’un fichier vidéo. Pour plus d’informations, consultez [Analyser un contenu vidéo](analyze-videos-tutorial-with-api.md).|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Le préréglage vous permet d’extraire plusieurs insights audio d’un fichier audio ou vidéo. La sortie inclut un fichier JSON (avec tous les insights) et un fichier VTT pour la transcription audio. Ce paramètre accepte une propriété qui spécifie la langue du fichier d’entrée sous la forme d’une chaîne [BCP47](https://tools.ietf.org/html/bcp47). Les analyses audio sont les suivantes :

* Transcription audio : transcription des mots prononcés avec horodatages. Plusieurs langues sont prises en charge
* Indexation de l’orateur : mappage des orateurs et des mots prononcés correspondants
* Analyse du sentiment vocal : sortie de l’analyse des sentiments effectuée sur la transcription audio
* Mots clés : mots clés extraits de la transcription audio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Ce préréglage vous permet d’extraire plusieurs insights audio et vidéo à partir d’un fichier vidéo. La sortie inclut un fichier JSON (avec tous les insights), un fichier VTT pour la transcription audio et une collection de miniatures. Ce paramètre accepte également une chaîne [BCP47](https://tools.ietf.org/html/bcp47) (représentant la langue de la vidéo) en tant que propriété. Les insights vidéo incluent tous les insights audio mentionnés ci-dessus en complément des éléments suivants :

* Suivi du visage : durée pendant laquelle des visages sont présentes dans la vidéo. Chaque visage est associé à un identifiant de visage et à une collection de miniatures correspondante
* Texte visuel : texte détecté par la reconnaissance optique des caractères. Le texte est horodaté et également utilisé pour extraire des mots clés (en plus de la transcription audio)
* Images clés : collection d’images clés extraites de la vidéo
* Modération du contenu visuel : partie des vidéos qui a été marquée d’un drapeau l’identifiant comme un contenu pour adulte ou provocateur par nature
* Annotation : résultat de l’annotation des vidéos sur la base d’un modèle d’objet prédéfini

##  <a name="insightsjson-elements"></a>Éléments insights.json

La sortie inclut un fichier JSON (insights.json) contenant tous les insights trouvés dans le contenu vidéo ou audio. Ce fichier json peut contenir les éléments suivants :

### <a name="transcript"></a>transcription

|Nom|Description|
|---|---|
|id|ID de la ligne.|
|texte|La transcription proprement dite.|
|Langage|La langue de la transcription. Permet de prendre en charge la transcription lorsque chaque ligne peut avoir une langue différente.|
|instances|Liste des intervalles de temps pendant lesquels cette ligne est apparue. Si l’instance est un attribut transcript, il n’y a qu’une seule instance.|

Exemple :

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|Nom|Description|
|---|---|
|id|ID de la ligne ROC.|
|texte|Texte de l’OCR.|
|confidence|Degré de confiance de la reconnaissance.|
|Langage|Langue de l’OCR.|
|instances|Liste des intervalles de temps au cours desquels cette OCR est apparue (la même OCR peut apparaître plusieurs fois).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>visages

|Nom|Description|
|---|---|
|id|ID du visage.|
|Nom|Nom du visage. Il peut avoir la valeur 'Unknown #0' ou il peut s’agit d’une célébrité identifiée ou une personne formée par le client.|
|confidence|Degré de confiance de l’identification du visage.|
|description|Description de la célébrité. |
|thumbnailId|ID de la miniature de ce visage.|
|knownPersonId|S’il s’agit d’une personne connue, c’est son ID interne.|
|referenceId|Dans le cas d’une célébrité Bing, il s’agit de son ID Bing.|
|referenceType|Bing uniquement (pour le moment).|
|title|Dans le cas d’une célébrité, il s’agit de son poste (par exemple « PDG de Microsoft »).|
|imageUrl|Dans le cas d’une célébrité, il s’agit de l’URL de l’image associée.|
|instances|Instances où la visage est apparu dans l’intervalle de temps donné. Chaque instance possède également un thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>captures

|Nom|Description|
|---|---|
|id|ID de la capture.|
|keyFrames|Liste des images clés au sein de la capture (chacune possède un ID et une liste d’intervalles de temps d’instances). Les instances des images clés comptent un champ thumbnailId pourvu de l’ID de miniature de l’élément keyFrame.|
|instances|Liste des intervalles de temps de cette capture (les captures n’ont qu’1 seule instance).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|Nom|Description|
|---|---|
|CorrespondenceCount|Nombre de correspondances contenues dans la vidéo.|
|WordCount|Nombre de mots par intervenant.|
|SpeakerNumberOfFragments|Quantité de fragments de l’intervenant dans une vidéo.|
|SpeakerLongestMonolog|Monologue le plus long de l’intervenant. Si le monologue de l’intervenant comporte des silences, ils sont inclus. Les silences du début et de la fin du monologue sont supprimés.| 
|SpeakerTalkToListenRatio|Le calcul est basé sur le temps passé sur le monologue de l’intervenant (sans les silences intermédiaires) divisé par la durée totale de la vidéo. L’heure est arrondie à la troisième décimale.|


### <a name="sentiments"></a>sentiments

Les sentiments sont regroupés par leur champ sentimentType (neutre/positif/négatif). Par exemple, 0-0.1, 0.1-0.2.

|Nom|Description|
|---|---|
|id|ID du sentiment.|
|averageScore |Moyenne de tous les résultats obtenus pour toutes les instances de ce type de sentiment : neutre/positif/négatif|
|instances|Liste des intervalles de temps au cours desquels ce sentiment est apparu.|
|sentimentType |Le type peut être « Positive », « Neutral » ou «Negative ».|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>étiquettes

|Nom|Description|
|---|---|
|id|ID de l’étiquette.|
|Nom|Nom de l’étiquette (par exemple, « ordinateur », « TV »).|
|Langage|Langue du nom de l’étiquette (si traduction). BCP-47|
|instances|Liste des intervalles de temps au cours desquels cette étiquette est apparue (une étiquette peut apparaître plusieurs fois). Chaque instance possède un champ de confiance. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>mots clés

|Nom|Description|
|---|---|
|id|ID du mot clé.|
|texte|Texte du mot clé.|
|confidence|Degré de confiance de la reconnaissance du mot clé.|
|Langage|Langue du mot clé (si traduction).|
|instances|Liste des intervalles de temps pendant lesquels ce mot clé est apparu (un mot clé peut apparaître plusieurs fois).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Le bloc visualContentModeration contient des intervalles de temps qui sont susceptibles de contenir des éléments pour adultes selon Video Indexer. Si ce bloc est vide, aucun contenu pour adultes n’a donc été identifié.

Les vidéos trouvées qui contiennent des éléments pour adultes ou choquants peuvent être disponibles pour un affichage privé uniquement. Les utilisateurs peuvent soumettre une demande de révision manuelle du contenu, auquel cas l’attribut IsAdult contient le résultat de la révision manuelle.

|Nom|Description|
|---|---|
|id|ID de modération du contenu visuel.|
|adultScore|Degré du contenu pour adultes (d’après Content Moderator).|
|racyScore|Degré du contenu choquant (d’après Content Moderator).|
|instances|Liste des intervalles de temps où cette modération du contenu visuel est affichée.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Analyser des vidéos avec Azure Media Services](analyze-videos-tutorial-with-api.md)
