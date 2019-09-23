---
title: Vue d’ensemble de Fonctions durables - Azure
description: Introduction à l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5fd79b15f0f6398e2f48da25197fa6d5c2e010c2
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075915"
---
# <a name="what-are-durable-functions"></a>Présentation de Durable Functions

*Durable Functions* est une extension d’[Azure Functions](../functions-overview.md) qui vous permet d’écrire des fonctions avec état dans un environnement de calcul serverless. L’extension vous permet de définir des workflows avec état en écrivant des [*fonctions orchestrator*](durable-functions-orchestrations.md) et des entités avec état en écrivant des [*fonctions d’entité*](durable-functions-entities.md) à l’aide du modèle de programmation Azure Functions. En arrière-plan, l’extension gère l’état, les points de contrôle et les redémarrages à votre place, ce qui vous permet de vous concentrer sur votre logique métier.

## <a name="language-support"></a>Langages pris en charge

Durable Functions prend actuellement en charge les langages suivants :

* **C#**  : les [bibliothèques de classes précompilées](../functions-dotnet-class-library.md) et le [script C#](../functions-reference-csharp.md).
* **F#**  : les bibliothèques de classes précompilées et le script F#. Le script F# est pris en charge uniquement pour la version 1.x du runtime Azure Functions.
* **JavaScript** : pris en charge uniquement pour la version 2.x du runtime Azure Functions. Nécessite la version 1.7.0 ou ultérieure de l’extension Durable Functions. 

À l’avenir, Durable Functions est censé prendre en charge tous les [langages pris en charge dans Azure Functions](../supported-languages.md). Consultez la [liste des problèmes avec Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) pour connaître l’état actuel de la prise en charge de langages supplémentaires.

Comme dans Azure Functions, des modèles sont disponibles pour vous aider à développer des fonctions durables en utilisant [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) et le [portail Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Modèles d’application

Le principal cas d’usage de Durable Functions est la simplification d’exigences complexes de coordination avec état dans des applications serverless. Les sections suivantes décrivent des modèles d’application standard qui peuvent tirer parti de Durable Functions :

* [Chaînage de fonctions](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [API HTTP Async](#async-http)
* [Surveillance](#monitoring)
* [Interaction humaine](#human)
* [Aggrégateur](#aggregator)

### <a name="chaining"></a>Modèle 1 : Chaînage de fonctions

Dans le modèle de chaînage de fonctions, une séquence de fonctions s’exécute dans un ordre spécifique. Dans ce modèle, la sortie d’une fonction est appliquée à l’entrée d’une autre fonction.

![Schéma de modèle de chaînage de fonctions](./media/durable-functions-concepts/function-chaining.png)

Vous pouvez utiliser Durable Functions pour implémenter le modèle de chaînage de fonctions de façon concise, comme indiqué dans l’exemple suivant :

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

Dans cet exemple, les valeurs `F1`, `F2`, `F3` et `F4` représentent les noms des autres fonctions dans l’application de fonction. Vous pouvez implémenter le flux de contrôle à l’aide de constructions de codage impératives normales. Le code s’exécute du haut vers le bas. Le code peut impliquer une sémantique de flux contrôle de langage existante, notamment des instructions conditionnelles et des boucles. Vous pouvez inclure une logique de gestion des erreurs dans des blocs `try`/`catch`/`finally`.

Vous pouvez utiliser le paramètre `context` [DurableOrchestrationContext] \(.NET\) et l’objet `context.df` (JavaScript) pour appeler d’autres fonctions par nom, passer des paramètres et retourner la sortie d’une fonction. Chaque fois que le code appelle `await` (C#) ou `yield` (JavaScript), l’infrastructure Durable Functions crée des points de contrôle de la progression de l’instance de la fonction actuelle. En cas de recyclage du processus ou de la machine virtuelle au milieu de l’exécution, l’instance de la fonction reprend à partir de l’appel à `await` ou `yield` précédent. Pour en savoir plus, consultez la section suivante, Modèle 2 : Fan out/fan in.

> [!NOTE]
> L’objet `context` dans JavaScript représente le [contexte de fonction](../functions-reference-node.md#context-object) dans son ensemble, et pas uniquement le paramètre [DurableOrchestrationContext].

### <a name="fan-in-out"></a>Modèle 2 : Fan out/fan in

Dans le modèle fan out/fan in, vous exécutez plusieurs fonctions en parallèle, puis attendez que toutes ces fonctions se terminent. Un travail d’agrégation est souvent effectué sur les résultats retournés par les fonctions.

![Schéma du modèle fan out/fan in](./media/durable-functions-concepts/fan-out-fan-in.png)

Avec des fonctions normales, vous pouvez effectuer un processus fan out en configurant la fonction afin qu’elle envoie plusieurs messages vers une file d’attente. Mais le processus fan-in est beaucoup plus difficile. Dans ce cas, dans une fonction normale, vous écrivez du code pour surveiller l’achèvement des fonctions déclenchées en file d’attente et stocker les sorties des fonctions.

L’extension Durable Functions gère ce modèle avec un code relativement simple :

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Dans le cadre du processus fan-out, la distribution s’effectue vers plusieurs instances de la fonction `F2`. Le travail est suivi à l’aide d’une liste de tâches dynamique. L’API .NET `Task.WhenAll` ou JavaScript `context.df.Task.all` est appelée pour attendre la fin de toutes les fonctions appelées. Les sorties de la fonction `F2` sont ensuite agrégées à partir de la liste de tâches dynamique puis transmises à la fonction `F3`.

La création automatique de points de contrôle qui se produit lors de l’appel à `await` ou `yield` sur `Task.WhenAll` ou `context.df.Task.all` garantit qu’un incident ou qu’un redémarrage potentiel survenu au milieu du processus ne nécessite aucun redémarrage d’une quelconque tâche déjà terminée.

> [!NOTE]
> Dans de rares circonstances, un plantage peut se produire dans la fenêtre après l’exécution d’une fonction d’activité, mais avant que son achèvement ne soit enregistré dans l’historique d’orchestration. Si cela se produit, la fonction d’activité s’exécute à nouveau depuis le début après la reprise du processus.

### <a name="async-http"></a>Modèle 3 : API HTTP Async

Le modèle d’API HTTP asynchrone traite le problème de coordination de l’état des opérations à exécution longue avec des clients externes. Pour implémenter ce modèle, une méthode courante consiste à faire déclencher l’action à exécution longue par un point de terminaison HTTP. Le client est ensuite redirigé vers un point de terminaison d’état que le client interroge pour savoir quand l’opération est terminée.

![Schéma du modèle d’API HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions fournit une **prise en charge intégrée** pour ce modèle, en simplifiant ou même en supprimant le code que vous devez écrire pour interagir avec des exécutions de fonctions de longue durée. Ainsi, les exemples de démarrage rapide de Durable Functions ([C#](durable-functions-create-first-csharp.md) et [JavaScript](quickstart-js-vscode.md)) montrent une commande REST simple qui vous permet de démarrer de nouvelles instances de fonctions orchestrator. Lorsqu’une instance démarre, l’extension expose des API HTTP webhook qui interrogent l’état de la fonction d’orchestrateur. 

L’exemple suivant montre les commandes REST permettant de démarrer un orchestrateur et d’interroger son état. Par souci de clarté, certains détails du protocole ont été retirés de l’exemple.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Étant donné que le runtime de Durable Functions gère l’état à votre place, vous n’avez pas besoin d’implémenter votre propre mécanisme de suivi de l’état.

L’extension Durable Functions expose des API HTTP intégrées qui gèrent les orchestrations à exécution longue. Vous pouvez également implémenter ce modèle vous-même à l’aide de vos propres déclencheurs de fonction (comme un déclencheur HTTP, une file d’attente ou Azure Event Hubs) et de la [liaison du client d’orchestration](durable-functions-bindings.md#orchestration-client). Ainsi, vous pouvez utiliser un message de file d’attente pour déclencher l’arrêt. Sinon, vous pouvez vous servir d’un déclencheur HTTP protégé par une stratégie d’authentification Azure Active Directory à la place des API HTTP intégrées qui utilisent une clé générée pour l’authentification.

Pour plus d’informations, consultez l’article [Fonctionnalités HTTP](durable-functions-http-features.md), qui explique comment vous pouvez exposer des processus asynchrones à exécution longue sur HTTP à l’aide de l’extension Durable Functions.

### <a name="monitoring"></a>Modèle 4 : Surveiller

Le modèle de surveillance fait référence à un processus souple et récurrent dans un flux de travail. Il peut s’agir, par exemple, d’une interrogation se poursuivant jusqu’à ce que certaines conditions soient remplies. Vous pouvez utiliser un [déclencheur de minuteur](../functions-bindings-timer.md) standard pour un scénario simple, comme une tâche de nettoyage périodique, mais son intervalle est statique et la gestion de la durée de vie des instances devient complexe. Vous pouvez utiliser Durable Functions pour créer des intervalles de récurrence flexibles, gérer la durée de vie des tâches et créer plusieurs processus de surveillance à partir d’une seule orchestration.

L’inversion du scénario d’API HTTP asynchrone antérieur représente un exemple du modèle de surveillance. Au lieu d’exposer un point de terminaison d’un client externe pour surveiller une opération longue, l’analyse de longue durée consomme un point de terminaison externe, puis attend un changement d’état.

![Schéma du modèle de surveillance](./media/durable-functions-concepts/monitor.png)

Avec quelques lignes de code, vous pouvez utiliser Durable Functions pour créer plusieurs moniteurs qui observent des points de terminaison arbitraires. Les moniteurs peuvent mettre fin à une exécution lorsqu’une condition est remplie, ou [DurableOrchestrationClient](durable-functions-instance-management.md) peut arrêter les moniteurs. Vous pouvez modifier l’intervalle `wait` d’un moniteur selon une condition spécifique (par exemple, avec un backoff exponentiel). 

Le code suivant implémente un moniteur de base :

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quand une requête est reçue, une nouvelle instance d’orchestration est créée pour cet ID de tâche. L’instance interroge un état jusqu’à ce qu’une condition soit respectée et que vous quittiez la boucle. Un minuteur durable contrôle la fréquence d’interrogation. Des opérations supplémentaires peuvent ensuite être exécutées, ou l’orchestration peut prendre fin. Quand la valeur `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) dépasse la valeur `expiryTime`, le moniteur se termine.

### <a name="human"></a>Modèle 5 : Interaction humaine

De nombreux processus automatisés impliquent un certain type d’interaction humaine. L’implication de personnes humaines dans un processus automatisé est complexe, car elles ne sont pas toujours aussi disponibles et réactives que les services cloud. Un processus automatisé peut rendre cette interaction possible en utilisant des délais d’expiration et une logique de compensation.

Un processus d’approbation est un exemple de processus d’entreprise impliquant une interaction humaine. L’approbation d’un manager peut être requise si une note de frais dépasse un certain montant. Si le manager n’approuve pas cette note de frais sous 72 heures (par exemple, s’il est en vacances), un processus d’escalade est déclenché pour obtenir l’approbation d’une autre personne (par exemple, le supérieur hiérarchique de ce manager).

![Schéma du modèle d’interaction humaine](./media/durable-functions-concepts/approval.png)

Vous pouvez implémenter le modèle utilisé dans cet exemple à l’aide d’une fonction d’orchestrateur. L’orchestrateur utilise un [minuteur durable](durable-functions-timers.md) pour demander l’approbation. L’orchestrateur procède à l’escalade si le délai d’expiration est atteint. L’orchestrateur attend un [événement externe](durable-functions-external-events.md), par exemple une notification générée par une interaction humaine.

Les exemples suivants créent un processus d’approbation illustrant le modèle d’interaction humaine :

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Pour créer le minuteur durable, appelez `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). La notification est reçue par `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Ensuite, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) est appelée pour déterminer s’il faut procéder à l’escalade (le délai d’expiration est atteint en premier) ou traiter l’approbation (approbation reçue avant la fin du délai d’expiration).

Un client externe peut remettre la notification d’événement à une fonction d’orchestrateur en attente au moyen [d’APIS HTTP intégrées](durable-functions-http-api.md#raise-event) ou de l’API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) à partir d’une autre fonction :

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>Modèle 6 : Agrégateur (préversion)

Le sixième modèle repose sur l’agrégation de données d’événement sur une période au sein d’une seule *entité* adressable. Dans ce modèle, les données agrégées peuvent provenir de plusieurs sources, être transmises par lots ou être dispersées sur de longues périodes. L’agrégateur devra peut-être effectuer une action sur les données d’événement à leur arrivée, et des clients externes devront peut-être interroger les données agrégées.

![Schéma de l’agrégateur](./media/durable-functions-concepts/aggregator.png)

L’implémentation de ce modèle avec des fonctions normales sans état fait naître un défi de taille : le contrôle d’accès concurrentiel. Vous devrez non seulement vous soucier du risque de modification des mêmes données par plusieurs threads au même moment, mais également veiller à ce que l’agrégateur s’exécute sur une seule machine virtuelle à la fois.

À l’aide d’une [fonction d’entité durable](durable-functions-preview.md#entity-functions), vous pouvez implémenter ce modèle facilement par le biais d’une fonction unique.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Les entités durables peuvent également être modélisées en tant que classes .NET. Ce modèle peut être utile si la liste des opérations est fixe et devient volumineuse. L’exemple suivant est une implémentation équivalente de l’entité `Counter` à l’aide de classes et de méthodes .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Les clients peuvent empiler les *opérations* dans le cadre d’une fonction d’entité (processus également appelé « signalisation ») à l’aide de la [liaison du client d’entité](durable-functions-bindings.md#entity-client).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Les proxies générés dynamiquement sont également disponibles pour signaler les entités de type sécurisé. En plus de la signalisation, les clients peuvent aussi interroger l’état d’une fonction d’entité à l’aide de [méthodes de type sécurisé](durable-functions-bindings.md#entity-client-usage) sur la liaison du client d’orchestration.

> [!NOTE]
> Actuellement, les fonctions d’entité sont disponibles uniquement dans .NET dans le cadre de la [préversion de Durable Functions 2.0](durable-functions-preview.md).

## <a name="the-technology"></a>La technologie

En arrière-plan, l’extension Durable Functions repose sur le [Framework Durable Task](https://github.com/Azure/durabletask), une bibliothèque open source sur GitHub utilisée pour la génération de workflows dans le code. Tout comme Azure Functions est l’évolution serverless d’Azure WebJobs, Durable Functions est l’évolution serverless de l’infrastructure Durable Task Framework. Microsoft et d’autres organisations utilisent couramment l’infrastructure Durable Task Framework pour automatiser les processus critiques. Il convient parfaitement à l’environnement Azure Functions sans serveur.

## <a name="code-constraints"></a>Contraintes du code

Pour pouvoir fournir des garanties d’exécution longue et fiable, les fonctions orchestrator possèdent un ensemble de règles de codage qui doivent être suivies. Pour plus d’informations, consultez l’article [Contraintes du code des fonctions orchestrator](durable-functions-code-constraints.md).

## <a name="billing"></a>Facturation

Durable Functions suit le même modèle de facturation qu’Azure Functions. Pour plus d’informations, consultez [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Lors de l’exécution de fonctions orchestrator dans le [plan de consommation](../functions-scale.md#consumption-plan) Azure Functions, il est nécessaire de connaître certains comportements de facturation. Pour plus d’informations sur ces comportements, consultez l’article [Facturation Durable Functions](durable-functions-billing.md).

## <a name="jump-right-in"></a>Démarrage rapide

Vous pouvez démarrer avec Durable Functions en moins de dix minutes en suivant l’un de ces tutoriels de démarrage rapide propres à chaque langage :

* [C# avec Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript avec Visual Studio Code](quickstart-js-vscode.md)

Dans les deux guides de démarrage rapide, vous créez et testez localement une fonction durable « hello world ». Vous allez ensuite publier le code de la fonction dans Azure. La fonction que vous créez orchestre et chaîne des appels à d’autres fonctions.

## <a name="learn-more"></a>En savoir plus

La vidéo suivante présente les avantages d’utiliser Durable Functions :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Pour obtenir une discussion plus approfondie sur Durable Functions et la technologie sous-jacente, regardez la vidéo suivante (elle est consacrée à .NET, mais les concepts s’appliquent également à d’autres langages prises en charge) :

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Durable Functions est une extension avancée d’[Azure Functions](../functions-overview.md) et ne convient donc pas à toutes les applications. Pour une comparaison avec d’autres technologies d’orchestration Azure, consultez [Comparer Azure Functions et Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Fonctionnalités et types de fonctions Durable Functions](durable-functions-types-features-overview.md)
