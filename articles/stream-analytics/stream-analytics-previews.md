---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en cours d’évaluation.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: df3e8c1cd91c676c64d15c46c5acdc3d5bcfaa8e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161378"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="public-previews"></a>Préversions publiques

Les fonctionnalités suivantes sont disponibles en préversion publique. Vous pouvez tirer parti de ces fonctionnalités aujourd’hui, mais ne les utilisez pas dans votre environnement de production.

### <a name="online-scaling"></a>Scalabilité en ligne

Avec la scalabilité en ligne, il n’est pas nécessaire d’arrêter un travail pour pouvoir modifier l’allocation d’unités de streaming. Vous pouvez augmenter ou diminuer la capacité d’unités de streaming d’un travail en cours d’exécution sans avoir à l’arrêter. Cette fonctionnalité s’appuie sur la promesse client de pipelines stratégiques longue durée que Stream Analytics offre aujourd’hui. Pour plus d’informations, voir [Configurer les unités de streaming Azure Stream Analytics](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Désérialiseurs personnalisés C#
Les développeurs peuvent exploiter toute la puissance d’Azure Stream Analytics pour traiter des données Protobuf, XML ou dans un format personnalisé. Il est possible d’implémenter des [désérialiseurs personnalisés](custom-deserializer-examples.md) en C#, qui permettent de désérialiser des événements reçus par Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilité avec du code personnalisé C#

Les développeurs qui créent des modules Stream Analytics dans le cloud ou sur IoT Edge peuvent écrire ou réutiliser des fonctions C# personnalisées et les appeler directement dans la requête grâce à des [fonctions définies par l’utilisateur](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Authentification Managed Identity avec Power BI

Stream Analytics assure une prise en charge complète de l’authentification Managed Identity avec Power BI pour offrir une expérience de tableau de bord dynamique.

### <a name="anomaly-detection"></a>Détection des anomalies

Les modèles Machine Learning d’Azure Stream Analytics prennent en charge la détection des *pics* et des *chutes*, qui vient s’ajouter à la détection des tendances négatives lentes, positives lentes et bidirectionnelles. Pour plus d’informations, consultez [Détection d’anomalies dans Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Déboguer les étapes de requêtes dans Visual Studio

Vous pouvez afficher un aperçu de l’ensemble de lignes intermédiaire sur un diagramme de données lorsque vous effectuez des tests locaux dans les outils Azure Stream Analytics pour Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Tests locaux avec des données actives dans Visual Studio Code

Vous pouvez tester vos requêtes sur des données actives sur votre ordinateur local avant de soumettre le travail sur Azure. Chaque itération de test prend moins de deux à trois secondes en moyenne, ce qui se traduit par un processus de développement très efficace.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pour Azure Stream Analytics

Les travaux Azure Stream Analytics peuvent être créés dans Visual Studio Code. Consultez notre [tutoriel de prise en main de Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="anomaly-detection"></a>Détection des anomalies

Azure Stream Analytics introduit de nouveaux modèles Machine Learning avec la prise en charge de la détection de *pics* et de *chutes* qui vient s’ajouter à la détection des tendances négatives lentes, positives lentes et bidirectionnelles. Pour plus d’informations, consultez [Détection d’anomalies dans Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).


### <a name="integration-with-azure-machine-learning"></a>Intégration à Azure Machine Learning

Vous pouvez mettre à l’échelle les tâches Stream Analytics avec des fonctions Machine Learning (ML). Pour en savoir plus sur la façon dont vous pouvez utiliser les fonctions ML dans votre tâche Stream Analytics, consultez [Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Découvrez un scénario concret avec [Analyse des sentiments à l’aide d’Azure Stream Analytics et Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Tests de données actives dans Visual Studio

Visual Studio Tools pour Azure Stream Analytics améliore la fonctionnalité de test en local qui vous permet de tester vos requêtes sur les flux d’événements en direct, à partir de sources cloud, telles que Event Hub ou IoT hub. Découvrez comment [Tester les données actives localement à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Fonctions .NET définies par l’utilisateur sur IoT Edge

Avec les fonctions .NET standard définies par l’utilisateur, vous pouvez exécuter le code .NET Standard au sein même de votre pipeline de streaming. Vous pouvez créer des classes C# simples, ou importer des bibliothèques et un projet complet. Une expérience de création et de débogage complète est prise en charge dans Visual Studio. Pour plus d’informations, consultez [Développer des fonctions .NET Standard définies par l’utilisateur pour des tâches Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Autres préversions

Les fonctionnalités suivantes sont également disponibles sur demande en préversion.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Scoring haute performance en temps réel avec des modèles ML personnalisés gérés par Azure Machine Learning

Azure Stream Analytics prend en charge le scoring en temps réel haute performance en tirant parti de modèles Machine Learning préentraînés personnalisés, gérés par Azure Machine Learning et hébergés dans Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI), suivant un flux de travail pour lequel il n’est pas nécessaire d’écrire du code. [Inscrivez-vous](https://aka.ms/asapreview1) pour accéder à la préversion.

### <a name="support-for-azure-stack"></a>Prise en charge pour Azure Stack
Cette fonctionnalité est activée sur le runtime Azure IoT Edge, tire parti de fonctionnalités Azure Stack personnalisées, comme la prise en charge native pour les entrées et les sorties locales s’exécutant sur Azure Stack (par exemple Event Hubs, IoT Hub, Stockage Blob). Cette nouvelle intégration vous permet de créer des architectures hybrides qui peuvent analyser vos données à proximité de l’endroit où elles sont générées, en réduisant la latence et en optimisant les insights.
Cette fonctionnalité permet de créer des architectures hybrides capables d’analyser les données à proximité de l’endroit où elles sont générées, ce qui réduit la latence et optimise les insights. Il est nécessaire de [s’inscrire](https://aka.ms/asapreview1) pour accéder à cette préversion.
