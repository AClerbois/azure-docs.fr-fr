---
title: Former des modèles ML avec des estimateurs
titleSuffix: Azure Machine Learning service
description: Découvrir comment effectuer un entraînement à nœud unique et un entraînement distribué sur des modèles Machine Learning traditionnels et d’apprentissage profond en utilisant la classe Estimator des services Azure Machine Learning
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 93b26b2861c5603770a954943174d6436296ad07
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668376"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Former des modèles avec Azure Machine Learning à l’aide de l’estimateur

Avec Azure Machine Learning, vous pouvez facilement envoyer votre script de formation à [différentes cibles de calcul](how-to-set-up-training-targets.md#compute-targets-for-training) à l’aide des objets [RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) et [ScriptRunConfig](how-to-set-up-training-targets.md#submit). Ce modèle permet une grande flexibilité et un contrôle maximal.

Pour faciliter la formation de modèles Deep Learning, le kit de développement logiciel (SDK) Python d’Azure Machine Learning fournit une alternative d’abstraction de plus haut niveau, la classe d’estimateur, qui permet aux utilisateurs de construire facilement des configurations d’exécution. Vous pouvez créer et utiliser un [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) générique pour soumettre un script de formation à l’aide de n’importe quel framework de formation choisi par vos soins (tel que scikit-learn) et que vous souhaitez exécuter sur une cible de calcul, qu’il s’agisse de votre ordinateur local, d’une seule machine virtuelle dans Azure ou d’un cluster GPU dans Azure. Pour les tâches PyTorch, TensorFlow et Chainer, Azure Machine Learning fournit aussi respectivement les estimateurs [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) et [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), qui simplifient l’utilisation de ces frameworks.

## <a name="train-with-an-estimator"></a>Entraîner avec un estimateur

Une fois que vous avez créé votre [espace de travail](concept-workspace.md) et configuré votre [environnement de développement](how-to-configure-environment.md), l’entraînement d’un modèle dans Azure Machine Learning passe par les étapes suivantes :  
1. Créer une [cible de calcul à distance](how-to-set-up-training-targets.md) (notez que vous pouvez également utiliser l’ordinateur local comme cible de calcul)
2. Charger vos [données d’entraînement](how-to-access-data.md) dans la banque de données (facultatif)
3. Créer votre [script d’entraînement](tutorial-train-models-with-aml.md#create-a-training-script)
4. Créer un objet `Estimator`
5. Soumettre l’estimateur à un objet d’expérience sous l’espace de travail

Cet article concerne les étapes 4 et 5. Pour les étapes 1 à 3, reportez-vous au [tutoriel Entraîner un modèle](tutorial-train-models-with-aml.md) pour obtenir un exemple.

### <a name="single-node-training"></a>Entraînement à nœud unique

Utilisez `Estimator` pour un entraînement à nœud unique exécuté sur une cible de calcul distante dans Azure pour un modèle scikit-learn. Vous devez déjà avoir créé votre objet [cible de calcul](how-to-set-up-training-targets.md#amlcompute) `compute_target`, ainsi que votre objet [banque de données ](how-to-access-data.md) `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Cet extrait de code spécifie les paramètres suivants au constructeur `Estimator`.

Paramètre | Description
--|--
`source_directory`| Répertoire local qui contient l’ensemble du code nécessaire à la tâche d’entraînement. Ce dossier est copié de votre ordinateur local vers la cible de calcul distante 
`script_params`| Dictionnaire spécifiant les arguments de ligne de commande de votre script d'entraînement `entry_script`, sous la forme de paires `<command-line argument, value>`. Pour spécifier un indicateur détaillé dans `script_params`, utilisez `<command-line argument, "">`.
`compute_target`| Cible de calcul à distance sur laquelle votre script de formation s’exécute, ici un cluster de capacité de calcul Machine Learning Azure ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Veuillez noter que même si le cluster AmlCompute est la cible couramment utilisée, il est également possible de choisir d’autres types de cibles de calcul, tels que des machines virtuelles Azure, voire un ordinateur local.)
`entry_script`| Chemin de fichier (relatif à `source_directory`) du script d’entraînement à exécuter sur la cible de calcul distante. Ce fichier et tous les autres fichiers dont il dépend doivent se trouver dans ce dossier
`conda_packages`| Liste des packages Python à installer via conda et dont a besoin votre script d’entraînement.  

Le constructeur possède un autre paramètre appelé `pip_packages` que vous pouvez utiliser pour tous les packages pip nécessaires

Maintenant que vous avez créé votre objet `Estimator`, soumettez la tâche d’entraînement à exécuter sur la cible de calcul à distance avec un appel à la fonction `submit` sur votre objet [Expérience](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Dossiers spéciaux** : deux dossiers, *outputs* et *logs*, reçoivent un traitement spécial de la part d’Azure Machine Learning. Pendant l’entraînement, quand vous écrivez des fichiers dans les dossiers *outputs* et *logs* relatifs au répertoire racine (`./outputs` et `./logs`, respectivement), les fichiers sont automatiquement chargés dans votre historique des exécutions. Vous pouvez ainsi y accéder une fois l’exécution terminée.
>
> Pour créer des artefacts pendant l’entraînement (par exemple, des fichiers de modèle, des points de contrôle, des fichiers de données ou des images tracées), écrivez-les dans le dossier `./outputs`.
>
> De même, vous pouvez écrire tous les journaux d’activité de votre exécution d’entraînement dans le dossier `./logs`. Pour utiliser [l’intégration TensorBoard](https://aka.ms/aml-notebook-tb) d’Azure Machine Learning, écrivez vos journaux d’activité TensorBoard dans ce dossier. Lorsque votre exécution est en cours, vous pouvez lancer TensorBoard et diffuser ces journaux d’activité en continu.  Plus tard, vous pourrez également restaurer les journaux d’activité à partir de l’une de vos exécutions précédentes.
>
> Par exemple, pour télécharger sur votre ordinateur local un fichier écrit dans le dossier *outputs* après votre exécution d’entraînement distant : `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Entraînement distribué et images Docker personnalisées

Il existe deux autres scénarios d’entraînement que vous pouvez mener à bien avec `Estimator` :
* Utilisation d’une image Docker personnalisée
* Entraînement distribué sur un cluster à plusieurs nœuds

Le code suivant montre comment procéder à une formation distribuée pour un modèle Keras. Par ailleurs, au lieu d’utiliser les images Azure Machine Learning par défaut, il spécifie une image personnalisée Docker Hub `continuumio/miniconda` pour la formation.

Vous devez déjà avoir créé votre objet de [cible de calcul](how-to-set-up-training-targets.md#amlcompute) `compute_target`. Pour créer l’estimateur, procédez comme suit :

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Le code ci-dessus expose les nouveaux paramètres suivants au constructeur `Estimator` :

Paramètre | Description | Default
--|--|--
`custom_docker_image`| Nom de l’image que vous voulez utiliser. Indiquez uniquement des images disponibles dans les référentiels Docker publics (dans ce cas, Docker Hub). Pour utiliser une image à partir d’un référentiel Docker privé, utilisez le paramètre `environment_definition` du constructeur. [Consultez l’exemple](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Nombre de nœuds à utiliser pour la tâche d’entraînement. | `1`
`process_count_per_node`| Nombre de processus (ou « workers ») à exécuter sur chaque nœud. Dans ce cas, utilisez les GPU `2` disponibles sur chaque nœud.| `1`
`distributed_backend`| Back-end pour lancer l’entraînement distribué, que l’estimateur propose via MPI.  Pour mener à bien un entraînement parallèle ou distribué (par exemple, `node_count`> 1 ou `process_count_per_node`> 1, ou les deux), définissez `distributed_backend='mpi'`. L’implémentation MPI utilisée par AML est [Open MPI](https://www.open-mpi.org/).| `None`

Enfin, envoyez la tâche d’entraînement :
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Intégration et suivi GitHub

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Par exemple, l’ID de validation en cours pour le dépôt est consigné au sein de l’historique.

## <a name="examples"></a>Exemples
Pour un notebook qui montre les fonctions de base du modèle d’estimateur, consultez :
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Pour un notebook qui entraîne un modèle scikit-learn à l’aide de l’estimateur, consultez :
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Pour les notebooks d’entraînement des modèles à l’aide d’estimateurs spécifiques deep-learning-framework, consultez :
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Entraîner des modèles PyTorch](how-to-train-pytorch.md)
* [Entraîner des modèles TensorFlow](how-to-train-tensorflow.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
