---
title: Transformation de colonne dérivée dans le flux de données de mappage Azure Data Factory | Microsoft Docs
description: Découvrez comment transformer des données à l’échelle dans Azure Data Factory avec la transformation de colonne dérivée du mappage Data Flow.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 60451fa6152590ed0fde51be436c867f39906acf
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514817"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformation de colonne dérivée dans le mappage Data Flow

Utilisez la transformation de colonne dérivée pour générer de nouvelles colonnes dans votre flux de données ou pour modifier des champs existants.

## <a name="derived-column-settings"></a>Paramètres de la colonne dérivée

Pour remplacer une colonne existante, sélectionnez-la via le menu déroulant Colonne. Sinon, utilisez le champ de sélection de colonne en tant que zone de texte et tapez un nouveau nom pour votre colonne. Pour générer l’expression de la colonne dérivée, cliquez sur la zone « Entrez expression » pour ouvrir le [Générateur d’expressions Data Flow](concepts-data-flow-expression-builder.md).

![Paramètres de la colonne dérivée](media/data-flow/dc1.png "Paramètres de la colonne dérivée")

Pour ajouter des colonnes dérivées, pointez sur une colonne dérivée existante et cliquez sur l’icône plus. Choisissez **Ajouter une colonne** ou **Ajouter un modèle de colonne**. Les modèles de colonnes peuvent s’avérer utiles si le nom de vos colonnes est variable selon les sources. Pour plus d’informations, voir [Modèles de colonnes](concepts-data-flow-column-pattern.md).

![Nouvelle sélection de colonne dérivée](media/data-flow/columnpattern.png "Nouvelle sélection de colonne dérivée")

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exemples

L’exemple ci-dessous est une colonne dérivée nommée `CleanData` qui prend un flux entrant `MoviesYear` et crée deux colonnes dérivées. La première colonne dérivée remplace la colonne `Rating` par la valeur de l’évaluation en tant que type entier. La deuxième colonne dérivée est un modèle qui correspond à chaque colonne dont le nom commence par « movies ». Pour chaque colonne correspondante, il crée une colonne `movie` qui est égale à la valeur de la colonne correspondante avec le préfixe « movie_ ». 

Dans l’expérience utilisateur Data Factory, cette transformation ressemble à l’image ci-dessous :

![Exemple de dérivation](media/data-flow/derive-script1.png "Exemple de dérivation")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [langage d’expression de mappage Data Flow](data-flow-expression-functions.md).
