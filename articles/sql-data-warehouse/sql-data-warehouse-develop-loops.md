---
title: Utilisation des boucles T-SQL dans Azure SQL Data Warehouse | Microsoft Docs
description: Conseils relatifs à l’utilisation de boucles T-SQL et au remplacement de curseurs dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: b7c21566916c9728900e69dc6480098fadae7622
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301206"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Utilisation de boucles T-SQL dans SQL Data Warehouse
Conseils relatifs à l’utilisation de boucles T-SQL et au remplacement de curseurs dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.

## <a name="purpose-of-while-loops"></a>Objectif des boucles WHILE

SQL Data Warehouse prend en charge la boucle [WHILE](/sql/t-sql/language-elements/while-transact-sql) pour les blocs d’instructions dont l’exécution se répète. Cette boucle WHILE se poursuit aussi longtemps que les conditions spécifiées sont vraies, ou jusqu’à ce que le code arrête la boucle de manière spécifique, via le mot clé BREAK. Les boucles s’avèrent utiles pour remplacer des curseurs définis dans le code SQL. Heureusement, presque tous les curseurs écrits en code SQL présentent le type à avance rapide, en lecture seule. Par conséquent, les boucles [WHILE] sont une alternative intéressante pour remplacer les curseurs.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Remplacement des curseurs dans SQL Data Warehouse
Toutefois, avant de vous lancer, vous devez vous poser la question suivante : « Puis-je réécrire ce curseur afin d’utiliser des opérations basées sur un jeu ? » Dans de nombreux cas, la réponse est oui, et cette approche est souvent la meilleure. Une opération basée sur un jeu s’exécute généralement plus rapidement qu’une méthode itérative de type ligne par ligne.

Les curseurs à avance rapide et en lecture seule peuvent facilement être remplacés par des constructions en boucle. Voici un exemple simple. Cet exemple de code met à jour les statistiques de chaque table dans la base de données. En effectuant une itération sur les tables dans la boucle, chaque commande s’exécute en séquence.

Tout d’abord, créez une table temporaire contenant un numéro de ligne unique, utilisé pour identifier les instructions individuelles :

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Deuxièmement, initialisez les variables nécessaires pour exécuter la boucle :

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ensuite, effectuez une boucle avec les instructions, en les exécutant l’une après l’autre :

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Enfin, supprimez la table temporaire créée à la première étape.

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).

