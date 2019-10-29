---
title: Présentation de la base de connaissances (préversion)
titleSuffix: Azure Cognitive Search
description: Envoyez des documents enrichis dans le stockage Azure afin de pouvoir consulter, remodeler et utiliser des documents enrichis dans la Recherche cognitive Azure et dans d’autres applications.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 82f8606f4b4201833667347d3ed16fdd73f70a36
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790365"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Présentation des bases de connaissances dans la Recherche cognitive Azure

> [!Note]
> La base de connaissances est en préversion et n’a pas été conçue pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>

La base de connaissances est une fonctionnalité de la Recherche cognitive Azure. Elle permet de conserver la sortie d’un [pipeline d’enrichissement de l’IA](cognitive-search-concept-intro.md) pour une analyse future ou tout autre traitement en aval. Un *document enrichi* est la sortie d’un pipeline, créée à partir d’un contenu qui a été extrait, structuré et analysé à l’aide de processus IA. Dans un pipeline IA standard, les documents enrichis sont temporaires, utilisés uniquement pendant l’indexation, puis ignorés. Avec la base de connaissances, les documents enrichis sont conservés. 

Si vous avez utilisé des compétences cognitives avec la Recherche cognitive Azure par le passé, vous savez déjà que des *ensembles de compétences* permettent de déplacer un document dans le cadre d’une séquence d’enrichissements. Le résultat peut être un index de recherche ou (nouveauté de cette préversion) des projections d’une base de connaissances. Les deux sorties, l’index de recherche et la base de connaissances, partagent le même contenu, mais sont stockées et utilisées de manières très différentes.

Physiquement, une base de connaissances représente un [stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), soit le stockage Table Azure, soit le stockage Blob Azure, ou les deux. Tout outil ou processus pouvant se connecter au Stockage Azure peut utiliser le contenu d’un magasin de connaissances.

![Diagramme de base de connaissances au sein d’un pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Diagramme de base de connaissances au sein d’un pipeline")

Les projections vous permettent de structurer des données dans une base de connaissances. Par exemple, des projections vous permettent de choisir si la sortie est enregistrée en tant qu’objet blob unique ou collection de tables associées. 

Pour utiliser la base de connaissances, ajoutez un élément `knowledgeStore` à un ensemble de compétences qui définit des opérations par étapes dans un pipeline d'indexation. Pendant l’exécution, la Recherche cognitive Azure crée un espace dans votre compte de stockage Azure et projette les documents enrichis en tant qu’objets blob ou dans des tables, en fonction de votre configuration.

## <a name="benefits-of-knowledge-store"></a>Avantages de la base de connaissances

Une base de connaissances vous fournit une structure, un contexte et un contenu réel, glanés à partir de fichiers de données non structurés et semi-structurés, tels que des objets blob, des fichiers image ayant fait l'objet d'une analyse, ou même des données structurées transformées en nouveaux formulaires. Une [procédure pas à pas](knowledge-store-howto.md) conçue pour cette préversion montre comment un document JSON dense est partitionné en sous-structures, reconstitué en nouvelles structures et mis à disposition pour des processus situés en aval, comme des charges de travail liées au Machine Learning et à la science des données.

Bien qu’il soit utile de voir ce qu’un pipeline d’enrichissement de l’IA peut produire, le vrai pouvoir de la base de connaissances réside dans sa capacité à remodeler les données. Vous pouvez commencer avec un ensemble de compétences de base, puis procéder à une itération sur celui-ci afin d’ajouter des niveaux de structure croissants, que vous pouvez ensuite combiner en nouvelles structures, utilisables dans des applications autres que la Recherche cognitive Azure.

La base de connaissances vous offre notamment les avantages suivants :

+ Utilisez des documents enrichis dans des [outils d'analyse et de génération d'états](#tools-and-apps) autres que des outils de recherche. Power BI avec Power Query est un excellent choix, mais n'importe quel outil ou application capable de se connecter à Stockage Azure peut puiser dans une base de connaissances que vous avez créée.

+ Affinez un pipeline d'indexation IA lors des étapes de débogage et de la définition des ensembles de compétences. Une base de connaissances vous présente le produit de la définition d'un ensemble de compétences dans un pipeline d'indexation IA. Vous pouvez utiliser ces résultats afin de concevoir un ensemble de compétences plus performant car vous voyez exactement à quoi ressemblent les enrichissements. Vous pouvez utiliser l'[Explorateur Stockage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) de Stockage Azure pour afficher le contenu d'une base de connaissances.

+ Modelez les données pour les transformer en nouveaux formulaires. Le remodelage est codifié en ensembles de compétences, mais le fait est qu'un ensemble de compétences peut désormais fournir cette capacité. La [compétence Modélisateur](cognitive-search-skill-shaper.md) de la Recherche cognitive Azure a été étendue pour s’adapter à cette tâche. Le remodelage vous permet de définir une projection alignée sur l'utilisation que vous prévoyez de faire des données tout en préservant les relations.

> [!Note]
> L’enrichissement de l’IA à l’aide de Cognitive Services n’est pas un domaine familier pour vous ? La Recherche cognitive Azure s’intègre aux fonctionnalités de vision et de langage de Cognitive Services pour extraire et enrichir les données sources à l’aide de la reconnaissance optique de caractères sur des fichiers image, de la reconnaissance d’entités et de l’extraction d’expressions clés à partir de fichiers texte et autres. Pour plus d’informations, consultez [Enrichissement de l’IA dans la Recherche cognitive Azure](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Création d’une base de connaissances

Une base de connaissances fait partie d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) qui, à son tour, fait partie d’un [indexeur](search-indexer-overview.md). 

Dans cette préversion, vous pouvez créer une base de connaissances avec l’API REST et `api-version=2019-05-06-Preview`, ou via l’Assistant **Importation de données** du portail.

### <a name="json-representation-of-a-knowledge-store"></a>Représentation JSON d’une base de connaissances

Le document JSON suivant spécifie une `knowledgeStore`, qui fait partie d'un ensemble de compétences appelé par un indexeur (non affiché). Si vous connaissez déjà l’enrichissement de l’IA, un ensemble de compétences détermine la création, l’organisation et la substance de chaque document enrichi. Un ensemble de compétences doit contenir au moins une compétence, très probablement une compétence Modélisateur si vous modulez des structures de données.

Une `knowledgeStore` se compose d’une connexion et de projections. 

+ La connexion cible un compte de stockage dans la même région que Recherche Azure. 

+ Les projections sont des paires tables-objets. Les `Tables` définissent l’expression physique des documents enrichis dans le stockage Table Azure. Les `Objects` définissent les objets physiques dans le stockage Blob Azure.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>Sources de données d’une base de connaissances

Si une base de connaissances est la sortie d’un pipeline d’enrichissement de l’IA, quelles sont les entrées ? Les données d’origine que vous souhaitez extraire, enrichir et finalement enregistrer dans une base de connaissances peuvent provenir de n’importe quelle source de données Azure prise en charge par les indexeurs de recherche : 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [stockage d’objets blob Azure](search-howto-indexing-azure-blob-storage.md)

* [Stockage Table Azure](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Les indexeurs et les ensembles de compétences que vous créez permettent d’extraire et d’enrichir ou de transformer ce contenu dans le cadre d’une charge de travail d’indexation, puis d’enregistrer les résultats dans une base de connaissances.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>API REST utilisées dans la création d’une base de connaissances

Seules deux API ont les extensions requises pour la création d’une base de connaissances (Créer un ensemble de compétences et Créer un indexeur). D’autres API sont utilisées en l’état.

| Object | API REST | Description |
|--------|----------|-------------|
| source de données | [Créer une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Ressource identifiant une source de données Azure externe qui fournit les données sources utilisées pour créer des documents enrichis.  |
| ensemble de compétences | [Créer un ensemble de compétences (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ressource coordonnant l'utilisation de [compétences intégrées](cognitive-search-predefined-skills.md) et de [compétences cognitives personnalisées](cognitive-search-custom-skill-interface.md) utilisées dans un pipeline d'enrichissement lors de l'indexation. Un ensemble de compétences a une définition de `knowledgeStore` en tant qu’élément enfant. |
| index | [Création d'index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma exprimant un index de recherche. Des champs dans l’index sont mappés à des champs dans les données sources ou à des champs créés lors de la phase d’enrichissement (par exemple, un champ pour les noms de l’organisation créé par la reconnaissance d’entité). |
| indexeur | [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Une ressource définissant les composants utilisés lors de l’indexation : notamment une source de données, un ensemble de compétences, des associations de champs à partir de structures de données sources et intermédiaires pour cibler l’index, et l’index lui-même. L’exécution de l’index est le déclencheur de l’ingestion des données et de l’enrichissement. La sortie est un index de recherche basé sur le schéma d'index, alimenté avec des données sources et enrichi par des ensembles de compétences.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Composition physique d’une base de connaissances

 Une *projection*, qui est un élément d’une définition de `knowledgeStore`, articule le schéma et la structure de la sortie afin qu’elle corresponde à l’usage prévu. Vous pouvez définir plusieurs projections si vous disposez d’applications qui consomment les données dans différents formats et sous différents formes. 

Les projections peuvent être articulées sous forme d’objets ou de tables :

+ En tant qu'objet, la projection est mappée avec Stockage Blob, où la projection est enregistrée dans un conteneur, dans lequel se trouvent les objets ou les représentations hiérarchiques au format JSON pour les scénarios tels qu'un pipeline de science des données.

+ En tant que table, la projection est mappée avec Stockage Table. Une représentation tabulaire préserve les relations dans les scénarios tels que l'analyse de données ou l'exportation sous forme de dataframes pour le Machine Learning. Les projections enrichies peuvent ensuite être facilement importées dans d'autres magasins de données. 

Vous pouvez créer différentes projections dans une base de connaissances afin de répondre aux besoins de divers groupes de votre organisation. Un développeur peut avoir besoin d'accéder à l'intégralité de la représentation JSON d'un document enrichi, tandis que les scientifiques ou les analystes des données peuvent préférer les structures de données granulaires ou modulaires modelées par votre ensemble de compétences.

Par exemple, si l'un des objectifs du processus d'enrichissement est également de créer un jeu de données pour former un modèle, la projection des données dans le magasin d'objets peut permettre d'utiliser les données de vos pipelines de science des données. Sinon, si vous souhaitez créer un tableau de bord Power BI rapide basé sur les documents enrichis, la projection tabulaire peut être efficace.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Connexion à l’aide d’outils et d’applications

Une fois les enrichissements disponibles dans le stockage, n'importe quel outil ou technologie capable de se connecter à Stockage Blob ou Table Azure peut être utilisé pour explorer, analyser ou utiliser le contenu. La liste suivante est un début :

+ L'[Explorateur Stockage](knowledge-store-view-storage-explorer.md) permet d'afficher la structure et le contenu des documents enrichis. Considérez-le comme votre outil de référence pour afficher le contenu de la base de connaissances.

+ [Power BI](knowledge-store-connect-power-bi.md) pour les outils de création de rapports et d’analyse si vous avez des données numériques.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) permet d'effectuer d'autres manipulations.


<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Cognitive Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Étapes suivantes

La base de connaissances offre la persistance de documents enrichis, utile lors de la conception d’un ensemble de compétences, ou la création de structures et de contenu pour une consommation par les applications clientes qui peuvent accéder à un compte de stockage Azure.

L’approche la plus simple pour créer des documents enrichis consiste à utiliser l’Assistant **Importation de données**, mais vous pouvez également utiliser Postman et l’API REST, ce qui est plus utile si vous souhaitez des insights sur la façon dont les objets sont créés et référencés.

> [!div class="nextstepaction"]
> [Créer une base de connaissances à l’aide du portail](knowledge-store-create-portal.md)
> [Créer une base de connaissances à l’aide de Postman et de l’API REST](knowledge-store-create-rest.md)
