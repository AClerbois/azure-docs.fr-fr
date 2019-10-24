---
title: Exploration des journaux d’activité .NET dans Application Insights
description: Effectuer une recherche dans les journaux d’activité générés par Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: 654e4bc35de1ed33842944ba360d319705589683
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372506"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Découvrir les journaux des traces .NET/.NET Core et Python dans Application Insights

Envoyez des journaux de suivi de diagnostic pour votre application ASP.NET/ASP.NET Core à partir de ILogger, NLog, log4Net ou System.Diagnostics.Trace vers [Azure Application Insights][start]. Pour les applications Python, envoyez des journaux de traçage de diagnostics à l’aide d’AzureLogHandler dans OpenCensus Python pour Azure Monitor. Vous pourrez ensuite les explorer et y effectuer des recherches. Ces journaux d’activité sont fusionnés avec d’autres fichiers journaux provenant de votre application, ce qui vous permet d’identifier les traces associées à chaque demande d’utilisateur et les mettre en corrélation avec d’autres événements et des rapports d’exception.

> [!NOTE]
> Avez-vous besoin du module de collecte de journaux ? Il s’agit d’un adaptateur très utile pour les enregistreurs d’événements tiers. Cependant, si vous n’utilisez pas déjà NLog, log4Net ou System.Diagnostics.Trace, vous pouvez appeler [**Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) directement.
>
>
## <a name="install-logging-on-your-app"></a>Installation de la journalisation sur votre application
Installez le framework de journalisation de votre choix dans votre projet, ce qui générera une entrée dans app.config ou web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configuration d’Application Insights pour la collecte des journaux d’activité
Si vous ne l’avez pas encore fait, [ajoutez Application Insights à votre projet](../../azure-monitor/app/asp-net.md). Une option permettant d’inclure le collecteur de journaux doit s’afficher.

Vous pouvez également cliquer avec le bouton droit sur votre projet dans l’Explorateur de solutions pour **configurer Application Insights**. Sélectionnez l’option **Configure trace collection** (Configurer la collecte des traces).

> [!NOTE]
> Menu Application Insights non disponible ou aucune option pour le collecteur de journaux ? Consultez la [Résolution des problèmes](#troubleshooting).

## <a name="manual-installation"></a>Installation manuelle
Utilisez cette méthode si votre type de projet n’est pas pris en charge par le programme d’installation Application Insights (par exemple, un projet de bureau Windows).

1. Si vous prévoyez d'utiliser log4Net ou NLog, installez-le dans votre projet.
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
3. Recherchez « Application Insights ».
4. Sélectionnez l’un des packages suivants :

   - Pour ILogger : [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Pour NLog : [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Pour Log4Net : [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Pour System.Diagnostics : [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Le package NuGet installe les assemblys nécessaires et modifie le fichier web.config ou app.config le cas échéant.

## <a name="ilogger"></a>ILogger

Pour des exemples d’utilisation de l’implémentation Application Insights ILogger avec des applications de console et ASP.NET Core, consultez [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](ilogger.md) (ApplicationInsightsLoggerProvider pour les journaux d’activité .NET Core ILogger).

## <a name="insert-diagnostic-log-calls"></a>Insertion d'appels de journaux de diagnostic
Si vous utilisez System.Diagnostics.Trace, un appel standard serait :

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si vous préférez log4net ou NLog, utilisez :

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Utiliser les événements EventSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Ensuite, modifiez la section `TelemetryModules` du fichier [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * **Name** spécifie le nom de l’événement EventSource à collecter.
 * **Level** spécifie le niveau de journalisation à collecter : *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose*, ou *Warning*.
 * **Keywords** (facultatif) spécifie la valeur entière de combinaisons de mots clés à utiliser.

## <a name="use-diagnosticsource-events"></a>Utiliser les événements DiagnosticSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) de façon à les envoyer à Application Insights en tant que traces. Commencez par installer le package NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Ensuite, modifiez la section « TelemetryModules » du fichier [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pour chaque DiagnosticSource à tracer, ajoutez une entrée avec l’attribut **Name** défini sur le nom de votre DiagnosticSource.

## <a name="use-etw-events"></a>Utiliser les événements ETW
Vous pouvez configurer les événements du suivi d’événements pour Windows (ETW, Event Tracing for Windows) à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EtwCollector`. Ensuite, modifiez la section « TelemetryModules » du fichier [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Les événements ETW peuvent uniquement être collectés si le processus hébergeant le Kit de développement logiciel (SDK) s’exécute sous une identité membre des Utilisateurs ou Administrateurs du journal de performances.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * **ProviderName** est le nom du fournisseur ETW à collecter.
 * **ProviderGuid** spécifie le GUID du fournisseur ETW à collecter. Il peut être utilisé à la place de `ProviderName`.
 * **Level** définit le niveau de journalisation à collecter. Ce niveau peut être *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* ou *Warning*.
 * **Keywords** (facultatif) définit la valeur entière de combinaisons de mots clés à utiliser.

## <a name="use-the-trace-api-directly"></a>Utiliser directement l’API de suivi
Vous pouvez appeler directement l’API de suivi d’Application Insights. Les adaptateurs de journalisation utilisent cette API.

Par exemple :

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

l’un des avantages de TrackTrace est que vous pouvez insérer des données relativement longues dans le message. Par exemple, vous pourriez y encoder des données POST.

Vous pouvez également ajouter un niveau de gravité à votre message. Comme pour les autres données de télémétrie, vous pouvez ajouter des valeurs de propriété qui permettent de filtrer ou rechercher différents jeux de traces. Par exemple :

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Cela vous permettrait, dans [Recherche][diagnostic], de filtrer facilement tous les messages d’un niveau de gravité particulier portant sur une certaine base de données.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler pour OpenCensus Python
Le gestionnaire de journaux Azure Monitor vous permet d’exporter des journaux Python vers Azure Monitor.

Instrumentez votre application avec le [kit SDK OpenCensus Python](../../azure-monitor/app/opencensus-python.md) pour Azure Monitor.

Cet exemple montre comment envoyer un journal relatif aux avertissements à Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Exploration de vos journaux d’activité
Exécutez votre application en mode débogage, ou déployez-la en direct.

Dans le volet de vue d’ensemble de votre application, dans le [portail Application Insights][portal], sélectionnez [Rechercher][diagnostic].

Vous pouvez par exemple :

* Filtrer selon les traces de journal ou les éléments avec des propriétés spécifiques
* Inspecter un élément spécifique en détail
* Rechercher d’autres données de journal système liées à la même demande d’utilisateur (ou ayant le même OperationId)
* Enregistrer la configuration d’une page en tant que favori

> [!NOTE]
>Si votre application envoie des données en grand nombre et si vous utilisez le Kit de développement logiciel (SDK) Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité *d’échantillonnage adaptatif* peut fonctionner et transmettre uniquement une portion de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="how-do-i-do-this-for-java"></a>Comment faire pour Java ?
Utilisez les [adaptateurs de journaux Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Le menu contextuel du projet ne contient aucune option Application Insights
* Assurez-vous que Developer Analytics Tools est installé sur l’ordinateur de développement. Dans Visual Studio, sélectionnez **Outils** > **Extensions et mises à jour**, puis recherchez **Developer Analytics Tools**. S’il n’est pas dans l’onglet **Installé**, ouvrez l’onglet **En ligne** et installez-le.
* Il peut s’agir d’un type de projet non pris en charge par Developer Analytics Tools. Utilisez [l’installation manuelle](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Aucune option d’adaptateur de journalisation dans l’outil de configuration
* Installez d’abord l’infrastructure de journalisation.
* Si vous utilisez System.Diagnostics.Trace, assurez-vous qu’il est [configuré dans *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Vérifiez que vous disposez de la dernière version d’Application Insights. Dans Visual Studio, sélectionnez **Outils** > **Extensions et mises à jour**, puis ouvrez l’onglet **Mises à jour**. Si **Developer Analytics Tools** est présent, sélectionnez-le pour le mettre à jour.

### <a name="emptykey"></a>J’obtiens le message d’erreur « Instrumentation key cannot be empty » (La clé d’instrumentation ne peut pas être vide).
Vous avez probablement installé le package Nuget de l’adaptateur de journalisation sans installer Application Insights. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur *ApplicationInsights.config*, puis sélectionnez **Mettre à jour Application Insights**. Vous êtes invité à vous connecter à Azure et à créer une ressource Application Insights ou à réutiliser une ressource existante. Ceci devrait corriger le problème.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Je peux voir des traces mais pas d’autres événements dans la recherche de diagnostic
Le passage des événements et des demandes dans le pipeline peut prendre un certain temps.

### <a name="limits"></a>Quelle est la quantité de données conservée ?
Plusieurs facteurs affectent la quantité de données conservées. Consultez la section [Limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) de la page sur les métriques d’événement client pour plus d’informations.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Je ne vois pas certaines entrées de journal que j’attendais
Si votre application envoie de grandes quantités de données et si vous utilisez le Kit de développement logiciel (SDK) Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement une portion de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Étapes suivantes

* [Diagnostiquer les défaillances et les exceptions dans ASP.NET][exceptions]
* [En savoir plus sur Recherche][diagnostic]
* [Configuration des tests de disponibilité et de réactivité][availability]
* [Dépannage][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md