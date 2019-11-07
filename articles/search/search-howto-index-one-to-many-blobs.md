---
title: Indexer un blob en plusieurs documents d’index de recherche à partir de l’indexeur d’objets blob Azure pour la recherche en texte intégral
titleSuffix: Azure Cognitive Search
description: Analysez les objets blob Azure à la recherche de contenu textuel à l’aide de l’indexeur d’objets blob de la Recherche cognitive Azure. Chaque objet blob peut contenir un ou plusieurs documents d’index de recherche.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 863d38f6ea0f071a1c1a6678d025ec5b37a306dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466426"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexation d’objets blob pour produire plusieurs documents de recherche
Par défaut, un indexeur d’objets blob traite le contenu d’un objet blob comme un document de recherche unique. Certaines valeurs **parsingMode** prennent en charge les scénarios où un objet blob individuel peut entraîner plusieurs documents de recherche. Les différents types de **parsingMode** qui permettent à un indexeur d’extraire plusieurs documents de recherche à partir d’un objet blob sont les suivants :
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Clé de document de type un-à-plusieurs
Chaque document qui s’affiche dans un index de la Recherche cognitive Azure est identifié par une clé de document. 

En l’absence de mode d’analyse et de mappage explicite pour le champ de clé dans l’index, la Recherche cognitive Azure [mappe](search-indexer-field-mappings.md) automatiquement la propriété `metadata_storage_path` en guise de clé. Ce mappage garantit que chaque objet blob apparaît sous la forme d’un document de recherche distinct.

Lorsque vous utilisez un des modes d’analyse répertoriées ci-dessus, un objet blob correspond à « plusieurs » documents de recherche, et la clé de document repose uniquement sur des métadonnées d’objets blob non adaptées. Pour contourner cette contrainte, la Recherche cognitive Azure est capable de générer une clé de document de type « un-à-plusieurs » pour chaque entité extraite à partir d’un objet blob. Cette propriété est nommée `AzureSearch_DocumentKey` et ajoutée à chaque entité extraite à partir de l’objet blob. La valeur de cette propriété est garantie unique pour chaque entité _parmi les objets blob_, et les entités seront affichées sous forme de documents de recherche distincts.

Par défaut, en l’absence de mappage explicite pour le champ d’index de clé, le champ `AzureSearch_DocumentKey` est mappé à celui-ci, à l’aide de la fonction de mappage de champs `base64Encode`.

## <a name="example"></a>Exemples
Supposons que vous avez une définition d’index avec les champs suivants :
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Et que votre conteneur d’objets blob a des objets blob avec la structure suivante :

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Lorsque vous créez un indexeur et définissez **parsingMode** sur `jsonLines` (sans spécifier de mappage explicite pour le champ de clé), le mappage suivant est implicitement appliqué.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Cette configuration produit un index de Recherche cognitive Azure contenant les informations suivantes (ID codé en base64 raccourci par souci de concision)

| id | température | pression | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mappage de champ personnalisé pour le champ de clé d’index

En prenant la même définition d’index que l’exemple précédent, supposons que votre conteneur d’objets blob a des objets blob avec la structure suivante :

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Lorsque vous créez un indexeur avec `delimitedText` **parsingMode**, il peut sembler naturel de configurer une fonction de mappage pour le champ de clé comme suit :

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Toutefois, ce mappage ne permet _pas_ d’afficher 4 documents dans l’index, car le champ `recordid` n’est pas unique _parmi les objets blob_. Par conséquent, nous vous recommandons d’utiliser le mappage de champs implicite appliqué à partir de la propriété `AzureSearch_DocumentKey` pour le champ d’index de clé avec les modes d’analyse « un-à-plusieurs ».

Si vous ne souhaitez pas configurer un mappage de champs explicite, assurez-vous que la valeur _sourceField_ est distincte pour chaque entité **parmi tous les objets blob**.

> [!NOTE]
> L’approche utilisée par `AzureSearch_DocumentKey`, visant à assurer l’unicité des entités extraites, est susceptible de changer. Par conséquent, ne vous fiez pas à sa valeur pour les besoins de votre application.

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes pas déjà familiarisé avec la structure et le flux de travail de base de l’indexation d’objets BLOB, vous devez passer en revue [Indexation du stockage d’objets blob Azure avec la Recherche Azure](search-howto-index-json-blobs.md) en premier. Pour plus d’informations sur les modes d’analyse pour les différents types de contenu blob, consultez les articles suivants.

> [!div class="nextstepaction"]
> [Indexation d’objets blob CSV](search-howto-index-csv-blobs.md)
> [Indexation d’objets blob JSON](search-howto-index-json-blobs.md)
