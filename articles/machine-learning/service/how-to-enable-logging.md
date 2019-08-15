---
title: Activer la journalisation dans Azure Machine Learning service
titleSuffix: Azure Machine Learning service
description: Découvrez comment activer la journalisation dans Azure Machine Learning service avec aussi bien le package de journalisation Python que des fonctionnalités spécifiques du kit de développement logiciel.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 9deb369dc812616cd7256bbab4101531ca08f127
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847928"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Activer la journalisation dans Azure Machine Learning service

Le kit de développement logiciel (SDK) Python Azure Machine Learning permet d’activer la journalisation avec aussi bien le package de journalisation Python par défaut que des fonctionnalités spécifiques du SDK, pour la journalisation en local et dans un espace de travail sur le portail. Les journaux d’activité fournissent aux développeurs des informations en temps réel sur l’état de l’application et peuvent aider à diagnostiquer les erreurs et les avertissements. Dans cet article, vous allez découvrir différentes façons d’activer la journalisation dans les domaines suivants :

> [!div class="checklist"]
> * Modèles d’apprentissage et cibles de calcul
> * Création d'images
> * Modèles déployés
> * Paramètres `logging` Python

[Créer un espace de travail pour Azure Machine Learning service](how-to-manage-workspace.md). Pour plus d’informations sur le SDK, consultez ce [guide](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="training-models-and-compute-target-logging"></a>Journalisation des modèles d’apprentissage et des cibles de calcul

Il existe plusieurs moyens d’activer la journalisation pendant le processus d’apprentissage du modèle ; les exemples présentés illustrent les modèles de conception courants. Pour consigner les données relatives à l’exécution dans un espace de travail sur le cloud, il suffit d’utiliser la fonction `start_logging` sur la classe `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Pour découvrir d’autres fonctions de journalisation, consultez la documentation de référence de la classe [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

Pour activer la journalisation locale de l’état de l’application pendant la progression de l’apprentissage, utilisez le paramètre `show_output`. La journalisation détaillée permet d’afficher les détails du processus d’apprentissage, ainsi que des informations sur les éventuelles cibles de calcul ou ressources à distance. Utilisez le code suivant pour activer la journalisation lors de la soumission de l’expérience.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Vous pouvez également utiliser le même paramètre dans la fonction `wait_for_completion` lors de l’exécution qui en résulte.

```python
run.wait_for_completion(show_output=True)
```

Le kit SDK prend également en charge le package de journalisation Python par défaut dans certains scénarios à des fins d’apprentissage. L’exemple suivant active le niveau de journalisation `INFO` dans un objet `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Vous pouvez également utiliser le paramètre `show_output` lorsque vous créez une cible de calcul persistante. Spécifiez le paramètre dans la fonction `wait_for_completion` pour activer la journalisation lors de la création de cibles de calcul.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Journalisation lors de la création d’images

Le fait d’activer la journalisation lors de la création d’images permet de voir les éventuelles erreurs qui se produisent pendant le processus de build. Définissez le paramètre `show_output` sur la fonction `wait_for_deployment()`.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Journalisation pour les modèles déployés

Pour récupérer les journaux d’activité d’un service web déjà déployé, chargez ce service et utilisez la fonction `get_logs()`. Les journaux d’activité peuvent contenir des informations détaillées sur les éventuelles erreurs qui se seraient produites au cours du déploiement.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Vous pouvez également consigner les appels de procédure personnalisés de votre service web en activant Application Insights, qui permet d’effectuer le monitoring des temps de requête/réponse, des taux de défaillance et des exceptions. Appelez la fonction `update()` sur un service web existant pour activer Application Insights.

```python
service.update(enable_app_insights=True)
```

Pour plus d’informations sur l’utilisation d’Application Insights sur le Portail Azure., voir le [guide pratique](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Paramètres de journalisation natifs de Python

Certains journaux d’activité du kit SDK risquent de contenir une erreur qui exige de définir le niveau de journalisation sur DÉBOGAGE. Pour définir le niveau de journalisation, ajoutez le code suivant à votre script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
