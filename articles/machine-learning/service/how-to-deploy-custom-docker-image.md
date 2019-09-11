---
title: Déployer des modèles avec une image de base Docker personnalisée
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser une image de base Docker personnalisée lors du déploiement de vos modèles de service Azure Machine Learning. Lors du déploiement d’un modèle formé, une image conteneur de base est déployée pour exécuter votre modèle à des fins d'inférence. Même si le service Azure Machine Learning vous fournit une image par défaut, vous pouvez également utiliser votre propre image de base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/22/2019
ms.openlocfilehash: 753f0bece5b8b52ebb50ab2a6e93056ce209cfbc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183563"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Déployer un modèle à l’aide d’une image de base Docker personnalisée

Découvrez comment utiliser une image Docker personnalisée lors du déploiement de modèles formés avec le service Azure Machine Learning.

Lorsque vous déployez un modèle formé sur un service web ou un appareil IoT Edge, un package contenant un serveur web pour gérer les demandes entrantes est créé.

Le service Azure Machine Learning fournit une image de base Docker par défaut pour vous éviter d’en créer une. Vous pouvez également des __environnements__ du service Azure Machine Learning pour sélectionner une image de base spécifique ou utiliser une image personnalisée que vous fournissez.

Une image de base est utilisée comme point de départ lorsque vous créez une image pour un déploiement. Elle fournit le système d’exploitation et les composants sous-jacents. Le processus de déploiement ajoute ensuite des composants supplémentaires, tels que votre modèle, l’environnement conda et d’autres ressources, à l’image avant de la déployer.

En règle générale, vous créez une image de base personnalisée lorsque vous souhaitez utiliser Docker pour gérer vos dépendances, maintenir un contrôle plus étroit sur les versions des composants ou gagner du temps lors du déploiement. Par exemple, vous souhaitez peut-être adopter comme standard une version spécifique de Python, de Conda ou d’un autre composant. Vous pouvez également souhaiter installer les logiciels requis par votre modèle, où le processus d’installation prend beaucoup de temps. L’installation du logiciel lors de la création de l’image de base signifie que vous n’avez pas à l’installer pour chaque déploiement.

> [!IMPORTANT]
> Lorsque vous déployez un modèle, vous ne pouvez pas remplacer les principaux composants tels que le serveur web ou des composants IoT Edge. Ces composants fournissent un environnement de travail connu qui est testé et pris en charge par Microsoft.

> [!WARNING]
> Microsoft peut ne pas être en mesure de vous aider à résoudre les problèmes dus à une image personnalisée. Si vous rencontrez des problèmes, vous pouvez être invité à utiliser l’image par défaut ou l’une des images fournies par Microsoft pour voir si le problème est spécifique à votre image.

Ce document est divisé en deux sections :

* Créer une image de base personnalisée : présente des informations aux administrateurs et DevOps sur la création d’une image personnalisée et la configuration de l’authentification dans un registre Azure Container Registry à l’aide de l’interface CLI Azure et CLI Machine Learning.
* Déployer un modèle à l’aide d’une image de base personnalisée : présente des informations aux scientifiques des données et DevOps/Ingénieurs ML sur l’utilisation des images personnalisées lors du déploiement d’un modèle entraîné à partir du kit de développement logiciel (SDK) Python ou de l’interface CLI ML.

## <a name="prerequisites"></a>Prérequis

* Espace de travail de service Azure Machine Learning. Pour plus d’informations, consultez l’article [Créer un espace de travail](how-to-manage-workspace.md).
* Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Registre [Azure Container Registry](/azure/container-registry) ou autre registre Docker accessible sur Internet.
* Les étapes de ce document supposent que vous êtes familiarisé avec la création et l’utilisation d’un objet de __configuration de l’inférence__ dans le cadre du déploiement de modèle. Pour plus d’informations, consultez la section « Préparer le déploiement » de [Comment et où déployer des modèles ?](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Créer une image de base personnalisée

Les informations contenues dans cette section supposent que vous utilisez un registre Azure Container Registry pour stocker les images Docker. Utilisez la liste de contrôle suivante quand vous prévoyez de créer des images personnalisées pour le service Azure Machine Learning :

* Allez-vous utiliser le registre Azure Container Registry créé pour l’espace de travail de service Azure Machine Learning ou un registre Azure Container Registry autonome ?

    Quand vous utilisez des images stockées dans le __registre de conteneurs pour l’espace de travail__, vous n’avez pas besoin de vous authentifier auprès du registre. L’authentification est gérée par l’espace de travail.

    > [!WARNING]
    > Le registre Azure Container Registry pour votre espace de travail est __créé la première fois que vous entraînez ou déployez un modèle__ à l’aide de l’espace de travail. Si vous avez créé un espace de travail, mais sans entraîner ni créer un modèle, aucun registre Azure Container Registry n’existe pour l’espace de travail.

    Pour plus d’informations sur la récupération du nom du registre Azure Container Registry pour votre espace de travail, consultez la section [Obtenir les informations du registre de conteneurs](#getname) de cet article.

    Quand vous utilisez des images stockées dans un __registre de conteneurs autonome__, vous devez configurer un principal de service qui a au moins un accès en lecture. Vous indiquez ensuite l’ID de principal de service (nom d’utilisateur) et le mot de passe à toute personne qui utilise des images du registre. Cela ne s’applique toutefois pas si vous définissez le registre de conteneurs comme étant accessible publiquement.

    Pour plus d’informations sur la création d’un registre Azure Container Registry privé, consultez [Créer un registre de conteneurs privé](/azure/container-registry/container-registry-get-started-azure-cli).

    Pour plus d’informations sur l’utilisation des principaux de service avec Azure Container Registry, consultez [Authentification Azure Container Registry avec des principaux de service](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry et informations sur l’image : Indiquez le nom de l’image à toute personne qui doit l’utiliser. Par exemple, une image nommée `myimage`, stockée dans un registre nommé `myregistry`, est référencée en tant que `myregistry.azurecr.io/myimage` lors de l’utilisation de l’image pour le déploiement de modèle

* Exigences des images : Le service Azure Machine Learning prend uniquement en charge les images Docker qui fournissent les logiciels suivants :

    * Ubuntu 16.04 ou ultérieur.
    * Conda 4.5.# ou ultérieur.
    * Python 3.5.# ou 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obtenir les informations du registre de conteneurs

Dans cette section, découvrez comment obtenir le nom du registre Azure Container Registry pour votre espace de travail de service Azure Machine Learning.

> [!WARNING]
> Le registre Azure Container Registry pour votre espace de travail est __créé la première fois que vous entraînez ou déployez un modèle__ à l’aide de l’espace de travail. Si vous avez créé un espace de travail, mais sans entraîner ni créer un modèle, aucun registre Azure Container Registry n’existe pour l’espace de travail.

Si vous avez déjà entraîné ou déployé des modèles à l’aide du service Azure Machine Learning, un registre de conteneurs a été créé pour votre espace de travail. Pour rechercher le nom de ce registre de conteneurs, effectuez les étapes suivantes :

1. Ouvrez un nouveau shell ou une invite de commandes et utilisez la commande suivante pour vous authentifier auprès de votre abonnement Azure :

    ```azurecli-interactive
    az login
    ```

    Suivez les invites pour vous authentifier auprès de l’abonnement.

2. Utilisez la commande suivante pour lister le registre de conteneurs pour l’espace de travail. Remplacez `<myworkspace>` par le nom de votre espace de travail de service Azure Machine Learning. Remplacez `<resourcegroup>` par le groupe de ressources Azure qui contient votre espace de travail :

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    Les informations renvoyées sont semblables à ce qui suit :

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    La valeur `<registry_name>` est le nom du registre Azure Container Registry pour votre espace de travail.

### <a name="build-a-custom-base-image"></a>Créer une image de base personnalisée

Les étapes de cette section vous guident tout au long de la création d’une image Docker personnalisée dans votre registre Azure Container Registry.

1. Créez un fichier texte nommé `Dockerfile` et utilisez le texte suivant comme contenu :

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. À partir d’un shell ou d’une invite de commandes, utilisez les éléments suivants pour vous authentifier auprès du registre Azure Container Registry. Remplacez `<registry_name>` par le nom du registre de conteneurs dans lequel vous voulez stocker l’image :

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Pour charger le fichier Dockerfile et le générer, utilisez la commande suivante. Remplacez `<registry_name>` par le nom du registre de conteneurs dans lequel vous voulez stocker l’image :

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Pendant le processus de génération, les informations sont renvoyées à la ligne de commande. Si la génération a réussi, vous recevez un message similaire au texte suivant :

    ```text
    Run ID: cda was successful after 2m56s
    ```

Pour plus d’informations sur la génération d’images avec un registre Azure Container Registry, consultez [Générer et exécuter une image conteneur à l’aide d’Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Pour plus d’informations sur le chargement d’images existantes sur un registre Azure Container Registry, consultez [Envoyer votre première image vers un registre de conteneurs Docker privé](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-base-image"></a>Utiliser une image de base personnalisée

Pour utiliser une image personnalisée, vous avez besoin des informations suivantes :

* __Nom de l’image__. Par exemple, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` est le chemin d’accès à une image Docker de base fournie par Microsoft.
* Si l’image se trouve dans un __dépôt privé__, vous avez besoin des informations suivantes :

    * __Adresse du registre__. Par exemple : `myregistry.azureecr.io`.
    * __Nom d’utilisateur__ et __mot de passe__ d’un principal de service qui a accès en lecture au registre.

    Si vous n’avez pas ces informations, contactez l’administrateur du registre Azure Container Registry qui contient votre image.

### <a name="publicly-available-base-images"></a>Images de base disponibles publiquement

Microsoft fournit plusieurs images Docker dans un dépôt accessible publiquement, qui peuvent être utilisées dans les étapes décrites dans cette section :

| Image | Description |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Image de base pour le service Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Contient le runtime ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Contient le runtime ONNX et les composants CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Contient le runtime ONNX et TensorRT. |

> [!TIP]
> Dans la mesure où ces images sont disponibles publiquement, vous n’avez pas besoin de fournir une adresse, un nom d’utilisateur ni un mot de passe lors de leur utilisation.

> [!IMPORTANT]
> Les images Microsoft qui utilisent CUDA ou TensorRT sont exclusivement réservées à une utilisation sur les services Microsoft Azure.

Pour plus d’informations, consultez [Conteneurs du service Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Si votre modèle est entraîné sur une capacité de calcul Azure Machine Learning__, avec la __version 1.0.22 ou une version ultérieure__ du SDK Azure Machine Learning, une image est créée durant l’entraînement. Pour découvrir le nom de cette image, utilisez `run.properties["AzureML.DerivedImageName"]`. L’exemple suivant illustre l’utilisation de cette image :
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Utiliser une image avec le kit SDK Azure Machine Learning

Pour utiliser une image stockée dans **Azure Container Registry pour votre espace de travail**, ou un **registre de conteneurs accessible publiquement**, définissez les attributs [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) suivants :

+ `docker.enabled=True`
+ `docker.base_image`: Définissez le registre et le chemin de l’image.

```python
from azureml.core import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Pour utiliser une image d’un __registre de conteneurs privé__ qui ne se trouve pas dans votre espace de travail, vous devez utiliser `docker.base_image_registry` pour spécifier l’adresse du dépôt ainsi qu’un nom d’utilisateur et un mot de passe :

```python
# Set the container registry information
myenv.docker.base_image_repository.address = "myregistry.azurecr.io"
myenv.docker.base_image_repository.username = "username"
myenv.docker.base_image_repository.password = "password"
```

Après avoir défini l’environnement, utilisez-le avec un objet [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) pour définir l’environnement d’inférence dans lequel le modèle et le service web s’exécuteront.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

À ce stade, vous pouvez passer au déploiement. Par exemple, l’extrait de code suivant déploie un service web localement à l’aide de la configuration d’inférence et de l’image personnalisée :

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Pour plus d’informations sur le déploiement, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="use-an-image-with-the-machine-learning-cli"></a>Utiliser une image avec l’interface CLI Machine Learning

> [!IMPORTANT]
> Actuellement, l’interface CLI Machine Learning peut utiliser des images du registre Azure Container Registry pour votre espace de travail ou des dépôts accessibles publiquement. Elle ne peut pas utiliser d’images de registres privés autonomes.

Lorsque vous déployez un modèle à l’aide de l’interface CLI Machine Learning, vous fournissez un fichier de configuration de l’inférence qui fait référence à l’image personnalisée. Le document JSON suivant montre comment référencer une image dans un registre de conteneurs public :

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Ce fichier est utilisé avec la commande `az ml model deploy`. Le paramètre `--ic` est utilisé pour spécifier le fichier de configuration de l’inférence.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Pour plus d’informations sur le déploiement d’un modèle à l’aide de l’interface CLI ML, consultez la section « Inscription du modèle, profilage et déploiement » de l’article [Extension CLI pour le service Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail [comment et où déployer des modèles](how-to-deploy-and-where.md).
* Découvrez comment [entraîner et déployer des modèles Machine Learning à l’aide d’Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
