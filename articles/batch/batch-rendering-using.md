---
title: Fonctionnalités de rendu - Azure Batch
description: Comment utiliser les fonctionnalités d’Azure Batch Rendering
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2f423de63b7cec24d9d9c4541ddab0ac38b1076a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542305"
---
# <a name="using-azure-batch-rendering"></a>Utilisation d’Azure Batch Rendering

Il existe plusieurs façons d’utiliser Azure Batch Rendering :

* API :
  * Écrire du code en utilisant l’une des API Batch.  Les développeurs peuvent intégrer les fonctionnalités Azure Batch dans leurs applications ou workflows existants, dans le cloud ou en local.
* Outils en ligne de commande :
  * Vous pouvez utiliser la [ligne de commande Azure](https://docs.microsoft.com/cli/azure/) ou [PowerShell](https://docs.microsoft.com/powershell/azure/overview) pour écrire un script d’utilisation de Batch.
  * En particulier, la [prise en charge des modèles CLI Batch](https://docs.microsoft.com/azure/batch/batch-cli-templates) facilite considérablement la création de pools et l’envoi de travaux.
* Interface utilisateur de Batch Explorer :
  * [Batch Explorer](https://github.com/Azure/BatchLabs) est un outil client multiplateforme qui permet également d’effectuer la gestion et le monitoring de comptes Batch.
  * Pour chacune des applications de rendu, un certain nombre de modèles de pool et de travail est fourni pour créer facilement des pools et envoyer des travaux.  Un jeu de modèles est listé dans l’interface utilisateur de l’application, les fichiers de modèle étant accessibles à partir de GitHub.
  * Vous pouvez créer entièrement des modèles personnalisés ou copier les modèles fournis de GitHub et les modifier.
* Plug-ins d’applications clientes :
  * Les plug-ins disponibles permettent d’utiliser le rendu Batch directement dans les applications clientes de conception et de modélisation.  Les plug-ins appellent principalement l’application Batch Explorer avec des informations contextuelles sur le modèle 3D actif et incluent des fonctionnalités permettant de gérer les ressources.

Pour les utilisateurs finaux qui ne sont ni des développeurs ni des experts Azure, le meilleur moyen d’essayer Azure Batch Rendering consiste à utiliser l’application Batch Explorer : soit directement, soit en l’appelant à partir d’un plug-in d’application cliente.

## <a name="using-batch-explorer"></a>Utilisation de Batch Explorer

Pour accéder à un tutoriel pas à pas couvrant le rendu à l’aide de Batch Explorer, consultez le [tutoriel Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Télécharger et installer

Les [téléchargements de Batch Explorer sont disponibles](https://azure.github.io/BatchExplorer/) pour Windows, OSX et Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Utilisation de modèles pour créer des pools et exécuter des travaux

Un ensemble complet de modèles est disponible pour une utilisation avec Batch Explorer. Ces modèles facilitent la création de pools et l’envoi de travaux pour les diverses applications de rendu sans avoir à spécifier toutes les propriétés nécessaires pour créer des pools, des travaux et des tâches directement avec Batch.  Les modèles disponibles dans Batch Explorer sont stockés et visibles dans [un dépôt GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

Les modèles fournis répondent aux besoins de toutes les applications présentes sur les images de machine virtuelle de rendu de la Place de marché.  Il existe plusieurs modèles pour chaque application, notamment des modèles de pool pour prendre en charge les besoins des pools UC et GPU, Windows et Linux. Les modèles de travaux incluent le rendu Blender (cadre complet ou mosaïque) et le rendu distribué V-Ray. L’ensemble des modèles fournis sera développé au fur et à mesure pour répondre aux besoins des autres fonctionnalités Batch, comme la mise à l’échelle automatique des pools.

Il est également possible de produire des modèles personnalisés : soit à partir de zéro, soit en modifiant les modèles fournis. Vous pouvez utiliser des modèles personnalisés en sélectionnant l’élément Modèles locaux dans la section Galerie de Batch Explorer.

### <a name="file-system-and-data-movement"></a>Système de fichiers et déplacement des données

La section Données dans Batch Explorer permet de copier des fichiers entre un système de fichiers local et des comptes de stockage Azure.

## <a name="client-application-plug-ins"></a>Plug-ins d’applications clientes

Des plug-ins sont disponibles pour certaines applications clientes.  Les plug-ins permettent de créer directement des pools et des travaux à partir de l’application ou d’appeler Batch Explorer.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des exemples de rendu Batch, suivez les deux tutoriels suivants :

* [Rendu à l’aide d’Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendu à l’aide de Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)