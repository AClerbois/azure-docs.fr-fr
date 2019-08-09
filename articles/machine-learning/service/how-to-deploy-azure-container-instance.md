---
title: Déployer des modèles sur Azure Container Instances
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer vos modèles Azure Machine Learning service en tant que service web Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 1bf8c22c2c7c8e8aaab35a4663652300a341c99c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571069"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Déployer un modèle sur Azure Container Instances

Découvrez comment utiliser Azure Machine Learning service pour déployer un modèle en tant que service web sur Azure Container Instances (ACI). Utilisez Azure Container Instances si l’une des conditions suivantes est vraie :

- Vous avez besoin de déployer et de valider rapidement votre modèle. Vous n’avez pas besoin de créer des conteneurs ACI à l’avance. Ils sont créés lors du processus de déploiement.
- Vous testez un modèle en cours de développement. 

Pour plus d’informations sur les quotas et la disponibilité d’ACI en fonction des régions, consultez l’article [Quotas et disponibilité dans les régions pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez la section [Créer un espace de travail Azure Machine Learning service](setup-create-workspace.md).

- Un modèle Machine Learning inscrit dans votre espace de travail. Si vous n’avez pas de modèle inscrit, consultez la section [Comment et où déployer des modèles](how-to-deploy-and-where.md).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-vscode-tools.md).

- Les extraits de code __Python__ de cet article considèrent que les variables suivantes sont définies :

    * `ws` – Sur votre espace de travail.
    * `model` – Sur votre modèle inscrit.
    * `inference_config` – Sur la configuration d’inférence pour le modèle.

    Pour plus d’informations sur le paramétrage de ces variables, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

- Les extraits de code __CLI__ de cet article considèrent que vous avez créé un document `inferenceconfig.json`. Pour plus d’informations sur la création de ce document, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Déployer sur ACI

Pour déployer un modèle sur Azure Container instances, créez une __configuration de déploiement__ décrivant les ressources de calcul nécessaires. Par exemple, le nombre de cœurs et la mémoire. Vous avez également besoin __d’une configuration d’inférence__ décrivant l’environnement nécessaire pour héberger le modèle et le service Web. Pour plus d’informations sur la création de la configuration d’inférence, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Utilisation de l’interface CLI

Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `mymodel:1` par le nom et la version du modèle inscrit. Remplacez `myservice` par le nom à attribuer à ce service :

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

Pour plus d’informations, consultez les informations de référence sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy). 

## <a name="using-vs-code"></a>Avec VS Code

Consultez la section[Déployer vos modèles avec VS Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Vous n’avez pas besoin de créer un conteneur ACI à tester à l’avance. Les conteneurs ACI sont créés en fonction des besoins.

## <a name="update-the-web-service"></a>Mise à jour du service web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
