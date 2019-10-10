---
title: 'Langage de requête Azure Cosmos DB : ARRAY_SLICE'
description: Découvrez la fonction système SQL ARRAY_SLICE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1df4177bb8b56bc98977af0f5180e8df5affb257
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348551"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Retourne une partie d’une expression de tableau.
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Arguments
  
*arr_expr*  
   Peut être toute expression de tableau.  
  
*num_expr*  
   Index numérique de base zéro à partir duquel commencer le tableau. Des valeurs négatives peuvent être utilisées pour spécifier l’index de départ par rapport au dernier élément du tableau, -1, par exemple, référence le dernier élément dans le tableau.  

*num_expr* est une expression numérique facultative qui définit le nombre maximal d’éléments dans le tableau obtenu.    

## <a name="return-types"></a>Types de retour
  
  Retourne une expression de tableau.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment obtenir différentes tranches d’un tableau avec `ARRAY_SLICE`.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de tableau Azure Cosmos DB](sql-query-array-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
