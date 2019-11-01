---
title: Expérience Journaux simples dans Azure Monitor (préversion) | Microsoft Docs
description: L’expérience Journaux simples vous permet de créer des requêtes de base dans Azure Monitor sans interagir directement avec KQL.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 0b8b23d5d355614bf74b1b22c6a8443b9a2f9391
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932984"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Expérience Journaux simples dans Azure Monitor (préversion)
Azure Monitor offre une [expérience riche](get-started-portal.md) pour la création de [requêtes de journal](log-query-overview.md) à l’aide du langage KQL. Toutefois, vous pouvez ne pas avoir besoin de toute la puissance de KQL et préférer une expérience simplifiée pour des exigences de requête de base. L’expérience Journaux simples vous permet de créer des requêtes de base sans interagir directement avec KQL. Vous pouvez également utiliser Journaux simples comme outil d’apprentissage pour KQL, car vous avez besoin de requêtes plus sophistiquées.

> [!NOTE]
> La fonctionnalité Journaux simples est actuellement implémentée en tant que test uniquement pour Cosmos DB et Key Vault. Partagez votre expérience avec Microsoft via [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) pour nous aider à déterminer si nous allons développer et publier cette fonctionnalité.


## <a name="scope"></a>Étendue
L’expérience Journaux simples récupère des données des tables *AzureDiagnostics*, *AzureMetrics* et *AzureActivity* pour la ressource sélectionnée. 

## <a name="using-simple-logs"></a>Utilisation de Journaux simples
Accédez à Cosmos DB ou Key Vault dans votre abonnement Azure avec les [paramètres de diagnostic configurés pour collecter les journaux dans un espace de travail Log Analytics](../platform/resource-logs-collect-storage.md). Cliquez sur **Journaux** dans le menu **Monitoring** (Supervision) pour ouvrir l’expérience Journaux simples.

![Menu](media/simple-logs/menu.png)

Sélectionnez un **champ** et un **opérateur**, puis spécifiez une **valeur** pour la comparaison. Cliquez sur **+** et spécifiez **Et/Ou** pour ajouter des critères supplémentaires.

![Critères](media/simple-logs/criteria.png)

Cliquez sur **Exécuter** pour afficher les résultats de la requête.

## <a name="view-and-edit-kql"></a>Afficher et modifier KQL
Sélectionnez **Éditeur de requête** pour ouvrir le KQL généré par la requête Journaux simples dans l’expérience Log Analytics complète. 

![Éditeur de requête](media/simple-logs/query-editor.png)

Vous pouvez modifier directement le KQL et utiliser d’autres fonctionnalités de Log Analytics telles que des filtres pour affiner vos résultats.

![Modifier KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Étapes suivantes

- Suivre un tutoriel sur l’[utilisation de Log Analytics dans le portail Azure](get-started-portal.md).
- Suivre un tutoriel sur l’[écriture de requêtes de journal](get-started-portal.md).
