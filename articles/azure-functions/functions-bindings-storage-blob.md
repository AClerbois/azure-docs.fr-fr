---
title: Liaisons Stockage Blob Azure pour Azure Functions
description: Comprendre comment utiliser les déclencheurs et les liaisons Stockage Blob Azure dans Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: c1c20e225e15769a8cb09f60dfc371f4ec4d81f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306864"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Liaisons Stockage Blob Azure pour Azure Functions

Cet article explique comment utiliser des liaisons Stockage Blob Azure dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour les objets blob. Cet article propose une section pour chaque liaison :

* [Déclencheur d’objet blob](#trigger)
* [Liaison d’entrée d’objet blob](#input)
* [Liaison de sortie d’objet blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Utilisez le déclencheur Event Grid au lieu du déclencheur de stockage Blob pour les comptes de stockage blob uniquement, afin de bénéficier d’une scalabilité élevée et pour réduire la latence. Pour plus d’informations, consultez la section [Déclencheur](#trigger).

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons du Stockage Blob sont fournies dans le package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Les liaisons du Stockage Blob sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur de stockage Blob démarre une fonction lors de la détection d’un objet blob nouveau ou mis à jour. Le contenu de l’objet blob est fourni comme entrée de la fonction.

Le [déclencheur Event Grid](functions-bindings-event-grid.md) contient la prise en charge intégrée des [événements blob](../storage/blobs/storage-blob-event-overview.md) et peut également être utilisé pour démarrer une fonction lors de la détection d’un objet blob nouveau ou mis à jour. Pour obtenir un exemple, consultez le tutoriel [Redimensionnement d’image avec Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

Utilisez Event Grid au lieu du déclencheur de stockage Blob pour les scénarios suivants :

* Comptes de stockage d’objets blob
* Scalabilité élevée
* Réduction de la latence

### <a name="blob-storage-accounts"></a>Comptes de stockage d’objets blob

Les [comptes Stockage Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) sont pris en charge pour les liaisons d’entrée et de sortie d’objet blob, mais pas pour les déclencheurs d’objet blob. Les déclencheurs de stockage d’objets blob nécessitent un compte de stockage à usage général.

### <a name="high-scale"></a>Scalabilité élevée

La scalabilité élevée peut être définie comme des conteneurs qui contiennent plus de 100 000 objets blob ou des comptes de stockage avec plus de 100 mises à jour d’objets blob par seconde.

### <a name="latency-issues"></a>Problèmes de latence

Si votre application de fonction est dans le plan Consommation, il peut y avoir jusqu’à 10 minutes de délai dans le traitement des nouveaux objets blob si une application de fonction est devenue inactive. Pour éviter toute latence, vous pouvez passer à un plan App Service dans lequel est activé AlwaysOn. Vous pouvez également utiliser un [déclencheur Event Grid](functions-bindings-event-grid.md) avec votre compte de stockage Blob. Pour obtenir un exemple, consultez le [tutoriel Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Déclencheur de stockage de file d’attente

En dehors d’Event Grid, une autre alternative au traitement des objets blob est le déclencheur de stockage de file d’attente, mais il n’intègre pas la prise en charge des événements blob. Vous devrez créer des messages de file d’attente pendant la création ou la mise à jour des objets blob. Pour obtenir un exemple qui suppose que vous avez procédé ainsi, consultez [l’exemple de liaison d’entrée d’objet blob plus loin dans cet article](#input---example).

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui écrit une entrée de journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La chaîne `{name}` dans le chemin d’accès du déclencheur d’objet blob `samples-workitems/{name}` crée une [expression de liaison](./functions-bindings-expressions-patterns.md) que vous pouvez utiliser dans le code de fonction pour accéder au nom de fichier de l’objet blob déclencheur. Pour plus d’informations, consultez la section [Modèles de nom d’objet blob](#trigger---blob-name-patterns) dans la suite de cet article.

Pour plus d’informations sur l’attribut `BlobTrigger`, consultez [Déclencheur - attributs](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur de blob dans un fichier *function.json* et un [code Python](functions-reference-python.md) qui utilise la liaison. La fonction enregistre un journal lorsqu’un objet blob est ajouté ou mis à jour dans le [conteneur](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La chaîne `{name}` dans le chemin d’accès du déclencheur d’objet blob `samples-workitems/{name}` crée une [expression de liaison](./functions-bindings-expressions-patterns.md) que vous pouvez utiliser dans le code de fonction pour accéder au nom de fichier de l’objet blob déclencheur. Pour plus d’informations, consultez la section [Modèles de nom d’objet blob](#trigger---blob-name-patterns) dans la suite de cet article.

Pour plus d’informations sur les propriétés du fichier *function.json*, consultez la section [Configuration](#trigger---configuration) qui décrit ces propriétés.

Voici le code Script C# qui lie à `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Voici le code Script C# qui lie à `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et un [code JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit un journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `samples-workitems`.

Voici le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La chaîne `{name}` dans le chemin d’accès du déclencheur d’objet blob `samples-workitems/{name}` crée une [expression de liaison](./functions-bindings-expressions-patterns.md) que vous pouvez utiliser dans le code de fonction pour accéder au nom de fichier de l’objet blob déclencheur. Pour plus d’informations, consultez la section [Modèles de nom d’objet blob](#trigger---blob-name-patterns) dans la suite de cet article.

Pour plus d’informations sur les propriétés du fichier *function.json*, consultez la section [Configuration](#trigger---configuration) qui décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Déclencheur - Exemple Python

L’exemple suivant montre une liaison de déclencheur de blob dans un fichier *function.json* et un [code Python](functions-reference-python.md) qui utilise la liaison. La fonction enregistre un journal lorsqu’un objet blob est ajouté ou mis à jour dans le [conteneur](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Voici le fichier *function.json* :

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La chaîne `{name}` dans le chemin d’accès du déclencheur d’objet blob `samples-workitems/{name}` crée une [expression de liaison](./functions-bindings-expressions-patterns.md) que vous pouvez utiliser dans le code de fonction pour accéder au nom de fichier de l’objet blob déclencheur. Pour plus d’informations, consultez la section [Modèles de nom d’objet blob](#trigger---blob-name-patterns) dans la suite de cet article.

Pour plus d’informations sur les propriétés du fichier *function.json*, consultez la section [Configuration](#trigger---configuration) qui décrit ces propriétés.

Voici le code Python :

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Déclencheur - exemple Java

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et un [code Java](functions-reference-java.md) qui utilise la liaison. La fonction écrit un journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `myblob`.

Voici le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Voici le code Java :

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur d’objet blob :

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Le constructeur de l’attribut prend une chaîne de chemin qui indique le conteneur à surveiller et éventuellement un [modèle de nom d’objet blob](#trigger---blob-name-patterns). Voici un exemple :

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fournit une autre manière de spécifier le compte de stockage à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Le compte de stockage à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `BlobTrigger`.
* L’attribut `StorageAccount` appliqué au même paramètre que l’attribut `BlobTrigger`.
* L’attribut `StorageAccount` appliqué à la fonction.
* L’attribut `StorageAccount` appliqué à la classe.
* Le compte de stockage par défaut pour l’application de fonction (paramètre d’application « AzureWebJobsStorage »).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `BlobTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `blobTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. Les exceptions sont notées à la section [utilisation](#trigger---usage). |
|**name** | n/a | Nom de la variable qui représente l’objet blob dans le code de la fonction. |
|**path** | **BlobPath** |[Conteneur](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) à superviser.  Peut être un [modèle de nom d’objet blob](#trigger---blob-name-patterns). |
|**Connexion** | **Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.<br><br>La chaîne de connexion doit être destinée à un compte de stockage à usage général, et non pas à un [compte Stockage Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

En langage C# et dans un script C#, vous pouvez utiliser les types de paramètres suivants pour l’objet blob déclencheur :

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Un objet POCO sérialisable au format JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Si vous essayez de lier à un des types de SDK Stockage et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

La liaison avec `string`, `Byte[]` ou POCO est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](#trigger---concurrency-and-memory-usage) plus loin dans cet article.

Dans JavaScript, accédez aux données de l’objet blob d’entrée en utilisant `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Déclencheur - modèles de nom d’objet blob

Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path` du fichier *function.json* ou dans le constructeur d’attribut `BlobTrigger`. Le modèle de nom peut être une [expression de filtre ou de liaison](./functions-bindings-expressions-patterns.md). Les sections suivantes fournissent des exemples.

### <a name="get-file-name-and-extension"></a>Obtenir l’extension et le nom de fichier

L’exemple suivant montre comment se lier séparément à l’extension et au nom de fichier de l’objet blob :

```json
"path": "input/{blobname}.{blobextension}",
```
Si l’objet blob est nommé *original-Blob1.txt*, les valeurs des variables `blobname` et `blobextension` dans le code de la fonction sont *original-Blob1* et *txt*.

### <a name="filter-on-blob-name"></a>Filtrer sur le nom de l’objet blob

L’exemple suivant déclenche uniquement sur les objets blob du conteneur `input` qui commencent par la chaîne « original- » :

```json
"path": "input/original-{name}",
```

Si le nom de l’objet blob est *original-Blob1.txt*, la valeur de la variable `name` dans le code de la fonction est `Blob1`.

### <a name="filter-on-file-type"></a>Filtrer sur le type de fichier

L’exemple suivant ne déclenche que sur des fichiers *.png* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrer sur les accolades dans les noms de fichiers

Pour rechercher les accolades dans les noms de fichiers, utilisez une séquence d’échappement sous la forme de deux accolades. L’exemple suivant filtre en recherchant les objets blob qui contiennent des accolades dans le nom :

```json
"path": "images/{{20140101}}-{name}",
```

Si l’objet blob est nommé *{20140101}-soundfile.mp3*, la valeur de la variable `name` dans le code de la fonction est *soundfile.mp3*.

## <a name="trigger---metadata"></a>Déclencheur - métadonnées

Le déclencheur d’objet blob fournit plusieurs propriétés de métadonnées. Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ces valeurs ont la même sémantique que le type [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

|Propriété  |Type  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Chemin de l’objet blob déclencheur.|
|`Uri`|`System.Uri`|URI de l’objet blob pour l’emplacement principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Propriétés système de l’objet blob. |
|`Metadata` |`IDictionary<string,string>`|Métadonnées définies par l’utilisateur pour l’objet blob.|

Par exemple, les exemples JavaScript et Script C# suivants enregistrent le chemin d’accès à l’objet blob déclencheur, notamment le conteneur :

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Déclencheur - reçus d’objet blob

Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour déterminer si la version d’un objet blob donné a été traitée, il gère des *reçus d’objet blob*.

Azure Functions stocke les reçus d’objet blob dans un conteneur appelé *azure-webjobs-hosts* dans le compte de stockage Azure de votre application de fonction (définie par le paramètre d’application `AzureWebJobsStorage`). Un reçu d’objet blob contient les informations suivantes :

* Fonction déclenchée (« *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>*  », par exemple : « MyFunctionApp.Functions.CopyBlob »)
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* Étiquette d’entité (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Pour forcer le retraitement d’un objet blob, supprimez manuellement le reçu de l’objet blob du conteneur *azure-webjobs-hosts*.

## <a name="trigger---poison-blobs"></a>Déclencheur - objets blob incohérents

En cas d’échec d’une fonction de déclencheur d’objet blob, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut.

Si les 5 tentatives échouent, Azure Functions ajoute un message à une file d’attente de stockage nommée *webjobs-blobtrigger-poison*. Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* Étiquette d’entité (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

## <a name="trigger---concurrency-and-memory-usage"></a>Déclencheur : concurrence et utilisation de la mémoire

Le déclencheur de blob utilise une file d’attente en interne. Le nombre maximal d’appels de fonction concurrents est par conséquent contrôlé par la [configuration des files d’attente dans host.json](functions-host-json.md#queues). Les paramètres par défaut limitent la concurrence à 24 appels. Cette limite s’applique séparément à chaque fonction qui utilise un déclencheur de blob.

[Le plan de consommation](functions-scale.md#how-the-consumption-and-premium-plans-work) limite une application de fonction sur une machine virtuelle (VM) à 1,5 Go de mémoire. La mémoire est utilisée par chaque instance de la fonction qui s’exécutent simultanément et par le runtime de fonctions lui-même. Si une fonction déclenchée par blob charge le blob entier en mémoire, la mémoire maximale utilisée par cette fonction uniquement pour les blobs est 24 * la taille maximale du blob. Par exemple, une application de fonction avec trois fonctions déclenchées par blob et les paramètres par défaut aurait une concurrence par machine virtuelle maximale de 3 * 24 = 72 appels de fonction.

Les fonctions JavaScript et Java chargent l’objet blob entier en mémoire et les fonctions C# le font si vous faites la liaison avec `string`, `Byte[]` ou POCO.

## <a name="trigger---polling"></a>Déclencheur : interrogation

Si le conteneur d’objets blob surveillé contient plus de 10 000 objets BLOB (parmi tous les conteneurs), le runtime Functions analyse fichiers journaux pour surveiller les objets BLOB nouveaux ou modifiés. Ce processus peut entraîner des retards. Il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, les [journaux d’activité de stockage sont créés selon le principe du meilleur effort](/rest/api/storageservices/About-Storage-Analytics-Logging). Il n’existe aucune garantie que tous les événements sont capturés. Dans certaines conditions, des journaux d’activité peuvent être omis.

Si vous avez besoin de traitement d’objets blob plus rapide ou plus fiable, envisagez de créer un [message de file d’attente](../storage/queues/storage-dotnet-how-to-use-queues.md) quand vous créez l’objet blob. Ensuite, utilisez un [déclencheur de file d’attente](functions-bindings-storage-queue.md) plutôt qu’un déclencheur d’objet blob pour traiter l’objet blob. Une autre option consiste à utiliser Event Grid ; consultez le didacticiel [Automatiser le redimensionnement des images téléchargées à l’aide d’Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Entrée

Utilisez une liaison d’entrée de stockage blob pour lire des objets blob.

## <a name="input---example"></a>Entrée - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#input---c-example)
* [Script C# (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Entrée - exemple C#

L’exemple suivant est une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur de file d’attente et une liaison d’entrée d’objet blob. Le message en file d’attente contient le nom du blob et la fonction journalise la taille du blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="input---c-script-example"></a>Entrée - exemple de script C#

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objet blob dans un fichier *function.json* et le code de [script C# (.csx)](functions-reference-csharp.md) qui utilise ces liaisons. La fonction effectue une copie d’un objet blob de texte. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Entrée - exemple JavaScript

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objets blob dans un fichier *function.json* et du [code JavaScript](functions-reference-node.md) qui utilise les liaisons. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Entrée - Exemple Python

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objets blob dans un fichier *function.json* et dans du [code Python](functions-reference-python.md) qui utilise les liaisons. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Python :

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Entrée : exemples Java

Cette section contient les exemples suivants :

* [Déclencheur HTTP, rechercher le nom de l’objet blob dans la chaîne de requête](#http-trigger-look-up-blob-name-from-query-string-java)
* [Déclencheur de file d’attente, recevoir le nom de l’objet blob à partir du message en file d’attente](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Déclencheur HTTP, rechercher le nom de l’objet blob dans la chaîne de requête (Java)

 L’exemple suivant montre une fonction Java qui utilise l’annotation ```HttpTrigger``` pour recevoir un paramètre qui contient le nom d’un fichier dans un conteneur de stockage d’objets blob. L’annotation ```BlobInput``` lit ensuite le fichier et transmet son contenu à la fonction en tant que ```byte[]```.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Déclencheur de file d’attente, recevoir le nom de l’objet blob à partir du message en file d’attente (Java)

 L’exemple suivant montre une fonction Java qui utilise l’annotation ```QueueTrigger``` pour recevoir un message qui contient le nom d’un fichier dans un conteneur de stockage d’objets blob. L’annotation ```BlobInput``` lit ensuite le fichier et transmet son contenu à la fonction en tant que ```byte[]```.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@BlobInput` sur les paramètres dont la valeur proviendrait d’un objet blob.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de `Optional<T>`.

## <a name="input---attributes"></a>Entrée - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Le constructeur de l’attribut prend le chemin de l’objet blob et un paramètre `FileAccess` indiquant la lecture ou l’écriture, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Déclencheur - attributs](#trigger---attributes).

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Blob`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `blob`. |
|**direction** | n/a | Cette propriété doit être définie sur `in`. Les exceptions sont notées à la section [utilisation](#input---usage). |
|**name** | n/a | Nom de la variable qui représente l’objet blob dans le code de la fonction.|
|**path** |**BlobPath** | Chemin de l’objet blob. |
|**Connexion** |**Connection**| Nom d’un paramètre d’application comportant la [chaîne de connexion de stockage](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.<br><br>La chaîne de connexion doit être pour un compte de stockage à usage général, et non pour un [compte de stockage d’objets blob uniquement](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **y accéder** | Indique si vous lirez ou écrirez. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

En langage C# et dans un script C#, vous pouvez utiliser les types de paramètres suivants pour la liaison d’entrée d’objet blob :

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Si vous essayez de lier à un des types de SDK Stockage et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

La liaison à `string` ou `Byte[]` est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](#trigger---concurrency-and-memory-usage) plus haut dans cet article.

Dans JavaScript, accédez aux données de l’objet blob en utilisant `context.bindings.<name from function.json>`.

## <a name="output"></a>Sortie

Utilisez les liaisons de sortie de stockage blob pour écrire des objets blob.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant est une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur d’objet blob et deux liaisons de sortie d’objet blob. La fonction est déclenchée par la création d’un objet blob d’image dans le conteneur *sample-images*. Il crée des copies de petite et moyenne taille de l’objet blob d’image.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objet blob dans un fichier *function.json* et le code de [script C# (.csx)](functions-reference-csharp.md) qui utilise ces liaisons. La fonction effectue une copie d’un objet blob de texte. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objets blob dans un fichier *function.json* et du [code JavaScript](functions-reference-node.md) qui utilise les liaisons. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Sortie - Exemple Python

<!--Same example for input and output. -->

L’exemple suivant montre des liaisons d’entrée et de sortie d’objets blob dans un fichier *function.json* et dans du [code Python](functions-reference-python.md) qui utilise les liaisons. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Python :

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Sortie : exemples Java

Cette section contient les exemples suivants :

* [Déclencheur HTTP, utilisation d’OutputBinding](#http-trigger-using-outputbinding-java)
* [Déclencheur de file d’attente, utilisation de la valeur de retour de la fonction](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Déclencheur HTTP, utilisation d’OutputBinding (Java)

 L’exemple suivant montre une fonction Java qui utilise l’annotation ```HttpTrigger``` pour recevoir un paramètre qui contient le nom d’un fichier dans un conteneur de stockage d’objets blob. L’annotation ```BlobInput``` lit ensuite le fichier et transmet son contenu à la fonction en tant que ```byte[]```. L’annotation ```BlobOutput``` lie à ```OutputBinding outputItem```, qui est ensuite utilisé par la fonction pour écrire le contenu de l’objet blob d’entrée dans le conteneur de stockage configuré.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Déclencheur de file d’attente, utilisation de la valeur de retour de la fonction (Java)

 L’exemple suivant montre une fonction Java qui utilise l’annotation ```QueueTrigger``` pour recevoir un message qui contient le nom d’un fichier dans un conteneur de stockage d’objets blob. L’annotation ```BlobInput``` lit ensuite le fichier et transmet son contenu à la fonction en tant que ```byte[]```. L’annotation ```BlobOutput``` lie à la valeur de retour de la fonction, qui est ensuite utilisée par le runtime pour écrire le contenu de l’objet blob d’entrée dans le conteneur de stockage configuré.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@BlobOutput` sur les paramètres de fonction dont la valeur serait écrite dans un objet du stockage Blob.  Le type de paramètre doit être `OutputBinding<T>`, où T désigne n’importe quel type Java natif ou un POJO.

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Le constructeur de l’attribut prend le chemin de l’objet blob et un paramètre `FileAccess` indiquant la lecture ou l’écriture, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Déclencheur - attributs](#trigger---attributes).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Blob`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `blob`. |
|**direction** | n/a | Cette propriété doit être définie sur `out` pour une liaison de type sortie. Les exceptions sont notées à la section [utilisation](#output---usage). |
|**name** | n/a | Nom de la variable qui représente l’objet blob dans le code de la fonction.  La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.|
|**path** |**BlobPath** | Chemin de l’objet blob. |
|**Connexion** |**Connection**| Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.<br><br>La chaîne de connexion doit être pour un compte de stockage à usage général, et non pour un [compte de stockage d’objets blob uniquement](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **y accéder** | Indique si vous lirez ou écrirez. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

En mode C# et script C#, vous pouvez lier aux types suivants pour écrire des blobs :

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requiert la liaison « in » `direction` dans *function.json* ou `FileAccess.Read` dans une bibliothèque de classes C#. Toutefois, vous pouvez utiliser l’objet conteneur fourni par le runtime pour les opérations d’écriture, telles que le chargement de blobs dans le conteneur.

<sup>2</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Si vous essayez de lier à un des types de SDK Stockage et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

Dans les fonctions asynchrones, utilisez la valeur de retour ou `IAsyncCollector` au lieu d’un paramètre `out`.

La liaison à `string` ou `Byte[]` est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](#trigger---concurrency-and-memory-usage) plus haut dans cet article.


Dans JavaScript, accédez aux données de l’objet blob en utilisant `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison |  Référence |
|---|---|
| Blob | [Codes d’erreur du service BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Objet blob, Table, File d’attente |  [Codes d’erreur de stockage](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente |  [Dépannage](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
