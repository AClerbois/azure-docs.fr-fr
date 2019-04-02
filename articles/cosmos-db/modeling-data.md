---
title: Modélisation de données de document dans une base de données NoSQL
titleSuffix: Azure Cosmos DB
description: Apprenez tout ce que devez savoir sur la modélisation des données dans des bases de données NoSQL et sur les différences entre la modélisation des données dans une base de données relationnelle et une base de données de documents.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: andrl
ms.custom: seodec18
ms.openlocfilehash: 5f117d51378f895755b4f5a27fe892d85e12074a
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762580"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modélisation des données dans Azure Cosmos DB

Bien que les bases de données sans schéma, comme Azure Cosmos DB, rendent très facile stocker et interroger des données non structurées et semi-structurées, vous devez prendre le temps de réfléchir sur votre modèle de données pour tirer le meilleur parti du service en termes de performances et l’évolutivité et la plus basse coût.

Comment les données seront-elles stockées ? Comment votre application va-t-elle récupérer et interroger des données ? Votre application est-elle des lectures ou écritures ?

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Qu'est-ce que la modélisation de données et pourquoi dois-je m'en soucier ?
* Comment la modélisation des données dans Azure Cosmos DB est différent pour une base de données relationnelle ?
* Comment exprimer les relations entre les données dans une base de données non relationnelle ?
* Quand dois-je incorporer les données et quand dois-je créer un lien vers les données ?

## <a name="embedding-data"></a>Incorporation de données

Lorsque vous démarrez la modélisation des données dans Azure Cosmos DB essayez de traiter vos entités en tant que **éléments autonomes** représenté en tant que documents JSON.

Pour la comparaison, nous allons tout d’abord voir comment nous pouvons modéliser les données dans une base de données relationnelle. L'exemple suivant montre comment une personne peut être stockée dans une base de données relationnelle.

![Modèle de base de données relationnelle](./media/sql-api-modeling-data/relational-data-model.png)

Lorsque vous travaillez avec des bases de données relationnelles, la stratégie consiste à normaliser toutes vos données. La normalisation de vos données généralement consiste à prendre une entité, une personne, par exemple et la décomposer en composants distincts. Dans l’exemple ci-dessus, une personne peut avoir plusieurs enregistrements de détail de contact, ainsi que plusieurs enregistrements d’adresse. Les détails du contact peuvent être subdivisés en extrayant courantes champs tels qu’un type. Vaut pour l’adresse, chaque enregistrement peut être de type *accueil* ou *Business*.

Le principe directeur lors de la normalisation des données consiste à **éviter de stocker des données redondantes** dans chaque enregistrement et à faire plutôt référence aux données. Dans cet exemple, pour lire une personne, avec toutes ses coordonnées et adresses, vous devez utiliser des jointures efficacement composer précédent (ou dénormaliser) vos données en cours d’exécution.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

La mise à jour d'une personne avec ses coordonnées et adresses nécessite des opérations d'écriture sur plusieurs tables individuelles.

Maintenant examinons à présent comment nous pourrions modéliser les mêmes données comme une entité autonome dans Azure Cosmos DB.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

À l’aide de l’approche ci-dessus nous **dénormalisées** enregistrer de la personne, par **incorporation** toutes les informations relatives à cette personne, telles que ses coordonnées et les adresses, dans un *unique JSON* document.
En outre, étant donné que nous ne sommes pas limités à un schéma fixe, nous avons la possibilité d'avoir des coordonnées de formes entièrement différentes.

Récupération d’un enregistrement complet de personne à partir de la base de données est désormais un **opération de lecture seule** par rapport à un seul conteneur et pour un seul élément. La mise à jour d’un enregistrement de personne, avec ses coordonnées et les adresses, est également un **unique l’opération d’écriture** par rapport à un seul élément.

Avec la dénormalisation des données, votre application aura peut-être besoin d'émettre moins de requêtes et de mises à jour pour effectuer les opérations courantes.

### <a name="when-to-embed"></a>Quand utiliser l'incorporation

En général, utilisez des modèles de données incorporés dans les cas suivants :

* Il existe **contenus** relations entre entités.
* Il existe des relations de type **un-à-plusieurs** entre des entités.
* Des données incorporées **changent rarement**.
* Il existe des données incorporées ne dépassera **sans limite**.
* Il existe des données incorporées sont **fréquemment interrogés**.

> [!NOTE]
> Normalement, les modèles de données dénormalisés offrent de meilleures performances en **lecture** .

### <a name="when-not-to-embed"></a>Quand éviter l'incorporation

Alors que la règle de base dans Azure Cosmos DB est de tout dénormaliser et d’incorporer toutes les données dans un seul élément, cela peut entraîner des situations qui doivent être évitées.

Prenons cet extrait de code JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Une entité post avec commentaires incorporés pourrait avoir cet aspect si nous étions en train de modéliser un système de blog, ou CMS, classique. Dans cet exemple, le problème est que le tableau de commentaires est **illimité**, c’est-à-dire qu’il n’existe aucune limite (pratique) au nombre de commentaires possibles pour une publication. Cela peut devenir un problème car la taille de l’élément Impossible d’augmenter à l’infini.

Comme la taille de l’élément devient la possibilité de transmettre les données sur le câble, ainsi que la lecture et la mise à jour de l’élément, à l’échelle, sera affectée.

Dans ce cas, il serait préférable de considérer le modèle de données suivantes.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Ce modèle présente les trois commentaires les plus récents incorporés dans le conteneur de post, ce qui est un tableau avec un ensemble fixe d’attributs. Les autres commentaires sont regroupés par lots de 100 commentaires et stockés en tant qu’éléments distincts. 100 a été choisi comme taille de lot parce que notre application fictive permet à l'utilisateur de charger 100 commentaires à la fois.  

Un autre cas où l’incorporation de données ne sont pas une bonne idée est lorsque les données incorporées sont souvent utilisées entre les éléments et changent fréquemment.

Prenons cet extrait de code JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Il pourrait représenter le portefeuille d'actions d'une personne. Nous avons choisi d’incorporer les informations boursières dans chaque document de portefeuille. Dans un environnement où les données associées changent fréquemment, comme une application de transactions boursières, incorporer les données qui changent fréquemment signifie que vous devez mettre à jour constamment chaque document de portefeuille, chaque fois que des actions sont échangées.

Des actions *zaza* peuvent être échangées des centaines de fois au cours d’une même journée, et des milliers d’utilisateurs peuvent posséder des actions *zaza* dans leur portefeuille. Avec un modèle de données comme le modèle ci-dessus, nous devons mettre à jour quotidiennement et à de nombreuses reprises des milliers de documents de portefeuille. Cela aboutit à un système qui n’est pas évolutif.

## <a name="referencing-data"></a>Référencement des données

Ainsi, l'incorporation de données fonctionne bien dans la plupart des cas, mais il est clair qu'il existe des scénarios où la dénormalisation de vos données provoque plus de problèmes qu'il n'en faudrait. Que faire, alors ?

Les bases de données relationnelles ne sont pas le seul endroit où vous pouvez créer des relations entre les entités. Dans une base de données de documents, vous pouvez avoir des informations dans un document qui sont en relation avec des données dans d’autres documents. Maintenant, je ne préconise absolument pas de créer des systèmes qui seraient mieux adaptés à une base de données relationnelle dans Azure Cosmos DB, ou toute autre base de données de documents, mais de simples relations conviennent et peuvent être utiles.

Dans le code JSON ci-dessous, nous avons choisi d'utiliser l'exemple de portefeuille d'actions précédent, mais cette fois, nous faisons référence à l'action dans le portefeuille au lieu de l'incorporer. Ainsi, lorsque l'action change fréquemment au cours de la journée, le seul document à mettre à jour est le document d'action (stock).

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

Cette approche présente cependant un inconvénient si votre application doit afficher des informations sur chaque action qui est conservée lors de l'affichage du portefeuille d'une personne ; dans ce cas, vous devez faire plusieurs aller et retour jusqu'à la base de données afin de charger les informations pour chaque document d'action. Ici, nous avons pris une décision pour améliorer l'efficacité des opérations d'écriture, qui ont lieu fréquemment pendant la journée, mais nous avons fait un compromis sur les opérations de lecture, qui ont potentiellement moins d'impact sur les performances de ce système.

> [!NOTE]
> Les modèles de données normalisés **peuvent nécessiter davantage d’aller-retour** jusqu’au serveur.

### <a name="what-about-foreign-keys"></a>Qu'en est-il des clés étrangères ?

Dans la mesure où il n'existe actuellement aucun concept d'une contrainte (clé étrangère ou autre), toutes les relations entre documents que vous avez dans les documents sont effectivement des « liens faibles » et elles ne sont pas vérifiées par la base de données. Si vous souhaitez vous assurer que les données auxquelles un document fait référence existent réellement, vous devez le faire dans votre application, ou en utilisant des déclencheurs côté serveur ou des procédures stockées sur Azure Cosmos DB.

### <a name="when-to-reference"></a>Quand utiliser des références

En général, utilisez des modèles de données normalisés dans les cas suivants :

* Représentation des relations **un-à-plusieurs** .
* Représentation des relations **plusieurs-à-plusieurs** .
* Les données associées **changent fréquemment**.
* Les données référencées peuvent être **illimitées**.

> [!NOTE]
> En général, la normalisation offre de meilleures performances en **écriture** .

### <a name="where-do-i-put-the-relationship"></a>Où placer la relation ?

La croissance de la relation permet de déterminer dans quel document doit être stockée la référence.

Examinons le code JSON ci-dessous qui modélise des éditeurs et des livres :

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Si le nombre de livres par éditeur est peu élevé avec une croissance faible limitée, il peut être utile de stocker la référence du livre dans le document d'éditeur (publisher). Toutefois, si le nombre de livres par éditeur est illimité, ce modèle de données aboutira à des tableaux mutables, croissants, comme dans l'exemple de document d'éditeur ci-dessus.

Un petit changement donnera un modèle qui représente toujours les mêmes données, mais évite désormais ces grandes collections mutables.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

Dans l'exemple ci-dessus, nous avons supprimé la collection illimitée dans le document d'éditeur (publisher). À la place, nous avons simplement une référence à l’éditeur dans chaque document de livre (book).

### <a name="how-do-i-model-manymany-relationships"></a>Comment modéliser des relations plusieurs-à-plusieurs ?

Dans une base de données relationnelle *plusieurs-à-plusieurs* , les relations sont souvent modélisées avec des tables de jointure qui relient simplement les enregistrements d’autres tables.

![Tables de jointures](./media/sql-api-modeling-data/join-table.png)

Vous pouvez être tenté de répliquer la même chose à l'aide de documents et de générer un modèle de données qui ressemble à ce qui suit.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Cette méthode fonctionne. Toutefois, le fait de charger soit un auteur avec ses livres soit un livre avec son auteur nécessite toujours au moins deux requêtes supplémentaires sur la base de données. Une requête pour le document de jointure (joining) et une autre requête pour extraire le document joint.

Si cette table de jointure ne fait rien d'autre que coller ensemble deux éléments de données, pourquoi ne pas la supprimer complètement ?
Examinons le code suivant.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Maintenant, si j’ai un auteur, je saurai immédiatement quels livres qu’ils ont écrits, et inversement si j’avais un document de livre chargé je connaîtrai les ID des auteurs. Cela permet de faire l'économie de cette requête intermédiaire sur la table de jointure en réduisant le nombre d'aller et retour jusqu'au serveur pour votre application.

## <a name="hybrid-data-models"></a>Modèles de données hybrides

Nous savons maintenant que l'incorporation (ou la dénormalisation) et le référencement (ou la normalisation) des données ont tous deux leurs avantages, mais qu'ils impliquent également des compromis.

Vous n'êtes pas toujours obligé de choisir soit l'un soit l'autre. N'ayez pas peur de combiner les deux.

En fonction des modèles d'utilisation et des charges de travail spécifiques de votre application, dans certains cas, la combinaison de données incorporées et de données référencées peut s'avérer intéressante et conduire à une logique d'application plus simple, avec moins d'aller et retour jusqu'au serveur, tout en préservant un bon niveau de performances.

Examinons le code JSON suivant.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

Ici nous avons suivi (principalement) le modèle incorporé, où les données des autres entités sont incorporées dans le document de niveau supérieur, mais les autres données sont référencées.

Dans le document de livre (book), nous pouvons voir quelques champs intéressants lorsque nous examinons le tableau des auteurs. Il existe un `id` champ qui est le champ que nous utilisons pour faire référence à un document d’auteur, une pratique courante dans un modèle normalisé, mais nous avons également `name` et `thumbnailUrl`. Nous pourrions avoir bloqué avec `id` et a laissé l’application pour obtenir des informations supplémentaires, il est nécessaire à partir du document d’auteur respectifs à l’aide de la « liaison », mais étant donné que notre application affiche le nom de l’auteur et d’une image miniature avec chaque livre affiché, nous pouvons enregistrer un aller-retour au serveur par livre dans une liste en dénormalisant **certains** données à partir de l’auteur.

Bien sûr, si le nom de l’auteur modifié ou qu’il souhaitait mettre à jour sa photo, nous devrions accéder et mettre à jour de chaque livre ils jamais publient, mais pour notre application, basée sur l’hypothèse que les auteurs ne changent pas leurs noms souvent, il s’agit d’une décision de conception acceptable.  

Dans cet exemple, il existe des valeurs **d’agrégats précalculés** pour économiser un traitement coûteux sur une opération de lecture. Dans l'exemple, certaines données incorporées dans le document d'auteur (author) sont des données calculées au moment de l'exécution. À chaque publication d’un nouveau livre, un document de type livre est créé **et** le champ countOfBooks est défini sur une valeur calculée en fonction du nombre de documents de type livre existant pour un auteur particulier. Cette optimisation serait appropriée dans les systèmes qui exigent de nombreuses lectures (read heavy), où nous pouvons nous permettre d'effectuer des calculs sur les écritures afin d'optimiser les lectures.

L’existence d’un modèle avec des champs précalculés est possible, car Azure Cosmos DB prend en charge les **transactions multidocuments**. De nombreuses boutiques NoSQL ne peuvent pas effectuer des transactions à travers plusieurs documents et plaident par conséquent en faveur de décisions de conception, telles que « incorporer tout systématiquement », en raison de cette limitation. Avec Azure Cosmos DB, vous pouvez utiliser des déclencheurs côté serveur, ou des procédures stockées, qui insèrent des livres et mettent à jour les auteurs au sein d’une transaction ACID. Aujourd’hui, vous n’êtes pas **tenu** d’intégrer tous les éléments dans un seul document, simplement pour vous assurer que vos données restent cohérentes.

## <a name="distinguishing-between-different-document-types"></a>Faire la distinction entre les différents types de documents

Dans certains scénarios, vous souhaiterez combiner différents types de documents dans la même collection ; Cela est généralement le cas lorsque vous souhaitez que plusieurs, liés se trouvent dans le même des documents [partition](partitioning-overview.md). Par exemple, vous pouvez placer les deux livres et critiques dans la même collection de livres et de partition par `bookId`. Dans ce cas, vous souhaitez généralement ajouter à vos documents avec un champ qui identifie leur type afin de les différencier.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Étapes suivantes

Comprendre que la modélisation des données dans un monde sans schéma reste aussi importante que jamais, telle est la principale leçon à tirer de cet article.

De même qu'il existe plusieurs façons de représenter un élément de données sur un écran, il existe plusieurs manières de modéliser vos données. Vous devez comprendre votre application et comment elle produira, utilisera et traitera les données. Ensuite, en appliquant certaines des instructions présentées ici, vous pouvez entreprendre de créer un modèle qui répond aux besoins immédiats de votre application. Lorsque vos applications doivent changer, vous pouvez exploiter la flexibilité d'une base de données sans schéma pour adopter ce changement et développer facilement votre modèle de données.

Pour en savoir plus sur Azure Cosmos DB, consultez la page de [documentation](https://azure.microsoft.com/documentation/services/cosmos-db/) du service.

Pour comprendre la répartition de vos données entre plusieurs partitions, consultez [Partitionnement, clés de partition et mise à l’échelle dans Azure Cosmos DB](sql-api-partition-data.md).
