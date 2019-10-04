---
title: 'Forêt d’arbres décisionnels à deux classes : Référence du module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Forêt d’arbres décisionnels à deux classes dans Azure Machine Learning service pour créer un modèle Machine Learning basé sur l’algorithme de forêt d’arbres décisionnels.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37a2ce77e438145219df9cb553d1881626e8a2c6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128395"
---
# <a name="two-class-decision-forest-module"></a>Module Forêt d’arbres décisionnels à deux classes

Cet article décrit l’un des modules de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour créer un modèle Machine Learning basé sur l’algorithme de forêt d’arbres décisionnels.  

Les forêts d’arbres décisionnels sont des modèles ensemblistes rapides et supervisés. Ce module est une bonne solution pour prédire une cible présentant un maximum de deux résultats. 

## <a name="understanding-decision-forests"></a>Bien comprendre les forêts d’arbres décisionnels

L’algorithme de forêt d’arbres décisionnels est une méthode d’apprentissage ensembliste destinée à des tâches de classification. Les méthodes ensemblistes sont fondées sur le principe général suivant : plutôt que de s’appuyer sur un modèle unique, on peut obtenir de meilleurs résultats et un modèle plus généralisé en créant plusieurs modèles liés et en les combinant d’une certaine façon. En général, les modèles ensemblistes offrent une meilleure couverture et une précision plus élevée que les arbres de décision uniques. 

Il existe de nombreuses façons de créer des modèles individuels et de les combiner pour former un ensemble. Cette implémentation particulière de la forêt d’arbres décisionnels consiste à créer plusieurs arbres de décision, puis à **voter** pour sélectionner la classe de sortie la plus populaire. Le vote est l’un des moyens les plus connus de générer des résultats dans un modèle ensembliste. 

+ De nombreux arbres de classification individuels sont créés, avec l’ensemble du jeu de données, mais différents points de départ (généralement randomisés). Cette approche est différente de celle de la forêt aléatoire, selon laquelle les arbres de décision individuels peuvent n’utiliser qu’une partie randomisée des données ou des fonctionnalités.
+ Chacun des arbres de la forêt d’arbres décisionnels donne en sortie un histogramme à fréquence non normalisée d’étiquettes. 
+ Le processus d’agrégation additionne ces histogrammes et normalise le résultat pour obtenir les « probabilités » de chaque étiquette. 
+ Les arbres présentant un niveau de confiance élevé en matière de prédiction pèsent davantage dans la décision finale de l’ensemble.

Les arbres de décision présentent en général de nombreux avantages pour les tâches de classification :
  
- Ils peuvent capturer des limites de décisions non linéaires.
- Il est possible d’effectuer un apprentissage et d’établir des prédictions sur de gros volumes de données, car ils sont efficaces dans l’utilisation du processeur et de la mémoire.
- La sélection de fonctionnalités est intégrée aux processus d’apprentissage et de classification.  
- Les arbres peuvent prendre en charge des données bruitées et de nombreuses fonctionnalités.  
- Ce sont des modèles non paramétriques, ce qui signifie qu’ils peuvent gérer des données présentant des distributions variées. 

Toutefois, les arbres de décision simples présentent un risque de surajustement par rapport aux données et sont moins généralisables que les ensembles d’arbres.

Pour plus d’informations, voir [Arbres de décision](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Comment configurer
  
1.  Ajoutez le module **Forêt d’arbres décisionnels à deux classes** à votre expérience dans Azure Machine Learning, puis ouvrez le volet **Propriétés** du module. 

    Vous trouverez le module sous **Machine Learning**. Développez **Initialiser**, puis **Classification**.  
  
2.  Pour **Méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer les arbres individuels :  **Bagging** ou **Réplication**.  
  
    -   **Bagging** : le bagging, également appelé *agrégation boostrap*, est une méthode selon laquelle chaque arbre est développé sur un nouvel échantillon, créé en échantillonnant le jeu de données d’origine de manière aléatoire à l’aide d’un ensemble de remplacement jusqu’à obtenir un jeu de données de taille identique à l’original.  
  
         Les sorties des modèles sont combinées grâce à un *vote* qui est une forme d’agrégation. Chacun des arbres de la forêt d’arbres décisionnels de classification donne en sortie un histogramme à fréquence non normalisée d’étiquettes. L’agrégation consiste à additionner et à normaliser ces histogrammes pour obtenir les « probabilités » de chaque étiquette. Ainsi, les arbres présentant un niveau de confiance élevé en matière de prédiction pèseront davantage dans la décision finale de l’ensemble.  
  
         Pour plus d’informations, voir l’entrée Wikipedia Bootstrap aggregating (en anglais).  
  
    -   **Réplication** : avec la réplication, l’apprentissage de chacun des arbres est effectué sur les mêmes données d’entrée. Le prédicat fractionné utilisé pour chaque nœud d’arbre est toujours déterminé de manière aléatoire ; les arbres sont variés.   
  
3.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.  
  
    -   **Paramètre unique** : si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.
  
4.  Dans le champ **Nombre d’arbres de décision**, saisissez le nombre maximal d’arbres de décision qui peuvent être créés dans l’ensemble. Plus il y en a, meilleure peut être la couverture, mais plus le temps d’apprentissage augmente.  
  
    > [!NOTE]
    >  Cette valeur contrôle également le nombre d’arbres qui s’affichent dans la visualisation du modèle entraîné. Si vous souhaitez voir ou imprimer un seul arbre, vous pouvez définir la valeur sur 1. Toutefois, un seul arbre sera produit (l’arbre avec le jeu de paramètres initial) sans qu’aucune autre itération soit effectuée.
  
5.  Dans le champ **Profondeur maximale des arbres de décision**, tapez un nombre pour limiter la profondeur de chacun des arbres de décision. Le fait d’augmenter la profondeur de l’arbre est susceptible d’améliorer la précision, au prix d’un risque de surajustement et d’augmentation du temps d’apprentissage.
  
6.  Dans le champ **Nombre de fractionnements aléatoires par nœud**, tapez le nombre de fractionnements à utiliser pour créer chacun des nœuds de l’arbre. Le *fractionnement* consiste à diviser de manière aléatoire les fonctionnalités à chaque niveau de l’arbre (nœud).
  
7.  Dans le champ **Nombre minimal d’échantillons par nœud terminal**, indiquez le nombre minimal de cas requis pour la création d’un nœud terminal dans un arbre.
  
     Plus cette valeur est grande, plus le seuil de création de règles augmente. Par exemple, avec la valeur par défaut, 1, un seul cas suffit à entraîner la création d’une règle. Si la valeur passe à 5, les données d’apprentissage doivent contenir au moins cinq cas remplissant les mêmes conditions.  
  
8.  Sélectionnez l’option **Autoriser les valeurs inconnues pour les fonctionnalités catégorielles** afin de créer un groupe pour les valeurs inconnues des jeux d’apprentissage ou de validation. Le modèle risque d’être moins précis pour les valeurs connues, mais il pourra fournir de meilleures prédictions pour les nouvelles valeurs (inconnues). 

     Si cette option est désélectionnée, le modèle n’accepte que les valeurs contenues dans les données d’apprentissage.
  
9. Joignez un jeu de données étiqueté et l’un des [modules d’apprentissage](module-reference.md) :  
  
    -   Si vous définissez **Créer un mode d’apprentissage** sur **Paramètre unique**, utilisez le module [Entraîner le du modèle](./train-model.md).  
  
    
## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour afficher l’arbre créé pour chaque itération, cliquez avec le bouton droit sur la sortie du module [Entraîner le modèle](./train-model.md) et sélectionnez **Visualiser**.
  
    Cliquez sur chaque arbre pour explorer les fractionnements et afficher les règles de chaque nœud.

+ Pour enregistrer un instantané du modèle, cliquez avec le bouton droit sur la sortie **Modèle entraîné** et sélectionnez **Enregistrer le modèle**. Le modèle enregistré n’est pas mis à jour lors des différentes exécutions de l’expérience.

+ Pour utiliser le modèle à des fins de scoring, ajoutez le module **Noter le modèle** à une expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 