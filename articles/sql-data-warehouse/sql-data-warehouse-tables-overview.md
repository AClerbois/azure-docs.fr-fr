---
title: Conception de tables - Azure SQL Data Warehouse | Microsoft Docs
description: Présentation de la conception de tables dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1073e1b4ad38c4b05c9195cf4ea16ade7416fbce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474966"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Conception de tables dans Azure SQL Data Warehouse

Découvrez les principaux concepts de la conception de tables dans Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Déterminer la catégorie des tables 

Un [schéma en étoile](https://en.wikipedia.org/wiki/Star_schema) organise les données dans des tables de faits et de dimension. Certaines tables sont utilisées pour l’intégration ou la mise en lots des données avant leur transfert dans une table de faits ou de dimension. Quand vous concevez une table, déterminez si les données de la table sont contenues dans une table de faits, de dimension ou d’intégration. Ceci est important pour choisir une structure et une distribution appropriées pour la table. 

- Les **tables de faits** contiennent des données quantitatives qui sont le plus souvent générées dans un système transactionnel avant d’être chargées dans l’entrepôt de données. Par exemple, une entreprise de vente au détail génère des transactions de ventes chaque jour et charge ensuite ces données dans une table de faits de l’entrepôt de données pour les analyser.

- Les **tables de dimension** contiennent des données d’attribut modifiables, mais qui changent peu en règle générale. Par exemple, le nom et l’adresse d’un client sont stockés dans une table de dimension et sont mis à jour uniquement si le profil du client change. Pour réduire la taille d’une table de faits volumineuse, il est inutile d’indiquer le nom et l’adresse du client dans chaque ligne de la table de faits. Au lieu de cela, la table de faits et la table de dimension peuvent partager un ID client. Vous pouvez alors créer une requête de jointure entre les deux tables pour associer le profil d’un client et les transactions qui le concernent. 

- Les **tables d’intégration** fournissent un emplacement pour l’intégration ou la mise en lots des données. Vous pouvez créer une table d’intégration en tant que table normale, table externe ou table temporaire. Vous pouvez, par exemple, charger des données dans une table de mise en lots, effectuer des transformations sur ces données, puis insérer les données dans une table de production.

## <a name="schema-and-table-names"></a>Noms de schéma et de table
Les schémas sont un bon moyen de regrouper les tables, de manière similaire, conjointement.  Si vous migrez plusieurs bases de données à partir d’une solution sur site vers SQL Data Warehouse, nous vous recommandons de migrer tous les tables de faits, dimension et intégration à un schéma dans SQL Data Warehouse. Par exemple, stockez toutes les tables de l’entrepôt de données [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) dans un seul schéma appelé wwi. Le code suivant crée un [schéma défini par l’utilisateur](/sql/t-sql/statements/create-schema-transact-sql) appelé wwi.

```sql
CREATE SCHEMA wwi;
```

Pour afficher l’organisation des tables dans SQL Data Warehouse, vous pouvez utiliser les préfixes fact, dim et int dans les noms de table. Le tableau suivant répertorie quelques noms de schéma et de table pour WideWorldImportersDW.  

| Table WideWorldImportersDW  | Type de table | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| City | Dimension | wwi.DimCity |
| Ordre | Fact | wwi.FactOrder |


## <a name="table-persistence"></a>Persistance de la table 

Les tables stockent les données soit définitivement dans le stockage Azure, soit temporairement dans le stockage Azure, soit dans un magasin de données externe à l’entrepôt de données.

### <a name="regular-table"></a>Table normale

Une table normale stocke les données dans le stockage Azure comme partie intégrante de l’entrepôt de données. La table et les données sont persistantes, qu’une session soit ou non ouverte.  Cet exemple crée une table normale contenant deux colonnes. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Table temporaire
Une table temporaire existe uniquement pendant la durée de la session. Vous pouvez utiliser une table temporaire pour empêcher les autres utilisateurs de voir les résultats temporaires et également pour réduire la nécessité pour le nettoyage.  Tables temporaires utilisent un stockage local pour vous proposer des performances rapides.  Pour plus d’informations, consultez [Tables temporaires](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Table externe
Une table externe pointe vers des données situées dans le stockage Blob Azure ou Azure Data Lake Store. Utilisée conjointement avec l’instruction CREATE TABLE AS SELECT, la sélection à partir d’une table externe permet d’importer des données dans SQL Data Warehouse. Les tables externes sont donc utiles pour charger des données. Pour obtenir un didacticiel sur le chargement, consultez [Utiliser PolyBase pour charger des données du Stockage Blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Types de données
SQL Data Warehouse prend en charge les types de données les plus couramment utilisés. Pour obtenir la liste des types de données pris en charge, consultez les [types de données dans la référence CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) dans l’instruction CREATE TABLE. Pour obtenir des conseils sur l’utilisation des types de données, consultez [Types de données](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tables distribuées
Une fonctionnalité essentielle de SQL Data Warehouse est la façon dont il peut stocker et agissent sur les tables entre [distributions](massively-parallel-processing-mpp-architecture.md#distributions).  SQL Data Warehouse prend en charge trois méthodes de distribution de données, de tourniquet (par défaut), de hachage et répliquées.

### <a name="hash-distributed-tables"></a>Tables distribuées par hachage
Une table distribuée par hachage distribue les lignes selon la valeur dans la colonne de distribution. Table de hachage distribuée est conçue pour optimiser les performances de requêtes sur des tables volumineuses. Il existe plusieurs facteurs à prendre en compte lors du choix d’une colonne de distribution. 

Pour plus d’informations, consultez le [Guide de conception pour les tables distribuées](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tables répliquées
Les tables répliquées effectuent une copie complète de la table disponible sur chaque nœud de calcul. Les requêtes sur les tables répliquées s’exécutent rapidement, car les jointures sur ce type de table ne nécessitent pas de déplacement de données. Toutefois, la réplication a besoin de plus de stockage et n’est donc pas une méthode appropriée pour les tables volumineuses. 

Pour plus d’informations, consultez [Guide de conception pour les tables répliquées](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tables par tourniquet
Une table par tourniquet distribue les lignes de la table uniformément sur toutes les distributions. Les lignes sont distribuées de façon aléatoire. Le chargement des données dans une table par tourniquet se fait rapidement.  Toutefois, les requêtes peuvent nécessiter davantage de déplacements de données que les autres méthodes de distribution. 

Pour plus d’informations, consultez le [Guide de conception pour les tables distribuées](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Méthodes courantes de distribution pour les tables
La catégorie de la table détermine souvent le choix de l’option de distribution de la table. 

| Catégorie de table | Option de distribution recommandée |
|:---------------|:--------------------|
| Fact           | Utilisez la distribution par hachage avec un index columnstore cluster. Les performances sont meilleures quand deux tables de hachage sont jointes sur la même colonne de distribution. |
| Dimension      | Utilisez la réplication pour les tables de petite taille. Si les tables sont trop volumineuses pour être stockées sur chaque nœud de calcul, utilisez la distribution par hachage. |
| Staging        | Utilisez la distribution par tourniquet (round robin) pour la table de mise en lots. Le chargement avec la fonctionnalité CTAS est rapide. Une fois les données dans la table intermédiaire, utilisez INSERT... Sélectionnez cette option pour déplacer les données vers les tables de production. |

## <a name="table-partitions"></a>Partitions de table
Une table partitionnée stocke les lignes de table et effectue des opérations sur ces lignes selon les plages de données définies. Par exemple, une table peut être partitionnée par jour, mois ou année. Vous pouvez améliorer les performances des requêtes via l’élimination de partition, qui limite l’analyse d’une requête aux seules données contenues dans une partition. Vous pouvez également tenir à jour les données à l’aide du basculement de partition. Comme les données dans SQL Data Warehouse sont déjà distribuées, un partitionnement excessif risque de ralentir les requêtes. Pour plus d’informations, consultez [Partitionnement de tables](sql-data-warehouse-tables-partition.md).  Lorsque le basculement dans la table de partition partitions qui ne sont pas vides, envisagez d’utiliser l’option TRUNCATE_TARGET dans votre [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) instruction si les données existantes sont le point d’être tronqué. Le dessous des commutateurs de code dans les données transformées quotidiennes dans le SalesFact remplaçant les données existantes. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Index Columnstore
Par défaut, SQL Data Warehouse stocke une table comme un index columnstore cluster. Ce format de stockage de données permet une compression élevée des données et offre des performances optimales pour les requêtes sur des tables volumineuses.  L’index columnstore cluster est généralement le meilleur choix, mais dans certains cas, un index cluster ou un segment de mémoire est la structure de stockage la plus appropriée.  Une table de segment de mémoire peut être particulièrement utile pour le chargement des données temporaires, comme une table intermédiaire qui est transformée en une table finale.

Pour obtenir la liste des fonctionnalités columnstore, consultez [Nouveautés pour les index columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Pour améliorer les performances des index columnstore, consultez [Optimiser la qualité du rowgroup pour les index columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistiques
L’optimiseur de requête utilise des statistiques au niveau des colonnes quand il crée le plan d’exécution d’une requête. Pour améliorer les performances des requêtes, il est important de disposer de statistiques sur des colonnes individuelles, en particulier les colonnes utilisées dans les jointures de requête. [Création de statistiques](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics) se produit automatiquement.  Toutefois, la mise à jour des statistiques n’est pas automatique. Mettez à jour les statistiques après l’ajout ou la modification d’un nombre significatif de lignes. Par exemple, effectuez une mise à jour des statistiques après un chargement. Pour plus d’informations, consultez [Gestion des statistiques](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Commandes pour la création de tables
Vous pouvez créer une table à partir d’une nouvelle table vide. Vous pouvez aussi créer une table et la remplir avec les résultats d’une instruction select. Le tableau suivant répertorie les instructions T-SQL disponibles pour la création d’une table.

| Instruction T-SQL | Description |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Crée une table vide en définissant toutes les colonnes et options de la table. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Crée une table externe. La définition de la table est stockée dans SQL Data Warehouse. Les données de la table sont stockées dans le Stockage Blob Azure ou Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Crée une table et la remplit avec les résultats d’une instruction select. Les colonnes et les types de données de la table sont basés sur les résultats de l’instruction select. Pour importer des données, cette instruction peut sélectionner les données dans une table externe. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Crée une table externe en exportant les résultats d’une instruction select vers un emplacement externe.  L’emplacement est le stockage Blob Azure ou Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Alignement des données sources avec l’entrepôt de données

Les tables de l’entrepôt de données sont remplies avec les données chargées à partir d’une autre source de données. Pour effectuer un chargement correct, le nombre et les types de données des colonnes dans les données sources doivent être alignés sur la définition de la table dans l’entrepôt de données. L’alignement des données est parfois l’étape la plus difficile dans la conception des tables. 

Si les données proviennent de plusieurs magasins de données, vous pouvez importer les données dans l’entrepôt de données et les stocker dans une table d’intégration. Vous pouvez ensuite effectuer des opérations de transformation sur les données de la table d’intégration en bénéficiant de toute la puissance de SQL Data Warehouse. Une fois que les données sont préparées, vous pouvez les insérer dans des tables de production.

## <a name="unsupported-table-features"></a>Fonctionnalités de table non prises en charge
SQL Data Warehouse prend en charge beaucoup des fonctionnalités de table proposées par d’autres bases de données, mais pas toutes.  La liste suivante répertorie certaines fonctionnalités de table qui ne sont pas prises en charge dans SQL Data Warehouse.

- [Contraintes de table](/sql/t-sql/statements/alter-table-table-constraint-transact-sql) Primary Key, Foreign Key, Unique et Check

- [Colonnes calculées](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Vues indexées](/sql/relational-databases/views/create-indexed-views)
- [Séquence](/sql/t-sql/statements/create-sequence-transact-sql)
- [Colonnes éparses](/sql/relational-databases/tables/use-sparse-columns)
- Clés de substitution. Implémentation avec [Identity](sql-data-warehouse-tables-identity.md).
- [Synonymes](/sql/t-sql/statements/create-synonym-transact-sql)
- [Déclencheurs](/sql/t-sql/statements/create-trigger-transact-sql)
- [Index uniques](/sql/t-sql/statements/create-index-transact-sql)
- [Types définis par l’utilisateur](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Requêtes de taille de table
Un moyen simple d’identifier l’espace et les lignes consommées par une table dans chacune des 60 distributions consiste à utiliser [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, l’utilisation des commandes DBCC peut être très limitante.  Les vues de gestion dynamique (DMV) affichent des informations plus détaillées que les commandes DBCC. Commencez par créer cette vue.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Résumé de l’espace de table

Cette requête renvoie les lignes et l’espace par table.  Elle vous permet de voir quelles tables sont les plus volumineuses et de déterminer si elles sont distribuées par tourniquet (round robin), réplication ou hachage.  Pour les tables distribuées par hachage, la requête affiche la colonne de distribution.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espace de table par type de distribution

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espace de table par type d’index

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Résumé de l’espace de distribution

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé les tables dans votre entrepôt de données, l’étape suivante va être de charger des données dans ces tables.  Pour suivre un tutoriel sur le chargement, consultez [Chargement de données dans SQL Data Warehouse](load-data-wideworldimportersdw.md).
