---
title: Indexeurs pour l’analyse des sources de données lors de l’indexation - Recherche Azure
description: Analysez une base de données Azure SQL, Cosmos DB ou le stockage Azure pour extraire les données pouvant faire l’objet d’une recherche et remplir un index Recherche Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e35573eea836fc8a88c7515409c070ec63aa3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024890"
---
# <a name="indexers-in-azure-search"></a>Indexeurs dans Azure Search

Dans Azure Search, un *indexeur* est un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche d’une source de données Azure externe et renseigne un index en fonction des mappages champ à champ entre l’index et votre source de données. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui ajoute des données à un index.

Les indexeurs sont basés sur des types de sources de données ou des plateformes, avec des indexeurs individuels pour SQL Server sur Azure, Cosmos DB, Stockage Table Azure et Stockage Blob. Les indexeurs de stockage d’objets blob ont des propriétés supplémentaires spécifiques aux types de contenu d’objet blob.

Vous pouvez utiliser un indexeur comme seul moyen d’ingestion des données ou utiliser une combinaison de techniques qui incluent l’utilisation d’un indexeur pour charger uniquement certains champs dans l’index.

Vous pouvez exécuter des indexeurs à la demande ou en fonction d’une planification d’actualisation des données périodique qui s’exécute jusqu’à une fois toutes les quinze minutes. Des mises à jour plus fréquentes requièrent un modèle d’émission qui met à jour simultanément les données dans Azure Search et dans votre source de données externe.

## <a name="approaches-for-creating-and-managing-indexers"></a>Méthodes de création et de gestion des indexeurs

Vous pouvez créer et gérer des indexeurs en suivant l’une de ces approches :

* [Portail > Assistant Importer des données](search-import-data-portal.md)
* [API REST du service](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Au départ, un nouvel indexeur est annoncé comme une fonctionnalité d’aperçu. Les fonctionnalités d’aperçu sont introduites dans les API (REST et .NET) et sont ensuite intégrées dans le portail après la promotion vers la disponibilité générale. Lors de l’évaluation d’un indexeur, vous devez envisager d’écrire du code.

## <a name="permissions"></a>Autorisations

Toutes les opérations liées aux indexeurs, notamment les requêtes GET d’état ou de définitions, nécessitent une [admin api-key](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Les indexeurs analysent les magasins de données sur Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Stockage de tables Azure](search-howto-indexing-azure-tables.md) 

> [!Note]
> Stockage Table Azure n’est pas pris en charge pour la [recherche cognitive](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Étapes de configuration de base
Les indexeurs peuvent offrir des fonctionnalités propres à la source de données. À cet égard, certains aspects de la configuration de l’indexeur ou de la source de données varient en fonction du type d’indexeur. Cependant, tous les indexeurs présentent une composition et des exigences de base identiques. Les étapes communes à tous les indexeurs sont décrites ci-dessous.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données
Un indexeur obtient une connexion de source de données à partir d’un objet *source de données*. La définition de source de données fournit une chaîne de connexion et éventuellement des informations d’identification. Appelez l’API REST de [création de source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) ou la [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) pour créer la ressource.

Les sources de données sont configurées et gérées indépendamment des indexeurs qui les utilisent. Autrement dit, une source de données peut être utilisée par plusieurs indexeurs pour charger plusieurs index à la fois.

### <a name="step-2-create-an-index"></a>Étape 2 : Création d'un index
Un indexeur automatise certaines tâches liées à l’ingestion des données, mais la création d’un index n’en fait généralement pas partie. Au préalable, vous devez disposer d’un index prédéfini présentant des champs qui correspondent à ceux de votre source de données externe. Les champs doivent correspondre par nom et type de données. Pour plus d’informations sur la structuration d’un index, consultez l’article [Create an Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index)(Création d’un index (API REST Azure Search)) ou [Index class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index) (Classe Index). Pour plus d’informations sur les associations de champ, consultez [Mappages de champs dans les indexeurs de Recherche Azure](search-indexer-field-mappings.md).

> [!Tip]
> Bien que les indexeurs ne puissent pas générer d’index pour vous, l’Assistant **Importation des données** du portail peut vous aider. Dans la plupart des cas, l’Assistant peut déduire un schéma d’index à partir des métadonnées existantes dans la source, en présentant un schéma d’index préliminaire que vous pouvez modifier en ligne pendant que l’Assistant est actif. Une fois que l’index est créé sur le service, les modifications supplémentaires dans le portail sont principalement limitées à l’ajout de nouveaux champs. Pensez à utiliser l’Assistant pour créer un index (mais pas pour le réviser). Pour mettre vos connaissances en pratique, parcourez la [procédure pas à pas dans le portail](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Étape 3 : Créer et planifier l’indexeur
La définition de l’indexeur est une construction qui rassemble tous les éléments liés à l’ingestion des données. Les éléments obligatoires incluent une source de données et un index. Les éléments facultatifs incluent une planification et des mappages de champs. Le mappage de champs n’est facultatif que si les champs source et les champs d’index correspondent parfaitement. Un indexeur peut faire référence à une source de données d’un autre service, à condition que celle-ci appartienne au même abonnement. Pour plus d’informations sur la structuration d’un indexeur, consultez l’article [Create Indexer (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)(Création d’un indexeur (API REST Azure Search)).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Exécuter des indexeurs à la demande

Bien qu’il soit courant de planifier l’indexation, un indexeur peut également être appelé à la demande à l’aide de la [commande Exécuter](https://docs.microsoft.com/rest/api/searchservice/run-indexer) :

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Lors de l’API s’exécute avec succès, l’appel de l’indexeur a été planifié, mais le traitement réel se produit de façon asynchrone. 

Vous pouvez surveiller l’état de l’indexeur dans le portail ou à l’aide de l’API Get Indexer Status. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Get indexer status

Vous pouvez récupérer l’état et l’historique d’exécution d’un indexeur à l’aide de la [commande Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) :


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

L'historique d'exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la dernière exécution est répertoriée en premier dans la réponse).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez la structure de base, l’étape suivante consiste à passer en revue les exigences et les tâches propres à chaque type de source de données.

* [Azure SQL Database ou SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
* [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
* [Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure](search-howto-index-json-blobs.md)
