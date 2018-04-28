---
title: Surveillance des applications et des ressources Azure | Microsoft Docs
description: Vue d’ensemble des services et fonctionnalités Microsoft qui contribuent à une stratégie de surveillance complète de vos services et applications Azure.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: robb,bwren
ms.openlocfilehash: 16478d0223f59abb239d39fa27453e41b6980727
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Surveillance des applications et des ressources Azure

La surveillance consiste à collecter et à analyser des données afin de déterminer les performances, l’intégrité et la disponibilité de votre application métier et des ressources dont elle dépend. Une stratégie de surveillance efficace permet d’identifier le fonctionnement détaillé des composants de votre application. Elle vous permet également d’augmenter votre temps d’activité en vous avertissant de manière proactive des problèmes critiques afin que vous puissiez les résoudre avant qu’ils ne deviennent effectifs.

Azure comprend plusieurs services qui effectuent individuellement un rôle ou une tâche spécifique dans l’espace d’analyse. Ensemble, ces services fournissent une solution complète pour la collecte, l’analyse et l’action sur les données de télémétrie de votre application et des ressources Azure qui les prennent en charge. Ces services peuvent aussi surveiller les ressources locales critiques afin de fournir un environnement de surveillance hybride. Comprendre les outils et les données disponibles est la première étape du développement d’une stratégie de surveillance complète pour votre application.

Le diagramme suivant présente une vue conceptuelle des composants qui collaborent pour assurer la surveillance des ressources Azure. Les sections suivantes décrivent ces composants et contiennent des liens vers des informations techniques détaillées.

![Vue d’ensemble de la surveillance](media/monitoring-overview/monitoring-products-overview.png)


## <a name="shared-capabilities"></a>Fonctionnalités partagées
Le service d’analyses approfondies et principales partagent des fonctionnalités qui fournissent les fonctionnalités suivantes.

### <a name="alerts"></a>Alertes
Les [alertes Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) vous avertissent de manière proactive en cas de condition critique, et sont susceptibles de prendre des mesures correctives. Les règles d’alerte peuvent utiliser les données provenant de plusieurs sources, notamment les métriques et les journaux. Elles utilisent des [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) qui contiennent des ensembles uniques de destinataires et d’actions en réponse à une alerte. Selon vos besoins, vous pouvez faire en sorte que les alertes démarrent des actions externes à l’aide de Webhooks et s’intègrent à vos outils ITSM.

### <a name="dashboards"></a>Tableaux de bord
Vous pouvez utiliser les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) pour combiner différentes sortes de données dans un même volet du [portail Azure](https://portal.azure.com). Vous pouvez ensuite partager le tableau de bord avec d’autres utilisateurs d’Azure.

Par exemple, vous pouvez créer un tableau de bord pour combiner :
- des vignettes qui présentent un graphique de métriques ;
- un tableau des journaux d’activité ;
- un graphique d’utilisation d’Application Insights ;
- la sortie d’une recherche dans les journaux dans Log Analytics.

Vous pouvez également exporter des données Log Analytics vers [Power BI](https://docs.microsoft.com/power-bi/). C’est là que vous pouvez tirer parti des visualisations supplémentaires. Vous pouvez également mettre ces données à la disposition d’autres utilisateurs qu’ils soient ou non membres de votre organisation.

### <a name="metrics-explorer"></a>Metrics Explorer
Les [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) sont des valeurs numériques générées par une ressource Azure qui vous aident à comprendre le fonctionnement et les performances de la ressource. La fonctionnalité Metrics Explorer vous permet d’envoyer des métriques vers Log Analytics afin de les analyser avec des données provenant d’autres sources.


## <a name="core-monitoring"></a>Analyse principale
L’analyse principale assure la surveillance essentielle et obligatoire des ressources Azure. Ces services nécessitent une configuration minimale et collectent des données de télémétrie de base utilisées par les services de surveillance Premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) active l’analyse principale des services Azure en permettant la collecte de [mesures](../monitoring-and-diagnostics/monitoring-overview-metrics.md), de [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) et de [journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Par exemple, le journal d’activité indique quand des ressources sont créées ou modifiées.

Les métriques fournissent des statistiques sur les performances pour différentes ressources, voire pour le système d’exploitation à l’intérieur d’une machine virtuelle. Vous pouvez afficher ces données avec un des explorateurs dans le portail Azure et créer des alertes en fonction de leurs mesures. Azure Monitor fournit les pipelines de mesures les plus rapides (de 5 minutes à 1 minute), vous devez ainsi l’utiliser pour les notifications et les alertes critiques de temps.

Vous pouvez également envoyer ces mesures et journaux Azure Log Analytics à des fins d’analyse détaillée et d’identification des tendances ou créer des règles d’alertes afin d’être informé des problèmes critiques issus de cette analyse de façon proactive.  

> [!NOTE]
> L’envoi à Log Analytics de métriques multidimensionnels au moyen des paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques avec des dimensions sont exportés en tant que métriques dimensionnels uniques aplatis, et agrégés entre les valeurs de la dimension.
>
> *Par exemple* : le métrique « Messages entrants » sur un hub d’événements peut être examiné et représenté sur un niveau par file d’attente. Toutefois, lors de son exportation vers Log Analytics le métrique est représenté sous forme de tous les messages entrants dans toutes les files d’attente du hub d’événements.
>
>

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) surveille en permanence les données de télémétrie de configuration et d’utilisation des ressources. Il vous donne ensuite des recommandations personnalisées basées sur les meilleures pratiques. Suivre ces recommandations vous aidera à améliorer les performances, la sécurité et la disponibilité des ressources qui prennent en charge vos applications.

### <a name="service-health"></a>Service Health
L’intégrité de votre application repose sur les services Azure dont elle dépend. [Azure Service Health](../service-health/service-health-overview.md) identifie les éventuels problèmes des services Azure qui peuvent nuire à votre application. Service Health vous permet également de planifier les tâches de maintenance.

### <a name="activity-log"></a>Journal d’activité
Le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) fournit des données sur le fonctionnement d’une ressource Azure. Ces informations incluent :
- les modifications de configuration de la ressource ;
- les incidents d’intégrité du service ;
- des recommandations pour améliorer l’utilisation de la ressource ;
- des informations relatives aux opérations de mise à l’échelle automatique.

Vous pouvez afficher les journaux correspondant à une ressource particulière sur sa page dans le portail Azure. Vous pouvez également afficher les journaux de plusieurs ressources dans l’explorateur Activity Log Explorer.

Vous pouvez également envoyer des entrées de journaux d’activité vers Log Analytics. Vous pouvez y analyser les journaux à l’aide des données collectées par les solutions de gestion, les agents des machines virtuelles et d’autres sources.

## <a name="deep-monitoring-services"></a>Services d’analyse approfondie
Les services Azure suivants offrent de puissantes fonctionnalités de collecte et d’analyse des données de surveillance à un niveau approfondi. Ces services s’appuient sur l’analyse principale et tirent profit des fonctionnalités communes dans Azure. Ils fournissent des données collectées à la fonctionnalité d’analyse puissante pour vous donner des informations uniques sur vos applications et votre infrastructure. Ils présentent des données dans le contexte de scénarios ciblant différents publics.

## <a name="deep-application-monitoring"></a>Analyse approfondie des applications
### <a name="application-insights"></a>Application Insights
Vous pouvez utiliser [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) pour surveiller la disponibilité, les performances et l’utilisation de votre application, qu’elle soit hébergée dans le cloud ou localement.

En instrumentant votre application pour qu’elle fonctionne avec Application Insights, vous pouvez obtenir des analyses approfondies et implémenter des scénarios DevOps. Vous pouvez alors rapidement identifier et diagnostiquer des erreurs sans attendre qu’un utilisateur ne les signale. Grâce aux informations recueillies, vous pouvez prendre des décisions avisées quant à la maintenance et à l’amélioration de votre application.

Application Insights dispose d’outils complets pour interagir avec les données qu’il collecte. Application Insights stocke ses données dans un référentiel commun. Il peut tirer parti des fonctionnalités partagées telles que les alertes, les tableaux de bord et une analyse approfondie grâce au langage de requête du service Log Analytics.

## <a name="deep-infrastructure-monitoring"></a>Analyse approfondie des infrastructures
### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) joue un rôle central dans l’analyse Azure en collectant des données de différentes ressources (y compris d’outils non Microsoft) dans un référentiel unique. C’est là que vous pouvez analyser les données à l’aide d’un puissant langage de requête.

Application Insights et Azure Security Center stockent leurs données dans le magasin de données Log Analytics et utilisent son moteur analytique. Les données sont également collectées auprès d’Azure Monitor, des solutions de gestion et des agents installés sur les machines virtuelles dans le cloud ou localement. Cette fonctionnalité partagée vous permet de constituer une image complète de votre environnement.

### <a name="management-solutions"></a>Liste des solutions de gestion
Les [solutions de gestion](../log-analytics/log-analytics-add-solutions.md) sont des jeux de logique empaquetés qui fournissent des informations détaillées pour une application ou un service spécifique. Elles s’appuient sur Log Analytics pour stocker et analyser les données de surveillance collectées.

Microsoft et ses partenaires proposent des solutions de gestion qui assurent la surveillance de divers services Azure et tiers. Exemples de solution de surveillance :
* [Container Monitoring](../log-analytics/log-analytics-containers.md), qui vous permet d’afficher et de gérer vos hôtes de conteneur.
* [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md), qui collecte et visualise les métriques de performances pour les bases de données Azure SQL Database.

Vous pouvez afficher toutes les solutions de gestion disponibles dans le Portail Azure sous l’écran *Monitor*.

### <a name="network-monitoring"></a>Analyse du réseau
Il existe plusieurs outils qui fonctionnent de concert afin d’analyser divers aspects de votre réseau, que ce soit dans Azure ou localement.  

[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) fournit une surveillance et des diagnostics basés sur un scénario pour différents scénarios réseau dans Azure. Il stocke des données dans les métriques et diagnostics Azure pour une analyse plus approfondie. Il fonctionne avec les solutions suivantes pour analyser les différents aspects de votre réseau.

[Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) : est une solution d’analyse réseau basée sur le cloud, qui surveille la connectivité sur les clouds publics, les centres de données et les environnements locaux.

[Analyse ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) est une fonctionnalité NPM qui surveille la connectivité et les performances de bout en bout sur les circuits Azure ExpressRoute.

[DNS Analytics](../log-analytics/log-analytics-dns.md) est une solution qui fournit la sécurité, les performances et des analyses relatives aux opérations, en fonction de vos serveurs DNS.

[Analyse des points de terminaison de service](../networking/network-monitoring-overview.md) teste l’accessibilité des applications et détecte les goulots d’étranglement au niveau des performances en local, sur les réseaux des opérateurs et sur les centres de données cloud/privés.


### <a name="service-map"></a>Service Map
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) fournit un aperçu de votre environnement IaaS en analysant les machines virtuelles avec leurs différents processus et dépendances envers d’autres ordinateurs et processus externes. Il intègre des événements, des données de performance et des solutions de gestion dans Log Analytics. Vous pouvez ensuite afficher ces données dans le contexte de chaque ordinateur ainsi que sa relation avec le reste de votre environnement.

La solution Service Map est semblable à la [cartographie d’application dans Application Insights](../application-insights/app-insights-app-map.md). Elle se concentre sur les composants d’infrastructure qui prennent en charge vos applications.


## <a name="example-scenarios"></a>Exemples de scénarios
Voici des exemples généraux qui illustrent l’utilisation de différents outils de surveillance dans Azure pour différents scénarios.

### <a name="monitoring-a-web-application"></a>Surveillance d’une application web
Prenez une application web déployée dans Azure via Azure App Service, Stockage Azure et une base de données SQL. Vous commencez par accéder aux [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et aux [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) de ces ressources dans leurs pages sur le portail Azure. Vous recherchez des informations critiques, telles que le nombre de requêtes envoyées à l’application et le temps de réponse moyen. Vous identifiez également les modifications apportées éventuellement à la configuration.

Vous accédez ensuite à la fonctionnalité Surveiller dans le portail afin d’afficher simultanément les métriques et les journaux des différentes ressources. Lorsque vous identifiez les paramètres standard des métriques, vous [créez des règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Ces règles vous informent de façon proactive lorsque par exemple le temps de réponse moyen augmente et dépasse un seuil. Pour obtenir un aperçu rapide des performances quotidiennes de votre application, créez un tableau de bord Azure pour afficher des graphiques de métriques représentant des indicateurs de performance clés critiques.

Pour effectuer une surveillance plus approfondie de votre application, vous [la configurez pour Application Insights](../application-insights/quick-monitor-portal.md). Vous pouvez maintenant collecter des données supplémentaires qui fournissent des informations encore plus précises sur le fonctionnement et les performances de votre application. Application Insights détecte les relations sous-jacentes entre les composants de votre application. Ce service offre une représentation visuelle par le biais de la [cartographie d’application](../application-insights/app-insights-app-map.md) associée au [suivi de bout en bout](../application-insights/app-insights-transaction-diagnostics.md) pour identifier le composant, la dépendance ou l’exception exacts où un problème s’est produit.

Vous créez des [tests de disponibilité](../application-insights/app-insights-monitor-web-app-availability.md) afin de tester de manière proactive votre application à partir de différentes régions. Pour aider vos développeurs, vous [activez le profileur](../application-insights/enable-profiler-compute.md) afin de pouvoir suivre les requêtes et toutes les exceptions jusqu’à une ligne spécifique de code. Pour en savoir encore plus sur les services utilisés dans votre application, ajoutez la [solution SQL Analytics](../log-analytics/log-analytics-azure-sql.md) afin de collecter des données supplémentaires dans Log Analytics.

Après un certain temps, vous décidez de rechercher la cause racine expliquant pourquoi, durant certaines périodes, les performances sur le site sont passées au-dessous d’un seuil. Vous écrivez une requête à l’aide de Log Analytics. Cela vous aide à mettre en corrélation les données de performances et d’utilisation collectées par Application Insights avec les données de performances et de configuration des ressources Azure qui prennent en charge votre application.


### <a name="monitoring-virtual-machines"></a>Surveillance des machines virtuelles
Vous disposez d’une combinaison de machines virtuelles Windows et Linux exécutées dans Azure. Vous utilisez Azure Monitor pour afficher les [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) et les [métriques au niveau de l’hôte](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Vous ajoutez [l’extension Azure Diagnostics](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) aux machines virtuelles afin de collecter des métriques du système d’exploitation invité. Vous créez ensuite des [règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour être informé de manière proactive lorsque des métriques de base telles que l’utilisation du processeur et la mémoire franchissent des seuils.

Pour recueillir plus de détails sur les machines virtuelles exécutant une application métier, vous [créez un espace de travail Log Analytics et activez l’extension de machine virtuelle](../log-analytics/log-analytics-quick-collect-azurevm.md) sur chaque machine. Vous configurez la [collecte de différentes sources de données](../log-analytics/log-analytics-data-sources.md) pour votre application et [créez des vues](../log-analytics/log-analytics-view-designer.md) afin d’obtenir des rapports sur ses opérations et ses performances quotidiennes. Ensuite, vous [créez des règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour être averti lors de la réception d’événements d’erreur particuliers.

Pour surveiller en continu l’intégrité de l’agent installé, vous ajoutez la [solution de gestion Agent Health](../operations-management-suite/oms-solution-agenthealth.md). Pour obtenir des informations encore plus précises sur l’application, vous [ajoutez l’agent de dépendance](../operations-management-suite/operations-management-suite-service-map-configure.md) aux machines virtuelles afin de les ajouter à [Service Map](../operations-management-suite/operations-management-suite-service-map.md). Service Map détecte les processus critiques et identifie les connexions entre les machines et d’autres services.

Après un signalement de panne, vous utilisez Service Map pour identifier les machines spécifiques ayant rencontré le problème. Vous créez ensuite une [requête sur les données Log Analytics](../log-analytics/log-analytics-log-search-new.md) pour identifier le problème à l’avenir. Vous créez aussi une règle d’alerte pour vous informer de manière proactive lorsque la condition est détectée.



## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus :

* [Azure Monitor](https://azure.microsoft.com/services/monitor/) pour une prise en main de l’analyse principale des mesures et des alertes.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) pour l’analyse des données et des journaux de surveillance collectés.
