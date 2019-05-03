---
title: Compétence de fusion de texte de la recherche cognitive - Recherche Azure
description: Fusionne en un seul champ consolidé du texte issu d’une collection de champs. Utilisez cette compétence cognitive dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bbf2e524d626ac17596ded61746c26f20a6caf1b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021837"
---
#    <a name="text-merge-cognitive-skill"></a>Compétence cognitive Fusion de texte

La compétence **Fusion de texte** consolide en un champ unique du texte issu d’une collection de champs. 

> [!NOTE]
> Cette compétence n’est pas liée à une API Cognitive Services et son utilisation ne vous est pas facturée. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| insertPreTag  | Chaîne à inclure avant chaque insertion. La valeur par défaut est `" "`. Pour omettre l’espace, choisissez la valeur `""`.  |
| insertPostTag | Chaîne à inclure après chaque insertion. La valeur par défaut est `" "`. Pour omettre l’espace, choisissez la valeur `""`.  |


##  <a name="sample-input"></a>Exemple d’entrée
Voici un exemple de document JSON fournissant des données d’entrée exploitables pour cette compétence :

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28],
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Exemple de sortie
Cet exemple montre la sortie de l’entrée précédente, à supposer que *insertPreTag* ait la valeur `" "` et *insertPostTag* la valeur `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Exemple étendu de définition de compétences

La fusion de texte permet notamment de fusionner la représentation textuelle d’images (texte issu d’une compétence OCR ou légende d’une image) dans le champ de contenu d’un document. 

L’exemple de compétences suivant utilise la reconnaissance optique des caractères pour extraire du texte à partir d’images incorporées dans le document. Ensuite, il crée un champ *merged_text* qui contiendra le texte avant et après reconnaissance de chaque image. Vous trouverez plus d'informations sur la reconnaissance optique des caractères [ici](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
L’exemple ci-dessus suppose l’existence d’un champ normalized-images. Pour obtenir ce champ, définissez la configuration *imageAction* dans la définition de votre indexeur sur *generateNormalizedImages* comme ci-dessous :

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un indexeur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
