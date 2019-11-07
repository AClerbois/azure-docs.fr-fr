---
title: Déboguer votre modèle
titleSuffix: Azure Machine Learning Studio (classic)
description: Comment déboguer les erreurs produites par les modules Entraîner le modèle et Noter le modèle dans Azure Machine Learning Studio (classique).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 053bb75bb7beea86215397bdfd81a1dbc9d1bcb9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493059"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Déboguer votre modèle dans Azure Machine Learning Studio (classique)

Lors de l’exécution d’un modèle, vous pouvez rencontrer les erreurs suivantes :

* le module [Former le modèle][train-model] génère une erreur ; 
* le module [Noter le modèle][score-model] produit des résultats incorrects. 

Cet article explique les causes possibles de ces erreurs.


## <a name="train-model-module-produces-an-error"></a>le module Former le modèle génère une erreur ;

![image1](./media/debug-models/train_model-1.png)

Le module [Former le modèle][train-model] attend deux entrées :

1. Le type de modèle Machine Learning issu de la collection de modèles fournie par Azure Machine Learning Studio (classique).
2. Les données d’apprentissage avec une colonne Étiquette spécifiée qui indique la variable à prédire (les autres colonnes sont supposées être des fonctionnalités).

Ce module peut générer une erreur dans les cas suivants :

1. La colonne Étiquette est spécifiée de manière incorrecte. Cela peut se produire si plusieurs colonnes sont sélectionnées en tant qu’étiquette, ou un index de colonne incorrect a été sélectionné. Par exemple, le second cas s’applique si un index de colonne de 30 est utilisé avec un jeu de données d’entrée qui ne comporte que 25 colonnes.

2. Le jeu de données ne contient aucune colonne Fonctionnalité. Par exemple, si le jeu de données d’entrée ne comporte qu’une seule colonne, identifiée en tant que colonne Étiquette, il n’existe aucune fonctionnalité avec laquelle générer le modèle. Dans ce cas, le module [Former le modèle][train-model] génère une erreur.

3. Le jeu de données d’entrée (Fonctionnalités ou Étiquette) contient une valeur Infini.

## <a name="score-model-module-produces-incorrect-results"></a>Le module Noter le modèle produit des résultats incorrects

![image2](./media/debug-models/train_test-2.png)

Dans une expérience d’apprentissage/de test classique pour un apprentissage supervisé, le module [Fractionner les données][split] divise le jeu de données en deux parties : une partie utilisée pour effectuer l’apprentissage du modèle et une partie est utilisée pour noter l’efficacité du comportement du modèle formé. Le modèle formé est ensuite utilisé pour noter les données de test, après quoi les résultats sont évalués afin de déterminer la précision du modèle.

Le module [Noter le modèle][score-model] nécessite deux entrées :

1. Une sortie du modèle formé à partir du module [Former le modèle][train-model].
2. Un jeu de données de notation différent du jeu de données utilisé pour former le modèle.

Même si l’expérience réussit, il est possible que le module [Noter le modèle][score-model] produise des résultats incorrects. Plusieurs scénarios peuvent donner lieu aux situations suivantes :

1. Si l’étiquette spécifiée est catégorielle et qu’un modèle de régression est formé sur les données, le module [Noter le modèle][score-model] produit une sortie incorrecte. Ceci est dû au fait que la régression requiert une variable dépendante continue. Dans ce cas, il est plus judicieux d’utiliser un modèle de classification. 

2. De la même façon, si un modèle de classification est formé sur un jeu de données comportant des nombres à virgule flottante dans la colonne Étiquette, il peut produire des résultats indésirables. Ceci s’explique par le fait que la classification requiert une variable dépendante discontinue qui autorise uniquement les valeurs couvrant un ensemble de classes fini et restreint.

3. Si le jeu de données de notation ne contient pas toutes les fonctionnalités utilisées pour effectuer l’apprentissage du modèle, le module [Noter le modèle][score-model] génère une erreur.

4. Si une ligne du jeu de données de notation présente une valeur manquante ou infinie pour l’une de ses fonctionnalités, le module [Noter le modèle][score-model] ne produit aucune sortie correspondant à cette ligne.

5. Le module [Noter le modèle][score-model] peut produire des sorties identiques pour toutes les lignes du jeu de données de notation. Ceci peut notamment se produire lors d’une tentative de classification à l’aide de forêts d’arbres de décision si le nombre minimal d’échantillons par nœud terminal est défini comme étant supérieur au nombre d’exemples d’apprentissage disponibles.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

