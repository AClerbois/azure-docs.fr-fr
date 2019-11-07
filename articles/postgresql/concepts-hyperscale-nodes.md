---
title: Nœuds dans Azure Database pour PostgreSQL - Hyperscale (Citus)
description: Découvrez les deux types de nœud, coordinateur et de travail, au sein d’un groupe de serveurs dans Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 5348c8bbe432cd024a8da93ca866ae4458f956e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468097"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nœuds dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Le type d’hébergement Hyperscale (Citus) permet à Azure Database pour les serveurs PostgreSQL (appelés nœuds) de se coordonner entre eux dans une architecture dite « sans partage » (« shared nothing »). Collectivement, les nœuds dans un groupe de serveurs contiennent davantage de données et utilisent plus de cœurs d’UC que ce qui serait possible sur un seul serveur. L’architecture permet également à la base de données de procéder à la mise à l’échelle en ajoutant des nœuds supplémentaires au groupe de serveurs.

## <a name="coordinator-and-workers"></a>Coordinateur et travailleurs

Chaque groupe de serveurs dispose d’un nœud coordinateur et de plusieurs Workers. Les applications envoient leurs requêtes vers le nœud coordinateur, qui les relaie vers les Workers concernés et accumule leurs résultats. Les applications ne sont pas en mesure de se connecter directement aux Workers.

Soit le coordinateur achemine chaque requête vers un nœud Worker unique, soit il la met en parallèle sur plusieurs d’entre eux, en fonction de l’emplacement des données requises, sur un nœud unique ou sur plusieurs nœuds. Le coordinateur décide de la mesure à prendre en consultant les tables de métadonnées. Ces tables suivent les noms DNS et l’intégrité des nœuds Worker, ainsi que la distribution des données entre les nœuds.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment les nœuds stockent les [données distribuées](concepts-hyperscale-distributed-data.md)
