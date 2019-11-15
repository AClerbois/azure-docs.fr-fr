---
title: Optimisation des performances avec la mise en cache des jeux de résultats
description: Vue d’ensemble de la fonctionnalité de mise en cache du jeu de résultats pour Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692942"
---
# <a name="performance-tuning-with-result-set-caching"></a>Optimisation des performances avec la mise en cache des jeux de résultats  
Lorsque la mise en cache du jeu de résultats est activée, Azure SQL Data Warehouse met automatiquement en cache les résultats de la requête dans la base de données utilisateur ce qui permet de les utiliser de façon répétée.  Ainsi, les exécutions de requêtes suivantes obtiennent les résultats directement à partir du cache persistant de sorte que le recalcul n’est pas nécessaire.   La mise en cache des jeux de résultats améliore les performances des requêtes et réduit l’utilisation des ressources de calcul.  De plus, les requêtes qui recourent au cache du jeu de résultats n’utilisent pas d’emplacements de concurrence et ne sont donc pas prises en compte pour l’application des limites de concurrence existantes. Pour des raisons de sécurité, les utilisateurs ne peuvent accéder aux résultats mis en cache que s’ils ont les mêmes autorisations d’accès aux données que les utilisateurs qui créent les résultats mis en cache.  

## <a name="key-commands"></a>Commandes clés
[Activer/désactiver la mise en cache du jeu de résultats pour une base de données utilisateur](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Activer/désactiver la mise en cache du jeu de résultats pour une session](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Vérifier la taille du jeu de résultats mis en cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Nettoyer le cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Éléments non mis en cache  

Une fois la mise en cache du jeu de résultats activée pour une base de données, les résultats sont mis en cache pour toutes les requêtes jusqu’à ce que le cache soit plein, à l’exception des requêtes suivantes :
- Requêtes utilisant des fonctions non déterministes telles que DateTime.Now()
- Requêtes utilisant des fonctions définies par l’utilisateur
- Requêtes utilisant des tables avec une sécurité au niveau des lignes ou une sécurité au niveau des colonnes activée
- Requêtes qui retournent des données avec une taille de ligne supérieure à 64 Ko

> [!IMPORTANT]
> Les opérations de création du cache de jeux de résultats et de récupération des données à partir du cache se produisent sur le nœud de contrôle d’une instance de l’entrepôt de données. Lorsque la mise en cache du jeu de résultats est activée, l’exécution de requêtes qui retournent un jeu de résultats volumineux (par exemple, supérieur à un million de lignes) peut entraîner une utilisation élevée du processeur sur le nœud de contrôle et ralentir la réponse globale à la requête sur l’instance.  Ces requêtes sont couramment utilisées lors de l’exploration de données et des opérations ETL. Pour éviter de contraindre le nœud de contrôle et de provoquer un problème de performances, il est conseillé aux utilisateurs de désactiver la mise en cache du jeu de résultats sur la base de données avant d’exécuter ce type de requêtes.  

Exécutez cette requête pendant toute la durée des opérations de mise en cache du jeu de résultats pour une requête :

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Voici un exemple de sortie pour une requête exécutée avec la mise en cache du jeu de résultats désactivée.

![Étapes-requête-avec-RSC-désactivée](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Voici un exemple de sortie pour une requête exécutée avec la mise en cache du jeu de résultats activée.

![Étapes-requête-avec-RSC-activée](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quand les résultats mis en cache sont utilisés

Le jeu de résultats mis en cache est réutilisé pour une requête si toutes les conditions suivantes sont réunies :
- L’utilisateur qui exécute la requête a accès à toutes les tables référencées dans la requête.
- Il existe une correspondance exacte entre la nouvelle requête et la requête précédente qui a généré le cache du jeu de résultats.
- Il n’y a aucune modification de données ou de schéma dans les tables à partir desquelles le jeu de résultats mis en cache a été généré.

Exécutez cette commande pour vérifier si une requête a été exécutée avec un accès ou un échec du cache de résultats. En cas de présence d’un cache, result_cache_hit retourne 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gérer les résultats mis en cache 

La taille maximale du cache du jeu de résultats est de 1 To par base de données.  Les résultats mis en cache sont automatiquement invalidés lorsque les données de requête sous-jacentes sont modifiées.  

L’éviction du cache est gérée par Azure SQL Data Warehouse automatiquement après cette planification : 
- Toutes les 48 heures si le jeu de résultats n’a pas été utilisé ou a été invalidé. 
- Lorsque le cache du jeu de résultats approche la taille maximale.

Les utilisateurs peuvent vider manuellement l’intégralité du cache du jeu de résultats à l’aide de l’une des options suivantes : 
- Désactiver la fonctionnalité cache du jeu de résultats pour la base de données 
- Exécutez DBCC DROPRESULTSETCACHE tout en étant connecté à la base de données

La suspension d’une base de données n’a pas pour effet de vider le jeu de résultats mis en cache.  

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
