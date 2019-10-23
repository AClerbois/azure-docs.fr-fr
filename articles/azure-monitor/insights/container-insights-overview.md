---
title: Vue d’ensemble d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit Azure Monitor pour les conteneurs qui surveille la solution AKS Container Service et la valeur qu’elle offre en surveillant l’intégrité de vos clusters AKS et Container Instances dans Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/02/2019
ms.openlocfilehash: 8a6a9862679508971edd23b2d4220446c2692d72
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555381"
---
# <a name="azure-monitor-for-containers-overview"></a>Vue d’ensemble d’Azure Monitor pour les conteneurs

Azure Monitor pour les conteneurs est une fonctionnalité conçue pour surveiller les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Azure Monitor pour les conteneurs vous permet de surveiller les performances en collectant des métriques sur le processeur et la mémoire à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Les journaux d’activité de conteneur sont aussi collectés.  Une fois que vous avez activé la supervision des clusters Kubernetes, les métriques et les journaux d’activité sont automatiquement collectés à l’aide d’une version conteneurisée de l’agent Log Analytics pour Linux. Les métriques sont rédigées dans la banque de métriques et les données de journaux dans la banque de journaux associée à votre espace de travail [Log Analytics](../log-query/log-query-overview.md). 

![Architecture d’Azure Monitor pour conteneurs](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Qu’est-ce qu’Azure Monitor pour les conteneurs vous apporte ?

Azure Monitor pour conteneurs offre une expérience de surveillance complète via différentes fonctionnalités vous permettant de comprendre les performances et l’intégrité de votre cluster Kubernetes et des charges de travail de conteneur. Avec Azure Monitor pour conteneurs, vous pouvez :

* Identifier les conteneurs AKS en cours d’exécution sur le nœud, ainsi que leur utilisation moyenne du processeur et de la mémoire. Cette information peut vous aider à identifier les goulots d’étranglement des ressources.
* Identifier l’utilisation du processeur et de la mémoire des groupes de conteneurs et de leurs conteneurs hébergés dans Azure Container Instances.  
* Identifier l’emplacement du conteneur dans un contrôleur ou un pod. Cette information peut vous permettre de voir les performances globales du contrôleur ou du pod.
* Voir la quantité de ressources utilisée par les charges de travail qui sont exécutées sur l’hôte et qui ne sont pas liées aux processus standard nécessaires à la prise en charge du pod
* Comprendre le comportement du cluster lorsqu’il subit des charges moyennes et très importantes. Cette information peut vous aider à identifier les besoins en capacité et à déterminer la charge maximale que le cluster peut supporter. 
* Configurer des alertes pour être notifié de façon proactive ou pour enregistrer lorsque l’utilisation de la mémoire et du processeur sur des nœuds ou des conteneurs dépasse vos seuils.
* Bénéficier d’une intégration à [Prometheus](https://prometheus.io/docs/introduction/overview/) pour afficher les métriques d’application et de charge de travail qu’il collecte à partir des nœuds et Kubernetes à l’aide de [requêtes](container-insights-log-search.md) pour créer des alertes personnalisées, des tableaux de bord et une analyse détaillée des performances.

    >[!NOTE]
    >La prise en charge de Prometheus est une fonctionnalité de la préversion publique actuellement.
    >

* Surveillez les charges de travail de conteneur [déployées sur AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Regardez la vidéo suivante qui fournit une présentation approfondie de niveau intermédiaire pour vous apprendre à surveiller votre cluster AKS avec Azure Monitor pour les conteneurs.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Comment accéder à cette fonctionnalité ?

Vous pouvez accéder à Azure Monitor pour les conteneurs de deux manières : à partir d’Azure Monitor ou à partir du cluster AKS sélectionné directement. À partir d’Azure Monitor, vous disposez d’une perspective globale de tous les conteneurs déployés, ceux qui sont surveillés et ceux qui ne sont pas, vous permettant ainsi de rechercher et de filtrer sur vos abonnements et groupes de ressources, puis d’explorer Azure Monitor pour les conteneurs à partir du conteneur sélectionné.  Sinon, vous pouvez accéder directement à la fonctionnalité à partir d’un conteneur AKS sélectionné dans la page AKS.  

![Vue d’ensemble des méthodes d’accès à Azure Monitor pour les conteneurs](./media/container-insights-overview/azmon-containers-experience.png)

Si vous souhaitez surveiller et gérer vos hôtes de conteneur Docker et Windows exécutés en dehors d’AKS pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de supervision des conteneurs](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment activer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance.  
