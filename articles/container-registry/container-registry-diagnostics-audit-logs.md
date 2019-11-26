---
title: Journaux des ressources pour Azure Container Registry | Microsoft Docs
description: Enregistrez et analysez les événements des journaux des ressources pour Azure Container Registry tels que l’authentification, l’envoi (push) d’images et le tirage (pull) d’images.
services: container-registry
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: ''
ms.date: 10/30/2019
ms.author: danlep
ms.openlocfilehash: e419f8c5cf06efc93294f9c428e9102c1f81b36a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180197"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics

Cet article explique comment collecter des données de journal pour un registre de conteneurs Azure à l’aide des fonctionnalités d’[Azure Monitor](../azure-monitor/overview.md). Azure Monitor collecte les [journaux de ressources](../azure-monitor/platform/resource-logs-overview.md) (anciennement appelés *journaux de diagnostic*) pour les événements pilotés par l’utilisateur dans votre registre. Collectez et utilisez ces données pour répondre à des besoins tels que :

* Auditer les événements d’authentification du registre pour garantir la sécurité et la conformité 

* Fournir une trace d’activité complète sur les artefacts du registre, tels que les événements d’envoi (push) et de tirage (pull), pour vous permettre de diagnostiquer les problèmes opérationnels avec votre registre 

La collecte de données de journal de ressources à l’aide d’Azure Monitor peut entraîner des coûts supplémentaires. Consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). 


> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

La journalisation des événements au niveau du référentiel n’inclut pas actuellement les événements de suppression ou de suppression des balises. Seuls les événements de référentiel suivants sont enregistrés :
* **Événements d’envoi (push)** pour les images et autres artefacts
* **Événements de tirage (pull)** pour les images et autres artefacts

## <a name="registry-resource-logs"></a>Journaux de ressources de registre

Les journaux de ressource contiennent des informations émises par des ressources Azure qui décrivent leur fonctionnement interne. Pour un registre de conteneurs Azure, les journaux contiennent des événements d’authentification et de niveau de référentiel stockés dans les tableaux suivants. 

* **ContainerRegistryLoginEvents** - Événements et état d’authentification du registre, y compris l’identité entrante et l’adresse IP
* **ContainerRegistryRepositoryEvents** - Opérations telles que « push and pull » (envoyer et tirer) pour les images et autres artefacts dans les référentiels de registre
* **AzureMetrics** - [Métriques du registre des conteneurs](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries), tels que le nombre agrégé d’opérations d’envoi (push) et de tirage (pull).

Pour les opérations, les données de journal incluent :
  * L’état de réussite ou d’échec
  * Les horodatages de début et de fin

Outre les journaux de ressources, Azure fournit un [journal d’activité](../azure-monitor/platform/activity-logs-overview.md), un enregistrement unique d’événements de gestion Azure au niveau de l’abonnement, tels que la création ou la suppression d’un registre de conteneurs.

## <a name="enable-collection-of-resource-logs"></a>Permet la collecte des journaux de ressources

La collecte des journaux de ressources pour un registre de conteneurs n’est pas activée par défaut. Activez explicitement les paramètres de diagnostic pour chaque registre à surveiller. Pour les options permettant d’activer les paramètre de diagnostic, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/platform/diagnostic-settings.md).

Par exemple, pour afficher les journaux et les métriques d’un registre de conteneurs quasiment en temps réel dans Azure Monitor, collectez les journaux des ressources dans un espace de travail Log Analytics. Pour activer ce paramètre de diagnostic à l’aide du Portail Azure :

1. Si vous n’avez pas encore d’espace de travail, créez-en un à l’aide du [Portail Azure](../azure-monitor/learn/quick-create-workspace.md). Pour réduire la latence lors de la collecte de données, assurez-vous que l’espace de travail se trouve dans la **même région** que votre registre de conteneurs.
1. Dans le portail, sélectionnez le registre, puis sélectionnez **Surveillance > Paramètres de diagnostic > Ajouter un paramètre de diagnostic**.
1. Entrez un nom pour le paramètre, puis sélectionnez **Envoyer à Log Analytics**.
1. Sélectionnez l’espace de travail pour les journaux de diagnostic du registre.
1. Sélectionnez les données de journal que vous souhaitez collecter, puis cliquez sur **Enregistrer**.

L’illustration suivante montre la création d’un paramètre de diagnostic pour un registre à l’aide du portail.

![Activer les paramètres de diagnostic](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Collectez uniquement les données dont vous avez besoin, en équilibrant les coûts et vos besoins de surveillance. Par exemple, si vous devez uniquement auditer les événements d’authentification, sélectionnez uniquement le journal **ContainerRegistryLoginEvents**. 

## <a name="view-data-in-azure-monitor"></a>Afficher des données dans Azure Monitor

Une fois que vous avez activé la collecte des journaux de diagnostic dans Log Analytics, quelques minutes peuvent être nécessaires pour que les données s’affichent dans Azure Monitor. Pour afficher les données dans le portail, sélectionnez le registre, puis sélectionnez **Surveillance > Journaux**. Sélectionnez l’une des tables qui contient les données du registre. 

Exécutez des requêtes pour afficher les données. Plusieurs exemples de requêtes sont fournis, ou exécutez les vôtres. Par exemple, la requête suivante récupère les 24 heures de données les plus récentes à partir de la table **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

L’image suivante présente un exemple de sortie :

![Interroger des données de journal](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Pour obtenir un didacticiel sur l’utilisation de Log Analytics dans le Portail Azure, consultez [Bien démarrer avec Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md), ou essayez l’[Environnement de démonstration](https://portal.loganalytics.io/demo) Log Analytics. 

Pour plus d’informations sur les requêtes de journal, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Exemples de requêtes supplémentaires

#### <a name="100-most-recent-registry-events"></a>Les 100 événements de registre les plus récents

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Destinations de journal supplémentaires

En plus d’envoyer les journaux à Log Analytics, ou comme alternative, un scénario courant consiste à sélectionner un compte de stockage Azure comme destination du journal. Pour archiver les journaux dans le stockage Azure, créez un compte de stockage avant d’activer l’archivage via les paramètres de diagnostic.

Vous pouvez également diffuser en continu les événements du journal de diagnostic vers un service [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Le service Event Hubs peut traiter à chaque seconde des millions d’événements que vous pouvez transformer et stocker à l’aide de tout fournisseur d’analyses en temps réel. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’utilisation de [Log Analytics](../azure-monitor/log-query/get-started-portal.md) et la création de [requêtes de journal](../azure-monitor/log-query/get-started-queries.md).
* Consultez [vue d’ensemble des journaux de plateforme Azure](../azure-monitor/platform/platform-logs-overview.md) pour en savoir plus sur les journaux de plateforme disponibles dans différentes couches d’Azure.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
