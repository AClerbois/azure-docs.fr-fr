---
title: Liaisons Azure Cosmos DB pour Azure Functions 1.x
description: Découvrez comment utiliser des déclencheurs et liaisons Azure Cosmos DB dans Azure Functions.
services: functions
author: craigshoemaker
ms.author: cshoe
manager: gwallace
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 0e6782c48543723438ee332313de268117dee3e9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480718"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Liaisons Azure Cosmos DB pour Azure Functions 1.x

> [!div class="op_single_selector" title1="Sélectionnez la version du runtime Azure Functions que vous utilisez : "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Cet article explique comment utiliser des liaisons [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Cosmos DB.

> [!NOTE]
> Cet article concerne Azure Functions 1.x. Pour plus d’informations sur l’utilisation de ces liaisons dans Functions 2.x, consultez [Liaisons Azure Cosmos DB pour Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Cette liaison était nommée à l’origine DocumentDB. Dans Functions version 1.x, seul le déclencheur a été renommé Cosmos DB. La liaison d’entrée, la liaison de sortie et le package NuGet conservent le nom DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Les liaisons Azure Cosmos DB sont uniquement utilisables avec l’API SQL. Pour toutes les autres API Azure Cosmos DB, vous devez accéder à la base de données à partir de votre fonction en utilisant le client statique de votre API, y compris pour l'[API Azure Cosmos DB pour MongoDB](../cosmos-db/mongodb-introduction.md), l'[API Cassandra](../cosmos-db/cassandra-introduction.md), l'[API Gremlin](../cosmos-db/graph-introduction.md), et l'[API Table](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons Azure Cosmos DB pour Functions version 1.x sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB), version 1.x. Le code source des liaisons se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur Azure Cosmos DB utilise le [flux de modification Azure Cosmos DB](../cosmos-db/change-feed.md) pour écouter les insertions et mises à jour sur plusieurs partitions. Le flux de modification publie les insertions et mises à jour, pas les suppressions.

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Ignorer les exemples de déclencheur](#trigger---attributes)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui est invoquée lorsqu’il y a des insertions ou mises à jour dans la base de données et la collection spécifiées.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Ignorer les exemples de déclencheur](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code Script C# :

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

[Ignorer les exemples de déclencheur](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code JavaScript :

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Déclencheur - configuration](#trigger---configuration). Voici un exemple d’attribut `CosmosDBTrigger` dans une signature de méthode :

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDBTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** || Cette propriété doit être définie sur `cosmosDBTrigger`. |
|**direction** || Cette propriété doit être définie sur `in`. Ce paramètre est défini automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** || Nom de variable utilisé dans le code de fonction, qui représente la liste des documents modifiés. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nom d’un paramètre d’application contenant la chaîne de connexion utilisée pour se connecter au compte Azure Cosmos DB surveillé. |
|**databaseName**|**DatabaseName**  | Nom de la base de données Azure Cosmos DB contenant la collection surveillée. |
|**collectionName** |**CollectionName** | Nom de la collection surveillée. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Facultatif) Nom d’un paramètre d’application contenant la chaîne de connexion au service stockant la collection de baux. S’il n’est pas défini, la valeur `connectionStringSetting` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. La chaîne de connexion de la collection de baux doit avoir des autorisations en écriture.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Facultatif) Nom de la base de données contenant la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur du paramètre `databaseName` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
|**leaseCollectionName** | **LeaseCollectionName** | (Facultatif) Nom de la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur `leases` est utilisée. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Facultatif) Lorsque la valeur est définie sur `true`, la collection de baux est créée automatiquement si elle n’existe pas. La valeur par défaut est `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Facultatif) Définit la quantité d’unités de requête à attribuer lors de la création de la collection de baux. Ce paramètre est utilisé uniquement quand `createLeaseCollectionIfNotExists` est défini sur `true`. Ce paramètre est automatiquement défini lors de la création de la liaison à l’aide du portail.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Facultatif) Quand ce paramètre est défini, il ajoute un préfixe aux baux créés dans la collection Lease pour cette fonction. Cela permet à deux fonctions Azure de partager de façon efficace la même collection Lease en utilisant des préfixes différents.
|**feedPollDelay**| **FeedPollDelay**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, le délai entre le moment où toutes les modifications sont purgées du flux et le moment où une partition est interrogée afin d’identifier les nouvelles modifications. La valeur par défaut est 5 000 (5 secondes).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle pour déclencher une tâche afin de calculer si les partitions sont réparties uniformément parmi les instances d’hôte connues. La valeur par défaut est 13 000 (13 secondes).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle selon lequel le bail est pris sur un bail représentant une partition. Si le bail n’est pas renouvelé dans cet intervalle, il expire et une autre instance devient propriétaire de la partition. La valeur par défaut est 60 000 (60 secondes).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle de renouvellement de tous les baux pour les partitions actuellement détenues par une instance. La valeur par défaut est 17 000 (17 secondes).
|**checkpointFrequency**| **CheckpointFrequency**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle entre les points de contrôle du bail. La valeur par défaut est toujours après chaque appel de fonction.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Facultatif) Quand ce paramètre est défini, il personnalise la quantité maximum d’éléments reçus par appel de fonction.
|**startFromBeginning**| **StartFromBeginning**| (Facultatif) Quand cette propriété est définie, elle indique au déclencheur de démarrer la lecture des modifications à partir du début de l’historique de la collection au lieu de l’heure actuelle. Cela fonctionne uniquement au premier démarrage du déclencheur, car les points de contrôle sont déjà stockés lors des exécutions suivantes. L’affectation de la valeur `true` à cette propriété quand des baux ont déjà été créés n’a aucun effet.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

Le déclencheur nécessite une deuxième collection qu’il utilise pour stocker des _baux_ sur les partitions. La collection surveillée et la collection contenant les baux doivent être disponibles pour que le déclencheur fonctionne.

>[!IMPORTANT]
> Si plusieurs fonctions sont configurées pour utiliser un déclencheur Cosmos DB pour la même collection, chacune de ces fonctions doit utiliser une collection de bail dédiée ou spécifier un `LeaseCollectionPrefix` différent pour chaque fonction. Sinon, une seule des fonctions est déclenchée. Pour plus d’informations sur le préfixe, consultez la [section Configuration](#trigger---configuration).

Le déclencheur n’indique pas si un document a été mis à jour ou inséré, il fournit simplement le document lui-même. Si vous avez besoin de gérer les mises à jour et insertions différemment, vous pouvez le faire en implémentant des champs d’horodatage pour l’insertion ou la mise à jour.

## <a name="input"></a>Entrée

La liaison d’entrée Azure Cosmos DB utilise l’API SQL pour récupérer un ou plusieurs documents Azure Cosmos DB et les transmet au paramètre d’entrée de la fonction. L’ID du document ou les paramètres de requête peuvent être déterminés en fonction du déclencheur qui appelle la fonction.

## <a name="input---examples"></a>Entrée - Exemples

Consultez les exemples spécifiques à une langue qui lisent un document unique en spécifiant une valeur d’ID :

* [C#](#input---c-examples)
* [Script C# (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Ignorer les exemples d’entrée](#input---attributes)

### <a name="input---c-examples"></a>Entrée - Exemples C#

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, rechercher l’ID à partir de JSON](#queue-trigger-look-up-id-from-json-c)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-c)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-c)
* [Déclencheur HTTP, ID de recherche à partir des données de routage, utilisation de SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Les exemples font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Déclencheur de file d’attente, rechercher l’ID à partir de JSON (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. La fonction est déclenchée par un message de file d’attente qui contient un objet JSON. Le déclencheur de file d’attente analyse le JSON dans un objet nommé `ToDoItemLookup`, qui contient l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Déclencheur HTTP, rechercher l’ID à partir des données de routage (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. Cette fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Déclencheur HTTP, rechercher l’ID à partir de données de routage, utilisation de SqlQuery (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. Cette fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. La requête est spécifiée dans la propriété d’attribut `SqlQuery`.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient (C#)

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. Le code utilise une instance `DocumentClient` fournie par la liaisonAzure Cosmos DB pour lire une liste de documents. L’instance `DocumentClient` peut également être utilisée pour les opérations d’écriture.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

### <a name="input---c-script-examples"></a>Entrée - Exemples de script C#

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, rechercher l’ID à partir de la chaîne](#queue-trigger-look-up-id-from-string-c-script)
* [Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-c-script)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-c-script)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Les exemples de déclencheur HTTP font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Déclencheur de file d’attente, rechercher l’ID à partir de la chaîne (script C#)

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery (script C#)

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête (script C#)

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Voici le code Script C# :

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Déclencheur HTTP, rechercher l’ID à partir des données de routage (script C#)

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère un document unique. Cette fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery (script C#)

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. La requête est spécifiée dans la propriété d’attribut `SqlQuery`.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient (script C#)

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. Le code utilise une instance `DocumentClient` fournie par la liaisonAzure Cosmos DB pour lire une liste de documents. L’instance `DocumentClient` peut également être utilisée pour les opérations d’écriture.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorer les exemples d’entrée](#input---attributes)

### <a name="input---javascript-examples"></a>Entrée - Exemples JavaScript

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, rechercher l’ID à partir de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-javascript)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-javascript)
* [Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Déclencheur de file d’attente, rechercher l’ID à partir de JSON (JavaScript)

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête (JavaScript)

L’exemple suivant illustre une [fonction JavaScript](functions-reference-node.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Ignorer les exemples d’entrée](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Déclencheur HTTP, rechercher l’ID à partir des données de routage (JavaScript)

L’exemple suivant illustre une [fonction JavaScript](functions-reference-node.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Ignorer les exemples d’entrée](#input---attributes)



#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery (JavaScript)

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

[Ignorer les exemples d’entrée](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Entrée - Exemples F#

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Cet exemple nécessite un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

## <a name="input---attributes"></a>Entrée - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [la section de configuration suivante](#input---configuration).

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `DocumentDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     || Cette propriété doit être définie sur `documentdb`.        |
|**direction**     || Cette propriété doit être définie sur `in`.         |
|**name**     || Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** |**DatabaseName** |Base de données contenant le document.        |
|**collectionName** |**CollectionName** | Nom de la collection qui contient le document. |
|**id**    | **Id** | ID du document à récupérer. Cette propriété prend en charge les [expressions de liaison](./functions-bindings-expressions-patterns.md). Ne définissez pas à la fois la propriété **id** et la propriété **sqlQuery**. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré. |
|**sqlQuery**  |**SqlQuery**  | Requête SQL Azure Cosmos DB utilisée pour récupérer plusieurs documents. La propriété prend en charge les liaisons d’exécution, comme dans cet exemple : `SELECT * FROM c where c.departmentId = {departmentId}`. Ne définissez pas à la fois la propriété **id** et la propriété **sqlQuery**. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré.|
|**Connexion**     |**ConnectionStringSetting**|Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Spécifie la valeur de la clé de partition pour la recherche. Peut inclure des paramètres de liaison.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

Dans les fonctions C# et F#, lorsque la fonction se termine correctement, toutes les modifications apportées au document d’entrée par le biais des paramètres d’entrée nommés sont automatiquement conservées.

Dans les fonctions JavaScript, les mises à jour ne sont pas effectuées une fois la fonction terminée. Utilisez plutôt `context.bindings.<documentName>In` et `context.bindings.<documentName>Out` pour effectuer les mises à jour. Consultez [l’exemple JavaScript](#input---javascript-examples).

## <a name="output"></a>Output

La liaison de sortie Azure Cosmos DB vous permet d’écrire un nouveau document dans une base de données Azure Cosmos DB en utilisant l’API SQL.

## <a name="output---examples"></a>Sortie - exemples

Consultez les exemples propres à un langage particulier :

* [C#](#output---c-examples)
* [Script C# (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Voir aussi l’[exemple d’entrée](#input---c-examples) qui utilise `DocumentClient`.

[Ignorer les exemples de sortie](#output---attributes)

### <a name="output---c-examples"></a>Sortie - exemple C#

Cette section contient les exemples suivants :

* Déclencheur de la file d’attente, écriture d’un document
* Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector

Les exemples font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorer les exemples de sortie](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Déclencheur de la file d’attente, écriture d’un document (C#)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute un document à une base de données, à l’aide des données fournies dans le message de Stockage File d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Ignorer les exemples de sortie](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector (C#)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute une collection de documents à une base de données, à l’aide de données fournies dans un JSON de message de file d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Ignorer les exemples de sortie](#output---attributes)

### <a name="output---c-script-examples"></a>Sortie - Exemples de script C#

Cette section contient les exemples suivants :

* Déclencheur de la file d’attente, écriture d’un document
* Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector

[Ignorer les exemples de sortie](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Déclencheur de la file d’attente, écriture d’un document (script C#)

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector

Pour créer plusieurs documents, vous pouvez vous lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

Cet exemple fait référence à un type simple `ToDoItem` :

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Voici le fichier function.json :

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[Ignorer les exemples de sortie](#output---attributes)

### <a name="output---javascript-examples"></a>Sortie - Exemples JavaScript

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

[Ignorer les exemples de sortie](#output---attributes)

### <a name="output---f-examples"></a>Sortie - Exemples F#

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Cet exemple nécessite un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Sortie - configuration](#output---configuration). Voici un exemple d’attribut `DocumentDB` dans une signature de méthode :

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-examples).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `DocumentDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     || Cette propriété doit être définie sur `documentdb`.        |
|**direction**     || Cette propriété doit être définie sur `out`.         |
|**name**     || Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** | **DatabaseName**|Base de données contenant la collection dans laquelle le document est créé.     |
|**collectionName** |**CollectionName**  | Nom de la collection dans laquelle le document est créé. |
|**createIfNotExists**  |**CreateIfNotExists**    | Valeur booléenne indiquant si la collection doit être créée si elle n’existe pas déjà. La valeur par défaut est *false* car les nouvelles collections sont créées avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Lorsque `CreateIfNotExists` a la valeur true, définit le chemin de la clé de partition pour la collection créée.|
|**collectionThroughput**|**CollectionThroughput**| Lorsque `CreateIfNotExists` a la valeur true, définit le [débit](../cosmos-db/set-throughput.md) de la collection créée.|
|**Connexion**    |**ConnectionStringSetting** |Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Par défaut, lorsque vous écrivez dans le paramètre de sortie de votre fonction, un document est créé dans votre base de données. Ce document comporte un GUID généré automatiquement en tant qu’ID du document. Vous pouvez spécifier l’ID du document de sortie en spécifiant la propriété `id` dans l’objet JSON qui est passé au paramètre de sortie.

> [!Note]
> Lorsque vous spécifier l’ID d’un document existant, il est remplacé par le nouveau document de sortie.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| CosmosDB | [Codes d’erreur CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le traitement de base de données serverless avec Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
