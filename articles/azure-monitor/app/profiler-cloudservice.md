---
title: Profiler des services cloud Azure en production avec Application Insights | Microsoft Docs
description: Activez Application Insights Profiler pour Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8ad472b9c92e3bc2164146191a63985fd26becab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306372"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profiler des services cloud Azure en production avec Application Insights

Vous pouvez aussi déployer Application Insights Profiler sur ces services :
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applications Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler est installé avec l’extension Diagnostics Azure. Vous devez simplement configurer Diagnostics Azure pour installer Profiler et envoyer des profils à votre ressource Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Activer Profiler pour Azure Cloud Services
1. Veillez à utiliser [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur. Il suffit de confirmer que les fichiers *ServiceConfiguration\*.cscfg* ont une valeur `osFamily` au moins égale à « 5 ».

1. Ajoutez le [SDK Application Insights à Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Le bogue du profileur fourni avec WAD pour les Services cloud a été corrigé.** La dernière version de WAD (1.12.2.0) pour les Services cloud fonctionne avec toutes les versions récentes du kit de développement logiciel (SDK) App Insights. Les hôtes des Services cloud mettent automatiquement à niveau WAD, mais ce n'est pas immédiat. Pour forcer une mise à niveau, vous pouvez redéployer votre service ou redémarrer le nœud.

1. Effectuer le suivi des requêtes avec Application Insights :

    * Pour les rôles web ASP.Net, Application Insights peut suivre automatiquement les demandes.

    * Pour les rôles de travail, [ajoutez du code pour suivre les demandes](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configurez l'extension Diagnostics Azure pour activer Profiler :

    a. Recherchez le fichier *diagnostics.wadcfgx*[Diagnostics Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) pour votre rôle d’application, comme indiqué ici :  

      ![Recherche du fichier config de diagnostics](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Si vous ne trouvez pas le fichier, consultez [Configurer les diagnostics pour les services Azure Cloud Services et Machines virtuelles](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg` :  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Si le fichier *diagnostics.wadcfgx* contient également un autre récepteur de type ApplicationInsights, les trois clés d’instrumentation suivantes doivent correspondre :  
    > * La clé qui est utilisée par votre application. 
    > * La clé qui est utilisée par le récepteur ApplicationInsights. 
    > * La clé qui est utilisée par le récepteur ApplicationInsightsProfiler. 
    >
    > Vous trouverez la valeur réelle de la clé d’instrumentation utilisée par le récepteur `ApplicationInsights` dans les fichiers *ServiceConfiguration\*.cscfg*. 
    > Après la publication du SDK Azure Visual Studio 15.5, seules les clés d’instrumentation utilisées par l’application et le récepteur ApplicationInsightsProfiler doivent correspondre.

1. Déployez votre service avec la nouvelle configuration de diagnostic pour qu’Application Insights Profiler soit configuré pour s’exécuter sur votre service.
 
## <a name="next-steps"></a>Étapes suivantes

* Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](monitor-web-app-availability.md)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
* Consultez [Traces du profileur](profiler-overview.md?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
* Pour résoudre les problèmes de Profiler, consultez [Résolution des problèmes de Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
