---
title: Détection intelligente dans Azure Application Insights | Microsoft Docs
description: Application Insights réalise une analyse télémétrique approfondie automatique de votre application et vous avertit des éventuels problèmes de performances.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: c284236af64c3981a5d15570bcd2705bb7d6a99b
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121052"
---
# <a name="smart-detection-in-application-insights"></a>Détection intelligente dans Application Insights
 La détection intelligente vous informe automatiquement des éventuels problèmes de performances dans votre application web. Elle effectue une analyse proactive des données de télémétrie que votre application envoie à [Application Insights](../../azure-monitor/app/app-insights-overview.md). S’ils détectent une augmentation soudaine du taux d’échec, ou des modèles anormaux de performances client ou serveur, vous recevez une alerte. Cette fonctionnalité ne nécessite aucune configuration. Elle fonctionne si votre application envoie suffisamment de données de télémétrie.

Vous pouvez accéder aux alertes de détection intelligente via les courriers électroniques que vous recevez et à partir du panneau Détection intelligente.

## <a name="review-your-smart-detections"></a>Passer en revue vos détections intelligentes
Vous pouvez découvrir des détections de deux manières :

* **Vous recevez un message électronique** de la part d’Application Insights. Voici un exemple typique :
  
    ![Alerte par courrier électronique](./media/proactive-diagnostics/03.png)
  
    Cliquez sur le grand bouton pour afficher plus de détails dans le portail.
* **La vignette Détection intelligente** figurant sur le panneau d’aperçu de votre application indique le nombre d’alertes récentes. Cliquez sur la mosaïque pour afficher la liste des alertes récentes.

![Afficher les détections récentes](./media/proactive-diagnostics/04.png)

Sélectionnez une alerte pour afficher les détails la concernant.

## <a name="what-problems-are-detected"></a>Quels sont les problèmes détectés ?
Il existe trois types de détection :

* [Détection intelligente des anomalies de type échec](../../azure-monitor/app/proactive-failure-diagnostics.md). Nous utilisons l’apprentissage automatique pour définir la fréquence attendue des demandes entraînant un échec pour votre application, en la mettant en corrélation avec la charge et d’autres facteurs. Si le taux d’échec est situé en dehors de la plage attendue, nous envoyons une alerte.
* [Détection intelligente des anomalies de performances](../../azure-monitor/app/proactive-performance-diagnostics.md). Vous recevez des notifications si le temps de réponse d’une opération ou la durée d’une dépendance ralentit par rapport à l’historique de la ligne de base ou si nous identifions un modèle anormal dans le temps de réponse ou le temps de chargement de la page.   
* [Détection intelligente - Dépannage de problèmes de service cloud Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vous recevez des alertes si votre application est hébergée dans Azure Cloud Services et qu’une instance de rôle présente des échecs de démarrage, un recyclage fréquent ou des erreurs d’exécution.

(Les liens d’aide dans chaque notification vous renvoient vers les articles pertinents.)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md)
* [Navigateur de recherche](../../azure-monitor/app/diagnostic-search.md)
* [Analytics : un puissant langage de requête](../../azure-monitor/log-query/get-started-portal.md)

La détection intelligente est entièrement automatique. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](../../azure-monitor/app/alerts.md)
* [Tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) 

