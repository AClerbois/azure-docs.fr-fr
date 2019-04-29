---
title: Glossaire des outils des bases de données élastiques | Microsoft Docs
description: Explication des termes utilisés pour les outils de base de données élastique
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585581"
---
# <a name="elastic-database-tools-glossary"></a>Glossaire des outils de base de données élastique

Les termes suivants sont définis pour les [outils des bases de données élastiques](sql-database-elastic-scale-introduction.md), une fonction de Base de données SQL Azure. Les outils permettent de gérer les [cartes de partition](sql-database-elastic-scale-shard-map-management.md) et incluent la [bibliothèque cliente](sql-database-elastic-database-client-library.md), [l’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md), les [pools élastiques](sql-database-elastic-pool.md) et les [requêtes](sql-database-elastic-query-overview.md). 

Ces termes sont utilisés dans [Ajout d’une partition à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) et [Utiliser la classe RecoveryManager pour résoudre les problèmes de carte de partition](sql-database-elastic-database-recovery-manager.md).

![Termes liés ç l’infrastructure flexible][1]

**Base de données** : base de données SQL Azure. 

**Routage dépendant des données** : fonctionnalité qui permet à une application de se connecter à une partition en fonction d’une clé de sharding spécifique. Consultez [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Comparer à la **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)**.

**Carte de partitions globale** : mappage entre des clés de sharding et leurs partitions respectives au sein d’un **jeu de partitions**. La carte de partitions globale est stockée dans le **gestionnaire des cartes de partitions**. Comparer à la **carte de partitions locale**.

**Carte de partitions de liste** : carte de partitions dans laquelle les clés de sharding sont mappées individuellement. Comparer à la **carte de partitions de plage**.   

**Carte de partitions locale** : stockée sur une partition, la carte de partitions locale contient des mappages pour les shardlets qui résident sur la partition.

**Requête sur plusieurs partitions** : possibilité d’émettre une requête sur plusieurs partitions ; les ensembles de résultats sont retournés avec la sémantique UNION ALL (également appelée « requête de distribution ramifiée »). Comparer au **routage dépendant des données**.

**Multilocataire** et **Monolocataire** : cette illustration montre une base de données monolocataire et une base de données multilocataire :

![Bases de données mono-utilisateur et mutualisée](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Voici une représentation de bases de données **partitionnées** de type mono-utilisateur et mutualisée. 

![Bases de données mono-utilisateur et mutualisée](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Carte de partitions de plage** : carte de partitions dans laquelle la stratégie de distribution de partitions est basée sur plusieurs plages de valeurs contiguës. 

**Tables de référence** : tables non partitionnées, mais qui sont répliquées sur plusieurs partitions. Par exemple, les codes postaux peuvent être stockés dans une table de référence. 

**Partition** : base de données Azure SQL Database qui stocke les données d’un jeu de données partitionnées. 

**Élasticité des partitions** : capacité à effectuer une **mise à l’échelle horizontale** et une **mise à l’échelle verticale**.

**Tables partitionnées** : tables qui sont partitionnées, c’est-à-dire dont les données sont réparties entre plusieurs partitions en fonction de la valeur de leur clé de sharding. 

**Clé de sharding** : valeur de colonne qui détermine la façon dont les données sont réparties entre les partitions. Les types de valeur disponibles sont les suivants : **int**, **bigint**, **varbinary** ou **uniqueidentifier**. 

**Ensemble de partitions** : collection de partitions attribuées à la même carte de partitions dans le gestionnaire de cartes de partitions.  

**Shardlet** : toutes les données associées à la valeur unique d’une clé de sharding sur une partition. Un shardlet est la plus petite unité de transfert de données possible lors de la redistribution des tables partitionnées. 

**Carte de partitions** : ensemble de mappages entre les clés de sharding et leurs partitions respectives.

**Gestionnaire de cartes de partitions** : objet de gestion et magasin de données qui contient les cartes de partitions, les emplacements des partitions et les mappages pour un ou plusieurs ensembles de partitions.

![Mappages][2]

## <a name="verbs"></a>Verbes et adverbes
**Mise à l’échelle horizontale** : action qui consiste à effectuer le scale-out (ou le scale-in) d’une collection de partitions en ajoutant ou supprimant des partitions dans une carte de partitions, comme dans l’exemple ci-dessous.

![Mise à l’échelle horizontale et verticale][3]

**Fusionner** : action qui consiste à déplacer les shardlets de deux partitions vers une seule et à mettre à jour la carte de partitions en conséquence.

**Déplacement de shardlet** : action qui consiste à déplacer un shardlet unique vers une partition différente. 

**Partitionner** : action qui consiste à partitionner horizontalement des données structurées de façon identique sur plusieurs bases de données en fonction d’une clé de sharding.

**Fractionner** : action qui consiste à déplacer plusieurs shardlets d’une partition vers une autre (généralement nouvelle). Une clé de partitionnement est fournie par l'utilisateur comme point de fractionnement.

**Mise à l’échelle verticale** : action qui consiste à effectuer le scale-up (ou le scale-down) de la taille de calcul d’une partition individuelle. Par exemple, modifier une partition standard vers l’édition Premium (qui génère plus de ressources informatiques). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

