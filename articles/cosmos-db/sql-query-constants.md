---
title: Constantes SQL dans Azure Cosmos DB
description: Découvrez les constantes SQL dans Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343213"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Constantes de requête SQL Azure Cosmos DB  

 Une constante, également appelée un littéral ou une valeur scalaire, est un symbole représentant une valeur de données spécifique. Le format d’une constante dépend du type de données de la valeur qu’elle représente.  
  
 **Prise en charge des types de données scalaires :**  
  
|**Type**|**Ordre des valeurs**|  
|-|-|  
|**Undefined**|Valeur unique : **Undefined**|  
|**Null**|Valeur unique : **null**|  
|**Booléen**|Valeurs : **false**, **true**.|  
|**Nombre**|Un nombre à virgule flottante double précision répondant à la norme IEEE 754.|  
|**Chaîne**|Une séquence de zéro ou plusieurs caractères Unicode. Les chaînes doivent figurer entre guillemets simples ou doubles.|  
|**Tableau**|Une séquence de zéro ou plusieurs éléments. Chaque élément peut être une valeur de tout type de données scalaires, à l’exception de **Undefined**.|  
|**Object**|Un jeu non ordonné de zéro ou plusieurs paires nom/valeur. Le nom est une chaîne Unicode, la valeur peut être de n’importe quel type de données scalaire, sauf **Undefined**.|  
  
## <a name="bk_syntax"></a>Syntaxe
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> Arguments
  
* `<undefined_constant>; Undefined`  
  
  Représente la valeur undefined du type Undefined.  
  
* `<null_constant>; null`  
  
  Représente la valeur **null** du type **Null**.  
  
* `<boolean_constant>`  
  
  Représente une constante de type booléen.  
  
* `false`  
  
  Représente une valeur **false** de type booléen.  
  
* `true`  
  
  Représente une valeur **true** de type booléen.  
  
* `<number_constant>`  
  
  Représente une constante.  
  
* `decimal_literal`  
  
  Les littéraux décimaux sont représentés à l’aide de la notation décimale, ou de la notation scientifique.  
  
* `hexadecimal_literal`  
  
  Les littéraux hexadécimaux sont représentés à l’aide du préfixe « 0 x », suivi d’un ou plusieurs chiffres hexadécimaux.  
  
* `<string_constant>`  
  
  Représente une constante de type chaîne.  
  
* `string _literal`  
  
  Les littéraux de chaîne sont des chaînes Unicode représentées par une séquence de zéro ou plusieurs caractères Unicode ou séquences d’échappement. Les littéraux de chaîne sont placés entre guillemets simples (apostrophes, ’) ou guillemets doubles (").  
  
  Les séquences d’échappement suivantes sont autorisées :  
  
|**Séquence d’échappement**|**Description**|**Caractères Unicode**|  
|-|-|-|  
|\\'|apostrophe (')|U+0027|  
|\\"|guillemet (")|U+0022|  
|\\\ |barre oblique inversée (\\)|U+005C|  
|\\/|barre oblique (/)|U+002F|  
|\b|retour arrière|U+0008|  
|\f|saut de page|U+000C|  
|\n|saut de ligne|U+000A|  
|\r|retour chariot|U+000D|  
|\t|tab|U+0009|  
|\uXXXX|Un caractère Unicode défini par 4 chiffres hexadécimaux.|U+XXXX|  

## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modéliser des données de document](modeling-data.md)
