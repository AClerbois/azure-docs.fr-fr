---
title: Créer des ressources techniques d’image conteneur Azure | Place de marché Azure
description: Créer les ressources techniques pour un conteneur Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819049"
---
# <a name="prepare-your-container-technical-assets"></a>Préparer vos ressources techniques de conteneur

Cet article décrit les étapes et les conditions requises pour configurer une offre de conteneur de la Place de marché Azure.

## <a name="before-you-begin"></a>Avant de commencer

Examinez la documentation [Azure Container Instances](https://docs.microsoft.com/azure/container-instances), qui fournit des démarrages rapides, des tutoriels et des exemples.

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.
 
En plus de votre domaine de solution, votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

-   Connaissances de base des [services Azure](https://azure.microsoft.com/services/) 
-   Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
-   Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
-   Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Expérience de travail avec [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Outils suggérés

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion de votre image conteneur :

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

En outre, nous vous recommandons d’ajouter les outils suivants à votre environnement de développement :

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Nous vous suggérons également d’examiner les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et, si vous utilisez Visual Studio, sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Créer l’image conteneur

Consultez les liens suivants pour plus d’informations :

* [Tutoriel : Créer une image conteneur à déployer sur Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutoriel : Générer et déployer des images conteneurs dans le cloud avec Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Étapes suivantes

[Créer votre offre de conteneur](./cpp-create-offer.md)
