---
title: Azure Application Insights pour les applications ASP.NET Core | Microsoft Docs
description: Superviser la disponibilité, les performances et l’utilisation des applications web ASP.NET Core.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: a48c2fdcce5126747f00cd3b901839864d438346
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058276"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pour applications ASP.NET Core

Cet article décrit comment activer Application Insights pour une application [ASP.NET Core](https://docs.microsoft.com/aspnet/core). Lorsque vous suivez les instructions de cet article, Application Insights collecte les requêtes, dépendances, exceptions, compteurs de performances, pulsations et journaux à partir de votre application ASP.NET Core.

L’exemple que nous utiliserons ici est une [application MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) qui cible `netcoreapp2.2`. Vous pouvez appliquer ces instructions à toutes les applications ASP.NET Core.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Le [SDK Application Insights pour ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) peut surveiller vos applications peu importe où et comment elles s’exécutent. Si votre application est en cours d’exécution et dispose d’une connectivité réseau vers Azure, les données de télémétrie peuvent être collectées. La surveillance Application Insights est prise en charge partout où .NET Core est pris en charge. La prise en charge couvre les éléments suivants :
* **Système d’exploitation** : Windows, Linux ou Mac.
* **Méthode d’hébergement** : dans le processus ou en dehors du processus. 
* **Méthode de déploiement** : dépendante de l’infrastructure ou autonome.
* **Serveur web** : IIS (Internet Information Server) ou Kestrel. 
* **Plateforme d’hébergement** : fonctionnalité Web Apps d’Azure App Service, machine virtuelle Azure, Docker, Azure Kubernetes Service (AKS), etc.
* **IDE** : Visual Studio, VS Code ou ligne de commande.

> [!NOTE]
> Si vous utilisez ASP.NET Core 3.0-preview avec Application Insights, utilisez la version [2.8.0-beta2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta2) ou une version ultérieure. Il s’agit de la seule version connue pour fonctionner correctement avec ASP.NET Core 3.0. De plus, l’intégration basée sur Visual Studio n’est pas encore prise en charge pour les applications ASP.NET Core 3.0.

## <a name="prerequisites"></a>Prérequis

- Application ASP.NET Core fonctionnelle. Si vous avez besoin créer une application ASP.NET Core, suivez ce [didacticiel ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Clé d’instrumentation Application Insights valide. Cette clé est requise pour envoyer les données de télémétrie à Application Insights. Si vous avez besoin créer une ressource Application Insights pour obtenir une instrumentation clé, consultez [Créer une ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Activer les données de télémétrie Application Insights côté serveur (Visual Studio)

1. Ouvrez votre projet dans Visual Studio.

    > [!TIP]
    > Si vous le souhaitez, vous pouvez configurer un contrôle de code source pour votre projet afin de suivre les modifications apportées par Application Insights. Pour activer le contrôle de code source, sélectionnez **Fichier** > **Ajouter au contrôle de code source**.

2. Sélectionnez **Projet** > **Ajouter Application Insights Telemetry**.

3. Sélectionnez **Prise en main**. Selon votre version de Visual Studio, le texte de la sélection peut varier légèrement. Certaines versions antérieures affichent un bouton **Démarrer gratuitement** à la place.

4. Sélectionnez votre abonnement. Ensuite, sélectionnez **Ressource** > **Inscrire**.

5. Après avoir ajouté Application Insights à votre projet, vérifiez que vous utilisez la dernière version stable du SDK. Accédez à **Projet** > **Gérer les packages NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Au besoin, choisissez **Mettre à jour**.

     ![Capture d’écran montrant où sélectionner le package Application Insights pour la mise à jour](./media/asp-net-core/update-nuget-package.png)

6. Si vous avez suivi l’info-bulle facultative et ajouté votre projet au contrôle de code source, accédez à **Affichage** > **Team Explorer** > **Modifications**. Sélectionnez ensuite chaque fichier pour afficher une comparaison des modifications apportées par les données de télémétrie Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Activer les données de télémétrie Application Insights côté serveur (hors Visual Studio)

1. Installez le [package NuGet du SDK Application Insights pour ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Nous vous recommandons de toujours utiliser la dernière version stable. Recherchez les notes de publication complètes pour le SDK sur le [référentiel GitHub open source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    L’exemple de code suivant montre les modifications à ajouter au fichier `.csproj` de votre projet.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Ajoutez `services.AddApplicationInsightsTelemetry();` à la méthode `ConfigureServices()` dans la classe `Startup`, comme dans cet exemple :

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Configurez la clé d’instrumentation.

    Bien que vous puissiez fournir la clé d’instrumentation en tant qu’argument à `AddApplicationInsightsTelemetry`, nous vous recommandons de la spécifier dans la configuration. L’exemple de code suivant montre comment spécifier une clé d’instrumentation dans `appsettings.json`. Assurez-vous que `appsettings.json` est copié dans le dossier racine de l’application lors de la publication.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Vous pouvez également spécifier la clé d’instrumentation dans une des variables d’environnement suivantes :

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Par exemple :

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    En règle générale, `APPINSIGHTS_INSTRUMENTATIONKEY` spécifie la clé d’instrumentation pour les applications déployées vers Web Apps.

    > [!NOTE]
    > Une clé d’instrumentation spécifiée dans le code prévaut sur la variable d’environnement `APPINSIGHTS_INSTRUMENTATIONKEY`, qui prévaut sur les autres options.

## <a name="run-your-application"></a>Exécuter votre application

Exécutez votre application et lancez des requêtes. Les données de télémétrie doivent être transmis à Application Insights maintenant. Le SDK Application Insights collecte automatiquement les données de télémétrie.

|Requêtes/dépendances |Détails|
|---------------|-------|
|Demandes | Requêtes web entrantes vers votre application. |
|HTTP ou HTTPS | Appels effectués avec `HttpClient`. |
|SQL | Appels effectués avec `SqlClient`. |
|[Stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Appels effectués avec le client Stockage Azure. |
|[Kit de développement logiciel (SDK) client EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 et versions ultérieures. |
|[Kit de développement logiciel (SDK) client ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 et versions ultérieures. |
|Azure Cosmos DB | Suivi automatique uniquement si HTTP/HTTPS est utilisé. Application Insights ne capture pas le mode TCP. |

### <a name="performance-counters"></a>Compteurs de performances

La prise en charge des [compteurs de performances](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) dans ASP.Net Core est limitée :

* Les versions 2.4.1 et ultérieures du kit SDK collectent les compteurs de performances si l’application s’exécute dans Azure Web Apps (Windows).
* Les versions 2.7.1 et ultérieures du kit SDK collectent les compteurs de performances si l’application s’exécute dans Windows et cible `NETSTANDARD2.0` ou version ultérieure.
* Pour les applications ciblant .NET Framework, toutes les versions du SDK prennent en charge les compteurs de performances.
* Les versions 2.8.0-beta3 et ultérieures du kit SDK prennent en charge le compteur processeur/mémoire dans Linux. Aucun autre compteur n’est pris en charge dans Linux. La méthode recommandée pour obtenir les compteurs système dans Linux (et dans d’autres environnements non-Windows) consiste à utiliser [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

[EventCounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) est une méthode multiplateforme permettant de publier et de consommer des compteurs dans .NET/.NET Core. Cette fonctionnalité existait avant, mais aucun fournisseur intégré n’avait publié ces compteurs. À compter de .NET Core 3.0, plusieurs compteurs sont publiés dès le départ, comme les compteurs CLR, les compteurs ASP.NET Core, etc.

Les versions 2.8.0-beta3 et ultérieures du kit SDK prennent en charge la collecte des compteurs EventCounter. Par défaut, le kit SDK collecte les compteurs suivants et ces compteurs peuvent être interrogés dans Metrics Explorer ou à l’aide d’une requête Analytics sous la table PerformanceCounter. Le nom des compteurs sera au format « Category|Counter ».

|Category | Compteur|
|---------------|-------|
|System.Runtime | cpu-usage |
|System.Runtime | working-set |
|System.Runtime | gc-heap-size |
|System.Runtime | gen-0-gc-count |
|System.Runtime | gen-1-gc-count |
|System.Runtime | gen-2-gc-count |
|System.Runtime | time-in-gc |
|System.Runtime | gen-0-size |
|System.Runtime | gen-1-size |
|System.Runtime | gen-2-size |
|System.Runtime | loh-size |
|System.Runtime | alloc-rate |
|System.Runtime | assembly-count |
|System.Runtime | exception-count |
|System.Runtime | threadpool-thread-count |
|System.Runtime | monitor-lock-contention-count |
|System.Runtime | threadpool-queue-length |
|System.Runtime | threadpool-completed-items-count |
|System.Runtime | active-timer-count |
|Microsoft.AspNetCore.Hosting | requests-per-second |
|Microsoft.AspNetCore.Hosting | total-requests |
|Microsoft.AspNetCore.Hosting | current-requests |
|Microsoft.AspNetCore.Hosting | failed-requests |

### <a name="ilogger-logs"></a>Journaux ILogger

Les [journaux ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) de gravité `Warning` ou supérieure sont automatiquement capturés dans le SDK version 2.7.0-beta3 et versions ultérieures.

### <a name="live-metrics"></a>Métriques temps réel

Il faut parfois quelques minutes avant que les données de télémétrie apparaissent dans le portail. Pour rapidement vous assurer que tout fonctionne, il est préférable d’utiliser les [métriques temps réel](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) lorsque vous effectuez des requêtes vers l’application en cours d’exécution.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Activer les données de télémétrie côté client pour les applications web

Les étapes précédentes sont suffisantes pour commencer à collecter des données de télémétrie côté serveur. Si votre application comporte des composants côté client, suivez les étapes ci-dessous pour démarrer la collecte des [données de télémétrie d’utilisation](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. Dans `_ViewImports.cshtml`, ajoutez l’injection :

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Dans `_Layout.cshtml`, insérez `HtmlHelper` à la fin de la section `<head>`, mais avant tout autre script. Si vous souhaitez signaler les données de télémétrie JavaScript personnalisées à partir de la page, injectez-la par après cet extrait de code :

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Les noms de fichier `.cshtml` mentionnés précédemment proviennent d’un modèle d’application MVC par défaut. Enfin, si vous souhaitez activer correctement la surveillance côté client pour votre application, l’extrait de code JavaScript doit apparaître dans la section `<head>` de chaque page de votre application que vous souhaitez surveiller. Vous pouvez atteindre cet objectif de ce modèle d’application en ajoutant l’extrait de code JavaScript à `_Layout.cshtml`. 

Si votre projet n’inclut pas `_Layout.cshtml`, vous pouvez toujours ajouter la [surveillance côté client](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Pour ce faire, ajoutez l’extrait de code JavaScript dans un fichier équivalent qui contrôle l’élément `<head>` de toutes les pages au sein de votre application. Vous pouvez également ajouter l’extrait de code à plusieurs pages, mais cette solution est difficile à gérer, et nous ne la recommandons pas généralement.

## <a name="configure-the-application-insights-sdk"></a>Configurer le SDK Application Insights

Vous pouvez personnaliser le SDK Application Insights pour ASP.NET Core afin de modifier la configuration par défaut. Les utilisateurs du SDK Application Insights ASP.NET peuvent maîtriser la modification de configuration à l’aide de `ApplicationInsights.config` ou en modifiant `TelemetryConfiguration.Active`. Vous modifiez la configuration différemment pour ASP.NET Core. Ajoutez le SDK ASP.NET Core à l’application et configurez-le à l’aide de l’[injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) intégrée d’ASP.NET Core. Apportez presque toutes les modifications de configuration dans la méthode `ConfigureServices()` de la classe `Startup.cs`, sauf instruction contraire. Pour plus d’informations, consultez les sections suivantes.

> [!NOTE]
> Dans les applications ASP.NET Core, car la modification de la configuration en altérant `TelemetryConfiguration.Active` n’est pas prise en charge.

### <a name="using-applicationinsightsserviceoptions"></a>Utilisation d’ApplicationInsightsServiceOptions

Vous pouvez modifier quelques paramètres courants en passant de `ApplicationInsightsServiceOptions` à `AddApplicationInsightsTelemetry`, comme dans cet exemple :

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Liste complète des paramètres dans `ApplicationInsightsServiceOptions`

|Paramètre | Description | Default
|---------------|-------|-------
|EnableQuickPulseMetricStream | Active/désactive la fonctionnalité LiveMetrics | true
|EnableAdaptiveSampling | Active/désactive l’échantillonnage adaptatif | true
|EnableHeartbeat | Active/désactive la fonctionnalité des pulsations, qui envoie régulièrement (toutes les 15 minutes, par défaut) une métrique personnalisée nommée « HeartBeatState » avec des informations sur le runtime, telles que la version .NET, des informations d’environnement Azure, le cas échéant, etc. | true
|AddAutoCollectedMetricExtractor | Active/désactive l’extracteur AutoCollectedMetrics, qui est un TelemetryProcessor qui envoie des métriques pré-agrégées sur les demandes/dépendances avant l’échantillonnage. | true
|RequestCollectionOptions.TrackExceptions | Active/désactive la création de rapports de suivi des exceptions non gérées par le module de collecte des demandes. | false dans NETSTANDARD2.0 (car les exceptions sont suivies avec ApplicationInsightsLoggerProvider), true dans le cas contraire.

Consultez les [paramètres configurables dans `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs) pour obtenir la liste la plus à jour.

### <a name="sampling"></a>échantillonnage

Le SDK Application Insights pour ASP.NET Core prend en charge l’échantillonnage à taux fixe et adaptatif. L’échantillonnage adaptatif est activé par défaut. 

Pour plus d'informations, voir la [configuration de l’échantillonnage adaptatif pour les applications ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Ajout de TelemetryInitializers

Utilisez des [initialiseurs de télémétrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) lorsque vous souhaitez définir des propriétés globales qui sont envoyées avec toutes les données de télémétrie.

Ajoutez un nouvel élément `TelemetryInitializer` au conteneur `DependencyInjection`, comme indiqué dans le code suivant. Le SDK ne repère automatiquement tous les éléments `TelemetryInitializer` qui sont ajoutés au conteneur `DependencyInjection`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Suppression de TelemetryInitializers

Les initialiseurs de télémétrie sont présents par défaut. Pour supprimer tout ou partie des initialiseurs de télémétrie, utilisez l’exemple de code suivant *après* avoir appelé `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetry-processors"></a>Ajout de processeurs de télémétrie

Vous pouvez ajouter des processeurs de télémétrie personnalisés à `TelemetryConfiguration` à l’aide de la méthode d’extension `AddApplicationInsightsTelemetryProcessor` sur `IServiceCollection`. Vous utilisez des processeurs de télémétrie dans des [scénarios de filtrage avancés](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) pour permettre un contrôle plus direct sur les informations contenues ou non dans le données de télémétrie que vous envoyez au service Application Insights. Utilisez l’exemple suivant.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configuration ou suppression de l’élément TelemetryModules par défaut

Application Insights utilise les modules de télémétrie pour collecter automatiquement des informations de télémétrie utiles sur des charges de travail spécifiques sans qu’un suivi manuel de l’utilisateur ne soit nécessaire.

Les modules de collecte automatique suivants sont activés par défaut. Ces modules sont chargés de collecter automatiquement les données de télémétrie. Vous pouvez les désactiver ou les configurer pour modifier leur comportement par défaut.

* `RequestTrackingTelemetryModule` - Collecte RequestTelemetry à partir des demandes web entrantes.
* `DependencyTrackingTelemetryModule` - Collecte DependencyTelemetry à partir des appels http sortants et des appels SQL.
* `PerformanceCollectorModule` - Collecte les compteurs PerformanceCounters Windows.
* `QuickPulseTelemetryModule` - Collecte les données de télémétrie à afficher dans le portail des métriques en temps réel.
* `AppServicesHeartbeatTelemetryModule` - Collecte les pulsations (envoyées en tant que métriques personnalisées) à propos de l’environnement Azure App Service où l’application est hébergée.
* `AzureInstanceMetadataTelemetryModule` - Collecte les pulsations (envoyées en tant que métriques personnalisées) à propos de l’environnement Azure VM où l’application est hébergée.
* `EventCounterCollectionModule` - Collecte les compteurs [EventCounter](#eventcounter). Ce module est une nouvelle fonctionnalité disponible dans les versions 2.8.0-beta3 et ultérieures du kit SDK.

Pour configurer tout `TelemetryModule` par défaut, utilisez la méthode d’extension `ConfigureTelemetryModule<T>` sur `IServiceCollection`, comme illustré dans l’exemple suivant.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
                            (module, o) =>
                            {
                                module.Counters.Clear();
                                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
                            }
                        );

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>Configuration d’un canal de télémétrie

Le canal par défaut est `ServerTelemetryChannel`. Vous pouvez le remplacer, comme le montre l’exemple suivant.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Désactiver la télémétrie dynamiquement

Si vous souhaitez désactiver la télémétrie de manière conditionnelle et dynamique, vous pouvez résoudre l’instance `TelemetryConfiguration` avec le conteneur d’injection de dépendance ASP.net n’importe où dans votre code et définir l’indicateur `DisableTelemetry` sur celle-ci.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Notez que le code ci-dessus n’empêche pas les modules de collecte automatique de collecter des données de télémétrie. Seul l’envoi de données de télémétrie à Application Insights est désactivé avec l’approche ci-dessus. Si un module de collecte automatique particulier n’est pas souhaité, il est préférable de [supprimer le module de télémétrie](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Comment puis-je suivre les données de télémétrie qui ne sont pas automatiquement collectées ?

Obtenez une instance de `TelemetryClient` à l’aide de l’injection de constructeur et appelez la méthode `TrackXXX()` requise dessus. Nous ne recommandons pas de créer d’autres instances `TelemetryClient` dans une application ASP.NET Core. Une instance singleton de `TelemetryClient` est déjà inscrite dans le conteneur `DependencyInjection`, qui partage `TelemetryConfiguration` avec le reste de la télémétrie. La création d’une instance `TelemetryClient` est recommandée uniquement si celle-ci a besoin d’une configuration distincte du reste de la télémétrie. 

L’exemple suivant montre comment suivre des données de télémétrie supplémentaires à partir d’un contrôleur.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Pour plus d’informations sur la création de rapports personnalisés pour les données dans Application Insights, consultez la [documentation de référence sur l’API des métriques personnalisées Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Certains modèles Visual Studio utilisaient la méthode d’extension UseApplicationInsights() sur IWebHostBuilder pour activer Application Insights. Cette syntaxe est-elle toujours valide ?

Oui, l’activation d’Application Insights avec cette méthode est valide. Cette technique est utilisée dans l’intégration de Visual Studio et dans les extensions Web Apps. Toutefois, nous vous recommandons d’utiliser `services.AddApplicationInsightsTelemetry()`, car cette méthode fournit des surcharges pour contrôler la configuration. Les deux méthodes font la même chose en interne. Si vous n’avez pas besoin d’appliquer une configuration personnalisée, vous pouvez appeler l’une ou l’autre de ces méthodes.

`IWebHostBuilder` est remplacé par `IHostBuilder` dans ASP.NET Core 3.0 et, pour éviter toute confusion, Application Insights version 2.8.0-beta3 et ultérieure marquent la méthode UseApplicationInsights() comme obsolète et elle sera supprimée dans la version majeure suivante.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Je déploie mon application ASP.NET Core dans Web Apps. Dois-je toujours activer l’extension Application Insights à partir de Web Apps ?

Si le SDK est installé au moment de la génération, comme indiqué dans cet article, vous n’avez pas besoin activer l’[extension Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) à partir du portail App Service. Même si l’extension est installée, elle se désactive quand elle détecte que le SDK est déjà ajouté à l’application. Si vous activez Application Insights à partir de l’extension, il est inutile d’installer et de mettre à jour le SDK. Mais si vous activez Application Insights en suivant les instructions de cet article, vous bénéficiez de davantage de flexibilité pour plusieurs raisons :

   * Les données de télémétrie Application Insights continueront à fonctionner :
       * sur tous les systèmes d’exploitation, notamment Windows, Linux et Mac ;
       * dans tous les modes de publication, notamment le mode autonome ou le mode dépendant de l’infrastructure ;
       * dans toutes les versions cibles de .Net Framework, y compris le .NET Framework complet ;
       * avec toutes les options d’hébergement, notamment Web Apps, les machines virtuelles, Linux, les conteneurs, Azure Kubernetes Service et l’hébergement non Azure.
       * dans toutes les versions de .NET Core, y compris les préversions.
   * Vous pouvez voir les données de télémétrie localement lorsque vous déboguez à partir de Visual Studio.
   * Vous pouvez suivre des données de télémétrie personnalisées supplémentaires à l’aide de l’API `TrackXXX()`.
   * Vous avez le contrôle total de la configuration.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Puis-je activer la surveillance Application Insights à l’aide d’outils tels que Status Monitor ?

Non. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) et [Status Monitor version 2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) prennent actuellement en charge ASP.NET 4.x uniquement.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights est-il automatiquement activée pour mon application ASP.NET Core 2.0 ?

Le métapackage 2.0 `Microsoft.AspNetCore.All` incluait le SDK Application Insights (version 2.1.0). Si vous exécutez l’application sous le débogueur Visual Studio, Visual Studio active Application Insights et affiche les données de télémétrie localement dans l’IDE. Les données de télémétrie n’étaient pas envoyées au service Application Insights, sauf si une clé d’instrumentation était spécifiée. Nous vous recommandons de suivre les instructions de cet article pour activer Application Insights, même pour les applications 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Si j’exécute mon application sous Linux, toutes les fonctionnalités sont-elles prises en charge ?

Oui. La prise en charge de fonctionnalités pour le SDK est la même sur toutes les plateformes, avec les exceptions suivantes :

* Les compteurs de performance sont pris en charge uniquement dans Windows.
* Même si `ServerTelemetryChannel` est activé par défaut, si l’application est exécutée sous Linux ou MacOS, le canal ne crée pas automatiquement un dossier de stockage local pour conserver temporairement les données de télémétrie s’il existe des problèmes de réseau. En raison de cette limitation, les données de télémétrie sont perdues en cas de problèmes temporaires de réseau ou de serveur. Pour contourner ce problème, configurez un dossier local pour le canal :

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source

[Lisez et contribuez au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vidéo

- Regardez cette vidéo détaillée externe pour [configurer Application Insights en partant de zéro avec .NET Core et Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Regardez cette vidéo détaillée externe pour [configurer Application Insights en partant de zéro avec .NET Core et Visual Studio Code](https://youtu.be/ygGt84GDync).

## <a name="next-steps"></a>Étapes suivantes

* [Explorez les flux d’utilisateurs](../../azure-monitor/app/usage-flows.md) pour comprendre comment les utilisateurs naviguent dans votre application.
* [Configurez la collecte de captures instantanées](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) pour voir l’état du code source et des variables au moment où une exception est levée.
* [Utilisez l’API](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue détaillée des performances et de l’utilisation de votre application.
* Utilisez des [tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) pour vérifier votre application en permanence dans le monde entier.
* [Injection de dépendances dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
