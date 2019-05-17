---
title: Filtres de facette pour la navigation de recherche dans les applications - Recherche Azure
description: Effectuez un filtrage selon des critères par identité de sécurité d’utilisateur, emplacement géographique ou valeurs numériques pour réduire les résultats de recherche des requêtes effectuées dans Recherche Azure, service de recherche dans le cloud hébergé sur Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8dffc5b87aefe23953d3a74f1d96b5ee03e0315d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597391"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Comment créer un filtre de facette dans la Recherche Azure 

La navigation par facettes est utilisée pour le filtrage autonome sur les résultats d’une requête dans une application de recherche, dans le cadre de laquelle votre application propose des contrôles d’interface utilisateur permettant de limiter une recherche à des groupes de documents (par exemple, des catégories de marques) ; la Recherche Azure fournit la structure de données sur laquelle reposera l’opération. Dans cet article, nous passerons rapidement en revue les étapes de base de la procédure de création d’une structure de navigation par facettes, sur laquelle sera basée l’expérience de recherche que vous souhaitez proposer. 

> [!div class="checklist"]
> * Choisissez des champs pour le filtrage et la création de facettes
> * Définissez des attributs sur le champ
> * Créez l’index et chargez les données
> * Ajoutez des filtres de facettes à une requête
> * Gérez les résultats

Les facettes sont dynamiques et renvoyées sur une requête. Les réponses associées à une recherche incluent les catégories de facettes utilisées pour parcourir les résultats. Si vous n’êtes pas familiarisé avec les facettes, l’exemple suivant illustre une structure de navigation par facettes.

  ![](./media/search-filters-facets/facet-nav.png)

Vous découvrez ce type de navigation et souhaitez en savoir plus ? Consultez la section [Implémentation de la navigation à facettes dans la Recherche Azure](search-faceted-navigation.md).

## <a name="choose-fields"></a>Choisir des champs

Vous pouvez calculer des facettes sur la base de champs à une seule valeur, ou de collections. Les champs qui fonctionnent le mieux la navigation à facettes présentent une cardinalité faible : un petit nombre de valeurs distinctes qui se répètent tout au long de documents dans votre corpus de recherche (par exemple, une liste de couleurs, des pays/régions ou des noms de marques). 

Facettes sont activées sur le champ par champ lorsque vous créez l’index en définissant le `facetable` attribut `true`. Vous devez généralement définir également la `filterable` attribut `true` pour ces champs, afin que votre application de recherche peut filtrer sur ces champs en fonction de l’utilisateur final sélectionne des facettes. 

Lors de la création d’un index à l’aide de l’API REST, toute [type de champ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) qui peut éventuellement être utilisé dans la navigation à facettes est marquée comme `facetable` par défaut :

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Types de champs numériques : `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Collections des types ci-dessus (par exemple, `Collection(Edm.String)` ou `Collection(Edm.Double)`)

Vous ne pouvez pas utiliser `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` champs dans la navigation à facettes. Facettes fonctionnent mieux sur les champs présentant une faible cardinalité. En raison de la résolution de coordonnées géographiques, il est rare que les deux jeux de coordonnées est égales dans un jeu de données. Par conséquent, elles ne sont pas prises en charge pour les coordonnées géographiques. Vous aurez besoin d’un champ de ville ou de région pour les facettes créées par lieu.

## <a name="set-attributes"></a>Définir des attributs

Les attributs d’index qui contrôlent l’utilisation d’un champ sont ajoutés aux définitions de champs individuels dans l’index. Dans l’exemple suivant, les champs présentant une faible cardinalité, utile pour les facettes, se composent de : `category` (hôtel, motel, auberge), `tags`, et `rating`. Ces champs ont la `filterable` et `facetable` des attributs définis explicitement dans l’exemple suivant à titre d’illustration. 

> [!Tip]
> Pour optimiser le stockage et les performances, une meilleure pratique consiste à désactiver la création de facettes pour les champs qui ne doivent pas être utilisés en tant que facettes. En particulier, les champs de chaîne pour les valeurs uniques, comme un nom de produit ou d’ID, doivent être définis `"facetable": false` pour empêcher leur utilisation accidentelle (et inefficace) dans la navigation à facettes.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Cette définition d’index est copiée à partir de la section relative à la [création d’un index de Recherche Azure à l’aide de l’API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Il est identique, à l’exception de légères différences dans les définitions de champ. Le `filterable` et `facetable` attributs sont ajoutés de manière explicite `category`, `tags`, `parkingIncluded`, `smokingAllowed`, et `rating` champs. Dans la pratique, `filterable` et `facetable` serait activé par défaut sur ces champs lorsque vous utilisez l’API REST. Lorsque vous utilisez le kit SDK .NET, ces attributs doivent être activés explicitement.

## <a name="build-and-load-an-index"></a>Créer et charger un index

La [création et le remplissage de l’index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) sont une étape intermédiaire (et peut-être évidente) avant la formulation d’une requête. Nous signalons cette étape ici par souci d’exhaustivité. Une manière de déterminer si l’index est disponible consiste à vérifier la liste des index dans le [portail](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Ajoutez des filtres de facettes à une requête

Dans le code d’application, construisez une requête spécifiant toutes les parties d’une requête valide, y compris les expressions de recherche, les facettes, les filtres, les profils de score, à savoir tout ce qui sert à formuler une requête. L’exemple suivant génère une requête qui crée une navigation par facettes selon le type d’hébergement, d’évaluation et d’autres équipements.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Renvoyer des résultats filtrés sur les événements clic

Lorsque l’utilisateur final clique sur une valeur de facette, le gestionnaire pour l’événement click doit utiliser une expression de filtre de comprendre l’intention de l’utilisateur. Étant donné un `category` facette, en cliquant sur la catégorie « motel » est implémentée avec un `$filter` expression qui sélectionne les hébergements de ce type. Lorsqu’un utilisateur clique sur « motel » pour indiquer que seuls motels doivent être affichés, la requête suivante, l’application envoie inclut `$filter=category eq 'motel'`.

L’extrait de code suivant ajoute la catégorie au filtre si l’utilisateur sélectionne une valeur à partir de la facette « Category ».

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Si l’utilisateur clique sur une valeur de facette pour un champ de la collection comme `tags`, par exemple le regroupement « valeur », votre application doit utiliser la syntaxe de filtre suivante : `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Conseils et solutions de contournement

### <a name="initialize-a-page-with-facets-in-place"></a>Initialiser une page sur laquelle des facettes sont définies

Si vous souhaitez initialiser une page sur laquelle des facettes sont définies, vous pouvez envoyer une requête dans le cadre de l’initialisation de la page, afin d’amorcer la page avec une structure de facette initiale.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Conserver une structure de navigation par facettes de manière asynchrone par rapport aux résultats filtrés

L’une des difficultés liées à la navigation par facettes dans la Recherche Azure est le fait que les facettes existent uniquement pour les résultats actuels. Dans la pratique, il est courant de conserver un ensemble statique de facettes, afin que l’utilisateur puisse naviguer dans l’ordre inverse, en suivant les étapes déjà parcourues pour explorer les autres chemins d’accès via le contenu de la recherche. 

Il s’agit d’un cas d’usage courant, mais non fourni de manière prête à l’emploi par la structure de navigation par facettes. Les développeurs qui veulent utiliser des facettes statiques contournent cette limitation en émettant deux requêtes filtrées, l’une dont la portée se limite aux résultats et l’autre, qui permet de créer une liste statique de facettes pour la navigation.

## <a name="see-also"></a>Voir aussi

+ [Filtres dans Recherche Azure](search-filters.md)
+ [Création d’une API REST d’index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)
