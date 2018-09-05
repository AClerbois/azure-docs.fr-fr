---
title: Vue d’ensemble d’OpenShift dans Azure | Microsoft Docs
description: Vue d’ensemble d’OpenShift dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190341"
---
# <a name="openshift-in-azure"></a>OpenShift dans Azure

OpenShift est une plateforme d’applications de conteneur ouverte et extensible qui permet aux entreprises d’avoir accès à Docker et Kubernetes.  

OpenShift inclut Kubernetes pour la gestion et l’orchestration du conteneur. Il offre également des outils de développement centrés sur les opérations qui permettent :

- Un développement d’applications rapide.
- Un déploiement et une mise à l’échelle plus simples.
- La maintenance du cycle de vie à long terme des applications et des équipes.

Plusieurs versions de OpenShift sont disponibles :

- OKD (anciennement OpenShift Origin)
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Parmi ces quatre versions décrites dans cet article, seules deux sont compatibles avec un déploiement dans Azure par les clients : OpenShift Origin et OpenShift Container Platform.

## <a name="okd-formerly-openshift-origin"></a>OKD (anciennement OpenShift Origin)

OKD est un projet initial [open source](https://www.okd.io/) d’OpenShift dont le support est assuré par la communauté. OKD peut être installé sur CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform est une [version commerciale](https://www.openshift.com) adaptée aux entreprises dont Red Hat est l’éditeur et assure le support. Avec cette version, le client achète les droits nécessaires pour OpenShift Container Platform et est responsable de l’installation et de la gestion de l’ensemble de l’infrastructure.

Étant donné que le client est « propriétaire » de l’ensemble de la plateforme, il peut l’installer dans son centre de données local ou sur un cloud public (comme Azure, AWS ou Google).

## <a name="openshift-online"></a>OpenShift Online

Online est une version d’OpenShift *multilocataire* gérée par Red Hat qui utilise Container Platform. Red Hat gère toute l’infrastructure sous-jacente (comme les machines virtuelles, le cluster OpenShift, la mise en réseau et le stockage). 

Avec cette version, le client déploie des conteneurs, mais n’a aucun contrôle sur les hôtes qu’ils exécutent. Étant donné qu’Online est multilocataire, les conteneurs peuvent coexister sur les mêmes hôtes de machine virtuelle que les conteneurs d’autres clients. Le coût est fixé par conteneur.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated est une version d’OpenShift *à locataire unique*, gérée par Red Hat, qui utilise Container Platform. Red Hat gère toute l’infrastructure sous-jacente (machines virtuelles, cluster OpenShift, mise en réseau, stockage, etc.). Le cluster est propre à un seul client et est exécuté dans un cloud public (comme AWS ou Google, et Azure disponible début 2018). Un cluster de départ inclut quatre nœuds d’application pour 48 000 $ par an (payé au préalable).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les éléments prérequis communs pour OpenShift dans Azure](./openshift-prerequisites.md)
- [Déployer OpenShift Origin dans Azure](./openshift-origin.md)
- [Déployer OpenShift Container Platform dans Azure](./openshift-container-platform.md)
- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes liés au déploiement d’OpenShift](./openshift-troubleshooting.md)
