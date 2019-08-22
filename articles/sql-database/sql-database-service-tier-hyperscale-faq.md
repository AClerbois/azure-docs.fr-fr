---
title: Questions fréquentes (FAQ) sur le niveau Hyperscale dans Azure SQL Database | Microsoft Docs
description: Réponses aux questions fréquemment posées par les clients sur une base de données Azure SQL dans le niveau de service Hyperscale, communément appelée « base de données Hyperscale ».
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 951d5bb10fbeeac090a1edb510b7214855477eac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515357"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Questions fréquentes (FAQ) sur les bases de données SQL Azure Hyperscale

Cet article fournit des réponses aux questions fréquemment posées pour les clients envisageant d’utiliser une base de données pour le niveau de service Hyperscale Azure SQL Database, communément appelée « base de données Hyperscale ». Cet article décrit les scénarios pris en charge par Hyperscale et les services multifonctionnalités qui sont compatibles avec SQL Database Hyperscale en général.

- Ce FAQ est destiné aux personnes qui ont des connaissances générales sur le niveau de service Hyperscale et qui cherchent des réponses à leurs questions et préoccupations spécifiques.
- Il n’est pas un guide de mise en œuvre et n’est pas destiné à répondre à des questions sur la façon d’utiliser une base de données SQL Database Hyperscale. Pour cela, nous vous recommandons de vous reporter à la documentation [Azure SQL Database Hyperscale](sql-database-service-tier-hyperscale.md).

## <a name="general-questions"></a>Questions générales

### <a name="what-is-a-hyperscale-database"></a>Qu’est-ce qu’une base de données Hyperscale ?

Une base de données Hyperscale est une base de données Azure SQL dans le niveau de service Hyperscale qui repose sur la technologie de stockage scale-out d’Hyperscale. Une base de données Hyperscale prend en charge jusqu’à 100 To de données, offre des performances et un débit élevés, ainsi qu’une mise à l’échelle rapide pour répondre aux exigences des charges de travail. La mise à l’échelle est transparente pour l’application : la connectivité, le traitement des requêtes, etc., fonctionnent comme pour toute autre base de données SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quels sont les types de ressources et les modèles d’achat qui prennent en charge Hyperscale ?

Le niveau de service Hyperscale est disponible seulement pour les bases de données uniques avec le modèle d’achat vCore dans Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>En quoi le niveau de service Hyperscale diffère-t-il des niveaux de service Usage général et Critique pour l’entreprise ?

Les niveaux de service basés sur des vCore diffèrent principalement en disponibilité, type de stockage et IOPS.

- Le niveau de service Usage général est approprié pour la plupart des charges de travail métier, offrant un ensemble équilibré d’options de calcul et de stockage, où les temps de latence des E/S et de basculement ne sont pas la priorité.
- Le niveau de service Hyperscale est optimisé pour les charges de travail des bases de données très volumineuses.
- Le niveau de service Critique pour l’entreprise est approprié pour les charges de travail métier où la latence des E/S est une priorité.

| | Type de ressource | Usage général |  Hyperscale | Critique pour l’entreprise |
|:---:|:---:|:---:|:---:|:---:|
| **Idéal pour** |Tous|  La plupart des charges de travail d’entreprise. Offre des options de calcul et de stockage équilibrées et économiques. | Applications de données avec des besoins en capacité pour des données volumineuses, et la possibilité de mettre à l’échelle automatiquement le stockage et de mettre à l’échelle la capacité de calcul de façon fluide. | Applications OLTP avec des débits de transactions élevés et la latence des E/S la plus faible. Offre la meilleure résilience aux défaillances grâce à l’utilisation de plusieurs réplicas isolés.|
|  **Type de ressource** ||Base de données unique / pool élastique / instance managée | Base de données unique | Base de données unique / pool élastique / instance managée |
| **Taille de calcul**|Base de données unique / pool élastique * | 1 à 80 cœurs virtuels | 1 à 80 cœurs virtuels* | 1 à 80 cœurs virtuels |
| |Instance gérée | 8, 16, 24, 32, 40, 64, 80 cœurs virtuels | N/A | 8, 16, 24, 32, 40, 64, 80 cœurs virtuels |
| **Type de stockage** | Tous |Stockage distant Premium (par instance) | Stockage découplé avec cache disque SSD local (par instance) | Stockage SSD local ultra-rapide (par instance) |
| **Taille de stockage** | Base de données unique / pool élastique | 5 Go - 4 To | Jusqu’à 100 To | 5 Go - 4 To |
| | Instance gérée  | 32 Go - 8 To | N/A | 32 Go - 4 To |
| **Débit d’E/S** | Base de données unique** | 500 IOPS par vCore avec 7000 IOPS au maximum | L’architecture hyperscale est une architecture à plusieurs niveaux avec une mise en cache sur plusieurs niveaux. L’efficacité des IOPS dépend de la charge de travail. | 5 000 IOPS avec un maximum de 200 000 IOPS|
| | Instance gérée | Dépend de la taille de fichier | N/A | instance managée : Dépend de la taille de fichier|
|**Disponibilité**|Tous|1 réplica, pas d’échelle lecture, pas de cache local | Plusieurs réplicas, jusqu’à 15 échelles lecture, cache local partiel | 3 réplicas, 1 échelle lecture, haute disponibilité redondante interzone, cache local complet |
|**Sauvegardes**|Tous|RA-GRS, 7 à 35 jours (7 jours par défaut)| RA-GRS, 7 jours, récupération jusqu’à une date et heure (PITR) à durée constante | RA-GRS, 7 à 35 jours (7 jours par défaut) |

\* Pools élastiques non pris en charge dans le niveau de service Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>À qui est destiné le niveau de service Hyperscale ?

Le niveau de service Hyperscale est principalement destiné aux clients qui ont de grandes bases de données SQL Server locales et qui veulent moderniser leurs applications en les déplaçant dans le cloud, ou aux clients qui utilisent déjà Azure SQL Database et qui veulent étendre considérablement le potentiel de croissance de leurs bases de données. Hyperscale est également destiné aux clients qui recherchent à la fois des hautes performances et une scalabilité élevée. Avec Hyperscale, vous bénéficiez des avantages suivants :

- Prise en charge d’une taille de base de données pouvant atteindre 100 To
- Sauvegardes de base de données rapides, quelle que soit la taille des bases de données (les sauvegardes sont basées sur des captures instantanées des fichiers)
- Restaurations de base de données rapides, quelle que soit la taille des bases de données (les restaurations sont effectuées à partir des captures instantanées des fichiers)
- Débits de journalisation plus élevés aboutissant à des temps de validation des transactions courts, quelle que soit la taille des bases de données
- Échelle horizontale en lecture vers un ou plusieurs nœuds en lecture seule, de façon à déporter votre charge de travail de lecture, et pour les serveurs de secours.
- Scale-up rapide des calculs, en durée constante, de façon à gagner en puissance pour faire face à des charges de travail importantes, puis à réduire cette puissance, toujours en durée constante. C’est similaire au scale-up et au scale-down entre par exemple une machine P6 et P11, mais est beaucoup plus rapide, car il ne s’agit pas d’une opération relative à la taille des données.

### <a name="what-regions-currently-support-hyperscale"></a>Quelles régions prennent actuellement en charge Hyperscale ?

Le niveau de service Hyperscale d’Azure SQL Database est actuellement disponible dans les régions répertoriées dans [Vue d’ensemble de l’option Hyperscale d’Azure SQL Database](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Puis-je créer plusieurs bases de données Hyperscale dans un serveur logique ?

Oui. Pour plus d’informations et pour connaître les limites quant au nombre de bases de données Hyperscale par serveur logique, consultez [Limites des ressources de SQL Database pour les bases de données uniques et mises en pool en pool sur un serveur logique](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quelles sont les caractéristiques en matière de performances d’une base de données Hyperscale ?

L’architecture de SQL Database Hyperscale fournit des performances et des débits élevés avec la prise en charge de bases de données de grande taille. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Quelle est la scalabilité d’une base de données Hyperscale ?

SQL Database Hyperscale offre une scalabilité rapide en fonction de la demande de votre charge de travail.

- **Scale-up/down**

  Avec Hyperscale, vous pouvez augmenter la taille de calcul principale en termes de ressources, comme le processeur et la mémoire, puis mettre à l’échelle vers le bas, en durée constante. Comme le stockage est partagé, le scale-up et le scale-down ne sont pas une opération relative à la taille des données.  
- **Scale-up/down**

  Avec Hyperscale, vous avec également la possibilité de provisionner un ou plusieurs nœuds de calcul supplémentaires, que vous pouvez utiliser pour répondre à vos demandes de lecture. Cela signifie que vous pouvez utiliser ces nœuds de calcul supplémentaires comme nœuds en lecture seule pour déporter votre charge de travail de lecture en dehors de la capacité de calcul principale. En plus des opérations en lecture seule, ces nœuds servent également comme serveur de secours en cas de basculement à partir du serveur principal.

  Le provisionnement de chacun de ces nœuds de calcul supplémentaires peut être effectué en durée constante et est une opération en ligne. Vous pouvez vous connecter à ces nœuds de calcul supplémentaires en lecture seule en définissant l’argument `ApplicationIntent` de votre chaîne de connexion sur `readonly`. Les connexions marquées avec `readonly` sont automatiquement routées vers un des nœuds de calcul supplémentaires en lecture seule.

## <a name="deep-dive-questions"></a>Questions approfondies

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Puis-je associer Hyperscale et des bases de données uniques dans un serveur logique unique ?

Oui, vous pouvez.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Hyperscale nécessite-t-il la modification de mon modèle de programmation d’application ?

Non, votre modèle de programmation d’application reste tel quel. Vous utilisez votre chaîne de connexion comme d’habitude et les autres modes normaux pour interagir avec votre base de données Azure SQL.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Quels sont les niveaux d’isolation des transactions par défaut sur une base de données SQL Database Hyperscale ?

Sur le nœud principal, le niveau d’isolement des transactions est RCSI (Read Committed Snapshot Isolation). Sur les nœuds secondaires d’échelle lecture, le niveau d’isolement est Capture instantanée.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Puis-je utiliser ma licence SQL Server locale ou IaaS pour SQL Database Hyperscale ?

Oui, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) est disponible pour Hyperscale. Chaque cœur SQL Server Standard peut être mappé à 1 cœur virtuel Hyperscale. Chaque cœur SQL Server Entreprise peut être mappé à 4 cœurs virtuels Hyperscale. Vous n’avez pas besoin d’une licence SQL pour les réplicas secondaires. Le prix Azure Hybrid Benefit est appliqué automatiquement aux réplicas (secondaires) en échelle lecture.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Pour quelles charges de travail SQL Database Hyperscale est-il conçu ?

SQL Database Hyperscale prend en charge toutes les charges de travail SQL Server, mais il est principalement optimisé pour OLTP. Vous pouvez également exécuter des charges de travail hybrides (HTAP) et analytiques (mini-Data Warehouse).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Comment puis-je choisir entre Azure SQL Data Warehouse et SQL Database Hyperscale ?

Si vous exécutez actuellement des requêtes analytiques interactives avec SQL Server comme entrepôt de données, SQL Database Hyperscale est une option intéressante, car vous pouvez héberger des entrepôts de données de taille relativement petite (par exemple de quelques To à quelques dizaines de To) à un coût inférieur, et vous pouvez migrer la charge de travail de votre entrepôt de données vers SQL Database Hyperscale sans changer le code T-SQL.

Si vous exécutez de l’analytique des données à grande échelle avec des requêtes complexes, et en utilisant Parallel Data Warehouse, Teradata ou des entrepôts de données d’autres processeurs massivement parallèles, SQL Data Warehouse peut être le meilleur choix.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Questions sur la capacité de calcul de SQL Database Hyperscale

### <a name="can-i-pause-my-compute-at-any-time"></a>Puis-je interrompre à tout moment ma capacité de calcul ?

Pas pour l’instant, mais vous pouvez faire descendre en puissance votre calcul et le nombre de réplicas pour réduire les coûts pendant les heures creuses.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Puis-je provisionner une capacité de calcul avec de la RAM supplémentaire pour ma charge de travail utilisant beaucoup de mémoire ?

Non. Pour obtenir davantage de RAM, vous devez effectuer une mise à niveau vers une taille de calcul plus grande. Pour plus d’informations, consultez [Tailles de stockage et taille de calcul Hyperscale](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Puis-je provisionner plusieurs nœuds de calcul de tailles différentes ?

Non.

### <a name="how-many-read-scale-replicas-are-supported"></a>Combien de réplicas en échelle lecture sont pris en charge ?

Les bases de données Hyperscale sont créées par défaut avec un seul réplica à l’échelle lecture (deux réplicas au total). Vous pouvez mettre à l’échelle le nombre de réplicas en lecture seule entre 0 et 4 à l’aide du [portail Azure](https://portal.azure.com), de [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase), ou encore de l’interface [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Pour la haute disponibilité, dois-je provisionner des nœuds de calcul supplémentaires ?

Dans les bases de données Hyperscale, la résilience est fournie au niveau du stockage. Un seul réplica est suffisant pour fournir la résilience. Quand le réplica de capacité de calcul est défaillant, un nouveau réplica est créé automatiquement sans perte de données.

Cependant, s’il n’existe qu’un seul réplica, un certain temps peut être nécessaire pour générer le cache local dans le nouveau réplica après le basculement. Pendant la phase de reconstruction du cache, la base de données extrait les données directement à partir des serveurs de pages, ce qui entraîne une dégradation des IOPS et des performances des requêtes.

Pour les applications critiques qui nécessitent une haute disponibilité, vous devez provisionner au moins 2 nœuds de calcul, y compris le nœud de calcul principal (par défaut). De cette façon, un serveur de secours est disponible en cas de basculement.

## <a name="data-size-and-storage-questions"></a>Questions sur la taille et le stockage des données

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Quelle est la taille maximale de base de données prise en charge avec SQL Database Hyperscale ?

100 To

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Quelle est la taille du journal des transactions avec Hyperscale ?

Le journal des transactions avec Hyperscale est pratiquement infini. Vous n’avez pas à vous soucier d’un espace insuffisant pour le journal sur un système qui a un débit de journalisation élevé. Cependant, le débit de génération du journal peut être limité pour les charges de travail agressives continues. La vitesse de génération de journal maximale soutenue est d’environ 100 Mo/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Ma base de données temporaire évolue-t-elle au fil de la croissance de ma base de données ?

Votre base de données `tempdb` se trouve sur un stockage SSD local et elle est configurée en fonction de la taille de calcul que vous provisionnez. Votre base de données `tempdb` est optimisée et configurée de façon à fournir des performances optimales. La taille de `tempdb` n’est pas configurable et elle est gérée pour vous par le sous-système de stockage.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>La taille de ma base de données augmente-t-elle automatiquement ou dois-je gérer la taille des fichiers de données ?

La taille de votre base de données croît automatiquement au fil de l’insertion/ingestion de données.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Quelle est la plus petite taille de base de données prise en charge par SQL Database Hyperscale ou avec laquelle il peut démarrer ?

10 Go

### <a name="in-what-increments-does-my-database-size-grow"></a>De quel incrément la taille de ma base de données augmente-t-elle ?

1 Go

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Le stockage dans SQL Database Hyperscale est-il local ou distant ?

Dans Hyperscale, les fichiers de données sont stockés dans le stockage Azure standard. Les données sont mises en cache en grande quantité sur un stockage SSD local, sur des ordinateurs proches des nœuds de calcul. En outre, les nœuds de calcul ont un cache sur un SSD local et en mémoire (pool de mémoires tampons, etc.), afin de réduire la fréquence des extractions de données sur les nœuds distants.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Puis-je gérer ou définir des fichiers ou des groupes de fichiers avec Hyperscale ?

Non
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Puis-je provisionner une limite stricte sur la croissance des données pour ma base de données ?

Non

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Comment les fichiers de données se présentent-ils avec SQL Database Hyperscale ?

Les fichiers de données sont contrôlés par les serveurs de pages. Au fil de l’augmentation de la taille des données, des fichiers de données et des nœuds de serveur de pages associés sont ajoutés.

### <a name="is-database-shrink-supported"></a>La réduction de base de données est-elle prise en charge ?

Non

### <a name="is-database-compression-supported"></a>La compression de base de données est-elle prise en charge ?

OUI

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Si j’ai une table très grande, les données de ma table sont-elles réparties dans plusieurs fichiers de données ?

Oui. Les pages de données associées à une table donnée peuvent être stockées dans plusieurs fichiers de données, qui font tous partie du même groupe de fichiers. SQL Server utilise une [stratégie de remplissage proportionnel](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) pour distribuer les données entre des fichiers de données.

## <a name="data-migration-questions"></a>Questions relatives à la migration des données

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Puis-je déplacer mes bases de données Azure SQL vers le niveau de service Hyperscale ?

Oui. Vous pouvez déplacer vos bases de données Azure SQL existantes vers Hyperscale. Il s’agit d’une migration unidirectionnelle. Vous ne pouvez pas déplacer des bases de données depuis Hyperscale vers un autre niveau de service. Nous vous recommandons d’effectuer une copie de vos bases de données de production et de migrer vers Hyperscale pour la preuve de concepts.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Puis-je déplacer mes bases de données Hyperscale vers d’autres éditions ?

Non. Actuellement, vous ne pouvez pas déplacer une base de données Hyperscale vers un autre niveau de service.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Est-ce que je perds des fonctionnalités ou des capacités après la migration vers le niveau de service Hyperscale ?

Oui. Certaines des fonctionnalités Azure SQL Database ne sont pas encore prises en charge dans Hyperscale, notamment, sans limitation, la conservation à long terme des sauvegardes. Après la migration de vos bases de données vers Hyperscale, ces fonctionnalités cessent de fonctionner.  Nous espérons que ces limites ne seront pas définitives.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Puis-je déplacer ma base de données SQL Server locale ou ma base de données sur machine virtuelle SQL Server vers Hyperscale ?

Oui. Vous pouvez utiliser toutes les technologies de migration existantes pour migrer vers Hyperscale, notamment BACPAC, la réplication transactionnelle et le chargement de données logique. Voir aussi [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Quelle est la durée du temps d’arrêt pendant la migration depuis un environnement local ou de machines virtuelles vers Hyperscale, et comment puis-je le minimiser ?

Le temps d’arrêt est le même que quand vous migrez vos bases de données vers une base de données unique dans Azure SQL Database. Vous pouvez utiliser la [réplication transactionnelle](replication-to-sql-database.md#data-migration-scenario
) afin de réduire le temps d’arrêt dû à la migration pour les bases de données d’une taille allant jusqu’à plusieurs To. Pour une très grande base de données (plus de 10 To), vous pouvez envisager de migrer les données avec ADF, Spark ou d’autres technologies de déplacement de données.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Combien de temps cela prend-t-il de charger une quantité X de données dans SQL Database Hyperscale ?

Hyperscale peut consommer 100 Mo/s de données nouvelles ou modifiées.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Puis-je lire des données dans un stockage d’objets blob (comme PolyBase et SQL Data Warehouse) et faire un chargement rapide ?

Vous pouvez lire des données depuis Stockage Azure et charger des données dans une base de données Hyperscale (exactement comme vous pouvez le faire avec une base de données unique normale). Actuellement, PolyBase n’est pas pris en charge sur Azure SQL Database. Vous pouvez faire comme si vous aviez PolyBase en utilisant [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) ou en exécutant un travail Spark dans [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) avec le [connecteur Spark pour SQL](sql-database-spark-connector.md). Le connecteur Spark pour SQL prend en charge l’insertion en bloc.

Le modèle de récupération simple ou de journalisation en bloc n’est pas pris en charge dans Hyperscale. Le modèle de récupération complète est nécessaire pour fournir la haute disponibilité. Cependant, Hyperscale fournit un meilleur débit d’ingestion en comparaison d’une base de données Azure SQL unique, en raison de la nouvelle architecture de la journalisation.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>SQL Database Hyperscale permet-il le provisionnement de plusieurs nœuds pour l’ingestion de grandes quantités de données ?

Non. SQL Database Hyperscale est une architecture SMP, et non pas un traitement multiprocesseur asymétrique ou une architecture multimaître. Vous pouvez créer plusieurs réplicas seulement pour un scale-out des charges de travail en lecture seule.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Quelle est la version la plus ancienne de SQL Server pour laquelle la migration de données vers SQL Database Hyperscale est prise en charge ?

SQL Server 2005. Pour plus d’informations, consultez [Migrer vers une base de données unique ou une base de données mise en pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Pour les problèmes de compatibilité, consultez [Résolution des problèmes de compatibilité de la migration de base de données](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>SQL Database Hyperscale prend-t-il en charge la migration depuis d’autres sources de données, comme Aurora, MySQL, Oracle, DB2 et d’autres plateformes de base de données ?

Oui. L’utilisation de données provenant d’autres sources de données que SQL Server nécessite une migration logique. Vous pouvez utiliser [Azure Database Migration Service](../dms/dms-overview.md) pour effectuer une migration logique.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Questions sur la continuité des activités et la reprise d’activité

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quels sont les contrats SLA fournis pour une base de données Hyperscale ?

Avec la valeur de charge primaire par défaut, plus une valeur secondaire accessible en lecture, le contrat de niveau de service est disponible à 99,95 %.  Avec plus de réplicas, le contrat de niveau de service atteint les 99,99 %.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Les sauvegardes de base de données sont-elles gérées pour moi par le service Azure SQL Database ?

OUI

### <a name="how-often-are-the-database-backups-taken"></a>Quelle est la fréquence des sauvegardes de base de données ?

Il n’existe pas de sauvegardes traditionnelles complètes, différentielles et de journal pour les bases de données SQL Database Hyperscale. Au lieu de cela, des captures instantanées normales des fichiers de données et du journal généré sont simplement conservées telles quelles pour la période de rétention configurée ou sont mises à votre disposition.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database Hyperscale prend-t-il en charge la récupération jusqu`à une date et heure ?

OUI

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Quel est l’objectif de point de récupération (RPO)/objectif de délai de récupération (RTO) avec la sauvegarde/restauration dans SQL Database Hyperscale ?

L’objectif de point de récupération est de 0 minute. L’objectif de délai de récupération est inférieur à 10 minutes, quelle que soit la taille de la base de données. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Les sauvegardes de grandes bases de données affectent-elles les performances de calcul sur mon nœud principal ?

Non. Les sauvegardes sont gérées par le sous-système de stockage et tirent parti des captures instantanées des fichiers. Elles n’affectent pas la charge de travail utilisateur sur le nœud principal.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Puis-je effectuer une géorestauration avec une base de données SQL Database Hyperscale ?

Oui.  La géo-restauration est entièrement prise en charge.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Puis-je configurer la géoréplication avec une base de données SQL Database Hyperscale ?

Pas pour l'instant.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Mes nœuds de calcul secondaires sont-ils géorépliqués avec SQL Database Hyperscale ?

Pas pour l'instant.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Puis-je sauvegarder une base de données SQL Database Hyperscale et la restaurer sur mon serveur local ou sur SQL Server dans une machine virtuelle ?

Non. Le format de stockage pour les bases de données Hyperscale est différent du format SQL Server traditionnel, et vous ne contrôlez pas les sauvegardes et vous n’y avez pas accès. Pour prendre vos données dans une base de données SQL Database Hyperscale, utilisez le service d’exportation, ou utilisez des scripts et BCP.

## <a name="cross-feature-questions"></a>Questions sur les différences de fonctionnalités

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Est-ce que je perds des fonctionnalités ou des capacités après la migration vers le niveau de service Hyperscale ?

Oui. Certaines fonctionnalités d’Azure SQL Database ne sont pas prises en charge dans Hyperscale, notamment, sans limitation, la conservation à long terme des sauvegardes. Après la migration de vos bases de données vers Hyperscale, ces fonctionnalités cessent de fonctionner.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>PolyBase fonctionne-t-il avec SQL Database Hyperscale ?

Non. PolyBase n’est pas pris en charge sur Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>La capacité de calcul peut-elle prendre en charge R et Python ?

Non. R et Python ne sont pas pris en charge dans Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Les nœuds de calcul sont-ils placés dans des conteneurs ?

Non. Votre base de données se trouve sur une machine virtuelle de calcul et pas dans un conteneur.

## <a name="performance-questions"></a>Questions sur les performances

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Quel débit puis-je envoyer sur la plus grande capacité de calcul de SQL Database Hyperscale ?

Nous avons remarqué que le débit constant était de 100 Mo/s de données modifiées (génération de données de journal des transactions)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Combien d’IOPS puis-je obtenir sur la plus grande capacité de calcul de SQL Database Hyperscale ?

Les IOPS et la latence d’E/S varient en fonction des modèles de charge de travail.  Si les données devant être accessibles sont des données locales du cache de calcul, les modèles d’E/S seront les mêmes que ceux du disque SSD local.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Mon débit est-il affecté par les sauvegardes ?

Non. La capacité de calcul est découplée de la couche de stockage pour éviter un impact sur le calcul.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Mon débit est-il affecté quand je provisionne des nœuds de calcul supplémentaires ?

Comme le stockage est partagé et qu’aucune réplication physique directe n’est faite entre les nœuds de calcul principaux et secondaires, techniquement, le débit sur le nœud principal n’est pas affecté par l’ajout de nœuds en échelle lecture. Cependant, nous pouvons limiter la charge de travail agressive continue pour que le journal s’applique sur les nœuds secondaires et les serveurs de pages à rattraper, et éviter ainsi de mauvaises performances de lecture sur les nœuds secondaires.

## <a name="scalability-questions"></a>Questions sur la scalabilité

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Quel est le temps nécessaire pour un scale-up et un scale-down d’un nœud de calcul ?

Monter ou descendre le calcul en puissance doit prendre 5 à 10 minutes, quelle que soit la taille des données.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Ma base de données est-elle hors connexion pendant l’exécution de l’opération de scale-up/down ?

Non. Le scale-up/down se fait en ligne.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Dois-je m’attendre à la suppression des connexions quand les opérations de mise à l’échelle sont en cours ?

Le scale-up ou le scale-down entraînent la suppression des connexions existantes quand un basculement se produit vers le nœud de calcul avec la taille cible. L’ajout de réplicas en lecture n’entraîne pas la suppression des connexions.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Le scale-up et le scale-down de nœuds de calcul sont-ils des opérations automatiques ou déclenchées par l’utilisateur final ?

Utilisateur final. Pas automatique.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Est-ce que ma base de données `tempb` augmente lors du scale-up de la capacité de calcul ?

Oui. La base de données temporaire augmente automatiquement lors du scale-up de la capacité de calcul.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Puis-je approvisionner plusieurs nœuds de calcul principaux, comme un système multimaître où plusieurs têtes de calcul principales peuvent gérer un niveau de concurrence plus élevé ?

Non. Seul le nœud de calcul principal accepte les demandes de lecture/écriture. Les nœuds de calcul secondaires acceptent seulement les demandes en lecture seule.

## <a name="read-scale-questions"></a>Questions sur l’échelle lecture

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Combien de nœuds de calcul secondaires puis-je provisionner ?

Nous créons par défaut deux réplicas pour les bases de données Hyperscale. Si vous souhaitez ajuster le nombre de réplicas, vous pouvez le faire depuis le [Portail Microsoft Azure](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Comment se connecter à ces nœuds de calcul secondaires ?

Vous pouvez vous connecter à ces nœuds de calcul supplémentaires en lecture seule en définissant l’argument `ApplicationIntent` de votre chaîne de connexion sur `readonly`. Les connexions marquées avec `readonly` sont automatiquement routées vers un des nœuds de calcul supplémentaires en lecture seule.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Puis-je créer un point de terminaison dédié pour le réplica en échelle lecture ?

Non. Vous pouvez vous connecter au réplica en échelle lecture uniquement en spécifiant `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Est-ce que le système effectue un équilibrage de charge intelligent de la charge de travail de lecture ?

Non. La charge de travail en lecture seule est redirigée vers un réplica avec échelle lecture choisi de façon aléatoire.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Puis-je effectuer un scale-up/down des nœuds de calcul secondaires indépendamment de la capacité de calcul principale ?

Non. Les nœuds de calcul secondaires sont également utilisés pour la haute disponibilité, ils doivent donc avoir la même configuration que le réplica principal, en cas de basculement.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Le dimensionnement de la base de données temporaire est-il différent pour ma capacité de calcul principale et pour mes nœuds de calcul secondaires supplémentaires ?

Non. Votre base de données `tempdb` est configurée en fonction du provisionnement de la taille de calcul : vos nœuds de calcul secondaires ont la même taille que la capacité de calcul principale.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Puis-je ajouter des index et des vues sur mes nœuds de calcul secondaires ?

Non. Les bases de données Hyperscale ont un stockage partagé, ce qui signifie que tous les nœuds de calcul voient les mêmes tables, les mêmes index et les mêmes vues. Si vous voulez des index supplémentaires optimisés pour les lectures sur les nœuds secondaires, vous devez d’abord les ajouter sur le nœud principal.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Quel est le décalage entre le nœud de calcul principal et le nœud de calcul secondaire ?

À partir du moment où une transaction est validée sur le nœud principal, en fonction du débit de la génération du journal, la synchronisation peut être instantanée ou ne prendre que quelques millisecondes.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le niveau de service Hyperscale, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).
