---
title: 'Langage de requête Azure Cosmos DB : LOG10'
description: Découvrez la fonction système SQL LOG10 dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349715"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Retourne le logarithme en base 10 de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expression*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Remarques
  
  Les fonctions LOG10 et POWER sont inversement liées entre elles. Par exemple, 10 ^ LOG10 (n) = n.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant déclare une variable et renvoie la valeur de LOG10 de la variable spécifiée (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
