---
title: 'Tutoriel : Commencer l’orchestration avec les pipelines'
description: Dans ce tutoriel, vous allez découvrir comment orchestrer des pipelines et des activités en utilisant Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e32a6a9817f2a3176e96e39c5e261875e8f4ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093477"
---
# <a name="orchestrate-with-pipelines"></a>Orchestrer avec des pipelines

Dans ce tutoriel, vous allez découvrir comment orchestrer des pipelines et des activités en utilisant Synapse Studio. 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez orchestrer un large éventail de tâches dans Azure Synapse.

1. Dans Synapse Studio, accédez au hub **Orchestrer**.
1. Sélectionnez **+**  > **Pipeline** pour créer un pipeline.
1. Accédez au hub **Développer** et recherchez le notebook que vous avez créé précédemment.
1. Faites glisser ce notebook dans le pipeline.
1. Dans le pipeline, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.
1. Dans **Choisissez un déclencheur**, sélectionnez **Nouveau** et, pour la **périodicité**, définissez le déclencheur pour qu’il s’exécute toutes les heures.
1. Sélectionnez **OK**.
1. Sélectionnez **Publier tout**. Le pipeline s’exécute toutes les heures.
1. Pour lancer l’exécution du pipeline immédiatement, sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
                                 
