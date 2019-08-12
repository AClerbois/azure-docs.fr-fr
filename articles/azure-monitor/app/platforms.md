---
title: 'Application Insights : langages, plateformes et intégrations | Microsoft Docs'
description: Langages, plateformes et intégrations disponibles pour Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: b2670ec844df192bb73e8b1e76b1ebf611b2539e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619380"
---
# <a name="supported-languages"></a>Langues prises en charge

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Plateformes et infrastructures prises en charge

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentation pour les applications déjà déployées (sans codes, basées sur un agent)
* [Machines virtuelles et groupes de machines virtuelles identiques Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET : pour les applications déjà actives](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md) incluant les rôles web et de travail
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentation par le biais du code (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Application Windows universelle](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Rôles de travail, services et applications de bureau Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Frameworks de journalisation
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportation et analyse de données
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Kits de développement logiciel (SDK) non pris en charge
Nous sommes conscients que plusieurs autres kits de développement logiciel (SDK) pris en charge par la communauté existent, et que nous avons même contribué à certains d’entre eux. Toutefois, Azure Monitor assure uniquement la prise en charge lors de l’utilisation des kits de développement logiciel pris en charge figurant sur cette page. Nous évaluons constamment les opportunités de développer notre prise en charge d’autres langues. Suivez notre page d’[annonces GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) pour recevoir les dernières informations concernant les kits de développement (SDK).
