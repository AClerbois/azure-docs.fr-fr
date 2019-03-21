---
title: Azure Service Fabric - surveillance des performances avec Azure Monitor enregistre | Microsoft Docs
description: Découvrez comment configurer l’agent Log Analytics de façon à effectuer le monitoring des conteneurs et des compteurs de performances de vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 6562156432a86c346a0fee382af50f210e3cf6dc
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308514"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Surveillance des performances avec les journaux d’Azure Monitor

Cet article explique comment ajouter l’agent Log Analytics à un cluster sous la forme d’une extension du groupe de machines virtuelles identiques, puis le connecter à un espace de travail Azure Log Analytics existant. Cela permet de collecter des données de diagnostic sur les conteneurs, les applications et les performances. Si vous l’ajoutez en tant qu’extension à la ressource de groupe de machines virtuelles identiques, Azure Resource Manager veille à l’installer sur tous les nœuds, même lors de la mise à l’échelle du cluster.

> [!NOTE]
> Cet article suppose que vous disposez d’un espace de travail Azure Log Analytics déjà configuré. Si vous ne le faites pas, rendez-vous sur [configuré des journaux d’Azure Monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Ajouter l’extension d’agent via Azure CLI

La meilleure façon d’ajouter l’agent Log Analytics à un cluster est d’utiliser les API des groupes de machines virtuelles identiques avec Azure CLI. Si vous n’avez pas encore configuré Azure CLI, accédez au portail Azure, puis ouvrez une instance [Cloud Shell](../cloud-shell/overview.md) ou [installez Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Lorsque votre instance de Cloud Shell est demandée, veillez à vous trouver dans le même abonnement que votre ressource. Utilisez la commande `az account show` pour vérifier que la valeur « name » correspond à celle de l’abonnement de votre cluster.

2. Dans le Portail, accédez au groupe de ressources où se trouve votre espace de travail Log Analytics. Cliquez sur la ressource d’analytique de journal (le type de la ressource sera espace de travail Analytique de journal). Une fois que vous vous trouvez sur la page de vue d’ensemble des ressources, cliquez sur **Paramètres avancés** dans la section Paramètres du menu de gauche.

    ![Page de propriétés de journal analytique](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Cliquez sur **Serveurs Windows** si vous créez un cluster Windows, ou sur **Serveurs Linux** si vous créez un cluster Linux. Cette page affiche les éléments `workspace ID` et `workspace key` (répertoriés en tant que clé primaire dans le portail). Vous aurez besoin de ces éléments pour l’étape suivante.

4. Exécutez la commande pour installer l’agent Log Analytics sur votre cluster en utilisant l’API `vmss extension set` de votre service Cloud Shell :

    Pour un cluster Windows :

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pour un cluster Linux :

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Voici un exemple illustrant l’ajout de l’agent Log Analytics à un cluster Windows.

    ![Commande CLI de l’agent Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Normalement, l’ajout de l’agent aux nœuds prend moins de 15 minutes. Vous pouvez vérifier que les agents ont bien été ajoutés à l’aide de l’API `az vmss extension list` :

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Ajouter l’agent en utilisant un modèle Resource Manager

Des exemples de modèles Gestionnaire des ressources qui déploient un espace de travail Azure Log Analytics et ajoutent un agent à chacun de vos nœuds sont disponibles pour [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Vous pouvez télécharger et modifier ces modèles pour déployer un cluster qui correspond mieux à vos besoins.

## <a name="view-performance-counters"></a>Afficher les compteurs de performances

Maintenant que vous avez ajouté l’agent Log Analytics, accédez au portail Log Analytics pour choisir les compteurs de performances à collecter.

1. Dans le portail Azure, accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Sélectionnez **ServiceFabric\<nomEspaceDeTravailLogAnalytics\>**.

2. Cliquez sur **Log Analytics**.

3. Cliquez sur **Paramètres avancés**.

4. Cliquez sur **Données**, puis sur **Compteurs de performances Windows ou Linux**. Une liste des compteurs par défaut que vous pouvez choisir d’activer s’affiche, et vous pouvez également définir l’intervalle de collecte. En outre, vous pouvez ajouter [d’autres compteurs de performances](service-fabric-diagnostics-event-generation-perf.md) à collecter. Le format approprié est référencé dans cet [article](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Cliquez sur **Enregistrer**, puis sur **OK**.

6. Fermez le panneau Paramètres avancés.

7. Sous le titre Général, cliquez sur **Récapitulatif de l’espace de travail**.

8. Des vignettes représentant un graphique s’affichent pour chacune des solutions activées, y compris pour Service Fabric. Cliquez sur le graphique **Service Fabric** pour accéder à la solution Service Fabric Analytics.

9. Vous pouvez voir quelques vignettes avec des graphiques sur le canal opérationnel et les événements de services fiables. La représentation graphique du flux de données correspondant aux compteurs que vous avez sélectionnés s’affichent dans Métriques de nœud.

10. Cliquez sur un graphique de métrique de conteneur pour afficher les détails supplémentaires. Vous pouvez également interroger les données du compteur de performances de la même façon que les événements de cluster et appliquer des filtres sur les nœuds, le nom du compteur de performances et les valeurs à l’aide du langage de requête Kusto.

![Interroger le compteur de performances Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Étapes suivantes

* Collectez les [compteurs de performances](service-fabric-diagnostics-event-generation-perf.md) dont vous avez besoin. Pour configurer l’agent Log Analytics de façon à collecter certains compteurs de performances, voir [Configurer les sources de données](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Configurer les journaux Azure Monitor pour configurer [alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics
* En guise d’alternative, vous pouvez collecter des compteurs de performances via [l’extension Azure Diagnostics et les envoyer à Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
