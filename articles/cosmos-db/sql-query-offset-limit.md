---
title: Clause OFFSET LIMIT dans Azure Cosmos DB
description: Découvrez la clause OFFSET LIMIT pour Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326875"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Clause OFFSET LIMIT dans Azure Cosmos DB

La clause OFFSET LIMIT est une clause facultative qui indique à la requête de récupérer un certain nombre de valeurs après en avoir ignorées. Le nombre OFFSET et le nombre LIMIT sont obligatoires dans la clause OFFSET LIMIT.

Quand OFFSET LIMIT est utilisé conjointement avec une clause ORDER BY, la requête génère le jeu de résultats en ignorant certaines valeurs, puis en récupérant certaines valeurs en les classant. Si aucune clause ORDER BY n’est utilisée, l’ordre des valeurs est déterministe.

## <a name="syntax"></a>Syntaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Arguments

- `<offset_amount>`

   Spécifie le nombre entier d’éléments que doivent ignorer les résultats de la requête.

- `<limit_amount>`
  
   Spécifie le nombre entier d’éléments que doivent inclure les résultats de la requête.

## <a name="remarks"></a>Remarques
  
  Le nombre OFFSET et le nombre LIMIT sont obligatoires dans la clause OFFSET LIMIT. Si une clause `ORDER BY` facultative est utilisée, la requête génère le jeu de résultats en classant d’abord les valeurs avant d’en ignorer et conserver les nombres indiqués. Sinon, la requête retourne un ordre fixe de valeurs. Cette clause est uniquement prise en charge pour les requêtes au sein d’une seule partition ; les requêtes entre partitions ne la prennent pas encore en charge.

## <a name="examples"></a>Exemples

Par exemple, voici une requête qui ignore la première valeur et retourne la deuxième valeur (dans l’ordre du nom de la ville de résidence) :

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Les résultats sont :

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Voici une requête qui ignore la première valeur et retourne la deuxième valeur (sans classement) :

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main](sql-query-getting-started.md)
- [Clause SELECT](sql-query-select.md)
- [Clause ORDER BY](sql-query-order-by.md)
