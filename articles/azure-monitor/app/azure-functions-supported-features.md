---
title: Azure Application Insights - Fonctionnalités prises en charge par Azure Functions | Microsoft Docs
description: Fonctionnalités Application Insights prises en charge pour Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0199d8f0c4a76a10fffcab7cf2819643d0ac2d68
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075359"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Fonctionnalités Application Insights prises en charge pour Azure Functions

Azure Functions offre une [intégration prédéfinie](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) à Application Insights, elle est disponible via l’interface ILogger. La liste des fonctionnalités actuellement prises en charge est indiquée ci-dessous. Servez-vous du guide d’Azure Functions pour [Bien démarrer](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Fonctionnalités prises en charge

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Kit SDK .NET d’Application Insights**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Collecte automatique de**        |                 |                   |               
| &bull; Requêtes                     | OUI             | OUI               | 
| &bull; Exceptions                   | OUI             | OUI               | 
| &bull; Compteurs de performances         | OUI             | OUI               |
| &bull; Dépendances                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | OUI               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | OUI               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | OUI               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | OUI               | 
| | | | 
| **Fonctionnalités prises en charge**                |                   |                   |               
| &bull; Pulsation rapide/Métriques temps réel       | OUI             | OUI               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de contrôle sécurisé|                 | OUI               | 
| &bull; Échantillonnage                     | OUI             | OUI               | 
| &bull; Pulsations                   |                 | OUI               | 
| | | | 
| **Corrélation**                       |                   |                   |               
| &bull; ServiceBus                     |                   | OUI               | 
| &bull; EventHub                       |                   | OUI               | 
| | | | 
| **Configurable**                      |                   |                   |           
| &bull; Entièrement configurable.<br/>Consultez [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pour obtenir des instructions.<br/>Consultez [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pour connaître toutes les options.               |                   | OUI                   | 


## <a name="performance-counters"></a>Compteurs de performance

La collecte automatique des compteurs de performances fonctionne uniquement sur les machines Windows.


## <a name="live-metrics--secure-control-channel"></a>Métriques en temps réel et canal de contrôle sécurisé

Les critères de filtres personnalisés que vous spécifiez sont renvoyés au composant de métriques temps réel dans le Kit de développement logiciel (SDK) Application Insights. Les filtres peuvent potentiellement contenir des informations sensibles telles que des ID clients. Vous pouvez sécuriser le canal avec une clé API secrète. Pour obtenir des instructions, consultez [Sécuriser le canal de contrôle](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>échantillonnage

Azure Functions permet l’échantillonnage par défaut dans sa configuration. Pour plus d’informations, consultez [Configurer l’échantillonnage](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Si votre projet prend une dépendance sur le kit de développement logiciel (SDK) Application Insights pour effectuer un suivi télémétrique manuel, vous constaterez peut-être un comportement inhabituel en présence d'une configuration d’échantillonnage différente de la configuration d’échantillonnage de Functions. 

Nous vous recommandons d’utiliser la même configuration que Functions. Avec **Functions v2**, vous pouvez obtenir la même configuration à l’aide de l’injection de dépendances dans votre constructeur :

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
