---
title: Qu’est-ce qu’Azure Dev Spaces ?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Découvrez comment Azure Dev Spaces fournit aux équipes une expérience de développement Kubernetes rapide et itérative dans les clusters Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ad884d5e054f23269b238b791e250c732d70a9b8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210636"
---
# <a name="what-is-azure-dev-spaces"></a>Qu’est-ce qu’Azure Dev Spaces ?

Azure Dev Spaces fournit aux équipes une expérience de développement Kubernetes rapide et itérative dans les clusters Azure Kubernetes Service (AKS). Azure Dev Spaces vous permet également de tester et déboguer tous les composants de votre application dans AKS avec une configuration minimale de machine de développement, sans répliquer ou simuler des dépendances.

![Le diagramme montre deux versions d’une application développée indépendamment. Elles sont ensuite combinées en une seule version dans un environnement de développement Azure Dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Simplification du développement Kubernetes avec Azure Dev Spaces

Grâce à Azure Dev Spaces, les équipes peuvent se concentrer sur le développement et l’itération rapide de leur application de microservices en exploitant directement leur architecture complète de microservices ou une application en cours d’exécution dans AKS. Azure Dev Spaces permet également de mettre à jour de façon indépendante certaines parties de votre architecture de microservices, sans affecter le reste du cluster AKS ou d’autres développeurs. Azure Dev Spaces est une solution conçue pour le développement et le test dans un développement de niveau inférieur et dans des environnements de test. Elle n’est pas destinée à être exécutée sur des clusters AKS de production.

Étant donné que les équipes peuvent utiliser l’application complète et collaborer directement dans AKS, Azure Dev Spaces offre les avantages suivants :

* Simplifie la configuration de l’ordinateur local
* Réduit le temps de configuration pour les nouveaux développeurs qui intègrent l’équipe
* Accélère le travail d’une équipe via une itération plus rapide
* Réduit le nombre d’environnements de développement et d’intégration redondants car les membres de l’équipe peuvent partager un cluster
* Supprime la nécessité de répliquer ou de simuler des dépendances
* Améliore la collaboration entre les équipes de développement et celles des collaborateurs, notamment les équipes DevOps

Azure Dev Spaces intègre des outils permettant de générer des ressources Docker et Kubernetes pour vos projets. Ces outils vous aident à ajouter facilement des applications nouvelles et existantes à la fois à un espace de développement et d’autres clusters AKS.

Pour plus d’informations sur le fonctionnement d’Azure Dev Spaces, consultez [Fonctionnement et configuration d’Azure Dev Spaces][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Régions et configurations prises en charge

Azure Dev Spaces est pris en charge par les clusters AKS dans [certaines régions][supported-regions] uniquement. Azure Dev Spaces prend en charge l’utilisation de l’interface [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou de [Visual Studio Code](https://code.visualstudio.com/download) avec l’[extension Azure Dev espaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installée sur Linux, macOS, Windows 8 ou ultérieur pour créer et exécuter vos applications sur AKS. Il prend aussi en charge l’utilisation de [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installé sur Windows avec la charge de travail de développement Azure.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage plus sur le fonctionnement d’Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Fonctionnement d’Azure Dev Spaces](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
