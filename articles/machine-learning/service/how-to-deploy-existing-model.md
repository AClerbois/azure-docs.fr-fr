---
title: Utiliser et déployer des modèles existants
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Azure Machine Learning avec des modèles qui ont été entraînés en dehors du service. Vous pouvez inscrire des modèles créés en dehors d’Azure Machine Learning, puis les déployer en tant que service web ou module Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: 4e82d52428195797fb5cdc7a54e900fff7d37655
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671445"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Utiliser un modèle existant avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment utiliser un modèle Machine Learning existant avec Azure Machine Learning.

Si vous avez un modèle Machine Learning qui a été entraîné en dehors d’Azure Machine Learning, vous pouvez toujours utiliser le service pour déployer le modèle en tant que service web ou sur un appareil IoT Edge. 

> [!TIP]
> Cet article fournit des informations de base sur l’inscription et le déploiement d’un modèle existant. Une fois déployé, Azure Machine Learning assure la supervision de votre modèle. Il vous permet également de stocker les données d’entrée envoyées au déploiement, qui peuvent être utilisées pour l’analyse de la dérive des données ou l’entraînement de nouvelles versions du modèle.
>
> Pour plus d’informations sur les concepts et termes utilisés ici, consultez [Gérer, déployer et superviser des modèles d’apprentissage automatique](concept-model-management-and-deployment.md).
>
> Pour obtenir des informations générales sur le processus de déploiement, consultez [Déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour plus d’informations, consultez [Créer un espace de travail](how-to-manage-workspace.md).

    > [!TIP]
    > Les exemples Python dans cet article partent du principe que la variable `ws` est définie sur votre espace de travail Azure Machine Learning.
    >
    > Les exemples CLI utilisent les espaces réservés `myworkspace` et `myresourcegroup`. Remplacez-les par le nom de votre espace de travail et du groupe de ressources qui le contient.

* Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et [Extension CLI Machine Learning](reference-azure-machine-learning-cli.md).

* Un modèle entraîné. Le modèle doit être conservé dans un ou plusieurs fichiers de votre environnement de développement.

    > [!NOTE]
    > Pour illustrer l’inscription d’un modèle entraîné en dehors d’Azure Machine Learning, les exemples d’extraits de code de cet article utilisent les modèles créés par le projet d’analyse des sentiments sur Twitter de Paolo Ripamonti : [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Inscrire les modèles

L’inscription d’un modèle vous permet de stocker, de gérer la version et d’effectuer le suivi des métadonnées sur les modèles dans votre espace de travail. Dans les exemples Python et CLI suivants, le répertoire `models` contient les fichiers `model.h5`, `model.w2v`, `encoder.pkl` et `tokenizer.pkl`. Cet exemple charge les fichiers contenus dans le répertoire `models` comme une nouvelle inscription de modèle nommée `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Pour plus d’informations, consultez les informations de référence sur [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-).

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Pour plus d’informations, consultez les informations de référence sur [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).


Pour plus d’informations sur l’inscription de modèle en général, consultez [Gérer, déployer et superviser des modèles d’apprentissage automatique](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Définir la configuration de l’inférence

La configuration de l’inférence définit l’environnement utilisé pour exécuter le modèle déployé. La configuration de l’inférence référence les entités suivantes qui sont utilisés pour exécuter le modèle quand il est déployé :

* Script d’entrée. Ce fichier (nommé `score.py`) charge le modèle au démarrage du service déployé. Il est également responsable de la réception de données, de leur transmission au modèle et du renvoi d’une réponse.
* Un [environnement](how-to-use-environments.md) Azure Machine Learning. Un environnement définit les dépendances logicielles nécessaires pour exécuter le modèle et le script d’entrée.

L’exemple suivant montre comment utiliser le SDK pour créer un environnement, puis l’utiliser avec une configuration d’inférence :

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
conda_dep.add_pip_package("keras")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Pour plus d’informations, consultez les articles suivants :

+ [Comment utiliser des environnements](how-to-use-environments.md).
+ Informations de référence sur [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).


L’interface CLI charge la configuration de l’inférence à partir d’un fichier YAML :

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Avec l’interface CLI, l’environnement conda est défini dans le fichier `myenv.yml` référencé par la configuration d’inférence. Le YAML suivant constitue le contenu de ce fichier :

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
```

Pour plus d’informations sur la configuration de l’inférence, consultez [Déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script d’entrée

Le script d’entrée a uniquement deux fonctions requises, `init()` et `run(data)`. Ces fonctions sont utilisées pour initialiser le service au démarrage et exécuter le modèle à l’aide des données de la demande transmises par un client. Le reste du script gère le chargement et l’exécution des modèles.

> [!IMPORTANT]
> Il n’existe pas de script d’entrée générique qui fonctionne pour tous les modèles. Il est toujours spécifique au modèle qui est utilisé. Il doit comprendre comment charger le modèle, le format de données attendu par le modèle et comment noter les données à l’aide du modèle.

Le code Python suivant est un exemple de script d’entrée (`score.py`) :

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Pour plus d’informations sur les scripts d’entrée, consultez [Déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Définir le déploiement

Le package [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) contient les classes utilisées pour le déploiement. La classe que vous utilisez détermine où le modèle est déployé. Par exemple, pour déployer un modèle comme un service web sur Azure Kubernetes Service, utilisez [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) pour créer la configuration de déploiement.

Le code Python suivant définit une configuration de déploiement pour un déploiement local. Cette configuration déploie le modèle sous la forme d’un service web sur votre ordinateur local.

> [!IMPORTANT]
> Un déploiement local nécessite une installation opérationnelle de [Docker](https://www.docker.com/) sur votre ordinateur local :

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Pour plus d’informations, consultez les informations de référence sur [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-).

L’interface CLI charge la configuration de déploiement à partir d’un fichier YAML :

```YAML
{
    "computeType": "LOCAL"
}
```

Le déploiement sur une autre cible de calcul, par exemple Azure Kubernetes Service dans le cloud Azure, est aussi simple que la modification de la configuration de déploiement. Pour plus d’informations, consultez [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Déployer le modèle

L’exemple suivant charge des informations sur le modèle inscrit nommé `sentiment`, puis le déploie en tant que service nommé `sentiment`. Pendant le déploiement, la configuration de l’inférence et la configuration de déploiement sont utilisées pour créer et configurer l’environnement de service :

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Pour plus d’informations, consultez les informations de référence sur [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-).

Pour déployer le modèle à partir de l’interface CLI, utilisez la commande suivante. Cette commande déploie la version 1 du modèle inscrit (`sentiment:1`) à l’aide des configurations de l’inférence et de déploiement stockées dans les fichiers `inferenceConfig.json` et `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Pour plus d’informations, consultez les informations de référence sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

Pour plus d’informations sur le déploiement, consultez [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consommation de requête-réponse

Après le déploiement, l’URI de scoring s’affiche. Cet URI peut être utilisé par les clients pour envoyer des demandes au service. L’exemple suivant est un client Python de base qui envoie des données au service et affiche la réponse :

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Pour plus d’informations sur le mode d’utilisation du service déployé, consultez [Créer un client](how-to-consume-web-service.md).

## <a name="next-steps"></a>Étapes suivantes

* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
* [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
* [Guide pratique pour créer un client pour un modèle déployé](how-to-consume-web-service.md)
