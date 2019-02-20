---
title: Syntaxe des requêtes Lucene - Recherche Azure
description: Informations de référence sur la syntaxe Lucene complète, telle qu’elle est utilisée avec Recherche Azure.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 59362b28390556f12cce8813635894c9f06b9a20
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007787"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Syntaxe des requêtes Lucene dans Recherche Azure
Vous pouvez écrire des requêtes sur Recherche Azure en utilisant la syntaxe riche en fonctionnalités de l’[analyseur de requêtes Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) pour des formes de requêtes spécialisées : caractère générique, recherche approximative, recherche de proximité et expressions régulières en sont quelques exemples. La plus grande partie de la syntaxe de l’analyseur de requêtes Lucene est [implémentée telle quelle dans Recherche Azure](search-lucene-query-architecture.md), à l’exception des *recherches de plage*, qui sont construites dans Recherche Azure via des expressions `$filter`. 

## <a name="how-to-invoke-full-parsing"></a>Comment appeler l’analyse complète

Définissez le paramètre de recherche `queryType` pour spécifier l’analyseur à utiliser. Les valeurs valides sont `simple|full`, `simple` étant la valeur par défaut et `full` la valeur pour Lucene. 

<a name="bkmk_example"></a> 

## <a name="example-showing-full-syntax"></a>Exemple montrant la syntaxe complète

L’exemple suivant recherche les documents dans l’index avec la syntaxe de requête Lucene, ce qu’indique le paramètre `queryType=full`. Cette requête renvoie les hôtels où le champ de catégorie contient le terme « budget » et tous les champs pouvant faire l’objet d’une recherche contenant l’expression « récemment rénové ». Les documents contenant l’expression « recently renovated » sont mieux classés en raison de la valeur de promotion du terme (3).  

Le paramètre `searchMode=all` est approprié dans cet exemple. Quand des opérateurs se trouvent sur la requête, vous devez généralement définir `searchMode=all` pour garantir que *tous* les critères sont satisfaits.

```  
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full  
```  

 Vous pouvez aussi utiliser POST :  

```  
POST /indexes/hotels/docs/search?api-version=2015-02-28  
{  
  "search": "category:budget AND \"recently renovated\"^3",  
  "queryType": "full",  
  "searchMode": "all"  
}  
```  

Pour d’autres exemples, consultez [Exemples de syntaxe de requête Lucene pour créer des requêtes dans Recherche Azure](search-query-lucene-examples.md). Pour plus d’informations sur la spécification de tous les paramètres des requêtes, consultez [Rechercher des documents &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Recherche Azure prend également en charge une [syntaxe de requête simple](query-simple-syntax.md) : il s’agit d’un langage de requête simple et robuste qui peut être utilisé pour la recherche directe de mots clés.  


##  <a name="bkmk_fields"></a> Requêtes sur des champs  
 Vous pouvez spécifier une construction `fieldname:searchterm` pour définir une opération de requête portant sur un champ, où le champ est un mot unique, et où le terme de recherche est également un mot ou une expression unique, éventuellement avec des opérateurs booléens. Voici quelques exemples :  

-   genre:jazz NOT history  

-   artists:("Miles Davis" "John Coltrane")

 Veillez à placer les chaînes multiples entre guillemets si vous voulez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux artistes distincts sont recherchés dans le champ `artists`.  

 Le champ spécifié dans `fieldname:searchterm` doit être un champ `searchable`.  Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champs, consultez [Créer un index ](https://docs.microsoft.com/rest/api/searchservice/create-index).  

##  <a name="bkmk_fuzzy"></a> Recherche approximative  
 Une recherche partielle recherche des correspondances dans les termes qui ont une construction similaire. D’après la [documentation Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), les recherches partielles sont basées sur la [Distance Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance).  

 Pour effectuer une recherche approximative, utilisez le symbole « ~ » (tilde) à la fin d’un mot avec un paramètre facultatif, un nombre compris entre 0 et 2 (la valeur par défaut), qui spécifie la distance de modification. Par exemple, « blue~ » ou « blue~1 » retournent « blue », « blues » et « glue ».

 La recherche approximative peut être appliquée seulement aux termes, et non pas aux expressions. Les recherches approximatives peuvent développer un terme jusqu’à un maximum de 50 termes qui répondent aux critères de distance.

##  <a name="bkmk_proximity"></a> Recherche de proximité  
 Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité. Par exemple, `"hotel airport"~5` recherche les termes « hotel » et « airport » distants de 5 mots ou moins dans un document.  


##  <a name="bkmk_termboost"></a> Promotion de termes  
 La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. À ne pas confondre avec les profils de score qui promeuvent certains champs, plutôt que des termes spécifiques.  

L’exemple suivant permet d’illustrer les différences entre les deux. Supposons qu’un profil de score promeuve les correspondances dans un certain champ, disons *genre* dans l’[exemple musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). En utilisant la promotion de termes, vous pouvez promouvoir encore plus certains termes de recherche. Par exemple, `rock^2 electronic` promeut les documents qui contiennent les termes de recherche dans le champ genre en les classant mieux que d’autres champs de recherche de l’index. Par ailleurs, les documents qui contiennent le terme de recherche *rock* bénéficient d’un meilleur classement que ceux qui contiennent le terme de recherche *electronic* en raison de la valeur de promotion du terme (2).  

 Pour promouvoir un terme, utilisez le signe « ^ » (caret) avec un facteur de promotion (un nombre) à la fin du terme recherché. Vous pouvez également promouvoir des expressions. Plus le facteur de promotion est élevé, plus le terme est pertinent par rapport aux autres termes de recherche. Par défaut, le facteur de promotion est égal à 1. Ce facteur doit être positif, mais il peut être inférieur à 1 (par exemple 0,20).  

##  <a name="bkmk_regex"></a> Recherche d’expression régulière  
 Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Par exemple, pour rechercher des documents contenant « motel » ou « hotel », spécifiez `/[mh]otel/`.  Les recherches d’expression régulière se font par comparaison avec des mots individuels.   

##  <a name="bkmk_wildcard"></a> Recherche par caractères génériques  
 Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (*) ou un caractère générique unique (?). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.  

 Par exemple, pour rechercher des documents contenant les mots avec le préfixe « note », comme « notebook » ou « notepad », spécifiez « note* ».  

> [!NOTE]  
>  Vous ne pouvez pas utiliser un signe * ou ? comme premier caractère d’une recherche.  
>  Aucune analyse de texte n’est effectuée sur les requêtes de recherche par caractères génériques. Au moment de la requête, les termes de la requête par caractères génériques sont comparés à des termes analysés dans l’index de recherche et développés.

##  <a name="bkmk_syntax"></a> Principes de base de la syntaxe  
 Les principes de base suivants de la syntaxe s’appliquent à toutes les requêtes qui utilisent la syntaxe Lucene.  

### <a name="operator-evaluation-in-context"></a>Évaluation des opérateurs en contexte

L’emplacement détermine si un symbole est interprété comme un opérateur ou simplement comme un autre caractère dans une chaîne.

Par exemple, dans la syntaxe complète Lucene, le tilde (~) est utilisé pour la recherche approximative et la recherche de proximité. Quand il est placé après une expression entre guillemets, « ~ » appelle la recherche de proximité. Quand il est placé à la fin d’un terme, « ~ » appelle la recherche approximative.

Au sein d’un terme, comme « business~analyst », le caractère n’est pas évalué comme opérateur. Dans ce cas, en supposant que la requête est une requête de terme ou d’expression, la [recherche en texte intégral](search-lucene-query-architecture.md) avec [analyse lexicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) supprime le ~ et décompose le terme « business~analyst » en deux : business OR analyst.

L’exemple ci-dessus concerne le tilde (~), mais le même principe s’applique à chaque opérateur.

### <a name="escaping-special-characters"></a>Échappement des caractères spéciaux

 Les caractères spéciaux doivent être placés en échappement pour être utilisé comme partie du texte de recherche. Vous pouvez les placer en échappement en les préfixant d’une barre oblique inverse (\\). Les caractères spéciaux qui doivent être placés en échappement sont les suivants :  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Par exemple, pour placer en échappement un caractère générique, utilisez \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Encodage de caractères dangereux et réservés dans les URL

 Vérifiez que tous les caractères dangereux et réservés dans une URL sont encodés. Par exemple, « # » est un caractère dangereux, car c’est un identificateur de fragment/ancre dans une URL. Le caractère doit être encodé en `%23` s’il est utilisé dans une URL. « & » et « = » sont des exemples de caractères réservés, car ils délimitent les paramètres et spécifient des valeurs dans Recherche Azure. Pour plus d’informations, consultez [RFC1738 : Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

 Les caractères dangereux sont ``" ` < > # % { } | \ ^ ~ [ ] ``. Les caractères réservés sont `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Opérateurs de priorité : regroupement et regroupement de champs  
 Vous pouvez utiliser des parenthèses pour créer des sous-requêtes, en incluant des opérateurs au sein de l’instruction entre parenthèses. Par exemple, `motel+(wifi||luxury)` recherche les documents contenant le terme « motel », et « wifi » ou « luxury » (ou les deux).

Le regroupement de champs est similaire, mais il délimite le regroupement à un seul champ. Par exemple, `hotelAmenities:(gym+(wifi||pool))` recherche « gym » et « wifi », ou « gym » et « pool », dans le champ « hotelAmenities ».  

### <a name="searchmode-parameter-considerations"></a>Considérations relatives au paramètre SearchMode  
 L’impact de `searchMode` sur les requêtes, comme décrit dans [Syntaxe des requêtes simples dans Recherche Azure](query-simple-syntax.md), s’applique également à la syntaxe de requête Lucene. Ainsi, `searchMode` en combinaison avec des opérateurs NOT peut entraîner des résultats de requête qui peuvent sembler étrange si vous ne comprenez pas bien les implications de la façon dont vous définissez le paramètre. Si vous conservez la valeur par défaut, `searchMode=any`, et que vous utilisez un opérateur NOT, l’opération est considérée comme une action OR : ainsi, « New York » NOT « Seattle » retourne toutes les villes qui ne sont pas Seattle.  

##  <a name="bkmk_boolean"></a> Opérateurs booléens  
 Spécifiez toujours les opérateurs booléens de texte (AND, OR, NOT) tout en majuscules.  

#### <a name="or-operator-or-or-"></a>Opérateur OR `OR` ou `||`

L’opérateur OR est une barre verticale. Par exemple, `wifi || luxury` recherche les documents contenant « wifi » ou « luxury », ou les deux. Comme OR est l’opérateur de conjonction par défaut, vous pouvez aussi l’omettre : ainsi, `wifi luxury` est l’équivalent de `wifi || luxuery`.

#### <a name="and-operator-and--or-"></a>Opérateur AND `AND`, `&&` ou `+`

L’opérateur AND est une esperluette ou un signe plus. Par exemple, `wifi && luxury` recherche les documents contenant à la fois « wifi » et « luxury ». Le caractère plus (+) est utilisé pour les termes obligatoirement présents. Par exemple, `+wifi +luxury` stipule que les deux termes doivent apparaître quelque part dans le champ d’un même document.


#### <a name="not-operator-not--or--"></a>Opérateur NOT `NOT`, `!` ou `-`

L’opérateur NOT est un point d’exclamation ou le signe moins. Par exemple, `wifi !luxury` recherche les documents qui ont le terme « wifi » et/ou n’ont pas « luxury ». L’option `searchMode` contrôle si un terme avec l’opérateur NOT est soumis à l’opération logique AND ou OR avec les autres termes de la requête en l’absence d’un opérateur + ou ||. Rappelez-vous que `searchMode` peut être défini sur `any` (la valeur par défaut) ou sur `all`.

L’utilisation de `searchMode=any` augmente le rappel des requêtes en incluant plus de résultats et est interprété par défaut comme « OR NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme *wifi* ou avec ceux qui ne contiennent pas le terme *luxury*.

L’utilisation de `searchMode=all` augmente la précision des requêtes en incluant moins de résultats, et par défaut, « - » est interprété comme « AND NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` et ne contiennent pas le terme `luxury`. Il s’agit sans doute d’un comportement plus intuitif pour l’opérateur -. Ainsi, vous pouvez préférer `searchMode=all` à `searchMode=any` si vous voulez optimiser les recherches pour la précision au lieu du rappel *et* si vos utilisateurs utilisent fréquemment l’opérateur `-` dans les recherches.

##  <a name="bkmk_querysizelimits"></a> Limite de taille des requêtes  
 Il existe une limite à la taille des requêtes que vous pouvez envoyer à Recherche Azure. Plus précisément, vous pouvez avoir au maximum 1 024 clauses (des expressions séparées par AND, OR, etc.). Il existe également une limite d’environ 32 Ko pour la taille d’un terme individuel dans une requête. Si votre application génère des requêtes de recherche par programmation, nous vous recommandons de la concevoir de façon à ce qu’elle ne génère pas des requêtes d’une taille illimitée.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Scoring des requêtes avec des caractères génériques et des expressions régulières
 Recherche Azure utilise sur un scoring basé sur la fréquence ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) pour les requêtes de texte. Cependant, pour les requêtes avec des caractères génériques et des expressions régulières où l’étendue des termes est potentiellement vaste, le facteur de fréquence est ignoré pour empêcher que le classement soit faussé par les correspondances avec des termes plus rares. Toutes les correspondances sont traitées de façon égale pour les recherches avec des caractères génériques et avec des expressions régulières.

## <a name="see-also"></a>Voir aussi  

+ [Recherche dans des documents](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe des expressions OData pour les filtres et le tri](query-odata-filter-orderby-syntax.md)   
+ [Syntaxe des requêtes simples dans Recherche Azure](query-simple-syntax.md)   
