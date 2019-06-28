---
title: Guide pratique pour la gestion de l’agent Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article décrit la gestion des tâches de maintenance les plus courantes avec l’agent en conteneur Log Analytics utilisé par Azure Monitor pour conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: e1d47be159d4721aed4b055a51acf675688b855e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071798"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Guide pratique pour la gestion de l’agent Azure Monitor pour conteneurs
Azure Monitor pour conteneurs utilise une version en conteneur de l’agent Log Analytics pour Linux. Après le déploiement initial, vous devrez sans doute exécuter des tâches courantes ou facultatives pendant son cycle de vie. Cet article explique comment mettre à niveau l’agent manuellement et comment désactiver la collecte des variables d’environnement à partir d’un conteneur donné. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Comment mettre à niveau l’agent Azure Monitor pour conteneurs
Azure Monitor pour conteneurs utilise une version en conteneur de l’agent Log Analytics pour Linux. Lorsqu'une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS).  

Si la mise à niveau de l’agent échoue, cet article décrit le processus pour mettre à niveau l’agent manuellement. Pour suivre les versions publiées, consultez [Annonces des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Mise à niveau de l’agent sur un cluster Kubernetes surveillé
Le processus de mise à niveau de l’agent se compose de deux étapes simples. La première étape consiste à désactiver la surveillance avec Azure Monitor pour conteneurs à l’aide de l'interface de ligne de commande Azure.  Suivez les étapes décrites dans l'article [Désactiver la surveillance](container-insights-optout.md?#azure-cli). L'interface de ligne de commande Azure permet de supprimer l’agent des nœuds du cluster, sans incidence sur la solution et les données correspondantes stockées dans l’espace de travail. 

>[!NOTE]
>Lors de cette activité de maintenance, les nœuds du cluster ne transfèrent pas les données collectées et les vues de performances n'affichent pas les données entre le moment où vous supprimez l'agent et celui où vous installez la nouvelle version. 
>

Afin d’installer la nouvelle version de l’agent, suivez les étapes décrites dans l’article [Activer la surveillance de l’intégration à l’aide de l’interface de ligne de commande Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli) pour terminer ce processus.  

Après avoir activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité mis à jour du cluster. Pour vérifier que l’agent a bien été mis à niveau, exécutez la commande : `kubectl logs omsagent-484hw --namespace=kube-system`

L’état doit ressembler à l’exemple suivant, où la valeur *omi* et *omsagent* doit correspondre à la dernière version spécifiée dans [l’historique des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Comment désactiver la collecte des variables d’environnement sur un conteneur
Azure Monitor pour conteneurs collecte des variables d’environnement à partir des conteneurs en cours d’exécution dans un pod, puis les affiche dans le volet des propriétés du conteneur sélectionné dans la vue **Conteneurs**. Vous pouvez contrôler ce comportement en désactivant la collecte pour un conteneur spécifique, soit pendant le déploiement du cluster AKS, soit après le déploiement en définissant la variable d’environnement *AZMON_COLLECT_ENV*. Cette fonctionnalité est disponible avec l’agent ciprod11292018 et versions ultérieures.  

Pour désactiver la collecte des variables d’environnement sur un nouveau conteneur ou sur un conteneur existant, définissez la variable **AZMON_COLLECT_ENV** avec la valeur **False** dans votre fichier de configuration yaml pour le déploiement de Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Exécutez la commande suivante pour appliquer la modification à votre conteneur AKS : `kubectl apply -f  <path to yaml file>`.

Pour vérifier que le changement de configuration est effectif, sélectionnez un conteneur dans la vue **Conteneurs** d’Azure Monitor pour conteneurs puis, dans le volet des propriétés, développez **Variables d’environnement**.  La section doit afficher uniquement la variable créée précédemment - **AZMON_COLLECT_ENV = FALSE**. Pour tous les autres conteneurs, la section des variables d’environnement doit répertorier toutes les variables d’environnement détectées.   

Pour réactiver la découverte des variables d’environnement, suivez le même processus et remplacez la valeur **False** par **True**, avant de réexécuter la commande `kubectl` pour mettre à jour le conteneur.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lors de la mise à niveau de l'agent, consultez le [guide de résolution des problèmes](container-insights-troubleshoot.md).