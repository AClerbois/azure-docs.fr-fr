---
title: "Vue d’ensemble d’Azure Container Instances | Azure Docs"
description: Comprendre Azure Container Instances
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6e614f1120b3dc54871b393ac0a2703c21b30ae8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-instances"></a>Azure Container Instances

Les conteneurs deviennent rapidement le meilleur moyen pour mettre en package, déployer et gérer des applications cloud. Azure Container Instances propose la façon la plus simple et rapide d’exécuter un conteneur dans Azure, sans avoir à configurer des machines virtuelles ni adopter un service de niveau supérieur.

Azure Container Instances est une excellente solution pour les scénarios qui peuvent fonctionner dans des conteneurs isolés, y compris les applications basiques, automatiser des tâches et créer des travaux. Pour les scénarios où vous devez disposer d’orchestration de conteneur complète (détection de service dans plusieurs conteneurs, mise à l’échelle automatique et mises à niveau d’application coordonnées), nous vous recommandons le service [Azure Container Service](https://docs.microsoft.com/azure/container-service/).

## <a name="fast-startup-times"></a>Temps de démarrage rapide

Le service Containers offre des avantages de démarrage conséquents sur les machines virtuelles. Avec Azure Container Instances, vous pouvez démarrer un conteneur dans Azure en quelques secondes, sans avoir à configurer ni gérer des machines virtuelles.

## <a name="hypervisor-level-security"></a>Sécurité au niveau de l’hyperviseur

D’un point de vue historique, les conteneurs ont offert l’isolation de dépendance d’application et la gouvernance des ressources, mais n’ont pas été considérés suffisamment renforcés pour une utilisation de plusieurs locataires hostile. Avec Azure Container Instances, votre application se retrouve aussi isolée dans un conteneur que dans une machine virtuelle.

## <a name="custom-sizes"></a>Tailles personnalisées

Les conteneurs sont généralement optimisés pour n’exécuter qu’une application, mais les besoins précis de ces applications peuvent grandement varier. Avec Azure Container Instances, vous pouvez demander ce dont vous avez besoin en termes de mémoire et de noyaux processeur. Vous payez pour ce que vous demandez et vous êtes facturé immédiatement, pour pouvoir optimiser avec précision vos dépenses selon vos besoins.

## <a name="public-ip-connectivity"></a>Connectivité IP publique

Avec Azure Container Instances, vous pouvez exposer vos conteneurs sur internet avec une adresse IP publique. Prochainement, nous comptons étendre nos fonctionnalités réseau afin d’inclure l’intégration avec les réseaux virtuels, des équilibreurs de charges et d’autres parties essentielles de l’infrastructure de mise en réseau Azure.

## <a name="persistent-storage"></a>Stockage persistant

Pour récupérer et conserver des états avec Azure Container Instances, nous proposons le montage direct des partages de fichiers Azure.

## <a name="linux-and-windows-containers"></a>Conteneurs Windows et Linux

Avec Azure Container Instances, vous pouvez programmer des conteneurs Windows et Linux avec la même API. Indiquez simplement le type de système d’exploitation. Le reste est identique.

## <a name="co-scheduled-groups"></a>Groupes co-planifiés

Azure Container Instances prend en charge la planification de groupes de plusieurs conteneurs qui partagent un même hôte, réseau local, stockage et cycle de vie. Vous pouvez ainsi combiner votre application principale avec d’autres ayant un rôle d’assistance, tel que la journalisation.

## <a name="next-steps"></a>Étapes suivantes

Essayez de déployer un conteneur dans Azure avec une seule commande à l’aide de notre [guide de démarrage rapide](container-instances-quickstart.md).