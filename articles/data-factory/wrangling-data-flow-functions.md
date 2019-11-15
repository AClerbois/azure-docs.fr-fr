---
title: Fonctions de transformation pour flux de données de wrangling dans Azure Data Factory
description: Vue d’ensemble des fonctions de flux de données de wrangling disponibles dans Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3274641f7b118e13b3ed727f609ce7471fd66b54
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682283"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Fonctions de transformation dans le flux de données de wrangling

Le flux de données de wrangling dans Azure Data Factory vous permet d’effectuer un rassemblement de données brutes à l’analyse et une préparation agile de données sans code à l’échelle du cloud. Le flux de données de wrangling s’intègre à [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) et rend les fonctions Power Query M disponibles pour le rassemblement de données brutes à l’analyse via l’exécution Spark. 

Actuellement, toutes les fonctions Power Query M ne sont pas prises en charge pour le rassemblement de données brutes à l’analyse, bien qu’elles soient disponibles pendant la création. Lors de la génération de vos flux de données de wrangling, le message d’erreur suivant s’affiche si une fonction n’est pas prise en charge :

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Voici une liste des fonctions Power Query M prises en charge.

## <a name="column-management"></a>Gestion des colonnes

* Sélection : [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Suppression : [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Renommage : [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Réorganisation : [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrage de lignes

Utilisez la fonction M [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) pour filtrer avec les conditions suivantes :

* Égalité et inégalité
* Comparaisons numériques, de texte et de date (mais pas date/heure)
* Informations numériques, telles que [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Autonomie du texte avec [Text.Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith) ou [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Plages de dates, y compris toutes les [fonctions Date](https://docs.microsoft.com/powerquery-m/date-functions) « IsIn » 
* Combinaisons de celles-ci au moyen des conditions and, or ou not

## <a name="adding-and-transforming-columns"></a>Ajout et transformation de colonnes

Les fonctions M suivantes ajoutent ou transforment les colonnes : [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Voici ci-après les fonctions de transformation prises en charge.

* Arithmétique numérique
* Concaténation de texte
* Arithmétique Date et Heure (Opérateurs arithmétiques, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Les durées peuvent être utilisées pour les opérations arithmétiques de date et d’heure, mais doivent être transformées en un autre type avant d’être écrites dans un récepteur (opérateurs arithmétiques, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration.Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Duration.Minutes](https://docs.microsoft.com/powerquery-m/duration-minutes), [Duration.Seconds](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* La plupart des fonctions standard, scientifiques et numériques trigonométriques (Toutes les fonctions sous [Opérations](https://docs.microsoft.com/powerquery-m/number-functions#operations), [Arrondi](https://docs.microsoft.com/powerquery-m/number-functions#rounding) et [Trigonométrie](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *sauf* Number.Factorial, Number.Permutations et Number.Combinations)
* Remplacement ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Extraction de texte positionnel ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Mise en forme du texte de base ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Fonctions de date/heure ([Date.Day](https://docs.microsoft.com/powerquery-m/date-day), [Date.Month](https://docs.microsoft.com/powerquery-m/date-month), [Date.Year](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Time.Minute](https://docs.microsoft.com/powerquery-m/time-minute), [Time.Second](https://docs.microsoft.com/powerquery-m/time-second), [Date.DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Expressions if (mais les branches doivent avoir des types correspondants)
* Filtres de lignes en tant que colonne logique
* Constantes de nombre, de texte, de logique, de date et de date/heure

<a name="mergingjoining-tables"></a>Fusion/jointure de tables
----------------------
* Power Query génère une jointure imbriquée (table.NestedJoin ; les utilisateurs peuvent également écrire manuellement [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Les utilisateurs doivent alors développer la colonne de jointure imbriquée dans une jointure non imbriquée (aucune prise en charge de Table.ExpandTableColumn dans un autre contexte).
* La fonction M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) peut être écrite directement afin d’éviter la nécessité d’une étape d’expansion supplémentaire, mais l’utilisateur doit s’assurer qu’il n’existe aucun nom de colonne en double au sein des tables jointes
* Types de jointures pris en charge :   [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Prise en charge de [Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) et de [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) en tant que comparateurs d’égalité clés

## <a name="group-by"></a>Regrouper par

Utilisez [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) pour agréger des valeurs.
* Doit être utilisé avec une fonction d’agrégation
* Fonctions d’agrégation prises en charge :   [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Tri

Utilisez [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) pour trier les valeurs.

## <a name="reducing-rows"></a>Réduction de lignes

Conserver et supprimer les premiers éléments, Conserver la plage (fonctions M correspondantes, qui prennent uniquement en charge les nombres, pas les conditions : [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Fonctionnalité connue non prise en charge

Les fonctions qui ne sont pas prises en charge sont répertoriées ici. Cette liste n’est pas exhaustive et peut faire l’objet de modifications.
* Fusionner les colonnes (peut être obtenu avec AddColumn)
* Diviser la colonne
* Ajouter des requêtes
* « Utiliser la première ligne comme en-tête » et « Utiliser les en-têtes comme première ligne »

## <a name="next-steps"></a>Étapes suivantes
