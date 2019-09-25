---
title: Apprentissage profond et apprentissage automatique
titleSuffix: Azure Machine Learning
description: Apprenez-en davantage sur le Deep Learning par rapport au Machine Learning et la façon dont les deux concepts se rapportent à l’intelligence artificielle. Le Deep Learning peut être mis en œuvre dans des scénarios de détection de fraude, de reconnaissances vocale et faciale, d’analyse des sentiments et de prévision de série chronologique.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: cff31916f837141ae54f3c14dd125be6a92a5008
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035502"
---
# <a name="deep-learning-vs-machine-learning"></a>Apprentissage profond et apprentissage automatique

Cet article vous aide à comparer le Deep Learning et le Machine Learning. Il compare les deux concepts et explique comment ils s’inscrivent dans la catégorie plus large de l’intelligence artificielle. Enfin, il explique également comment le Deep Learning peut s’appliquer à des scénarios du monde réel, tels que la détection de fraude, les reconnaissances vocale et faciale, l’analyse des sentiments et la prévision de série chronologique.

## <a name="deep-learning-machine-learning-and-ai"></a>Deep Learning, Machine Learning et intelligence artificielle

![Diagramme des relations : Intelligence artificielle, Machine Learning et Deep Learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Consultez les définitions suivantes pour comprendre la différence entre le deep learning, le machine learning et l’IA :

- Le **Deep Learning**, ou apprentissage profond, est un sous-ensemble du Machine Learning, ou apprentissage automatique, basé sur des réseaux neuronaux artificiels. Le _processus d’apprentissage_ est qualifié de _profond_ parce que la structure des réseaux neuronaux artificiels se compose de plusieurs couches d’entrée, de sortie et masquées. Chaque couche contient des unités qui transforment les données d’entrée en informations que la couche suivante peut utiliser une tâche prédictive spécifique. Grâce à cette structure, une machine est capable d’apprendre au travers de son propre traitement de données.

- Le **Machine Learning** est un sous-ensemble de l’intelligence artificielle utilisant des techniques (telles que le Deep Learning), qui permettent aux machines de tirer des enseignements de leur expérience pour améliorer la manière dont elles exécutent leur tâches. Le _processus d’apprentissage_ repose sur les étapes suivantes :

   1. Alimenter un algorithme en données (au cours de cette étape, vous pouvez fournir des informations supplémentaires au modèle, par exemple, en effectuant l’extraction de caractéristiques).
   1. Utilisez ces données pour entraîner un modèle.
   1. Tester et déployer le modèle.
   1. Utiliser le modèle déployé pour effectuer une tâche prédictive automatisée (en d’autres termes, appeler et utiliser le modèle déployé pour recevoir les prédictions retournées par le modèle).

- L’**intelligence artificielle** est une technique qui permet aux ordinateurs d’imiter l’intelligence humaine. Cette technique inclut le Machine Learning. 
 
Il est important de comprendre les relations entre l’intelligence artificielle, le Machine Learning et le Deep Learning. Le Machine Learning est un moyen d’aboutir à l’intelligence artificielle. Les techniques de Machine Learning et de Deep Learning vous permettent de créer des systèmes informatiques et des applications qui effectuent des tâches généralement associées à l’intelligence humaine. Ces tâches incluent la perception visuelle, la reconnaissance vocale, la prise de décision et la traduction de langue.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniques de Deep Learning et de Machine Learning 

À présent que vous comprenez les concepts de Machine Learning et de Deep Learning, comparons les deux techniques. Dans le Machine Learning, l’algorithme doit être informé de la façon d’effectuer une prédiction précise en utilisant plus d’informations (par exemple, en effectuant une extraction de caractéristiques). Dans le Deep Learning, l’algorithme peut apprendre à effectuer une prédiction précise par le biais de son propre traitement de données, grâce à la structure du réseau neuronal artificiel.

Le tableau suivant compare les deux techniques de manière plus détaillée :

| |Machine Learning dans sa globalité |Uniquement le Deep Learning|
|---|---|---|
|  **Nombre de points de données** | Peut utiliser de petites quantités de données fournies par les utilisateurs. | Requiert une grande quantité de données d’apprentissage pour en tirer des conclusions concises. |
|  **Dépendances matérielles** | Peut fonctionner sur des machines bas de gamme. Ne nécessite pas beaucoup de puissance de calcul. | Nécessite des machines haut de gamme. Effectue fondamentalement un grand nombre d’opérations de multiplication de matrices. Un GPU peut optimiser efficacement ces opérations. |
|  **Processus de personnalisation** | Nécessite que les caractéristiques soient identifiées et créées avec précision par les utilisateurs. | Apprend des caractéristiques de haut niveau à partir de données, et crée de nouvelles caractéristiques de façon autonome. |
|  **Approche d’apprentissage** | Fractionne les tâches en petits morceaux, puis combine les résultats reçus dans une conclusion. | Résout le problème de bout en bout. |
|  **Temps d’exécution** | Nécessite relativement peu de temps pour apprendre, de quelques secondes à quelques heures. | Nécessite un temps d’apprentissage anormalement long, car un algorithme d’apprentissage profond implique de nombreux paramètres. |
|  **Sortie** | La sortie est généralement une valeur numérique, telle qu’une note ou une classification. | La sortie peut être un texte, une note, un élément ou un son. |

## <a name="deep-learning-use-cases"></a>Cas d’utilisation du Deep Learning

En raison de sa structure de réseau neuronal artificiel, le Deep Learning excelle dans l’identification de modèles dans des données non structurées telles que des images, du son, de la vidéo et du texte. Pour cette raison, le Deep Learning transforme rapidement de nombreux secteurs, donc ceux de la santé, de l’énergie, des finances et des transports. Ces secteurs repensent actuellement leurs processus métier traditionnels. 

Certaines applications courantes du Deep Learning sont décrites dans les paragraphes suivants.

### <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée

L’une des utilisations des réseaux de Deep Learning est la reconnaissance d’entité nommée, qui permet d’extraire certains types d’informations de données non structurées et non étiquetées. Ces informations peuvent être des personnes, des lieux, des sociétés ou des objets. Elles peuvent ensuite être stockées dans un schéma structuré afin d’établir une liste d’adresses ou de servir de référence pour un moteur de validation d’identité.

### <a name="object-detection"></a>Détection d’objets

le Deep Learning est souvent utilisé pour la détection d’objets. La détection d’objet s’opère en deux temps : classification d’image, puis localisation d’image. La _classification_ d’image identifie les objets figurant sur l’image, tels que des voitures ou des personnes. La _localisation_ d’image fournit l’emplacement spécifique de ces objets. 

La détection d’objets est déjà utilisée dans les secteurs du jeu, de la distribution, du tourisme et des véhicules autonomes.

### <a name="image-caption-generation"></a>Génération de légende d’image

À l’instar de la reconnaissance d’image, la génération de légende d’image consiste à générer une légende décrivant le contenu d’une image. Lorsque vous pouvez détecter et étiqueter des objets dans des photographies, l’étape suivante consiste à convertir ces étiquettes en phrases descriptives et cohérentes. Généralement, les systèmes de génération de légende d’image utilisent de très grands réseaux neuronaux convolutifs pour la détection d’objets sur des photographies, puis un réseau neuronal récurrent (RNN) pour convertir les étiquettes en phrases cohérentes.

### <a name="machine-translation"></a>Traduction automatique

La traduction automatique consiste à traduire des mots, des phrases ou des expressions d’une langue dans une autre langue. La traduction automatique existe depuis longtemps, mais le Deep Learning produit des résultats impressionnants dans deux domaines spécifiques : la traduction automatique de texte (et la reconnaissance vocale) et la traduction automatique d’images. 

Avec une conversion appropriée des données, un réseau neuronal profond est capable de comprendre du texte, du son et des signaux visuels. La traduction automatique permet d’identifier des extraits de son dans des fichiers audio volumineux, ainsi que de transcrire de la parole ou des images en texte.

### <a name="text-analytics"></a>Analyse de texte

L’une des tâches importantes du Deep Learning est la découverte électronique. Les entreprises utilisent l’analytique de texte basée sur le Deep Learning pour détecter les échanges internes et la conformité aux réglementations gouvernementales. Des fonds spéculatifs utilisent l’analytique de texte pour explorer de vastes référentiels de documents afin de recueillir des informations sur les performances futures de leurs investissements et les tendances du marché. Le cas d’utilisation de l’analyse de texte basée sur le Deep Learning consiste à traiter des quantités considérables de données textuelles ainsi qu’à produire des agrégations.

## <a name="artificial-neural-networks"></a>Réseaux neuronaux artificiels

Les réseaux neuronaux artificiels sont formés par couches de nœuds connectés. Les modèles de Deep Learning utilisent des réseaux neuronaux comportant grand nombre de couches. 

Les sections suivantes explorent la plupart des typologies de réseau neuronal artificiel les plus populaires.

### <a name="feedforward-neural-network"></a>Réseau neuronal feedforward

Le réseau neuronal feedforward est le type de réseau neuronal artificiel le plus basique. Dans un réseau feedforward, les informations circulent dans une seule direction, de la couche d’entrée vers la couche de sortie. Les réseaux neuronaux feedforward transforment une entrée en la faisant passer par une série de couches masquées. Chaque couche est constituée d’un ensemble de neurones, et est entièrement connectée à tous les neurones de la couche précédente. La dernière couche entièrement connectée (couche de sortie) représente les prédictions générées.

### <a name="recurrent-neural-network"></a>Réseau de neurones récurrents

Les réseaux neuronaux récurrents sont un type de réseau neuronal artificiel largement utilisé. Ces réseaux enregistrent la sortie d’une couche et la renvoient à la couche d’entrée pour aider à prédire le résultat de la couche. Les réseaux neuronaux récurrents ont des capacités d’apprentissage exceptionnelles. Ils sont largement utilisés pour accomplir des tâches complexes telles que l’apprentissage d’écriture manuscrite et la reconnaissance du langage.

### <a name="convolutional-neural-networks"></a>Réseaux neuronaux convolutifs

Un réseau neuronal convolutif est un réseau neuronal artificiel particulièrement efficace qui présente une architecture unique. Les couches sont organisées en trois dimensions : largeur, hauteur et profondeur. Les neurones d’une couche ne sont pas connectés à tous les neurones de la couche suivante, mais uniquement à une petite région de neurones de celle-ci. Le résultat final est réduit à un seul vecteur de notes de probabilité, organisées dans la dimension de la profondeur. 

Les réseaux neuronaux convolutifs sont utilisés dans des domaines tels que la reconnaissance et la classification d’images.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants expliquent comment utiliser la technologie du deep learning dans [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri) :

- [Classifier des chiffres manuscrits à l’aide d’un modèle TensorFlow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Classifier des chiffres manuscrits à l’aide d’un estimateur TensorFlow et de Keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Classifier des images à l’aide d’un modèle Pytorch](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Classifier des chiffres manuscrits à l’aide d’un modèle Chainer](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
