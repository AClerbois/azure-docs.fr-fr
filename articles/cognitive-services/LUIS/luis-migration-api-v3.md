---
title: Migration de l’API V2 vers V3
titleSuffix: Azure Cognitive Services
description: Les API de point de terminaison de version 3 ont évolué. Servez-vous de ce guide pour comprendre comment migrer vers les API de point de terminaison de version 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213517"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Aperçu : Migrer vers la version 3.x de l’API pour les applications LUIS

Les API de point de terminaison de prédiction de requête ont évolué. Servez-vous de ce guide pour comprendre comment migrer vers les API de point de terminaison de version 3. 

Cette API V3 propose les nouveautés suivantes, notamment une évolution importante en matière de demandes et/ou de réponses JSON : 

* [Entités externes](#external-entities-passed-in-at-prediction-time)
* [Listes dynamiques](#dynamic-lists-passed-in-at-prediction-time)
* [Évolutions de JSON – Entités prédéfinies](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

La [demande](#request-changes) et la [réponse](#response-changes) du point de terminaison de prédiction de requête ont évolué de façon significative afin de prendre en charge les nouvelles fonctionnalités mentionnées ci-dessus, notamment les suivantes :

* [Évolutions de l’objet réponse](#top-level-json-changes)
* [Remplacement des références au nom de rôle d’entité par le nom d’entité](#entity-role-name-instead-of-entity-name)
* [Propriétés visant à marquer les entités dans les énoncés](#marking-placement-of-entities-in-utterances)

Les fonctionnalités LUIS suivantes ne sont **pas prises en charge** dans l’API V3 :

* Vérification orthographique Bing V7

La [documentation de référence](https://aka.ms/luis-api-v3) est disponible pour V3.

## <a name="endpoint-url-changes-by-slot-name"></a>Évolutions de l’URL de point de terminaison par nom d’emplacement

Le format de l’appel HTTP de point de terminaison V3 a évolué.

|MÉTHODE|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

Valeurs valides pour les emplacements :

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Évoluions de l’URL de point de terminaison par ID de version

Si vous souhaitez interroger par version, vous devez d’abord [publier via l’API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) avec `"directVersionPublish":true`. Interrogez le point de terminaison qui fait référence à l’ID de version plutôt qu’au nom d’emplacement.


|MÉTHODE|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Entités prédéfinies avec le nouveau JSON

Parmi les évolutions de l’objet réponse V3 figurent les [entités prédéfinies](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Demander des changements 

### <a name="query-string-parameters"></a>Paramètres de chaîne de requête

L’API V3 possède de différents paramètres de chaîne de requête.

|Nom de paramètre|Type|Version|Default|Objectif|
|--|--|--|--|--|
|`log`|boolean|V2 et V3|false|Stocker la requête dans un fichier journal.| 
|`query`|string|V3 uniquement|Pas de valeur par défaut. Obligatoire dans la demande GET.|**Dans V2**, l’énoncé à prédire se trouve dans le paramètre `q`. <br><br>**Dans V3**, la fonctionnalité est transmise dans le paramètre `query`.|
|`show-all-intents`|boolean|V3 uniquement|false|Retourner toutes les intentions avec le score correspondant dans l’objet **prediction.intents**. Les intentions sont retournées en tant qu’objets dans un objet `intents` parent. Cela permet un accès programmatique sans qu’il soit nécessaire de rechercher l’intention dans un tableau : `prediction.intents.give`. Dans V2, elles étaient retournées dans un tableau. |
|`verbose`|boolean|V2 et V3|false|**Dans V2**, quand la valeur est true, cela signifie que toutes les intentions prédites ont été retournées. Si vous avez besoin de toutes les intentions prédites, utilisez le paramètre `show-all-intents` de V3.<br><br>**Dans V3**, ce paramètre fournit uniquement les détails des métadonnées d’entité de la prédiction d’entité.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Corps JSON de prédiction de requête pour la demande `POST`

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriété|Type|Version|Default|Objectif|
|--|--|--|--|--|
|`dynamicLists`|array|V3 uniquement|Non requis.|Les [listes dynamiques](#dynamic-lists-passed-in-at-prediction-time) vous permettent d’étendre une entité de liste entraînée et publiée existante, déjà présente dans l’application LUIS.|
|`externalEntities`|array|V3 uniquement|Non requis.|Les [entités externes](#external-entities-passed-in-at-prediction-time) permettent à votre application LUIS d’identifier et d’étiqueter les entités pendant l’exécution, qui peuvent servir de fonctionnalités aux entités existantes. |
|`options.datetimeReference`|string|V3 uniquement|Pas de valeur par défaut|Utilisé pour déterminer le [décalage de datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Le format du datetimeReference est [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|V3 uniquement|false|Indique si l’[entité externe (dont le nom est identique à celui de l’entité existante)](#override-existing-model-predictions) de l’utilisateur est utilisée ou si l’entité existante du modèle est utilisée pour la prédiction. |
|`query`|string|V3 uniquement|Requis.|**Dans V2**, l’énoncé à prédire se trouve dans le paramètre `q`. <br><br>**Dans V3**, la fonctionnalité est transmise dans le paramètre `query`.|



## <a name="response-changes"></a>Évolutions de la réponse

Le JSON de réponse de requête a évolué pour permettre un accès programmatique élargi aux données les plus souvent utilisées. 

### <a name="top-level-json-changes"></a>Évolutions JSON de niveau supérieur

Quand `verbose` est défini sur true, ce qui retourne toutes les intentions et leurs scores dans la propriété `intents`, les principales propriétés JSON pour V2 sont les suivantes :

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Les principales propriétés JSON pour V3 sont :

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

L’objet `intents` est une liste non triée. Ne partez pas du principe que le premier enfant de `intents` correspond à `topIntent`. Utilisez à la place la valeur de `topIntent` pour rechercher le score :

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Voici ce qu’autorisent les évolutions du schéma JSON de réponse :

* Distinction claire entre l’énoncé d’origine, `query`, et la prédiction retournée, `prediction`.
* Accès programmatique aux données prédites facilité. Au lieu de procéder à une énumération dans un tableau dans V2, vous pouvez accéder aux valeurs par **nom** aussi bien pour les intentions que pour les entités. Pour les rôles d’entité prédite, le nom de rôle est retourné, car il est unique dans toute l’application.
* Les types de données, s’ils sont déterminés, sont respectés. Les valeurs numériques ne sont plus retournées sous forme de chaînes.
* Distinction entre les informations de prédiction de première priorité et les métadonnées supplémentaires, retournées dans l’objet `$instance`. 

### <a name="access-instance-for-entity-metadata"></a>Accès à `$instance` pour les métadonnées d’entité

Si vous avez besoin de métadonnées d’entité, la chaîne de requête doit utiliser l’indicateur `verbose=true` et la réponse contient les métadonnées de l’objet `$instance`. Des exemples sont présentés dans les réponses JSON des sections suivantes.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Chaque entité prédite est représentée sous forme de tableau

L’objet `prediction.entities.<entity-name>` contient un tableau, car chaque entité peut être prédite plusieurs fois dans l’énoncé. 

### <a name="list-entity-prediction-changes"></a>Évolutions des prédictions d’entité de liste

Le JSON d’une prédiction d’entité de liste a évolué pour devenir un tableau de tableaux :

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Chaque tableau interne correspond au texte situé à l’intérieur de l’énoncé. L’objet interne est un tableau, car le même texte peut apparaître dans plusieurs sous-listes d’une entité de liste. 

Lors d’un mappage entre l’objet `entities` et l’objet `$instance`, l’ordre des objets est conservé pour les prédictions d’entité de liste.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Le nom de rôle d’entité à la place du nom d’entité 

Dans V2, le tableau `entities` retournait toutes les entités prédites avec le nom d’entité en guise d’identificateur unique. Dans V3, si l’entité utilise des rôles et que la prédiction porte sur un rôle d’entité, l’identificateur principal est le nom de rôle. Cela est possible, car les noms de rôle d’entité doivent être uniques dans toute l’application, y compris les autres noms de modèle (intention, entité).

L’exemple suivant contient un énoncé qui comporte le texte `Yellow Bird Lane`. Ce texte est prédit en tant que rôle `Destination` d’une entité `Location` personnalisée.

|Texte d’énoncé|Nom de l’entité|Nom de rôle|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Dans V2, l’entité est identifiée par le _nom d’entité_ et le rôle fait office de propriété de l’objet :

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Dans V3, l’entité est référencée par le _rôle d’entité_, si la prédiction porte sur le rôle :

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Dans V3, le même résultat est obtenu avec l’indicateur `verbose` pour retourner les métadonnées d’entité :

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Entités externes transmises au moment de la prédiction

Les entités externes permettent à votre application LUIS d’identifier et d’étiqueter les entités pendant l’exécution, qui peuvent servir de fonctionnalités aux entités existantes. Cela vous permet d’utiliser vos propres extracteurs d’entités distincts et personnalisés avant d’envoyer des requêtes à votre point de terminaison de prédiction. Cette opération étant exécutée sur le point de terminaison de prédiction de requête, vous n’avez pas besoin de réentraîner votre modèle et de le publier.

L’application cliente fournit son propre extracteur d’entités en gérant la mise en correspondance des entités et en déterminant l’emplacement au sein de l’énoncé des entités mises en correspondance, puis en envoyant ces informations avec la demande. 

Les entités externes constituent le mécanisme qui permet d’étendre n’importe quel type d’entité tout en étant toujours utilisées comme signaux pour d’autres modèles comme les rôles, les composites et autres.

Cela s’avère utile pour une entité qui n’a accès à des données qu’au moment de l’exécution de la prédiction de requête. Les données qui évoluent constamment ou qui sont propres à chaque utilisateur sont des exemples de ce type de données. Vous pouvez étendre une entité de contact LUIS avec les informations externes d’une liste de contacts d’utilisateur. 

### <a name="entity-already-exists-in-app"></a>L’entité existe déjà dans l’application

La valeur de `entityName` pour l’entité externe, qui est transmise dans le corps POST de demande du point de terminaison, doit déjà exister dans l’application entraînée et publiée au moment où la demande est effectuée. Tous les types étant pris en charge, le type d’entité n’a pas d’importance.

### <a name="first-turn-in-conversation"></a>Premier tour de la conversation

Imaginez un premier énoncé de conversation de bot conversationnel où un utilisateur entre les informations incomplètes suivantes :

`Send Hazem a new message`

La demande du bot conversationnel à LUIS peut transmettre des informations dans le corps POST à propos de `Hazem` de façon à les faire correspondre directement à l’un des contacts de l’utilisateur.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La réponse de prédiction comprend cette entité externe, avec toutes les autres entités prédites, car elle est définie dans la demande.  

### <a name="second-turn-in-conversation"></a>Deuxième tour de la conversation

L’énoncé utilisateur suivant dans le bot conversationnel utilise un terme plus vague :

`Send him a calendar reminder for the party.`

Dans l’énoncé précédent, `him` sert de référence à `Hazem`. Dans le corps POST, le bot conversationnel peut mapper `him` à la valeur d’entité extraite du premier énoncé, `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La réponse de prédiction comprend cette entité externe, avec toutes les autres entités prédites, car elle est définie dans la demande.  

### <a name="override-existing-model-predictions"></a>Remplacer les prédictions de modèle existantes

La propriété des options `overridePredictions` indique que si l’utilisateur envoie une entité externe qui se chevauche avec une entité prédite de même nom, LUIS choisit l’entité transmise ou l’entité existant dans le modèle. 

Prenons l’exemple de la requête `today I'm free`. LUIS détecte `today` comme datetimeV2 avec la réponse suivante :

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Si l’utilisateur envoie l’entité externe :

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Si `overridePredictions` a la valeur `false`, LUIS retourne une réponse comme si l’entité externe n’était pas envoyée. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Si `overridePredictions` a la valeur `true`, LUIS retourne une réponse comprenant ce qui suit :

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Résolution :

La propriété _facultative_ `resolution` est retournée dans la réponse de prédiction, ce qui vous permet de transmettre les métadonnées associées à l’entité externe, puis de la recevoir en retour dans la réponse. 

Le principal objectif est d’étendre les entités prédéfinies, mais il ne se limite pas à ce type d’entité. 

La propriété `resolution` peut être un nombre, une chaîne, un objet ou un tableau :

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listes dynamiques transmises au moment de la prédiction

Les listes dynamiques vous permettent d’étendre une entité de liste entraînée et publiée existante, déjà présente dans l’application LUIS. 

Cette fonctionnalité est utile quand vos valeurs d’entité de liste doivent être modifiées périodiquement. Elle vous permet d’étendre une entité de liste déjà entraînée et publiée :

* Au moment de la demande du point de terminaison de prédiction de requête.
* Pour une demande unique.

L’entité de liste peut être vide dans l’application LUIS, mais elle doit exister. L’entité de liste n’est pas modifiée dans l’application LUIS, mais la capacité de prédiction au niveau du point de terminaison est étendue pour inclure jusqu’à 2 listes de plus de 1 000 éléments.

### <a name="dynamic-list-json-request-body"></a>Corps de demande JSON de liste dynamique

Envoyez le corps JSON suivant pour ajouter une nouvelle sous-liste constituée de synonymes à la liste et prédire l’entité de liste pour le texte, `LUIS`, avec le demande de prédiction de requête `POST` :

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

La réponse de prédiction comprend cette entité de liste, comprenant toutes les autres entités prédites, car elle est définie dans la demande. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset renommé datetimeReference

**Dans V2**, le [paramètre](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) `timezoneOffset` est envoyé dans la demande de prédiction comme paramètre de chaîne de requête, que la demande soit envoyée en tant que demande GET ou POST. 

**Dans V3**, la même fonctionnalité est fournie avec le paramètre de corps POST, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Marquage de l’emplacement des entités dans les énoncés

**Dans V2**, une entité était marquée dans un énoncé avec `startIndex` et `endIndex`. 

**Dans V3**, l’entité est marquée avec `startIndex` et `entityLength`.

## <a name="deprecation"></a>Dépréciation 

L’API V2 ne sera pas déconseillée pendant au moins 9 mois après la préversion V3. 

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de l’API V3 pour mettre à jour les appels REST existants vers les API de [point de terminaison](https://aka.ms/luis-api-v3) LUIS. 
