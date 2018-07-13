---
title: Informations de référence pour les développeurs C# sur Azure Functions
description: Découvrez comment développer sur Azure Functions à l’aide de C#.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: bde7a7788fd01bcbcc63296c0513af8eb4196021
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970177"
---
# <a name="azure-functions-c-developer-reference"></a>Informations de référence pour les développeurs C# sur Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Cet article est une introduction au développement d’Azure Functions à l’aide de C# dans des bibliothèques de classes .NET.

Azure Functions prend en charge le langage de programmation C#, mais également le langage de script C#. Pour plus d’informations sur l’[utilisation de C# dans le portail Azure](functions-create-function-app-portal.md), consultez [Informations de référence pour les développeurs de scripts C# (.csx)](functions-reference-csharp.md).

Cet article suppose que vous avez déjà lu les articles suivants :

* [Guide de développement Azure Functions](functions-reference.md)
* [Outils Azure Functions Visual Studio 2017](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projet de bibliothèque de classes Azure Functions

Dans Visual Studio, le modèle de projet **Azure Functions** crée un projet de bibliothèque de classes C# qui contient les fichiers suivants :

* [host.json](functions-host-json.md) : stocke les paramètres de configuration qui affectent toutes les fonctions dans le projet au cours d’une exécution locale ou dans Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) : stocke les paramètres de l’application et les chaînes de connexion utilisés au cours d’une exécution locale.

> [!IMPORTANT]
> Le processus de génération crée un fichier *function.json* pour chaque fonction. Ce fichier *function.json* n’est pas destiné à être directement modifié. Vous ne pouvez pas modifier la configuration des liaisons ni désactiver la fonction en modifiant ce fichier. Pour désactiver une fonction, utilisez l’attribut [Désactiver](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs). Par exemple, ajoutez un paramètre d’application booléen MY_TIMER_DISABLED, et appliquez `[Disable("MY_TIMER_DISABLED")]` à votre fonction. Vous pouvez ensuite activer et désactiver celle-ci en modifiant le paramètre d’application.

## <a name="methods-recognized-as-functions"></a>Méthodes reconnues en tant que fonctions

Dans une bibliothèque de classes, une fonction est une méthode statique avec un attribut `FunctionName` et un attribut de déclencheur, comme illustré dans l’exemple suivant :

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

L’attribut `FunctionName` marque une méthode comme point d’entrée de la fonction. Le nom doit être unique au sein d’un projet, commencer par une lettre et ne contenir que des lettres, des chiffres, `_` et `-`, jusqu’à 127 caractères. Les modèles de projets créent souvent une méthode nommée `Run`, mais le nom de la méthode peut être n’importe quel nom de méthode C# valide.

L’attribut de déclencheur spécifie le type de déclencheur et lie les données d’entrée à un paramètre de méthode. L’exemple de fonction est déclenché par un message de file d’attente, qui est lui-même transmis à la méthode dans le paramètre `myQueueItem`.

## <a name="method-signature-parameters"></a>Paramètres de signature de méthode

La signature de méthode peut contenir des paramètres autres que ceux utilisés avec l’attribut de déclencheur. Voici quelques-uns des paramètres supplémentaires que vous pouvez inclure :

* des [liaisons d’entrée et de sortie](functions-triggers-bindings.md) marquées comme telles à l’aide d’attributs ;  
* un paramètre `ILogger` ou `TraceWriter` de [journalisation](#logging) ;
* un paramètre `CancellationToken` pour [l’arrêt approprié](#cancellation-tokens) ;
* des paramètres [d’expressions de liaison](functions-triggers-bindings.md#binding-expressions-and-patterns) pour obtenir des métadonnées de déclencheur.

L’ordre des paramètres dans la signature de fonction n’a pas d’importance. Par exemple, vous pouvez placer les paramètres de déclencheur avant ou après les autres liaisons, de même que vous pouvez placer le paramètre de l’enregistreur d’événements avant ou après les paramètres de liaison ou de déclencheur.

### <a name="output-binding-example"></a>Exemple de liaison de sortie

L’exemple suivant modifie l’exemple précédent en ajoutant une liaison de file d’attente de sortie. La fonction écrit le message de file d’attente qui déclenche la fonction vers un nouveau message de file d’attente dans une autre file d’attente.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Les articles de référence sur les liaisons ([Liaisons de stockage File d’attente Azure pour Azure Functions](functions-bindings-storage-queue.md), par exemple) décrivent les types de paramètre que vous pouvez utiliser avec les attributs de liaison de déclencheur, d’entrée ou de sortie.

### <a name="binding-expressions-example"></a>Exemple d’expression de liaison

Le code suivant permet d’obtenir le nom de la file d’attente à surveiller à partir d’un paramètre d’application, et de récupérer l’heure de création du message de file d’attente dans le paramètre `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Fichier function.json généré automatiquement

Le processus de build crée un fichier *function.json* dans un dossier de fonction du dossier de build. Comme indiqué précédemment, ce fichier n’est pas destiné à être modifié directement. Vous ne pouvez pas modifier la configuration des liaisons ni désactiver la fonction en modifiant ce fichier. 

L’objectif de ce fichier est de fournir au contrôleur de mise à l’échelle les informations à utiliser pour les [ décisions de mise à l’échelle affectant le plan de consommation](functions-scale.md#how-the-consumption-plan-works). C’est pourquoi le fichier ne contient pas de liaisons d’entrée ou de sortie, mais uniquement des informations de déclencheur.

Le fichier *function.json* généré inclut une propriété `configurationSource` qui indique au runtime d’utiliser les attributs .NET pour les liaisons, au lieu de la configuration *function.json*. Voici un exemple :

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

La génération du fichier *function.json* est effectuée par le package NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Le même package est utilisé pour les versions 1.x et 2.x du runtime Functions. Le framework cible est ce qui différencie un projet 1.x d’un projet 2.x. Voici les parties correspondantes des fichiers *.csproj*, qui montrent les frameworks cibles et le même package `Sdk` :

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Parmi les dépendances de package `Sdk` figurent les déclencheurs et les liaisons. Un projet 1.x référence des liaisons et des déclencheurs 1.x parce que ceux-ci ciblent le .NET Framework, alors que les liaisons et les déclencheurs 2.x ciblent .NET Core.

Le package `Sdk` dépend également de [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) et indirectement de [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Ces dépendances garantissent que votre projet utilise des versions de package compatibles avec la version du runtime Functions qui est ciblée par le projet. Par exemple, `Newtonsoft.Json` a la version 11 pour .NET Framework 4.6.1, mais le runtime Functions qui cible .NET Framework 4.6.1 est compatible uniquement avec `Newtonsoft.Json` 9.0.1. Par conséquent, le code de fonction de votre projet doit également utiliser `Newtonsoft.Json` 9.0.1.

Le code source de `Microsoft.NET.Sdk.Functions` est disponible dans le dépôt GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Version du runtime

Visual Studio utilise les outils [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) pour exécuter les projets Functions. Ils constituent l’interface de ligne de commande du runtime Functions.

L’installation des outils Core Tools à l’aide de npm n’affecte pas la version de Core Tools utilisée par Visual Studio. Pour le runtime Functions version 1.x, Visual Studio stocke les versions de Core Tools dans *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* et utilise la dernière version stockée. Pour Functions 2.x, les outils Core Tools sont inclus dans l’extension **Azure Functions and Web Jobs Tools**. Pour les versions 1.x et 2.x, vous pouvez voir quelle version est utilisée dans la sortie de console lorsque vous exécutez un projet Functions :

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Types pris en charge pour les liaisons

Chaque liaison possède ses propres types pris en charge. Par exemple, un attribut de déclencheur d’objet blob peut être appliqué à un paramètre de chaîne, à un paramètre OCT, à un paramètre `CloudBlockBlob` ou à l’un des autres types pris en charge. L’[article sur les références de liaison pour les liaisons d’objets blob](functions-bindings-storage-blob.md#trigger---usage) répertorie tous les types de paramètre pris en charge. Pour plus d’informations, consultez [Déclencheurs et liaisons](functions-triggers-bindings.md), ainsi que les [documents sur les références de liaison pour chaque type de liaison](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Liaison à une valeur renvoyée par la méthode

Vous pouvez utiliser une valeur de retour de méthode pour une liaison de sortie en appliquant l’attribut à la valeur de retour de méthode. Pour obtenir des exemples, consultez [Déclencheurs et liaisons](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Écrire plusieurs valeurs de sortie

Pour écrire plusieurs valeurs dans une liaison de sortie, utilisez le type [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou le type [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Ces types sont des collections en écriture seule, écrites dans la liaison de sortie à la fin de la méthode.

Cet exemple écrit plusieurs messages de file d’attente dans la même file d’attente à l’aide de `ICollector` :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Journalisation

Pour consigner la sortie dans vos journaux de diffusion en continu en C#, ajoutez un argument de type `TraceWriter`. Nous vous recommandons de le nommer `log`. Évitez d’utiliser `Console.Write` dans Azure Functions. 

`TraceWriter` est défini dans le [Kit de développement logiciel (SDK) Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Le niveau de journalisation de `TraceWriter` est configurable dans [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Pour plus d’informations sur un framework de journalisation plus récent que vous pouvez utiliser à la place de `TraceWriter`, consultez [Écrire des journaux dans des fonctions C#](functions-monitoring.md#write-logs-in-c-functions) dans l’article **Surveiller l’exécution des fonctions Azure**.

## <a name="async"></a>Async

Pour rendre une fonction [asynchrone](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), utilisez le mot clé `async` et retournez un objet `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Jetons d’annulation

Une fonction peut accepter un paramètre [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) qui permet au système d’exploitation de notifier votre code quand la fonction est sur le point de se terminer. Vous pouvez utiliser cette notification pour vous assurer que la fonction ne s’arrête pas de manière inattendue et laisse les données dans un état incohérent.

L’exemple suivant montre comment vérifier l’arrêt imminent d’une fonction.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Variables d’environnement

Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `System.Environment.GetEnvironmentVariable`, comme illustré dans l’exemple de code suivant :

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Les paramètres de l’application peuvent être lus à partir de variables d’environnement lors du développement localement et de l’exécution dans Azure. Lors du développement localement, les paramètres de l’application proviennent de la collection `Values` dans le fichier *local.settings.json*. Dans les deux environnements, local et Azure, `GetEnvironmentVariable("<app setting name>")` récupère la valeur du paramètre d’application nommé. Par exemple, lorsque vous exécutez localement, « My Site Name » est retourné si votre fichier *local.settings.json* contient `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

La propriété [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) est une autre API permettant d’obtenir des valeurs de paramètre d’application, mais nous vous recommandons d’utiliser `GetEnvironmentVariable` comme indiqué ici.

## <a name="binding-at-runtime"></a>Liaison au runtime

Avec C# et d’autres langages .NET, vous pouvez utiliser un schéma de liaison [impératif](https://en.wikipedia.org/wiki/Imperative_programming), par opposition aux liaisons [*déclaratives*](https://en.wikipedia.org/wiki/Declarative_programming) dans les attributs. La liaison impérative est utile lorsque les paramètres de liaison doivent être calculés au moment du runtime plutôt que lors de la conception. Avec ce modèle, vous pouvez effectuer une liaison à la volée avec une liaison d’entrée et de sortie prise en charge dans le code de votre fonction.

Définissez une liaison impérative comme suit :

- **N’incluez pas** d’attribut dans la signature de fonction pour les liaisons impératives souhaitées.
- Transmettez un paramètre d’entrée [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilisez le modèle en C# suivant pour effectuer la liaison de données.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` est l’attribut .NET qui définit votre liaison et `T` est le type d’entrée ou de sortie pris en charge par ce type de liaison. `T` ne peut pas être un type de paramètre `out` (comme `out JObject`). Par exemple, la liaison de sortie de la table Mobile Apps prend en charge [six types de sortie](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mais vous pouvez utiliser uniquement [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) avec des liaisons impératives.

### <a name="single-attribute-example"></a>Exemple d’attribut unique

L’exemple de code suivant crée une [liaison de sortie d’objet blob de stockage](functions-bindings-storage-blob.md#output) avec un chemin d’objet blob défini au moment de l’exécution, puis écrit une chaîne vers l’objet blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) définit la liaison d’entrée ou de sortie de [l’objet blob de stockage](functions-bindings-storage-blob.md), et [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) est un type de liaison de sortie pris en charge.

### <a name="multiple-attribute-example"></a>Exemple d’attributs multiples

L’exemple précédent obtient le paramètre d’application pour la chaîne de connexion du compte de stockage principal de l’application de fonction (à savoir `AzureWebJobsStorage`). Vous pouvez spécifier un paramètre d’application personnalisé à utiliser pour le compte de stockage en ajoutant l’attribut [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) et en transmettant le tableau d’attributs dans `BindAsync<T>()`. Utilisez un paramètre `Binder`, et non `IBinder`.  Par exemple : 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Déclencheurs et liaisons 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les meilleures pratiques pour Azure Functions](functions-best-practices.md)
