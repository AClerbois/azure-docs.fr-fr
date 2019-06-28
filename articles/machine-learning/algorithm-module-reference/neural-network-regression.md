---
title: 'Régression de réseau neuronal : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Régression de réseau neuronal dans Azure Machine Learning service pour créer un modèle de régression à l’aide d’un algorithme de réseau neuronal personnalisable.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029309"
---
# <a name="neural-network-regression-module"></a>Module Régression de réseau neuronal

*Crée un modèle de régression à l’aide d’un algorithme de réseau neuronal*  
  
 Catégorie : Machine Learning / Initialiser le modèle / Régression
  
## <a name="module-overview"></a>Vue d’ensemble du module  

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour créer un modèle de régression à l’aide d’un algorithme de réseau neuronal personnalisable.
  
 Bien que les réseaux neuronaux soient largement connus pour une utilisation dans le Deep Learning et la modélisation de problèmes complexes tels que la reconnaissance d’image, ils s’adaptent facilement aux problèmes de régression. Une classe de modèles statistiques peut être appelée un réseau neuronal si elle utilise des pondérations adaptatives et est en mesure d’estimer des fonctions non linéaires de leurs entrées. La régression de réseau neuronal convient ainsi aux problèmes pour lesquels un modèle de régression plus traditionnel ne parvient pas à trouver de solution.
  
 La régression de réseau neuronal est une méthode d’apprentissage supervisée. Ainsi, elle nécessite un *jeu de données avec balises*, qui inclut une colonne d’étiquette. Dans la mesure où un modèle de régression prédit une valeur numérique, la colonne d’étiquette doit être un type de données numériques.  
  
 Vous pouvez effectuer l’apprentissage du modèle en fournissant un jeu de données avec balises et le modèle en tant qu’entrée pour [Train Model](./train-model.md) (Entraîner le modèle). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrées.  
  
## <a name="configure-neural-network-regression"></a>Configurer la régression de réseau neuronal 

Les réseaux neuronaux peuvent être hautement personnalisés. Cette section décrit la création d’un modèle selon deux méthodes :
  
+ [Créer un modèle de réseau neuronal à l’aide de l’architecture par défaut](#bkmk_DefaultArchitecture)  
  
    Si vous acceptez l’architecture de réseau neuronal par défaut, utilisez le volet **Propriétés** pour définir les paramètres qui contrôlent le comportement du réseau neuronal, comme le nombre de nœuds dans la couche masquée, le taux d’apprentissage et la normalisation.

    Commencez ici si vous ne connaissez pas encore les réseaux neuronaux. Le module prend en charge de nombreuses personnalisations, ainsi que le réglage de modèle, sans une connaissance approfondie des réseaux neuronaux. 

+ Définir une architecture personnalisée pour un réseau neuronal 

    Utilisez cette option si vous souhaitez ajouter des couches masquées supplémentaires ou personnaliser entièrement l’architecture du réseau, ses connexions et fonctions d’activation.
    
    Cette option est recommandée si vous connaissez déjà les réseaux neuronaux. Vous utilisez le langage Net# pour définir l’architecture réseau.  

##  <a name="bkmk_DefaultArchitecture"></a> Créer un modèle de réseau neuronal à l’aide de l’architecture par défaut
  
1.  Ajoutez le module **Régression de réseau neuronal** à votre expérience dans l’interface. Vous le trouverez sous **Machine Learning**, **Initialiser**, dans la catégorie **Régression**. 
  
2. Indiquez le mode d’apprentissage du modèle en définissant l’option **Create trainer mode** (Créer un mode d’apprentissage).  
  
    -   **Single Parameter** (Paramètre unique) : choisissez cette option si vous savez déjà comment vous souhaitez configurer le modèle.  

3.  Dans **Hidden layer specification** (Spécification de couche masquée), sélectionnez **Fully connected case** (Cas entièrement connecté). Cette option crée un modèle à l’aide de l’architecture réseau neuronal par défaut qui, pour un modèle de régression de réseau neuronal, comporte les attributs suivants :  
  
    + Le réseau a exactement une seule couche masquée.
    + La couche de sortie est entièrement connectée à la couche masquée, et la couche masquée est entièrement connectée à la couche d’entrée.
    + Le nombre de nœuds dans la couche masquée peut être défini par l’utilisateur (valeur par défaut de 100).  
  
    Étant donné que le nombre de nœuds dans la couche d’entrée est déterminé par le nombre de fonctionnalités dans les données d’apprentissage, un modèle de régression ne peut comporter qu’un seul nœud dans la couche de sortie.  
  
4. Pour **Number of hidden nodes** (Nombre de nœuds masqués), saisissez le nombre de nœuds masqués. La valeur par défaut est une couche masquée avec 100 nœuds. (Cette option n’est pas disponible si vous définissez une architecture personnalisée à l’aide de Net#.)
  
5.  Pour **Learning rate** (Taux d’apprentissage), saisissez une valeur qui définit l’étape effectuée à chaque itération, avant correction. Avec une valeur de taux d’apprentissage supérieure, le modèle convergera peut-être plus rapidement, mais cette valeur peut dépasser les minima locaux.

6.  Pour **Number of learning iterations** (Nombre d’itérations d’apprentissage), spécifiez le nombre maximal de fois où l’algorithme traite les cas d’apprentissage.

7.  Pour **The initial learning weights diameter** (Le diamètre initial des pondérations d’apprentissage), saisissez une valeur qui détermine les pondérations de nœud au début du processus d’apprentissage.

8.  Pour **The momentum** (La dynamique), saisissez une valeur à appliquer pendant l’apprentissage sous forme de pondération sur les nœuds des itérations précédentes.

10. Sélectionnez l’option **Shuffle examples** (Exemples de lecture aléatoire) pour modifier l’ordre des cas entre les itérations. Si vous désélectionnez cette option, les cas sont traités exactement dans le même ordre chaque fois que vous exécutez l’expérience.
  
11. Pour **Valeur de départ numérique aléatoire**, vous pouvez éventuellement saisir une valeur initiale à utiliser. La spécification d’une valeur initiale est utile lorsque vous souhaitez garantir la répétabilité entre les exécutions de la même expérience.
  
13. Connectez un jeu de données d’apprentissage à l’un des [modules de formation](module-reference.md) : 
  
    -   Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez [Train Model](./train-model.md) (Entraîner le modèle).  
  
   
14. Exécutez l’expérience.  

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle avec les pondérations de caractéristiques tirées de la formation et d’autres paramètres du réseau neuronal, cliquez avec le bouton droit sur la sortie du module [Train Model](./train-model.md) (Entraîner le modèle), puis sélectionnez **Visualiser**.  

+ Pour enregistrer un instantané du modèle formé, cliquez avec le bouton droit sur la sortie du **modèle formé** et sélectionnez **Save As Trained Model** (Enregistrer en tant que modèle formé). Ce modèle n’est pas mis à jour lors des exécutions consécutives de la même expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez l’[ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 