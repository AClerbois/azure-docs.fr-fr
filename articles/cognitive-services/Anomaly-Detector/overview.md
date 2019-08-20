---
title: Présentation de l’API Détecteur d’anomalies
titleSuffix: Azure Cognitive Services
description: Utiliser les algorithmes avancés de l’API Détecteur d’anomalies pour identifier des anomalies dans vos données de série chronologique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 70cb4a008391ad55562bc55fb45d877988e68643
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854770"
---
# <a name="what-is-the-anomaly-detector-api"></a>Présentation de l’API Détecteur d’anomalies

L’API Détecteur d’anomalies vous permet de surveiller et de détecter des anomalies dans vos données de série chronologique grâce à l’apprentissage automatique. L’API Détecteur d’anomalies s’adapte en identifiant et en appliquant automatiquement les modèles les mieux adaptés à vos données, indépendamment du secteur d’activité, du scénario ou du volume de données. À l’aide de vos données de série chronologique, l’API détermine les limites pour la détection des anomalies, les valeurs attendues et les points de données qui constituent des anomalies.

![Détecter des modifications de modèle dans les demandes de service](./media/anomaly_detection2.png)

L’utilisation du détecteur d'anomalies ne nécessite aucune expérience préalable de l’apprentissage automatique et l’API RESTful permet d’intégrer facilement le service dans vos applications et processus.

## <a name="features"></a>Caractéristiques

Grâce au détecteur d’anomalies, vous pouvez automatiquement détecter des anomalies, dans l’ensemble de vos données de série chronologique ou en temps réel. 

|Fonctionnalité  |Description  |
|---------|---------|
|Détecter les anomalies en temps réel. | Détectez les anomalies dans vos données de diffusion en continu à l’aide des points de données préalablement vus pour déterminer si le dernier point est une anomalie. Cette opération génère un modèle à l’aide des points de données que vous envoyez et détermine si le point cible est une anomalie. En appelant l’API avec chaque nouveau point de données que vous générez, vous pouvez surveiller vos données au moment de leur création. |
|Détecter les anomalies tout au long de votre jeu de données par lots. | Utilisez votre série chronologique pour détecter d’éventuelles anomalies dans l’ensemble de vos données. Cette opération génère un modèle à l’aide de vos données de série chronologique complètes, chaque point étant analysé avec le même modèle.         |
| Obtenir des informations supplémentaires sur vos données. | Obtenez des détails utiles sur vos données et sur les anomalies constatées, notamment les valeurs attendues ainsi que les limites et les positions des anomalies. |
| Ajuster les limites de détection des anomalies. | L’API Détecteur d’anomalies crée automatiquement des limites pour la détection des anomalies. Ajustez ces limites pour augmenter ou diminuer la sensibilité de l’API aux anomalies de données et mieux l’adapter à vos données. |

## <a name="demo"></a>Démonstration

Consultez cette [démonstration interactive](https://aka.ms/adDemo) pour comprendre le fonctionnement du détecteur d’anomalies.
Pour exécuter la démonstration, vous devez créer une ressource Détecteur d’anomalies et récupérer la clé API ainsi que le point de terminaison.

## <a name="notebook"></a>Bloc-notes

Pour savoir comment appeler l’API Détecteur d’anomalies, essayez ce [notebook Azure](https://aka.ms/adNotebook). Ce notebook Jupyter hébergé sur le web vous montre comment envoyer une demande d’API et visualiser le résultat.

Pour exécuter le notebook, procédez comme suit :

1. Obtenez une clé d’abonnement valide pour l’API Détecteur d’anomalies et un point de terminaison d’API. La section ci-dessous donne les instructions pour s’inscrire.
1. Connectez-vous, puis cliquez sur Cloner en haut à droite.
1. Désactivez l’option « public » dans la boîte de dialogue avant de procéder à l’opération de clonage. Dans le cas contraire, votre notebook, avec toutes les clés d’abonnement, sera public.
1. Cliquez sur **Exécuter sur le calcul gratuit**
1. Sélectionnez l’un des notebooks.
1. Ajoutez votre clé d’abonnement à l’API Détecteur d'anomalies valide à la variable `subscription_key`. 
1. Modifiez la variable `endpoint` à votre point de terminaison. Par exemple : `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Dans la barre de menus supérieure, cliquez sur **Cellule**, puis sur **Exécuter tout**.

## <a name="workflow"></a>Workflow

L’API Détecteur d'anomalies étant un service web RESTful, elle peut être facilement appelée à partir de n’importe quel langage de programmation capable d’exécuter des requêtes HTTP et d’analyser des réponses JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Après l’inscription :

1. Prenez vos données de série chronologique et convertissez-les en format JSON valide. Utilisez les [meilleures pratiques](concepts/anomaly-detection-best-practices.md) lors de la préparation de vos données pour obtenir les meilleurs résultats.
1. Envoyez une demande à l’API Détecteur d’anomalies avec vos données.
1. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="algorithms"></a>Algorithmes

* Reportez-vous au blog technique [Introducing Azure Anomaly Detector API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) sur les algorithmes sous-jacents.
* Reportez-vous au document [Time-Series Anomaly Detection Service at Microsoft](https://arxiv.org/abs/1906.03821) (accepté par KDD 2019) pour les algorithmes SR-CNN de pointe développés par Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies](quickstarts/detect-data-anomalies-csharp.md)
* [Démonstration en ligne](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) de l’API Détecteur d’anomalies
* La [référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) du Détecteur d'anomalies
