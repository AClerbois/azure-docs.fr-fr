---
title: 'Réseau neuronal multiclasse : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Multiclass Neural Network (Réseau neuronal multiclasse) dans Azure Machine Learning service pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant plusieurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128601"
---
# <a name="multiclass-neural-network-module"></a>Module Multiclass Neural Network (Réseau neuronal à multiclasse)

Cet article décrit un module de l’interface visuelle (préversion) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant plusieurs valeurs. 

Par exemple, les réseaux neuronaux de ce type peuvent être utilisés dans des tâches complexes de vision par ordinateur, telles que la reconnaissance de chiffres ou de lettres, la classification de documents et la reconnaissance de formes.

La classification à l’aide de réseaux neuronaux est une méthode d’apprentissage supervisée. Ainsi, elle nécessite un *jeu de données avec balises*, qui inclut une colonne d’étiquette.

Vous pouvez effectuer l’apprentissage du modèle en fournissant un jeu de données avec balises et le modèle en tant qu’entrée pour [Train Model](./train-model.md) (Entraîner le modèle). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrées.  

## <a name="about-neural-networks"></a>À propos des réseaux neuronaux

Un réseau neuronal est un ensemble de couches connectées entre elles. Les entrées représentent la première couche et sont connectées à une couche de sortie par un graphique acyclique constitué de nœuds et de bords pondérés.

Entre les couches d’entrée et de sortie, vous pouvez insérer plusieurs couches masquées. La plupart des tâches prédictives peuvent être accomplies facilement avec une ou quelques couches masquées. Toutefois, de récentes recherches ont montré que les réseaux neuronaux profonds avec nombreuses couches peuvent être efficaces pour les tâches complexes telles que la reconnaissance vocale ou des images. Les couches successives sont utilisées pour modéliser des niveaux de profondeur sémantique toujours plus importants.

La relation entre les entrées et les sorties est tirée de la formation du réseau neuronal sur les données d’entrée. Le graphique commence par les entrées, se poursuit avec la couche masquée et s’achève avec la couche de sortie. Tous les nœuds d’une couche sont connectés par les bords pondérés aux nœuds de la couche suivante.

Pour calculer la sortie du réseau pour une entrée donnée, une valeur est calculée sur chaque nœud dans les couches masquées et dans la couche de sortie. La valeur est définie selon le calcul de la somme pondérée des valeurs des nœuds de la couche précédente. Une fonction d’activation est ensuite appliquée à cette somme pondérée.

## <a name="configure-multiclass-neural-network"></a>Configurer un réseau neuronal multiclasse

1. Ajoutez le module **MultiClass Neural Network** (Réseau neuronal multiclasse) à votre expérience dans l’interface. Vous pouvez trouver ce module sous **Machine Learning**, **Initialiser**, dans la catégorie **Classification**.

2. **Create trainer mode** (Créer un mode d’apprentissage) : Utilisez cette option pour spécifier comment vous voulez que le modèle soit entraîné :

    - **Single Parameter** (Paramètre unique) : choisissez cette option si vous savez déjà comment vous souhaitez configurer le modèle.

    

3. **Hidden layer specification** (Spécification de couche masquée) : Sélectionnez le type d’architecture de réseau à créer.

    - **Fully connected case** (Cas entièrement connecté) : Sélectionnez cette option pour créer un modèle à l’aide de l’architecture de réseau neuronal par défaut. Voici les valeurs par défaut pour les modèles de réseau neuronal multiclasse :

        - Une couche cachée
        - La couche de sortie est entièrement connectée à la couche masquée.
        - La couche masquée est entièrement connectée à la couche d’entrée.
        - Le nombre de nœuds dans la couche d’entrée est déterminé par le nombre de caractéristiques dans les données de formation.
        - Le nombre de nœuds dans la couche masquée peut être défini par l’utilisateur. La valeur par défaut est 100.
        - Le nombre de nœuds dans la couche de sortie dépend du nombre de classes.
  
   

5. **Number of hidden nodes** (Nombre de nœuds masqués) : cette option vous permet de personnaliser le nombre de nœuds masqués dans l’architecture par défaut. Entrez le nombre de nœuds masqués. La valeur par défaut est une couche masquée avec 100 nœuds.

6. **The learning rate** (Le taux d’apprentissage) : définissez la taille de l’étape franchie à chaque itération, avant correction. Avec une valeur de taux d’apprentissage supérieure, le modèle convergera peut-être plus rapidement, mais cette valeur peut dépasser les minima locaux.

7. **Number of learning iterations** (Nombre d’itérations d’apprentissage) : spécifiez le nombre maximal de fois où l’algorithme doit traiter les cas d’apprentissage.

8. **The initial learning weights diameter** (Le diamètre initial des pondérations d’apprentissage) : spécifiez les pondérations de nœud au début du processus d’apprentissage.

9. **The momentum** (La dynamique) : spécifiez une pondération à appliquer pendant l’apprentissage aux nœuds des itérations précédentes.
  
11. **Shuffle examples** (Réorganiser les exemples de façon aléatoire) : sélectionnez cette option pour réorganiser les cas entre les itérations de façon aléatoire.

    Si vous désélectionnez cette option, les cas sont traités exactement dans le même ordre chaque fois que vous exécutez l’expérience.

12. **Random number seed** (Valeur de départ aléatoire) : Entrez une valeur à utiliser comme valeur de départ, si vous souhaitez garantir la répétabilité entre les exécutions de la même expérience.

14. Connectez un jeu de données d’apprentissage à l’un des [modules de formation](module-reference.md) : 

    - Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez [Train Model](train-model.md) (Entraîner le modèle).  
  

## <a name="results"></a>Résultats

Une fois la formation terminée :

- Pour afficher un résumé des paramètres du modèle avec les pondérations de caractéristiques tirées de la formation et d’autres paramètres du réseau neuronal, cliquez avec le bouton droit sur la sortie du module [Train Model](./train-model.md) (Entraîner le modèle), puis sélectionnez **Visualiser**.  

- Pour enregistrer un instantané du modèle formé, cliquez avec le bouton droit sur la sortie du **modèle formé** et sélectionnez **Save As Trained Model** (Enregistrer en tant que modèle formé). Ce modèle n’est pas mis à jour lors des exécutions consécutives de la même expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 