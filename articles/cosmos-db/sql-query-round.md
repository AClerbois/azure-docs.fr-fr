---
title: 'Langage de requête Azure Cosmos DB : ROUND'
description: Découvrez la fonction système SQL ROUND dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9712aedd3d6748f3bceea67a3270b6c080cc16f2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794284"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Retourne une valeur numérique, arrondie au nombre entier le plus proche.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Notes
  
  L’opération arrondit les valeurs médianes à la valeur la plus éloignée de zéro. Si l’entrée est une expression numérique qui se situe exactement entre deux entiers, le résultat est le nombre entier le plus éloigné de zéro. Cette fonction système bénéficiera d’un [index de plage](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Arrondi|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant arrondit les nombres positifs et négatifs suivants à l’entier le plus proche.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Voici le jeu de résultats obtenu.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
