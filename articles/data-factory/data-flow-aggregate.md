---
title: Transformation d’agrégation dans le flux de données de mappage Azure Data Factory
description: Découvrez comment agréger des données à grande échelle dans Azure Data Factory avec la transformation d’agrégation de flux de données de mappage.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f21367c36ae31aa2115c109933a581bef464baf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676909"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformation d’agrégation dans le flux de données de mappage 

La transformation d’agrégation définit des agrégations de colonnes dans vos flux de données. À l’aide du Générateur d’expressions, vous pouvez définir différents types d’agrégations telles que SUM, MIN, MAX et COUNT regroupées par colonnes calculées ou existantes.

## <a name="group-by"></a>Regrouper par

Sélectionnez une colonne existante ou créez une colonne calculée à utiliser en tant que groupe par la clause de votre agrégation. Pour utiliser une colonne existante, sélectionnez-la dans la liste déroulante. Pour créer une colonne calculée, placez le curseur sur la clause et cliquez sur **Colonne calculée**. Cela ouvre le [générateur d’expressions de flux de données](concepts-data-flow-expression-builder.md). Une fois que vous avez créé votre colonne calculée, entrez le nom de colonne de sortie dans le champ **Name as** (Nommer sous). Si vous souhaitez ajouter un groupe supplémentaire par clause, placez le curseur sur une clause existante, puis cliquez sur l’icône plus.

![Regroupement des transformations d’agrégation par paramètres](media/data-flow/agg.png "Regroupement des transformations d’agrégation par paramètres")

Un groupe par clause est facultatif dans une transformation d’agrégation.

## <a name="aggregate-column"></a>Colonne d’agrégation 

Accédez à l’onglet **Agrégations** pour créer des expressions d’agrégation. Vous pouvez soit remplacer une colonne existante par une agrégation, soit créer un nouveau champ avec un nouveau nom. L’expression d’agrégation est entrée dans la zone de droite en regard du sélecteur de nom de colonne. Pour modifier l’expression, cliquez sur la zone de texte pour ouvrir le Générateur d’expressions. Pour ajouter des agrégations supplémentaires, pointez sur une expression existante et cliquez sur l’icône plus pour créer une colonne d’agrégation ou un [modèle de colonne](concepts-data-flow-column-pattern.md).

Chaque expression d’agrégation doit contenir au moins une fonction d’agrégation.

![Paramètres d’agrégation de transformation d’agrégation](media/data-flow/agg2.png "Paramètres d’agrégation de transformation d’agrégation")


> [!NOTE]
> En mode Débogage, le Générateur d’expressions ne peut pas produire d’aperçus de données avec des fonctions d’agrégation. Pour afficher des aperçus de données pour les transformations d’agrégation, fermez le Générateur d’expressions et affichez les données via l’onglet Aperçu des données.

## <a name="reconnect-rows-and-columns"></a>Reconnecter les lignes et les colonnes

Les transformations d’agrégation sont similaires aux requêtes SQL Aggregate Select. Les colonnes qui ne sont pas incluses dans la clause Regrouper par ou les fonctions d’agrégation ne sont pas transmises à la sortie de votre transformation d’agrégation. Si vous souhaitez inclure d’autres colonnes dans votre sortie agrégée, effectuez l’une des méthodes suivantes :

* Utilisez une fonction d’agrégation telle que `last()` ou `first()` pour inclure cette colonne supplémentaire.
* Rattachez les colonnes à votre flux de sortie à l’aide du [modèle de jointure réflexive](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exemples

L’exemple ci-dessous prend un flux entrant `MoviesYear` et regroupe les lignes par colonne `year`. La transformation crée une colonne d’agrégation `avgrating` qui correspond à la moyenne de la colonne `Rating`. Cette transformation d’agrégation est nommée `AvgComedyRatingsByYear`.

Dans l’expérience utilisateur Data Factory, cette transformation ressemble à l’image ci-dessous :

![Exemple Regrouper par](media/data-flow/agg-script1.png "Exemple Regrouper par")

![Exemple d’agrégat](media/data-flow/agg-script2.png "Exemple d’agrégat")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Étapes suivantes

* Définir l’agrégation en fonction d’une fenêtre à l’aide de la [transformation Fenêtre](data-flow-window.md)
