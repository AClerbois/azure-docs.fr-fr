---
title: Ajouter la recherche en texte intégral à Stockage Blob Azure
titleSuffix: Azure Search
description: Extrayez du contenu et ajoutez une structure aux objets blob Azure au moment de créer un index de recherche en texte intégral dans Recherche Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 62ad2d32af10f72974b28bb582eeb5084a5c5ca0
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515835"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Ajouter la recherche en texte intégral à des données blob Azure en utilisant Recherche Azure

La recherche dans les différents types de contenu enregistrés dans le Stockage Blob Azure peut constituer un problème difficile à résoudre. Cependant, vous pouvez indexer le contenu de vos objets blob et y effectuer une recherche en quelques clics à l’aide de [Recherche Azure](search-what-is-azure-search.md). Recherche Azure propose une intégration prédéfinie qui permet d’effectuer une indexation à partir de Stockage Blob via un [*indexeur Blob*](search-howto-indexing-azure-blob-storage.md) qui ajoute à l’indexation des capacités de reconnaissance des sources de données.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Implications de l’ajout de la recherche en texte intégral aux données blob

Recherche Azure est un service de recherche cloud qui propose des moteurs d’indexation et de requête qui opèrent sur les index définis par l’utilisateur hébergés dans votre service de recherche. Colocaliser votre contenu recherchable avec le moteur de requête dans le cloud est une nécessité pour offrir aux utilisateurs les performances qu’ils attendent eu égard au délai d’affichage des résultats des requêtes de recherche.

Recherche Azure s’intègre avec Stockage Blog Azure au niveau de la couche d’indexation. Le contenu de vos objets blob est ainsi importé sous forme de documents de recherche indexés dans des *index inversés* et d’autres structures de requête qui prennent en charge les requêtes de texte en forme libre et les expressions de filtre. Sachant que le contenu de vos objets blob est indexé dans un index de recherche, il est possible de tirer parti de l’ensemble des fonctionnalités de requête de Recherche Azure pour accéder à ce contenu.

Une fois créé et rempli, l’index existe indépendamment de votre conteneur d’objets blob. Vous pouvez néanmoins réexécuter les opérations d’indexation de façon à actualiser l’index avec les modifications apportées au conteneur sous-jacent. Les informations d’horodatage des différents objets blob servent à détecter les modifications. Vous pouvez opter pour une exécution planifiée ou une indexation à la demande en guise de mécanisme d’actualisation.

Les entrées sont vos objets blob, dans un même conteneur, dans Stockage Blob Azure. Les objets blob peuvent correspondre à pratiquement tout type de données texte. Si vos objets blob contiennent des images, vous pouvez ajouter l’[enrichissement par IA à l’indexation d’objets blob](search-blob-ai-integration.md) pour créer et extraire du texte des images.

La sortie est toujours un index Recherche Azure, utilisé pour la recherche, l’extraction et l’exploration rapides de texte dans les applications clientes. Au milieu se trouve l’architecture proprement dite du pipeline d’indexation. Le pipeline est basé sur la fonctionnalité d’*indexeur*, décrite plus loin dans cet article.

## <a name="start-with-services-and-data"></a>Commencer avec les services et les données

Vous avez besoin de Recherche Azure et de Stockage Blob Azure. Dans Stockage Blob, vous avez besoin d’un conteneur qui fournit le contenu source.

Vous pouvez commencer directement dans votre page du portail des comptes Stockage. Dans la page de navigation de gauche, sous **Service Blob**, cliquez sur **Ajouter Recherche Azure** pour créer un nouveau service ou en sélectionner un existant. 

Une fois que vous avez ajouté Recherche Azure à votre compte de stockage, vous pouvez suivre le processus standard d’indexation de données blob. Nous vous recommandons d’utiliser l’Assistant **Importation des données** de Recherche Azure pour une mise en route simple ou d’appeler les API REST à l’aide d’un outil comme Postman. Ce tutoriel vous explique pas à pas comment appeler l’API REST dans Postman : [Indexer et rechercher des données semi-structurées (objets blob JSON) dans Recherche Azure](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Utiliser un indexeur d’objets blob

Un *indexeur* est un sous-service qui reconnaît les sources de données. Avec sa logique interne, il échantillonne les données, lit les données de métadonnées, extrait les données et les sérialise dans des documents JSON à partir de formats natifs pour être ensuite importées. 

Dans Stockage Azure, les objets blob sont indexés à l’aide de l’[indexeur de stockage d’objets blob de Recherche Azure](search-howto-indexing-azure-blob-storage.md). Vous pouvez appeler cet indexeur à partir de l’Assistant **Importation des données**, d’une API REST ou du kit SDK .NET. Dans le code, vous pouvez utiliser cet indexeur en définissant le type et en fournissant des informations de connexion qui incluent un compte Stockage Azure associé à un conteneur d’objets blob. Vous pouvez créer un sous-ensemble de vos objets blob en créant un répertoire virtuel, que vous pouvez ensuite transmettre comme paramètre, ou en filtrant sur une extension de type de fichier.

Un indexeur effectue le « craquage de document » en ouvrant un objet blob pour en inspecter le contenu. Une fois connecté à la source de données, il s’agit de la première étape du pipeline. Pour les données blob, c’est à ce stade que les fichiers PDF, les documents Office et d’autres types de contenu sont détectés. La craquage de document avec extraction de texte n’est pas facturé. Si vos objets blob contiennent des images, celles-ci sont ignorées si vous n’[ajoutez pas l’enrichissement par IA](search-blob-ai-integration.md). L’indexation standard s’applique uniquement au contenu texte.

L’indexeur d’objets blob est assorti de paramètres de configuration et prend en charge le suivi des modifications si les données sous-jacentes fournissent suffisamment d’informations. Vous trouverez des informations supplémentaires sur les fonctionnalités de base dans [Indexeur de stockage d’objets blob de Recherche Azure](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Types de contenu pris en charge

En exécutant un indexeur d’objets blob sur un conteneur, vous pouvez extraire du texte et des métadonnées à partir des types de contenu suivants avec une seule requête :

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexation de métadonnées blob

Pour faciliter le tri dans les objets blob constitués de tout type de contenu, un scénario courant consiste à indexer les métadonnées personnalisées et les propriétés système pour chaque objet blob. De cette façon, les informations de tous les objets blob sont indexées indépendamment du type de document et stockées dans un index de Recherche Azure. Le nouvel index vous permet alors d’effectuer un tri, un filtrage et une facette dans l’ensemble du contenu du stockage Blob.

### <a name="indexing-json-blobs"></a>Indexation d’objets JSON
Recherche Azure peut être configuré pour extraire le contenu structuré des objets blob qui contiennent des objets JSON. Recherche Azure peut lire les objets blob JSON et analyser le contenu structuré dans les champs adaptés du document Recherche Azure. Recherche Azure peut également extraire les objets blob contenant des objets JSON et mapper chaque élément avec un document Recherche Azure différent. Vous pouvez définir un mode d’analyse pour affecter le type d’objet JSON créé par l’indexeur.

## <a name="search-blob-content-in-a-search-index"></a>Rechercher du contenu d’objet blob dans un index de recherche 

La sortie d’une indexation est un index de recherche, qui permet une exploration interactive en utilisant des requêtes de texte libre et filtrées dans une application cliente. Pour une exploration et une vérification initiales de contenu, nous vous recommandons de commencer avec l’[Explorateur de recherche](search-explorer.md) sur le portail, qui vous permet d’examiner la structure du document. L’Explorateur de recherche vous permet d’utiliser une [syntaxe de requête simple](query-simple-syntax.md), une [syntaxe de requête complète](query-lucene-syntax.md) et une [syntaxe d’expression de filtre](query-odata-filter-orderby-syntax.md).

Une solution plus permanente consiste à regrouper les entrées de requête et à présenter la réponse sous forme de résultats de recherche dans une application cliente. Le tutoriel C# suivant explique comment créer une application de recherche : [Créer votre première application dans Recherche Azure](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un indexeur d’objets blob](search-howto-indexing-azure-blob-storage.md) 
