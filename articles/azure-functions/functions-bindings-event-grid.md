---
title: Déclencheur Event Grid pour Azure Functions
description: Découvrez comment gérer les événements Event Grid dans Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 7e0fb3cee8d4ec72e1ec44f7444264fabb1dd202
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724728"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Déclencheur Event Grid pour Azure Functions

Cet article explique comment gérer les événements [Event Grid](../event-grid/overview.md) dans Azure Functions.

Event Grid est un service Azure qui envoie des requêtes HTTP de notification au sujet des événements qui surviennent dans les *éditeurs*. L’éditeur d’un événement est le service ou la ressource qui est à l’origine de l’événement. Par exemple, un compte de Stockage Blob Azure est un éditeur, et [un chargement ou une suppression d’objet blob un événement](../storage/blobs/storage-blob-event-overview.md). [Certains services Azure intègrent la prise en charge de la publication d’événements sur Event Grid](../event-grid/overview.md#event-sources). 

Les *gestionnaires* d’événements reçoivent et traitent les événements. Azure Functions est l’un des nombreux [services Azure qui intègrent la prise en charge de la gestion des événements Event Grid](../event-grid/overview.md#event-handlers). Cet article explique comment utiliser un déclencheur Event Grid pour appeler une fonction à la réception d’un événement en provenance d’Event Grid.

Si vous préférez, vous pouvez utiliser un déclencheur HTTP pour gérer les événements Event Grid ; consultez la section [Utiliser un déclencheur HTTP comme déclencheur Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) dans la suite de cet article.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Le déclencheur Event Grid est fourni dans le package [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, version 1.x. Le code source pour le package se trouve dans le référentiel [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Le déclencheur Event Grid est fourni dans le package [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, version 2.x. Le code source pour le package se trouve dans le référentiel [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemples

Consultez l’exemple de déclencheur Event Grid correspondant au langage souhaité :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Vous trouverez un exemple de déclencheur HTTP dans la section [Guide pratique pour utiliser un déclencheur HTTP](#use-an-http-trigger-as-an-event-grid-trigger) dans la suite de cet article.

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) Functions 1.x liée à `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) Functions 2.x liée à `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.Data.ToString());
        }
    }
}
```

Pour plus d’informations, consultez [Packages](#packages), [Attributs](#attributes), [Configuration](#configuration), and [Utilisation](#usage).

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Voici le code de script C# Functions 1.x qui lie à `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Voici le code de script C# Functions 2.x qui lie à `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.Data.ToString());
}
```

Pour plus d’informations, consultez [Packages](#packages), [Attributs](#attributes), [Configuration](#configuration), and [Utilisation](#usage).

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs).

Voici un attribut `EventGridTrigger` dans une signature de méthode :

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*. Il n’y a aucun paramètre de constructeur ni aucune propriété à définir dans l’attribut `EventGridTrigger`.

|Propriété function.json |Description|
|---------|---------|----------------------|
| **type** | Obligatoire : doit être défini sur `eventGridTrigger`. |
| **direction** | Obligatoire : doit être défini sur `in`. |
| **name** | Obligatoire : nom de variable utilisé dans le code de fonction pour le paramètre qui reçoit les données de l’événement. |

## <a name="usage"></a>Usage

Pour les fonctions C# et F# dans Azure Functions 1.x, vous pouvez utiliser les types de paramètres suivants pour le déclencheur Event Grid :

* `JObject`
* `string`

Pour les fonctions C# et F# dans Azure Functions 2.x, vous pouvez également utiliser le type de paramètre suivant pour le déclencheur Event Grid :

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Définit les propriétés pour les champs communs à tous les types d’événements.

> [!NOTE]
> Dans Functions v1, si vous essayez de lier à `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, le compilateur affiche un message « déprécié » et vous conseille d’utiliser `Microsoft.Azure.EventGrid.Models.EventGridEvent` à la place. Pour utiliser le type le plus récent, référencez le package NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) et qualifiez entièrement le nom de type `EventGridEvent` en le faisant précéder de `Microsoft.Azure.EventGrid.Models`. Pour plus d’informations sur la façon de référencer des packages NuGet dans une fonction de script C#, consultez [Utiliser des packages NuGet](functions-reference-csharp.md#using-nuget-packages).

Pour les fonctions JavaScript, le paramètre nommé par la propriété *function.json* `name` comporte une référence à l’objet de l’événement.

## <a name="event-schema"></a>Schéma d’événement

Les données d’un événement Event Grid sont réceptionnées sous la forme d’un objet JSON dans le corps d’une requête HTTP, comme dans l’exemple suivant :

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Cet exemple est un tableau comportant un seul élément. Event Grid envoie toujours un tableau et peut y inclure plusieurs événements. Le runtime appelle la fonction une fois par élément du tableau.

Les propriétés de niveau supérieur présentes dans les données JSON de l’événement sont les mêmes quel que soit le type d’événement, tandis que la valeur de la propriété `data` est propre à chaque type d’événement. L’exemple correspond à un événement de Stockage Blob.

Vous trouverez des explications sur les propriétés communes et propres aux événements dans la section [Propriétés des événements](../event-grid/event-schema.md#event-properties) de la documentation Event Grid.

Le type `EventGridEvent` définit uniquement les propriétés de niveau supérieur ; la propriété `Data` est un `JObject`. 

## <a name="create-a-subscription"></a>Création d’un abonnement

Pour pouvoir recevoir des requêtes HTTP Event Grid, créez un abonnement Event Grid spécifiant l’URL de point de terminaison qui appelle la fonction.

### <a name="azure-portal"></a>Portail Azure

Pour les fonctions développées sur le Portail Azure avec le déclencheur Event Grid, sélectionnez **Ajouter un abonnement Event Grid**.

![Créer un abonnement sur le Portail](media/functions-bindings-event-grid/portal-sub-create.png)

Ce lien ouvre la page **Créer un abonnement aux événements** sur le Portail, en préremplissant l’URL de point de terminaison.

![URL de point de terminaison préremplie](media/functions-bindings-event-grid/endpoint-url.png)

Pour plus d’informations sur la création d’abonnements à l’aide du Portail Azure, consultez la section [Créer un événement personnalisé - Portail Azure](../event-grid/custom-event-quickstart-portal.md) dans la documentation Event Grid.

### <a name="azure-cli"></a>Azure CLI

Pour créer un abonnement avec [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), utilisez la commande [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create).

La commande a besoin de l’URL de point de terminaison qui appelle la fonction. L’exemple suivant illustre le modèle d’URL :

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

La clé système est une clé d’autorisation qui doit être incluse dans l’URL de point de terminaison d’un déclencheur Event Grid. La section suivante explique comment l’obtenir.

Voici un exemple d’abonnement à un compte de Stockage Blob (avec un espace réservé pour la clé système) :

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Pour plus d’informations sur la création d’un abonnement, consultez la section [Guide de démarrage rapide sur le Stockage Blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou les autres guides de démarrage rapide Event Grid.

### <a name="get-the-system-key"></a>Obtenir la clé système

Vous pouvez obtenir la clé système à l’aide de l’API suivante (HTTP GET) :

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Il s’agit d’une API d’administration, qui a donc besoin de votre [clé d’administration](functions-bindings-http-webhook.md#authorization-keys). Ne confondez pas la clé système (permettant d’appeler une fonction de déclenchement Event Grid) avec la clé d’administration (servant à effectuer des tâches d’administration sur l’application de fonction). Veillez à utiliser la clé système pour vous abonner à une rubrique Event Grid.

Voici un exemple de réponse fournissant la clé système :

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Pour plus d’informations, consultez la section [Clés d’autorisation](functions-bindings-http-webhook.md#authorization-keys) dans l’article de référence sur les déclencheurs HTTP. 

Vous pouvez également envoyer une requête HTTP PUT pour spécifier la valeur de la clé vous-même.

## <a name="local-testing-with-requestbin"></a>Tests locaux avec RequestBin

> [!NOTE]
> Le site RequestBin n’est pas disponible pour le moment, mais vous pouvez utiliser cette approche avec le site https://hookbin.com à la place. Si ce site est arrêté, vous pouvez utiliser [ngrok](#local-testing-with-ngrok).

Pour qu’un déclencheur Event Grid soit testé en local, les requêtes HTTP Event Grid doivent partir de leur origine dans le cloud et arriver sur l’ordinateur local. Vous pouvez pour cela capturer les requêtes en ligne et les renvoyer manuellement sur votre ordinateur local :

2. [Créez un point de terminaison RequestBin](#create-a-RequestBin-endpoint).
3. [Créez un abonnement Event Grid](#create-an-event-grid-subscription) qui envoie les événements au point de terminaison RequestBin.
4. [Générez une requête](#generate-a-request) et copiez le corps de la requête à partir du site RequestBin.
5. [Publiez (POST) manuellement la requête](#manually-post-the-request) sur l’URL localhost de votre fonction de déclenchement Event Grid.

À l’issue des tests, vous pourrez utiliser le même abonnement en production en mettant à jour le point de terminaison. Utilisez la commande Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-a-requestbin-endpoint"></a>Créer un point de terminaison RequestBin

RequestBin est un outil open source qui accepte les requêtes HTTP et montre le corps de la requête. L’URL http://requestb.in fait l’objet d’un traitement spécial de la part d’Azure Event Grid. Pour faciliter les tests, Event Grid envoie des événements à l’URL RequestBin sans avoir besoin d’une réponse correcte aux requêtes de validation d’abonnement. Un autre outil de test reçoit le même traitement : http://hookbin.com.

RequestBin n’est pas destiné à une utilisation avec débit élevé. Si vous envoyez plusieurs événements par push en simultané, vous pouvez ne pas voir tous les événements dans l’outil.

Créez un point de terminaison.

![Créer un point de terminaison RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Copiez l’URL de point de terminaison.

![Copier le point de terminaison RequestBin](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Créer un abonnement Event Grid

Créez un abonnement Event Grid du type à tester, et donnez-lui votre point de terminaison RequestBin. Pour plus d’informations sur la création d’abonnements, consultez la section [Créer un abonnement](#create-a-subscription) au début de cet article.

### <a name="generate-a-request"></a>Générer une requête

Déclenchez un événement qui génèrera du trafic HTTP sur votre point de terminaison RequestBin.  Par exemple, si vous avez créé un abonnement au Stockage Blob, chargez ou supprimez un objet blob. Lorsqu’une requête s’affiche sur votre page RequestBin, copiez le corps de la requête.

La requête de validation d’abonnement sera reçue la première ; ignorez toutes les requêtes de validation et copiez la requête d’événement.

![Copier le corps de la requête à partir de RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Publier (POST) manuellement la requête

Exécutez votre fonction Event Grid en local.

Utilisez un outil comme [Postman](https://www.getpostman.com/) ou [curl](https://curl.haxx.se/docs/httpscripting.html) pour créer une requête HTTP POST :

* Définissez un en-tête `Content-Type: application/json`.
* Définissez un en-tête `aeg-event-type: Notification`.
* Collez les données RequestBin dans le corps de la requête. 
* Publiez (POST) sur l’URL de votre fonction de déclenchement Event Grid, suivant ce modèle :

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

Le paramètre `functionName` doit être le nom spécifié dans l’attribut `FunctionName`.

Les captures d’écran suivantes montrent les en-têtes et le corps de la requête dans Postman :

![En-têtes dans Postman](media/functions-bindings-event-grid/postman2.png)

![Corps de la requête dans Postman](media/functions-bindings-event-grid/postman.png)

La fonction de déclenchement Event Grid s’exécute et affiche des journaux de ce type :

![Exemple de journaux d’une fonction de déclenchement Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Tests locaux avec ngrok

Une autre possibilité, pour tester un déclencheur Event Grid en local, consiste à automatiser la connexion HTTP entre Internet et l’ordinateur de développement. Vous pouvez pour cela utiliser un outil open source nommé [ngrok](https://ngrok.com/) :

3. [Créez un point de terminaison ngrok](#create-an-ngrok-endpoint).
4. [Exécutez la fonction de déclenchement Event Grid](#run-the-event-grid-trigger-function).
5. [Créez un abonnement Event Grid](#create-a-subscription) qui envoie les événements au point de terminaison ngrok.
6. [Déclenchez un événement](#trigger-an-event).

À l’issue des tests, vous pourrez utiliser le même abonnement en production en mettant à jour le point de terminaison. Utilisez la commande Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-an-ngrok-endpoint"></a>Créer un point de terminaison ngrok

Téléchargez *ngrok.exe* sur [ngrok](https://ngrok.com/), et exécutez-le avec la commande suivante :

```
ngrok http -host-header=localhost 7071
```

Le paramètre -host-header est nécessaire, car le runtime de la fonction attend des requêtes de localhost en cas d’exécution sur localhost. 7071 est le numéro de port par défaut pour les exécutions en local.

La commande crée une sortie de ce type :

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Vous utiliserez l’URL https://{sous-domaine}.ngrok.io pour votre abonnement Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Exécuter la fonction de déclenchement Event Grid

L’URL ngrok ne fait l’objet d’aucun traitement spécial de la part d’Event Grid ; votre fonction doit donc être en cours d’exécution en local lors de la création de l’abonnement. À défaut, la réponse de validation ne serait pas envoyée et la création de l’abonnement échouerait.

### <a name="create-a-subscription"></a>Création d’un abonnement

Créez un abonnement Event Grid du type à tester, et donnez-lui votre point de terminaison ngrok suivant ce modèle :

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

Le paramètre `functionName` doit être le nom spécifié dans l’attribut `FunctionName`.

Voici un exemple utilisant Azure CLI :

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Pour plus d’informations sur la création d’abonnements, consultez la section [Créer un abonnement](#create-a-subscription) au début de cet article.

### <a name="trigger-an-event"></a>Déclencher un événement

Déclenchez un événement qui génèrera du trafic HTTP sur votre point de terminaison ngrok.  Par exemple, si vous avez créé un abonnement au Stockage Blob, chargez ou supprimez un objet blob.

La fonction de déclenchement Event Grid s’exécute et affiche des journaux de ce type :

![Exemple de journaux d’une fonction de déclenchement Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Utiliser un déclencheur HTTP comme déclencheur Event Grid

Les événements Event Grid sont reçus en tant que requêtes HTTP, ce qui permet de les gérer à l’aide d’un déclencheur HTTP au lieu d’un déclencheur Event Grid, par exemple, afin de mieux maîtriser l’URL de point de terminaison qui appelle la fonction. 

Si vous utilisez un déclencheur HTTP, vous devrez écrire du code pour accomplir les actions que le déclencheur Event Grid effectue automatiquement :

* Envoyer une réponse de validation à une [requête de validation d’abonnement](../event-grid/security-authentication.md#webhook-event-delivery).
* Appeler la fonction une fois par élément du tableau d’événements contenu dans le corps de la requête.

L’exemple de code C# suivant pour un déclencheur HTTP simule le comportement d’un déclencheur Event Grid :

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

L’exemple de code JavaScript suivant pour un déclencheur HTTP simule le comportement d’un déclencheur Event Grid :

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: JSON.stringify({validationResponse: messages[0].data.validationCode}) }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Le code de gestion des événements s’intègre à la boucle par le biais du tableau `messages`.

Pour plus d’informations sur l’URL à utiliser pour appeler la fonction en local ou en cas d’exécution dans Azure, consultez la [documentation de référence des liaisons de déclencheurs HTTP](functions-bindings-http-webhook.md). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [En savoir plus sur Event Grid](../event-grid/overview.md)
