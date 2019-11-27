---
title: azcopy jobs show | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034136"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Affiche des informations détaillées sur l’ID d’un travail donné.

## <a name="synopsis"></a>Synopsis

Si l’ID du travail est fourni seul et sans indicateur, le résumé de progression du travail est retourné.

Le nombre d’octets et le pourcentage d’achèvement qui s’affichent lorsque vous exécutez cette commande reflètent uniquement les fichiers qui sont terminés dans le travail. Ils ne reflètent pas les fichiers partiellement terminés.

Si l’indicateur `with-status` est défini, la liste des transferts du travail avec la valeur donnée est affichée.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande show.|
|--with-status (chaîne)|Liste uniquement les transferts du travail avec cet état. Valeurs possibles : Started, Success, Failed|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
