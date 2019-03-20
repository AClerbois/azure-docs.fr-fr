---
title: Tests unitaires de l’extension Fonctions durables Azure
description: Découvrez comment effectuer des tests unitaires sur l’extension Fonctions durables.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094073"
---
# <a name="durable-functions-unit-testing"></a>Tests unitaires de l’extension Fonctions durables

Le test unitaire est une partie importante des pratiques de développement de logiciels modernes. Les tests unitaires vérifient le comportement de la logique métier et offrent une protection contre l’introduction, dans le futur, de changements importants qui passent inaperçus. Étant donné que l’extension Fonctions durables peut aisément se complexifier, les tests unitaires permettent d’éviter les changements importants. Les sections suivantes expliquent comment effectuer des tests unitaires sur les trois types de fonctions : Client d’orchestration, Orchestrateur et Fonctions d’activité.

## <a name="prerequisites"></a>Conditions préalables

Les exemples de cet article exigent de connaître les concepts et frameworks suivants :

* Test des unités

* Fonctions durables

* [xUnit](https://xunit.github.io/) : framework de test

* [moq](https://github.com/moq/moq4) : framework de simulation

## <a name="base-classes-for-mocking"></a>Classes de base pour la simulation

La simulation est prise en charge par le biais de trois classes abstraites dans Durable Functions :

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Ces classes sont des classes de base pour [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) et [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) qui définissent les méthodes Client d’orchestration, Orchestrateur et Activité. Les objets fictifs définissent un comportement attendu pour les méthodes de la classe de base afin que le test unitaire puisse vérifier la logique métier. Il existe un flux de travail en deux étapes pour le test unitaire de la logique métier dans le Client d’orchestration et l’Orchestrateur :

1. Utilisez les classes de base à la place de l’implémentation concrète lors de la définition de signatures du Client d’orchestration et de l’Orchestrateur.
2. Dans les tests unitaires, simulez le comportement des classes de base et vérifiez la logique métier.

Pour plus d’informations sur le test des fonctions qui utilisent la liaison du client d’orchestration et la liaison du déclencheur de l’orchestrateur, consultez les paragraphes suivants.

## <a name="unit-testing-trigger-functions"></a>Fonctions de déclencheur de test unitaire

Dans cette section, le test unitaire valide la logique de la fonction de déclencheur HTTP suivante pour démarrer de nouvelles orchestrations.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

La tâche du test unitaire est de vérifier la valeur de l’en-tête `Retry-After` fourni dans la charge utile de réponse. Le test unitaire simule alors certaines des méthodes [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) pour garantir un comportement prévisible.

Tout d’abord, un élément fictif de la classe de base, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html), est exigé. L’élément fictif peut être une nouvelle classe qui implémente [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Toutefois, l’utilisation d’un framework de simulation comme [moq](https://github.com/moq/moq4) simplifie le processus :

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

La méthode `StartNewAsync` est simulée pour retourner un ID d’instance connu.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

`CreateCheckStatusResponse` est ensuite simulé pour toujours retourner une réponse HTTP 200 vide.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` est également simulé :

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

À présent, la méthode `Run` est appelée à partir du test unitaire :

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 La dernière étape consiste à comparer la sortie avec la valeur attendue :

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Une fois toutes les étapes combinées, le test unitaire a le code suivant :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Fonctions d’orchestrateur de test unitaire

Les fonctions d’orchestrateur sont encore plus intéressantes pour le test unitaire, car elles ont généralement beaucoup plus de logique métier.

Dans cette section, les tests unitaires valident la sortie de la fonction d’orchestrateur `E1_HelloSequence` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Le code de test unitaire commence par créer un objet fictif :

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Les appels de méthode d’activité sont alors simulés :

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Le test unitaire appelle ensuite la méthode`HelloSequence.Run` :

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Enfin, la sortie est validée :

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Une fois toutes les étapes combinées, le test unitaire a le code suivant :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Fonctions d’activité de test unitaire

Les fonctions d’activité peuvent faire l’objet d’un test unitaire de la même façon que les fonctions non durables.

Dans cette section, le test unitaire valide le comportement de la fonction d’activité `E1_SayHello` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

De son côté, le test unitaire vérifie le format de la sortie. Les tests unitaires peuvent utiliser les types de paramètre directement ou simuler la classe [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [En savoir plus sur moq](https://github.com/Moq/moq4/wiki/Quickstart)
