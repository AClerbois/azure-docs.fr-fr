---
title: Surveiller l’exécution des fonctions Azure
description: Découvrez comment utiliser Azure Application Insights avec Azure Functions pour surveiller l’exécution des fonctions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 582e4d81851d570f99d25d626a1db8a9f5e98231
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881330"
---
# <a name="monitor-azure-functions"></a>Surveiller l’exécution des fonctions Azure

[Azure Functions](functions-overview.md) est intégré à [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) pour surveiller des fonctions. Cet article vous explique comment configurer Azure Functions de façon à envoyer des fichiers journaux générés par le système à Application Insights.

Nous vous recommandons d’utiliser Application Insights, car il collecte les données liées aux journaux, performances et erreurs. Il détecte automatiquement les anomalies de performances et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre la manière dont vos fonctions sont utilisées. Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité. Vous pouvez également utiliser Application Insights lors du développement d'un projet d'application de fonction local. Pour plus d’informations, consultez [Présentation d’Application Insights](../azure-monitor/app/app-insights-overview.md).

L’instrumentation Application Insights requise étant intégrée à Azure Functions, une clé d'instrumentation valide permet de connecter votre application de fonction à une ressource Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Tarification et limites d’Application Insights

Vous pouvez essayer gratuitement l’intégration d’Application Insights avec les applications de fonctions. La quantité de données pouvant être traitée quotidiennement à titre gratuit est limitée. Il est possible que cette limite soit atteinte lors du test. Azure envoie des notifications sur le portail et par e-mail lorsque la limite quotidienne est proche. Si vous manquez ces alertes et atteignez la limite fixée, les nouveaux journaux d’activité n’apparaîtront pas dans les requêtes Application Insights. N’oubliez pas ces limites pour éviter de passer du temps à résoudre des problèmes superflus. Pour plus d’informations, consultez l’article [Gérer la tarification et le volume de données dans Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Activer l’intégration à Application Insights

Pour qu’une application de fonction envoie des données à Application Insights, elle a besoin de connaître la clé d’instrumentation d’une ressource Application Insights. Cette clé doit se trouver dans un paramètre d’application nommé **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nouvelle application de fonction dans le portail

Lorsque vous [créez votre application de fonction dans le portail Azure](functions-create-first-azure-function.md), l'intégration d’Application Insights est activée par défaut. La ressource Application Insights, qui porte le même nom que votre application de fonction, est créée dans la même région ou dans la région la plus proche.

Pour examiner la ressource Application Insights en cours de création, sélectionnez-la de manière à développer la fenêtre **Application Insights**. Vous pouvez modifier le **Nouveau nom de ressource** ou choisir un autre **Emplacement** dans une [Zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez stocker vos données.

![Activer Application Insights pendant la création d’une application de fonction](media/functions-monitoring/enable-ai-new-function-app.png)

Lorsque vous sélectionnez **Créer**, une ressource Application Insights est créée avec votre application de fonction, et `APPINSIGHTS_INSTRUMENTATIONKEY` est défini dans les paramètres de l’application. Tout est prêt.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Ajouter à une application de fonction existante 

Lorsque vous créez une application de fonction en utilisant [Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md) ou [Visual Studio Code](functions-create-first-function-vs-code.md), vous devez créer la ressource Application Insights. Vous pouvez ensuite ajouter la clé d’instrumentation de cette ressource en tant que paramètre d’application dans votre application de fonction.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Les premières versions de Functions utilisaient la surveillance intégrée, ce qui n’est plus recommandé. Lorsque vous activez l’intégration d’Application Insights pour un telle application de fonction, vous devez également [désactiver la journalisation intégrée](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Affichage des données de télémétrie dans l’onglet Surveiller

Une fois l'[intégration d’Application Insights activée](#enable-application-insights-integration), vous pouvez afficher des données de télémétrie dans l'onglet **Surveiller**.

1. Dans la page d’application de fonction, sélectionnez une fonction exécutée au moins une fois après la configuration d’Application Insights. Sélectionnez ensuite l'onglet **Surveiller**.

   ![Sélectionner l’onglet Surveiller](media/functions-monitoring/monitor-tab.png)

1. Sélectionnez **Actualiser** régulièrement jusqu’à ce que la liste d’appels de fonction s’affiche.

   Cette opération peut prendre jusqu’à cinq minutes, le temps que le client de télémétrie organise les données par lots pour les transmettre au serveur. (Ce délai ne s’applique pas au [Flux de métriques temps réel](../azure-monitor/app/live-stream.md). Le service se connecte à l’hôte Functions lorsque vous chargez la page, de sorte que les journaux d’activité sont transmis directement sur la page.)

   ![Liste d’appels](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Pour afficher les journaux d’activité liés à un appel de fonction spécifique, sélectionnez le lien de la colonne **Date** correspondant à cet appel.

   ![Lien des détails d’appel](media/functions-monitoring/invocation-details-link-ai.png)

   La sortie de journalisation pour cet appel s’affiche dans une nouvelle page.

   ![Détails des appels](media/functions-monitoring/invocation-details-ai.png)

Comme vous pouvez le constater, les deux pages affichent un lien **Exécuter dans Application Insights** vers la requête Application Insights Analytics qui récupère les données.

![Exécuter dans Application Insights](media/functions-monitoring/run-in-ai.png)

La requête suivante s’affiche. Comme vous pouvez le constater, la liste d'appels se limite aux 30 derniers jours. Cette liste affiche uniquement 20 lignes (`where timestamp > ago(30d) | take 20`). La liste des détails d'appels porte sur les 30 derniers jours, sans limite.

![Liste d’appels Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Pour plus d’informations, consultez la section [Interroger les données de télémétrie](#query-telemetry-data) dans la suite de cet article.

## <a name="view-telemetry-in-application-insights"></a>Afficher les données de télémétrie dans Application Insights

Pour ouvrir Application Insights à partir d’une application de fonction dans le portail Azure, accédez à la page **Vue d’ensemble** de l'application de fonction. Sous **Fonctionnalités configurées**, sélectionnez **Application Insights**.

![Ouvrir Application Insights à partir de la page Vue d'ensemble de l'application de fonction](media/functions-monitoring/ai-link.png)

Pour plus d’informations sur l’utilisation d’Application Insights, consultez la [documentation d’Application Insights](https://docs.microsoft.com/azure/application-insights/). Cette section présente des exemples montrant comment afficher les données dans Application Insights. Si vous êtes déjà familiarisé avec Application Insights, vous pouvez passer directement aux [sections sur la configuration et la personnalisation des données de télémétrie](#configure-categories-and-log-levels).

![Onglet Vue d'ensemble d’Application Insights](media/functions-monitoring/metrics-explorer.png)

Les domaines d’Application Insights suivants peuvent s'avérer utiles lors de l'évaluation du comportement, du niveau de performance et des erreurs de vos fonctions :

| Tab | Description |
| ---- | ----------- |
| **[Échecs](../azure-monitor/app/asp-net-exceptions.md)** |  Créez des graphiques et des alertes basés sur les échecs de fonction et les exceptions de serveur. Le **Nom de l’opération** est le nom de la fonction. Les échecs de dépendances ne sont pas affichés, sauf si vous implémentez des données de télémétrie personnalisées pour les dépendances. |
| **[Niveau de performance](../azure-monitor/app/performance-counters.md)** | Analysez les problèmes liés au niveau de performance. |
| **Serveurs** | Affichez l’utilisation des ressources et le débit par serveur. Ces données peuvent être utiles pour déboguer les scénarios où les fonctions ralentissent vos ressources sous-jacentes. Les serveurs sont appelés **instances de rôle cloud**. |
| **[Métriques](../azure-monitor/app/metrics-explorer.md)** | Créez des graphiques et des alertes basés sur des métriques. Les métriques incluent le nombre d’appels de fonction, le délai d’exécution ainsi que les taux de réussite. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Affichez les données de métriques en temps réel. |

## <a name="query-telemetry-data"></a>Interroger les données de télémétrie

[Application Insights Analytics](../azure-monitor/app/analytics.md) vous donne accès à toutes les données de télémétrie sous forme de tables de base de données. Analytics fournit un langage de requête pour l’extraction, la manipulation et la visualisation des données.

![Sélectionner Analytics](media/functions-monitoring/select-analytics.png)

![Exemple Analytics](media/functions-monitoring/analytics-traces.png)

Voici un exemple de requête qui montre la distribution des demandes par nœud Worker au cours des 30 dernières minutes.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Les tables disponibles sont affichées sous l’onglet **Schéma** situé à gauche. Les données générées par les appels de fonction sont disponibles dans les tables suivantes :

| Table | Description |
| ----- | ----------- |
| **traces** | Journaux d’activité créés par le runtime et le code de fonction. |
| **requests** | Une demande par appel de fonction. |
| **exceptions** | Toutes les exceptions levées par le runtime. |
| **customMetrics** | Nombre d’appels ayant réussi ou échoué, taux de réussite, durée. |
| **customEvents** | Événements suivis par le runtime, par exemple : requêtes HTTP qui déclenchent une fonction. |
| **performanceCounters** | Informations sur le niveau de performance des serveurs sur lesquels les fonctions sont en cours d’exécution. |

Les autres tables concernent les tests de disponibilité et les données de télémétrie client et navigateur. Vous pouvez implémenter une télémétrie personnalisée en vue d’y ajouter des données.

Dans chaque table, un champ `customDimensions` contient certaines des données spécifiques à Azure Functions.  Par exemple, la requête suivante récupère toutes les traces dont le niveau de journal est `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Le runtime fournit les champs `customDimensions.LogLevel` et `customDimensions.Category`. Vous pouvez fournir des champs supplémentaires dans les journaux d’activité que vous écrivez dans votre code de fonction. Consultez [Journalisation structurée](#structured-logging) plus loin dans cet article.

## <a name="configure-categories-and-log-levels"></a>Configurer les catégories et les niveaux de journal

Vous pouvez utiliser Application Insights sans en personnaliser la configuration. La configuration par défaut risque d’engendrer d'importants volumes de données. Si vous utilisez un abonnement Azure Visual Studio, vous risquez d’atteindre votre plafond de données pour Application Insights. Plus loin dans cet article, vous découvrirez comment configurer et personnaliser les données que vos fonctions envoient à Application Insights. La journalisation d'une application de fonction est configurée dans le fichier [host.json].

### <a name="categories"></a>Catégories

L’enregistreur d’événements d’Azure Functions inclut une *catégorie* par journal. La catégorie indique quelle partie du code d’exécution ou de votre code de fonction a écrit le journal. 

Les journaux d’activité créés par le runtime d’Azure Functions avec une catégorie qui commence par « Host ». Dans la version 1.x, les journaux d’activité `function started`, `function executed` et `function completed` ont la catégorie `Host.Executor`. À partir de la version 2.x, ils ont la catégorie `Function.<YOUR_FUNCTION_NAME>`.

Si vous écrivez des journaux d’activité dans votre code de fonction, la catégorie est `Function` dans la version 1.x du runtime d’Azure Functions. Dans la version 2.x, la catégorie est `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Niveaux de journal

L’enregistreur d’événements d’Azure Functions inclut également un *niveau de journal* avec chaque journal. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) est une énumération, et le code d’entier indique l’importance relative :

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Déboguer       | 1 |
|Information | 2 |
|Avertissement     | 3 |
|Error       | 4 |
|Critique    | 5\. |
|Aucun        | 6 |

Le niveau de journal `None` est expliqué dans la section suivante. 

### <a name="log-configuration-in-hostjson"></a>Configuration du journal dans host.json

Le fichier [host.json] configure la quantité de journalisation qu’une application de fonction envoie à Application Insights. Pour chaque catégorie, vous indiquez le niveau de journal minimal à envoyer. Il existe deux exemples : le premier exemple cible le [runtime Functions version 2.x](functions-versions.md#version-2x) (.NET Core) et le second exemple, le runtime version 1.x.

### <a name="version-2x"></a>Version 2.x

Le runtime v2.x utilise la [hiérarchie des filtres de journalisation .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Cet exemple montre comment configurer les règles suivantes :

* En ce qui concerne les journaux d’activité de la catégorie `Host.Results` ou `Function`, envoyer uniquement les niveaux `Error` et supérieurs à Application Insights. Les journaux d’activité pour les niveaux `Warning` et inférieurs sont ignorés.
* En ce qui concerne les journaux d’activité de la catégorie `Host.Aggregator`, envoyer tous les journaux d’activité à Application Insights. Le niveau du journal `Trace` est identique à ce que certains enregistreurs d’événements appellent `Verbose`, mais utilisez `Trace` dans le fichier [host.json].
* Pour tous les autres journaux d’activité, envoyer uniquement les niveaux `Information` et supérieurs à Application Insights.

La valeur de catégorie dans [host.json] contrôle la journalisation de toutes les catégories qui commencent par la même valeur. `Host` dans [host.json] contrôle la journalisation de `Host.General`, `Host.Executor`, `Host.Results`, etc.

Si [host.json] inclut plusieurs catégories qui commencent par la même chaîne, les plus longues sont traitées en priorité. Supposons que vous souhaitiez que la totalité du runtime à l’exception de `Host.Aggregator` soit journalisée au niveau `Error`, mais que `Host.Aggregator` soit journalisé au niveau `Information` :

### <a name="version-2x"></a>Version 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Pour supprimer tous les journaux d’activité relatifs à une catégorie, vous pouvez utiliser le niveau de journal `None`. Aucun journal d’activité n’est écrit avec cette catégorie, et il n’existe aucun niveau de journal d’activité au-dessus de celle-ci.

Les sections suivantes décrivent les principales catégories de journaux d’activité générées par le runtime. 

### <a name="category-hostresults"></a>Catégorie Host.Results

Ces journaux d’activité apparaissent sous la dénomination « requests » dans Application Insights. Ils indiquent la réussite ou l’échec d’une fonction.

![Graphique de demandes](media/functions-monitoring/requests-chart.png)

Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données.

### <a name="category-hostaggregator"></a>Catégorie Host.Aggregator

Ces journaux d’activité fournissent des nombres et des moyennes d’appels de fonction sur une période de temps [configurable](#configure-the-aggregator). La période par défaut est 30 secondes ou 1 000 résultats, selon la première de ces éventualités. 

Les journaux d’activité sont disponibles dans la table **customMetrics** dans Application Insights. Le nombre d’exécutions, le taux de réussite et la durée en sont des exemples.

![Requête portant sur customMetrics](media/functions-monitoring/custom-metrics-query.png)

Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données.

### <a name="other-categories"></a>Autres catégories

Tous les journaux d’activité des catégories autres que celles déjà mentionnées apparaissent dans la table **traces** dans Application Insights.

![Requête portant sur traces](media/functions-monitoring/analytics-traces.png)

Tous les journaux d’activité ayant des catégories commençant par `Host` sont écrits par le runtime d’Azure Functions. Les journaux d’activité « Function started » et « Function completed » ont la catégorie `Host.Executor`. Pour les exécutions ayant réussi, ces journaux d’activité sont de niveau `Information`. Les exceptions sont journalisées au niveau `Error`. En outre, le runtime crée des journaux d’activité de niveau `Warning`, par exemple, les messages de file d’attente envoyés à la file d’attente de messages incohérents.

Les journaux d’activité écrits par votre code de fonction ont la catégorie `Function` et peuvent avoir n’importe quel niveau de journal d’activité.

## <a name="configure-the-aggregator"></a>Configurer le paramètre d’agrégation

Comme indiqué dans la section précédente, le runtime agrège les données sur les exécutions de fonction sur une période de temps. La période par défaut est 30 secondes ou 1 000 exécutions, selon la première de ces éventualités. Vous pouvez configurer ce paramètre dans le fichier [host.json].  Voici un exemple :

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurer l’échantillonnage

Application Insights présente une fonctionnalité [d’échantillonnage](../azure-monitor/app/sampling.md) qui peut vous éviter de produire une quantité excessive de données de télémétrie portant sur les exécutions terminées aux heures de forte activité. Lorsque le taux d'exécutions entrantes dépasse un seuil spécifié, Application Insights commence à ignorer de manière aléatoire certaines exécutions entrantes. Par défaut, le nombre maximal d’exécutions par seconde est fixé à 20 (cinq dans la version 1.x). Vous pouvez configurer l’échantillonnage dans [host.json].  Voici un exemple :

### <a name="version-2x"></a>Version 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [L’échantillonnage](../azure-monitor/app/sampling.md) est activé par défaut. Si vous pensez qu’il vous manque des données, il vous faut peut-être simplement adapter les paramètres d’échantillonnage à votre scénario de surveillance.

## <a name="write-logs-in-c-functions"></a>Écrire des journaux d’activité dans des fonctions C#

Vous pouvez écrire des journaux d’activité dans votre code de fonction qui apparaissent sous forme de traces dans Application Insights.

### <a name="ilogger"></a>ILogger

Utilisez un paramètre [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) dans vos fonctions au lieu d’un paramètre `TraceWriter`. Les journaux d’activité créés à l’aide de `TraceWriter` sont destinés à Application Insights, mais `ILogger` vous permet d’effectuer une [journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’objet `ILogger` permet d’appeler les [méthodes d’extension `Log<level>` sur ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) pour créer des journaux d’activité. Le code suivant écrit des journaux d’activité `Information` ayant la catégorie « Function ».

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Journalisation structurée

C’est l’ordre des espaces réservés, pas leurs noms, qui détermine les paramètres utilisés dans le message du journal. Supposons que vous disposiez du code suivant :

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Si vous conservez la même chaîne de message et que vous inversez l’ordre des paramètres, les valeurs dans le texte du message résultant ne sont pas situées à la bonne place.

Ce mode de gestion des espaces réservés vous permet d’effectuer une journalisation structurée. Application Insights stocke les paires nom de paramètre/valeur et la chaîne de message. Ainsi, les arguments du message deviennent des champs pouvant faire l’objet de requêtes.

Si votre appel de méthode de l’enregistreur d’événements ressemble à l’exemple précédent, vous pouvez interroger le champ `customDimensions.prop__rowKey`. L’ajout du préfixe `prop__` évite toute collision entre les champs ajoutés par le runtime et les champs ajoutés par votre fonction de code.

Vous pouvez également exécuter une requête portant sur la chaîne de message d’origine en référençant le champ `customDimensions.prop__{OriginalFormat}`.  

Voici un exemple de représentation JSON des données `customDimensions` :

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Journalisation des métriques personnalisées

Dans les fonctions de script C#, vous pouvez utiliser la méthode d’extension `LogMetric` sur `ILogger` pour créer des métriques personnalisées dans Application Insights. Voici un exemple d’appel de méthode :

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ce code est une alternative à l’appel de `TrackMetric` à l’aide de l’API Application Insights pour .NET.

## <a name="write-logs-in-javascript-functions"></a>Écrire des journaux d’activité dans les fonctions JavaScript

Dans les fonctions Node.js, utilisez `context.log` pour écrire des journaux d’activité. La journalisation structurée n’est pas activée.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Journalisation des métriques personnalisées

Lors d'une exécution sur la [version 1.x](functions-versions.md#creating-1x-apps) du runtime Functions, les fonctions Node.js peuvent utiliser la méthode `context.log.metric` pour créer des métriques personnalisées dans Application Insights. Actuellement, cette méthode n’est pas prise en charge dans la version 2.x. Voici un exemple d’appel de méthode :

```javascript
context.log.metric("TestMetric", 1234);
```

Ce code est une alternative à l’appel de `trackMetric` à l’aide du SDK Node.js pour Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Journaliser des données de télémétrie personnalisées dans les fonctions C#

Vous pouvez utiliser le package NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pour envoyer des données de télémétrie personnalisées à Application Insights. L’exemple C# suivant utilise [l’API de télémétrie personnalisée](../azure-monitor/app/api-custom-events-metrics.md). L’exemple concerne une bibliothèque de classes .NET, mais le code Application Insights est le même pour le script C#.

### <a name="version-2x"></a>Version 2.x

Le runtime version 2.x utilise les nouvelles fonctionnalités d’Application Insights pour mettre automatiquement en corrélation les données de télémétrie avec l’opération en cours. Il est inutile de définir manuellement les champs de l’opération `Id`, `ParentId` ou `Name`.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Version 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

N’appelez pas `TrackRequest` ou `StartOperation<RequestTelemetry>`, afin d’éviter les doublons de requêtes pour un appel de fonction.  Le runtime d’Azure Functions effectue automatiquement le suivi des demandes.

Ne définissez pas `telemetryClient.Context.Operation.Id`. Ce paramètre global provoque une erreur de corrélation lorsqu’un grand nombre de fonctions s’exécutent en même temps. Au lieu de cela, créez une nouvelle instance de télémétrie (`DependencyTelemetry`, `EventTelemetry`) et modifiez sa propriété `Context`. Passez dans l’instance de télémétrie correspondant à la méthode `Track` sur `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Cette méthode permet de s'assurer que les données de télémétrie comportent les bonnes informations de corrélation pour l’appel de fonction actuel.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Journaliser des données de télémétrie personnalisées dans les fonctions JavaScript

Voici un exemple d’extrait de code qui envoie des données de télémétrie personnalisées avec le [SDK Node.js Application Insights](https://github.com/microsoft/applicationinsights-node.js) :

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Le paramètre `tagOverrides` définit `operation_Id` sur l’ID d'appel de la fonction. Ce paramètre permet de mettre en corrélation toutes les données de télémétrie générées automatiquement et personnalisées pour un appel de fonction donné.

## <a name="dependencies"></a>Les dépendances

Functions v2 collecte automatiquement les dépendances pour les requêtes HTTP, ServiceBus et SQL.

Vous pouvez écrire du code personnalisé pour afficher les dépendances. Pour plus d'informations, consultez l'exemple de code dans la [section relative aux données de télémétrie personnalisées C#](#log-custom-telemetry-in-c-functions). L’exemple de code entraîne un *mappage d’application* dans Application Insights, qui se présente comme suit :

![Mise en correspondance d’applications](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Signaler des problèmes

Pour signaler un problème avec l’intégration d’Application Insights dans Azure Functions, ou pour faire une suggestion ou une demande, [créez un problème dans GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Journaux d’activité en continu

Lors du développement d’une application, vous souhaitez souvent savoir ce qui est écrit dans les journaux presque en temps réel lors de l’exécution dans Azure.

Il existe deux façons d’afficher un flux des fichiers journaux générés par vos exécutions de fonction.

* **Streaming des journaux intégré** : la plateforme App Service vous permet d’afficher un flux de vos fichiers journaux d’application. Cela équivaut à la sortie observée quand vous déboguez vos fonctions au cours du [développement local](functions-develop-local.md) et quand vous utilisez l’onglet **Test** dans le portail. Toutes les informations basées sur les journaux sont affichées. Pour plus d’informations, consultez [Diffusion en continu des journaux d’activité](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Cette méthode de streaming ne prend en charge qu’une seule instance et ne peut pas être utilisée avec une application exécutée sur Linux dans un plan Consommation.

* **Flux de métriques temps réel** : quand votre application de fonction est [connectée à Application Insights](#enable-application-insights-integration), vous pouvez afficher les données des journaux et d’autres métriques en quasi temps réel dans le portail Azure à l’aide de [Flux de métriques temps réel](../azure-monitor/app/live-stream.md). Appliquez cette méthode lors de la supervision de fonctions s’exécutant sur plusieurs instances ou sur Linux dans un plan Consommation. Cette méthode utilise des [données échantillonnées](#configure-sampling).

Les flux de journaux peuvent être affichés à la fois dans le portail et dans la plupart des environnements de développement locaux. 

### <a name="portal"></a>Portail

Vous pouvez afficher les deux types de flux de journaux dans le portail.

#### <a name="built-in-log-streaming"></a>Streaming des journaux intégré

Pour afficher les journaux de diffusion en continu dans le portail, sélectionnez l'onglet **Fonctionnalités de la plateforme** dans votre application de fonction. Ensuite, sous **Surveillance**, sélectionnez **Diffusion en continu des journaux**.

![Activer les journaux de diffusion en continu dans le portail](./media/functions-monitoring/enable-streaming-logs-portal.png)

Cela permet de connecter votre application au service de diffusion en continu des journaux, et les journaux d'application s'affichent dans la fenêtre. Vous pouvez basculer entre **Journaux d’activité d’application** et **Journaux d’activité des serveurs Web**.  

![Afficher la diffusion de journaux d’activité en continu dans le portail](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Flux de métriques temps réel

Pour voir le flux de métriques temps réel de votre application, sélectionnez l’onglet **Vue d’ensemble** de votre application de fonction. Quand Application Insights est activé, un lien **Application Insights** est visible sous **Fonctionnalités configurées**. Ce lien vous mène à la page Application Insights de votre application.

Dans Application Insights, sélectionnez **Flux de métriques temps réel**. Les [entrées de journaux échantillonnées](#configure-sampling) sont affichées sous **Exemple de télémétrie**.

![Voir les flux de métriques temps réel dans le portail](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Vous pouvez activer les journaux de diffusion en continu en utilisant [Azure CLI](/cli/azure/install-azure-cli). Utilisez les commandes suivantes pour vous connecter, choisir votre abonnement et diffuser en continu les fichiers journaux :

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez activer les journaux de diffusion en continu en utilisant [Azure PowerShell](/powershell/azure/overview). Dans PowerShell, utilisez les commandes suivantes pour ajouter votre compte Azure, choisir votre abonnement et diffuser en continu les fichiers journaux :

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Désactiver la journalisation intégrée

Lorsque vous activez Application Insights, désactivez la journalisation intégrée qui utilise le Stockage Azure. La journalisation intégrée permet de tester des charges de travail légères, mais n'a pas vocation à être utilisée en production avec des charges importantes. Nous recommandons Application Insights à des fins de surveillance de la production. Si la journalisation intégrée est utilisée en production, l’enregistrement de journal peut être incomplet en raison d’une limitation du Stockage Azure.

Pour désactiver la journalisation intégrée, supprimez le paramètre d’application `AzureWebJobsDashboard`. Pour plus d’informations sur la suppression de paramètres d’application dans le portail Azure, consultez la section **Paramètres de l’application** dans [Comment gérer une application de fonction dans le portail Azure](functions-how-to-use-azure-function-app-settings.md#settings). Avant de supprimer le paramètre d’application, assurez-vous qu’aucune fonction existante dans la même application de fonction ne l’utilise pour les déclencheurs ou les liaisons du Stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Application Insights](/azure/application-insights/)
* [Journalisation ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
