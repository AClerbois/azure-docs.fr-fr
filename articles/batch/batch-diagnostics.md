---
title: Métriques, alertes et journaux de diagnostic pour Azure Batch | Microsoft Docs
description: Enregistrez et analysez les événements du journal de diagnostic pour des ressources de compte Azure Batch telles que des pools et des tâches.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 85bda9e1a936f19a37be2f2c1c42f948cab727d6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122465"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métriques, alertes et journaux Batch pour l’évaluation de diagnostic et la surveillance

Cet article explique comment surveiller un compte Batch à l’aide de fonctionnalités [d’Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor collecte des [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et des [journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pour les ressources de votre compte Batch. Collectez et utilisez ces données de plusieurs façons pour surveiller votre compte Batch et diagnostiquer les problèmes. Vous pouvez également configurer des [alertes de métriques](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) afin de recevoir des notifications lorsqu’une métrique atteint une valeur spécifiée. 

## <a name="batch-metrics"></a>Métriques Batch

Les métriques sont des données de télémétrie Azure (également appelées compteurs de performance) émises par vos ressources Azure qui sont consommées par le service Azure Monitor. Parmi les métriques d’un compte Batch figurent par exemple : Événements de création de pool, Nombre de nœuds à priorité basse et Événements de fin de tâche. 

Consultez la [liste des métriques Batch prises en charge](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Ces métriques :

* sont activées par défaut dans chaque compte Batch sans configuration supplémentaire ;
* sont générées toutes les minutes ;
* ne sont pas conservées automatiquement, mais ont un historique propagé de 30 jours. Vous pouvez conserver les métriques d’activité dans le cadre de la [journalisation des diagnostics](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Afficher les mesures

Visualisez les métriques de votre compte Batch dans le portail Azure. La page **Vue d’ensemble** du compte par défaut affiche les métriques de nœuds de clé, de cœurs et de tâches. 

Pour afficher toutes les métriques de compte Batch : 

1. Dans le portail, cliquez sur **Tous les services** > **Comptes Batch**, puis sur le nom de votre compte Batch.
2. Sous **Surveillance**, cliquez sur **Métriques**.
3. Sélectionnez une ou plusieurs métriques. Si vous le souhaitez, sélectionnez des métriques de ressources supplémentaires à l’aide des listes déroulantes **Abonnements**, **Groupe de ressources**, **Type de ressource** et **Ressource**.

    ![Métriques Batch](media/batch-diagnostics/metrics-portal.png)

Pour récupérer les métriques par programmation, utilisez les API d’Azure Monitor. Par exemple, consultez [Récupérer les métriques Azure Monitor avec .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Alertes de métriques Batch

Éventuellement, vous pouvez configurer des *alertes de métriques* en temps quasi-réel qui se déclenchent quand la valeur d’une métrique spécifiée dépasse le seuil défini. Ces alertes génèrent une [notification](../monitoring-and-diagnostics/insights-alerts-portal.md) que vous choisissez lorsqu’elles sont activées (quand le seuil est atteint et que la condition d’alerte est remplie) et lorsqu’elles sont résolues (quand le seuil est de nouveau atteint et que la condition n’est plus remplie). 

Par exemple, vous souhaiterez peut-être configurer une alerte de métrique lorsque votre nombre de cœurs en priorité basse descend à un certain niveau, pour vous permettre ainsi d’ajuster la composition de vos pools.

Pour configurer une alerte de métrique dans le portail :

1. Cliquez sur **Tous les services** > **Comptes Batch**, puis sur le nom de votre compte Batch.
2. Sous **Surveillance**, cliquez sur **Règles d’alerte** > **Ajouter une alerte Métrique**.
3. Sélectionnez une métrique, une condition d’alerte (par exemple, quand une métrique dépasse une valeur particulière sur une certaine période) et une ou plusieurs notifications.

Vous pouvez également configurer une alerte en temps quasi-réel à l’aide de [l’API REST](https://docs.microsoft.com/rest/api/monitor/). Pour plus d’informations, consultez [Utiliser les alertes métriques les plus récentes pour les services Azure dans le Portail Azure](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).
## <a name="batch-diagnostics"></a>Diagnostics Batch

Les journaux de diagnostic contiennent des informations émises par les ressources Azure qui décrivent le fonctionnement de chaque ressource. Pour Batch, vous pouvez collecter les journaux suivants :

* Les événements **Journaux de service** émis par le service Azure Batch pendant la durée de vie d’une ressource Batch individuelle telle qu’un pool ou une tâche. 

* Les journaux **Métriques** au niveau du compte. 

Les paramètres pour autoriser la collecte des journaux de diagnostic ne sont pas activés par défaut. Activez explicitement les paramètres de diagnostic pour chaque compte Batch à surveiller.

### <a name="log-destinations"></a>Destinations des journaux

Un scénario courant consiste à sélectionner un compte de stockage Azure en tant que destination du journal. Pour stocker les journaux dans le stockage Azure, créez le compte avant d’activer la collecte de journaux. Si vous avez associé un compte de stockage à votre compte Batch, vous pouvez choisir ce compte comme destination du journal. 

Autres destinations facultatives pour les journaux de diagnostic :

* Diffusez les événements du journal de diagnostic Batch vers un service [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Le service Event Hubs peut traiter à chaque seconde des millions d’événements que vous pouvez transformer et stocker à l’aide de tout fournisseur d’analyses en temps réel. 

* Envoyez les journaux de diagnostic à [Azure Log Analytics](../log-analytics/log-analytics-overview.md) où vous pouvez les analyser via le portail Operations Management Suite (OMS), ou les exporter à des fins d’analyse vers Power BI ou Excel.

> [!NOTE]
> Vous risquez de payer des frais supplémentaires pour stocker ou traiter les données de journal de diagnostics avec les services Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Activer la collecte des journaux de diagnostic Batch

1. Dans le portail, cliquez sur **Tous les services** > **Comptes Batch**, puis sur le nom de votre compte Batch.
2. Sous **Surveillance**, cliquez sur **Journaux de diagnostic** > **Activer les diagnostics**.
3. Dans **Paramètres de diagnostic**, entrez un nom pour le paramètre et choisissez une destination de journal (compte de stockage existant, Event Hub ou Log Analytics). Sélectionnez **ServiceLog** et **AllMetrics**, ou l’un des deux.

    Lorsque vous sélectionnez un compte de stockage, définissez éventuellement une stratégie de rétention. Si vous ne spécifiez pas de nombre de jours de rétention, les données sont conservées pendant la durée de vie du compte de stockage.

4. Cliquez sur **Enregistrer**.

    ![Diagnostics Batch](media/batch-diagnostics/diagnostics-portal.png)

D’autres options sont disponibles pour activer la collecte de journaux : vous pouvez notamment utiliser Azure Monitor dans le portail pour configurer les paramètres de diagnostic, prendre un [modèle Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md) ou utiliser Azure PowerShell ou CLI. Consultez [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Accéder aux journaux de diagnostics dans le stockage

Si vous archivez les journaux de diagnostic Batch dans un compte de stockage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement lié se produit. Les objets blob sont créés selon le modèle d’affectation de noms suivant :

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exemple :

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Chaque fichier blob PT1H.json contient des événements au format JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h=12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m = 00) est toujours 00, étant donné que les événements du journal de diagnostic sont répartis en différents objets blob par heure. (Toutes les heures sont exprimées en heure UTC.)


Pour plus d’informations sur le schéma des journaux de diagnostic dans le compte de stockage, consultez [Archivage des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Pour accéder par programme aux journaux de votre compte de stockage, utilisez les API de stockage. 

### <a name="service-log-events"></a>Événements du journal de service
Les journaux de service Azure Batch, s’ils sont collectés, contiennent des événements émis par le service Azure Batch pendant la durée de vie d’une ressource Batch individuelle telle qu’un pool ou une tâche. Chaque événement émis par Batch est enregistré au format JSON. Par exemple, ceci est le corps d’un exemple d’**événement de création de pool** :

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Actuellement, le service Batch émet les événements du journal de service suivants. Cette liste n’est peut-être pas exhaustive, car des événements supplémentaires peuvent avoir été ajoutés depuis la dernière mise à jour de cet article.

| **Événements du journal de service** |
| --- |
| [Création de pool](batch-pool-create-event.md) |
| [Début de suppression de pool](batch-pool-delete-start-event.md) |
| [Fin de suppression de pool](batch-pool-delete-complete-event.md) |
| [Début de redimensionnement de pool](batch-pool-resize-start-event.md) |
| [Fin de redimensionnement de pool](batch-pool-resize-complete-event.md) |
| [Début de tâche](batch-task-start-event.md) |
| [Fin de tâche](batch-task-complete-event.md) |
| [Échec de tâche](batch-task-fail-event.md) |



## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.
* Consultez d’autres informations sur les [solutions de surveillance Batch](monitoring-overview.md).
