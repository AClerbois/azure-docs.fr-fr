---
title: Limites de ressources vCore Azure SQL Database – pools élastiques | Microsoft Docs
description: Cette page décrit certaines des limites de ressources vCore courantes pour des pools élastiques dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 11/04/2019
ms.openlocfilehash: 65c2bfe4d79f5b7d468999143524b96b60f0efaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495995"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de ressources pour les pools élastiques suivant le modèle d’achat vCore

Cet article détaille les limites de ressources des pools élastiques Azure SQL Database et des bases de données mises en pool suivant le modèle d’achat vCore.

Pour connaître les limites du modèle d’achat DTU, consultez [Limites des ressources DTU de SQL Database – pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage à l’aide du [Portail Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), de [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [d’Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou de [l’API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Pour obtenir des instructions et informations sur la mise à l’échelle, consultez [Mettre à l’échelle un pool élastique](sql-database-elastic-pool-scale.md).


## <a name="general-purpose---provisioned-compute---gen4"></a>Usage général - calcul provisionné - Gen4

> [!IMPORTANT]
> Les nouvelles bases de données Gen4 ne sont plus prises en charge dans les régions Australie Est et Brésil Sud.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Niveau de service d’usage général : Plateforme de calcul de génération 4 (partie 1)

|Taille de calcul|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Génération de calcul|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5\.|6|
|Mémoire (Go)|7|14|21|28|35|42|
|Nombre maximal de bases de données par pool|100|200|500|500|500|500|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Taille maximale des données (Go)|512|756|756|1536|1536|1536|
|Taille maximale du journal|154|227|227|461|461|461|
|Taille de TEMPDB (Go)|32|64|96|128|160|192|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|500|1 000|1 500|2000|2 500|3000|
|Limites du taux de journalisation (Mbits/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Nombre maximal de workers simultanés par pool (demandes) * |210|420|630|840|1050|1 260|
|Nombre maximal de connexions simultanées par pool* |210|420|630|840|1050|1 260|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1...3|0 ; 0,25 ; 0,5, 1...4|0 ; 0,25 ; 0,5 ; 1...5|0 ; 0,25 ; 0,5 ; 1...6|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.


### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Niveau de service d’usage général : Plateforme de calcul de génération 4 (partie 2)

|Taille de calcul|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération de calcul|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Mémoire (Go)|49|56|63|70|112|168|
|Nombre maximal de bases de données par pool|500|500|500|500|500|500|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Taille maximale des données (Go)|1536|2 048|2 048|2 048|3584|4096|
|Taille maximale du journal (Go)|461|614|614|614|1075|1229|
|Taille de TEMPDB (Go)|224|256|288|320|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|3 500|4000|4500|5 000|7000|7000|
|Limites du taux de journalisation (Mbits/s)|32,8125|37.5|37.5|37.5|37.5|37.5|
|Nombre maximal de workers simultanés par pool (demandes) *|1470|1680|1890|2100|3360|5040|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1470|1680|1890|2100|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1... 7|0 ; 0,25 ; 0,5 ; 1... 8|0 ; 0,25 ; 0,5 ; 1... 9|0 ; 0,25 ; 0,5 ; 1... 10|0 ; 0,25 ; 0,5 ; 1... 10 ; 16|0 ; 0,25 ; 0,5 ; 1... 10 ; 16 ; 24|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.


## <a name="general-purpose---provisioned-compute---gen5"></a>Usage général - calcul provisionné - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Niveau de service d’usage général : Plateforme de calcul de génération 5 (partie 1)

|Taille de calcul|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération de calcul|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Mémoire (Go)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Nombre maximal de bases de données par pool|100|200|500|500|500|500|500|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Taille maximale des données (Go)|512|756|756|1536|1536|1536|
|Taille maximale du journal (Go)|154|227|227|461|461|461|461|
|Taille de TEMPDB (Go)|64|128|192|256|320|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|1 000|2000|3000|4000|5 000|6000|7000|
|Limites du taux de journalisation (Mbits/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Nombre maximal de workers simultanés par pool (demandes) *|210|420|630|840|1050|1 260|1470|
|Nombre maximal de connexions simultanées par pool (requêtes)*|210|420|630|840|1050|1 260|1470|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5, 1...4|0 ; 0,25 ; 0,5 ; 1...6|0 ; 0,25 ; 0,5 ; 1... 8|0 ; 0,25 ; 0,5 ; 1... 10|0 ; 0,25 ; 0,5 ; 1... 12|0 ; 0,25 ; 0,5 ; 1... 14|
|Nombre de réplicas|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Niveau de service d’usage général : Plateforme de calcul de génération 5 (partie 2)

|Taille de calcul|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération de calcul|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Mémoire (Go)|81,6|91,8|102|122,4|163,2|204|408|
|Nombre maximal de bases de données par pool|500|500|500|500|500|500|500|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Taille maximale des données (Go)|2 048|2 048|3 072|3 072|4096|4096|4096|
|Taille maximale du journal (Go)|614|614|922|922|1229|1229|1229|
|Taille de TEMPDB (Go)|384|384|384|384|384|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|7000|7000|7000|7000|7000|7000|7000|
|Limites du taux de journalisation (Mbits/s)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Nombre maximal de workers simultanés par pool (demandes) *|1680|1890|2100|2520|3360|4200|8400|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1680|1890|2100|2520|3360|4200|8400|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1... 16|0 ; 0,25 ; 0,5 ; 1... 18|0 ; 0,25 ; 0,5 ; 1... 20|0 ; 0,25 ; 0,5 ; 1... 20 ; 24|0 ; 0,25 ; 0,5 ; 1... 20 ; 24 ; 32|0 ; 0,25 ; 0,5 ; 1... 16 ; 24 ; 32 ; 40|0 ; 0,25 ; 0,5 ; 1... 16 ; 24 ; 32 ; 40 ; 80|
|Nombre de réplicas|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Usage général - calcul provisionné - série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Génération de calcul de série Fsv2 (préversion)

|Taille de calcul|GP_Fsv2_72|
|:--- | --: |
|Génération de calcul|Série Fsv2|
|vCores|72|
|Mémoire (Go)|136|
|Nombre maximal de bases de données par pool|500|
|Prise en charge de ColumnStore|OUI|
|Stockage In-Memory OLTP (Go)|N/A|
|Taille maximale des données (Go)|4096|
|Taille maximale du journal (Go)|1 024|
|Taille maximale des données TempDB (Go)|333|
|Type de stockage|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|36000|
|Limites du taux de journalisation (Mbits/s)|37.5|
|Nombre maximal de workers simultanés par pool (demandes) *|1680|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1680|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0-72|
|Nombre de réplicas|1|
|Plusieurs zones de disponibilités|N/A|
|Lecture du Scale-out|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|




\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.


## <a name="business-critical---provisioned-compute---gen4"></a>Vital pour l’entreprise - calcul provisionné - Gen4

> [!IMPORTANT]
> Les nouvelles bases de données Gen4 ne sont plus prises en charge dans les régions Australie Est et Brésil Sud.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Niveau de service vital pour l’entreprise : Plateforme de calcul de génération 4 (partie 1)

|Taille de calcul|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération de calcul|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5\.|6|
|Mémoire (Go)|7|14|21|28|35|42|
|Nombre maximal de bases de données par pool|Seules les bases de données uniques sont prises en charge pour cette taille de calcul|50|100|100|100|100|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1|2|3|4|5\.|6|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|650|650|650|650|650|650|
|Taille maximale du journal (Go)|195|195|195|195|195|195|
|Taille de TEMPDB (Go)|32|64|96|128|160|192|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|5 000|10000|15000|20000|25000|30000|
|Limites du taux de journalisation (Mbits/s)|10|20|30|40|50|60|
|Nombre maximal de workers simultanés par pool (demandes) *|210|420|630|840|1050|1 260|
|Nombre maximal de connexions simultanées par pool (requêtes)*|210|420|630|840|1050|1 260|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|N/A|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1...3|0 ; 0,25 ; 0,5, 1...4|0 ; 0,25 ; 0,5 ; 1...5|0 ; 0,25 ; 0,5 ; 1...6|
|Nombre de réplicas|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Niveau de service vital pour l’entreprise : Plateforme de calcul de génération 4 (partie 2)

|Taille de calcul|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération de calcul|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Mémoire (Go)|81,6|91,8|102|122,4|163,2|204|
|Nombre maximal de bases de données par pool|100|100|100|100|100|100|
|Prise en charge de ColumnStore|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage In-Memory OLTP (Go)|7|8|9,5|11|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|650|650|650|650|1 024|1 024|
|Taille maximale du journal (Go)|195|195|195|195|307|307|
|Taille de TEMPDB (Go)|224|256|288|320|384|384|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|35000|40000|45000|50000|80000|120 000|
|Limites du taux de journalisation (Mbits/s)|70|80|80|80|80|80|
|Nombre maximal de workers simultanés par pool (demandes) *|1470|1680|1890|2100|3360|5040|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1470|1680|1890|2100|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1... 7|0 ; 0,25 ; 0,5 ; 1... 8|0 ; 0,25 ; 0,5 ; 1... 9|0 ; 0,25 ; 0,5 ; 1... 10|0 ; 0,25 ; 0,5 ; 1... 10 ; 16|0 ; 0,25 ; 0,5 ; 1... 10 ; 16 ; 24|
|Nombre de réplicas|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.

## <a name="business-critical---provisioned-compute---gen5"></a>Vital pour l’entreprise - calcul provisionné - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Niveau de service vital pour l’entreprise : Plateforme de calcul de génération 5 (partie 1)

|Taille de calcul|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération de calcul|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Mémoire (Go)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Nombre maximal de bases de données par pool|Seules les bases de données uniques sont prises en charge pour cette taille de calcul|50|100|100|100|100|100|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|4 713|6 284|8 655|11 026|13 397|
|Taille maximale des données (Go)|1 024|1 024|1536|1536|1536|3 072|3 072|
|Taille maximale du journal (Go)|307|307|307|461|461|922|922|
|Taille de TEMPDB (Go)|64|128|192|256|320|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|5 000|10000|15000|20000|25000|30000|35000|
|Limites du taux de journalisation (Mbits/s)|15|30|45|60|75|90|105|
|Nombre maximal de workers simultanés par pool (demandes) *|210|420|630|840|1050|1 260|1470|
|Nombre maximal de connexions simultanées par pool (requêtes)*|210|420|630|840|1050|1 260|1470|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|N/A|0 ; 0,25 ; 0,5, 1...4|0 ; 0,25 ; 0,5 ; 1...6|0 ; 0,25 ; 0,5 ; 1... 8|0 ; 0,25 ; 0,5 ; 1... 10|0 ; 0,25 ; 0,5 ; 1... 12|0 ; 0,25 ; 0,5 ; 1... 14|
|Nombre de réplicas|4|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Niveau de service vital pour l’entreprise : Plateforme de calcul de génération 5 (partie 2)

|Taille de calcul|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération de calcul|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Mémoire (Go)|81,6|91,8|102|122,4|163,2|204|408|
|Nombre maximal de bases de données par pool|100|100|100|100|100|100|100|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|15 768|18 139|20,51|25 252|37 936|52.22|131,64|
|Taille maximale des données (Go)|3 072|3 072|3 072|4096|4096|4096|4096|
|Taille maximale du journal (Go)|922|922|922|1229|1229|1229|1229|
|Taille de TEMPDB (Go)|384|384|384|384|384|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|40000|45000|50000|60000|80000|100000|200000|
|Limites du taux de journalisation (Mbits/s)|120|120|120|120|120|120|120|
|Nombre maximal de workers simultanés par pool (demandes) *|1680|1890|2100|2520|3360|4200|8400|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1680|1890|2100|2520|3360|4200|8400|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1... 16|0 ; 0,25 ; 0,5 ; 1... 18|0 ; 0,25 ; 0,5 ; 1... 20|0 ; 0,25 ; 0,5 ; 1... 20 ; 24|0 ; 0,25 ; 0,5 ; 1... 20 ; 24 ; 32|0 ; 0,25 ; 0,5 ; 1... 20 ; 24 ; 32 ; 40|0 ; 0,25 ; 0,5 ; 1... 20 ; 24 ; 32 ; 40 ; 80|
|Nombre de réplicas|4|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

## <a name="business-critical---provisioned-compute---m-series"></a>Vital pour l’entreprise - calcul provisionné - série M

### <a name="m-series-compute-generation-preview"></a>Génération de calcul de série M (préversion)

|Taille de calcul|GP_M_128|
|:--- | --: |
|Génération de calcul|Série M|
|vCores|128|
|Mémoire (Go)|3767|
|Nombre maximal de bases de données par pool|100|
|Prise en charge de ColumnStore|OUI|
|Stockage In-Memory OLTP (Go)|481|
|Taille maximale des données (Go)|4096|
|Taille maximale du journal (Go)|2 048|
|Taille maximale des données TempDB (Go)|4096|
|Type de stockage|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|40000|
|Limites du taux de journalisation (Mbits/s)|120|
|Nombre maximal de workers simultanés par pool (demandes) *|1680|
|Nombre maximal de connexions simultanées par pool (requêtes)*|1680|
|Nombre maximal de sessions autorisé|30000|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0-128|
|Nombre de réplicas|4|
|Plusieurs zones de disponibilités|OUI|
|Lecture du Scale-out|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|



\* Pour connaître le nombre maximal de Workers simultanés (demandes) pour une base de données individuelle, consultez [Limites de ressources des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md). Par exemple, si le pool élastique utilise Gen5 et que son nombre maximal de vCores par base de données est de 2, le nombre maximal de Workers simultanés est de 200.  Si le nombre maximal de vCores par base de données est de 0,5, le nombre maximal de Workers simultanés est de 50, puisque le nombre maximal de Workers est de 100 sur Gen5.  Pour les autres paramètres de nombre maximal de vCores par base de données qui sont inférieurs ou égaux à 1 vCore, le nombre maximum de Workers simultanés est adapté en conséquence.

Si tous les vCore d’un pool élastique sont occupés, chaque base de données du pool reçoit une quantité égale de ressources de calcul pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de vCore par base de données est défini sur une valeur différente de zéro.

## <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

> [!NOTE]
> Les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools qui ont la même taille de calcul. Par exemple, le nombre maximal de workers simultanés dans une base de données GP_Gen4_1 est de 200. Par conséquent, le nombre maximal de workers simultanés pour une base de données dans un pool GP_Gen4_1 est aussi de 200. Notez que le nombre total de workers simultanés dans le pool GP_Gen4_1 est de 210.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal de vCore par base de données |Nombre maximal de vCore pouvant être utilisés par une des bases de données du pool en fonction du nombre de vCore utilisés par les autres bases de données du pool. Le nombre maximal de vCore par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal de vCore par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données, dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation.|
| Nombre minimal de vCore par base de données |Nombre minimal de vCore garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal de vCore par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen de vCore utilisés par base de données. Le produit du nombre de bases de données du pool et du nombre minimal de vCore par base de données ne peut pas dépasser le nombre de vCore par pool.|
| Espace de stockage maximal par base de données |La taille de base de données maximale définie par l’utilisateur pour une base de données dans un pool. Les bases de données mises en pool se partagent l’espace de stockage du pool alloué. Par conséquent, la taille qu’une base de données peut atteindre est limitée au stockage de pool minimal restant et à la taille de base de données. La taille de base de données maximale fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les limites de ressources vCore d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat vCore](sql-database-vcore-resource-limits-single-databases.md)
- Pour connaître les limites de ressources DTU d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-single-databases.md)
- Pour connaître les limites de ressources DTU des pools élastiques, consultez l’article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources des instances gérées, consultez l'article consacré aux [limites de ressources des instances gérées](sql-database-managed-instance-resource-limits.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d'informations sur les limites de ressources au niveau du serveur de base de données et de l'abonnement, consultez l'article [Vue d'ensemble des limites de ressources sur un serveur SQL Database](sql-database-resource-limits-database-server.md).
