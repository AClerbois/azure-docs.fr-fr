---
title: Informations de référence sur les opérateurs de collection OData - Recherche Azure
description: Opérateurs de collection OData, any et all et expressions lambda dans les requêtes de Recherche Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647637"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Opérateurs de collection OData dans Recherche Azure - `any` et `all`

Lorsque vous écrivez une [expression de filtre OData](query-odata-filter-orderby-syntax.md) à utiliser avec Recherche Azure, il est souvent utile de filtrer sur les champs de la collection. Vous pouvez y parvenir à l’aide des opérateurs `any` et `all`.

## <a name="syntax"></a>Syntaxe

L’extension EBNF suivante ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) définit la grammaire d’une expression OData utilisant `any` ou `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Un diagramme de syntaxe interactif est également disponible :

> [!div class="nextstepaction"]
> [Diagramme de syntaxe OData pour Recherche Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consultez [Informations de référence sur la syntaxe d’expression OData pour Recherche Azure](search-query-odata-syntax-reference.md) pour l’extension EBNF complète.

Il existe trois formes d’expression qui filtrent les collections.

- Les deux premiers itèrent sur un champ de collection, en appliquant un prédicat donné sous la forme d’une expression lambda à chaque élément de la collection.
  - Une expression qui utilise `all` renvoie `true` si le prédicat a la valeur true pour chaque élément de la collection.
  - Une expression qui utilise `any` renvoie `true` si le prédicat a la valeur true pour au moins un élément de la collection.
- La troisième forme de filtre de collection utilise `any` sans expression lambda pour tester si un champ de la collection est vide. Si la collection comporte des éléments, elle renvoie `true`. Si la collection est vide, elle renvoie `false`.

Un **expression lambda** d’un filtre de collection est comme le corps d’une boucle dans un langage de programmation. Elle définit une variable, appelée **variable de portée**, qui contient l’élément actuel de la collection pendant l’itération. Elle définit également une autre expression booléenne qui est le critère de filtre à appliquer à la variable de portée pour chaque élément de la collection.

## <a name="examples"></a>Exemples

Correspondance des documents dont le champ `tags` contient exactement la chaîne « wifi » :

    tags/any(t: t eq 'wifi')

Correspondance des documents où chaque élément du champ `ratings` se situe entre 3 et 5, y compris :

    ratings/all(r: r ge 3 and r le 5)

Correspondance des documents où chaque coordonnée géographique du champ `locations` se trouve dans le polygone donné :

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Correspondance des documents dans lesquels le champ `rooms` est vide :

    not rooms/any()

Correspondance des documents où pour toutes les salles, le champ `rooms/amenities` contient « tv » et `rooms/baseRate` est inférieur à 100 :

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limites

Toutes les fonctionnalités d’expressions de filtre ne sont pas disponibles dans le corps d’une expression lambda. Les restrictions diffèrent selon le type de données du champ de la collection que vous souhaitez filtrer. Le tableau suivant récapitule les restrictions :

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Pour plus d’informations sur ces restrictions et pour obtenir des exemples, consultez [Résolution des problèmes de filtres de collection dans Recherche Azure](search-query-troubleshoot-collection-filters.md). Pour en savoir plus sur la raison de l’existence de ces restrictions, consultez [Présentation des filtres de collection dans Recherche Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Étapes suivantes  

- [Filtres dans Recherche Azure](search-filters.md)
- [Vue d’ensemble du langage d’expression OData pour Recherche Azure](query-odata-filter-orderby-syntax.md)
- [Informations de référence sur la syntaxe d’expression OData pour Recherche Azure](search-query-odata-syntax-reference.md)
- [Rechercher des documents &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
