---
title: Entraîner avec azureml-datasets
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des jeux de données lors de l’entraînement.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: 12c08a95cd43f9f988c98711141b1b1f879e8b3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489379"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Entraîner avec des jeux de données (préversion) dans Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir les deux façons de consommer des [jeux de données Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) lors des exécutions d’entraînement d’expérience à distance sans vous soucier des chaînes de connexion ou des chemins de données.

- Option 1 : Si vous disposez de données structurées, créez un TabularDataset et utilisez-le directement dans votre script d’entraînement.

- Option 2 : Si vous disposez de données non structurées, créez un FileDataset et montez ou téléchargez des fichiers sur un ordinateur distant pour l’entraînement.

Les jeux de données Azure Machine Learning fournissent une intégration transparente avec les produits d’entraînement Azure Machine Learning tels que [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) et [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py).

## <a name="prerequisites"></a>Prérequis

Pour créer des jeux de données et effectuer un entraînement avec eux, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!Note]
> Certaines classes Dataset (préversion) comportent des dépendances sur le package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py). Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Option 1 : Utiliser des jeux de données directement dans les script d’entraînement

Dans cet exemple, vous créez un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) et vous en servez d’entrée directe dans votre objet `estimator` pour l’entraînement. 

### <a name="create-a-tabulardataset"></a>Créer un TabularDataset

Le code suivant crée un TabularDataset non inscrit à partir d’une URL web. Vous pouvez également créer des jeux de données à partir de chemins ou de fichiers locaux dans des magasins de données. Pour en savoir plus sur la création des jeux de données, consultez [cette page](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Accéder au jeu de données d’entrée dans votre script d’entraînement

Les objets TabularDataset permettent de charger les données dans un DataFrame pandas ou spark afin que vous puissiez travailler avec des bibliothèques d’entraînement et de préparation des données familières. Pour tirer parti de cette fonctionnalité, vous pouvez transmettre un TabularDataset en tant qu’entrée dans votre configuration d’entraînement, puis le récupérer dans votre script.

Pour ce faire, accédez au jeu de données d’entrée par le biais de l’objet [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) dans votre script d’entraînement et utilisez la méthode [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--). 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configurer l’estimateur

Un objet [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) est utilisé pour soumettre l’exécution d’expérience. Azure Machine Learning propose des estimateurs préconfigurés pour les frameworks de Machine Learning courants, ainsi qu’un estimateur générique.

Ce code crée un objet estimateur générique, `est`, qui spécifie

* Un répertoire de script pour vos scripts. Tous les fichiers dans ce répertoire sont chargés dans les nœuds de cluster pour l’exécution.
* Le script d’entraînement, *train_titanic.py*.
* Le jeu de données d’entrée pour l’entraînement, `titanic`.
* La cible de calcul pour l’expérience.
* La définition de l’environnement pour l’expérience.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Option 2 :  Monter des fichiers sur une cible de calcul distante

Si vous souhaitez que vos fichiers de données soient disponibles sur la cible de calcul pour l’entraînement, utilisez [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) pour monter ou télécharger des fichiers référencés par celui-ci.

Quand vous montez un système de fichiers, vous l’associez à un répertoire (point de montage) et le rendez disponible sur la cible de calcul. Le montage est instantané, car les fichiers sont chargés uniquement au moment du traitement. Le montage est pris en charge et recommandé pour les calculs basés sur Linux, notamment Capacité de calcul Azure Machine Learning, les machines virtuelles et HDInsight. Pour le calcul non basé sur Linux, seul le téléchargement est pris en charge.  

>[!WARNING]
> Si la taille des données dépasse la limite de stockage de la cible de calcul, le téléchargement échoue.

### <a name="create-a-filedataset"></a>Créer un FileDataset

L’exemple suivant crée un FileDataset non inscrit à partir d’URL web. Pour en savoir plus sur la création des jeux de données à partir d’autres sources, consultez [cette page](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Configurer l’estimateur

Au lieu de transmettre le jeu de données par le biais du paramètre `inputs` dans l’estimateur, vous pouvez le transmettre par le biais de `script_params` et obtenir le chemin de données (point de montage) dans votre script d’entraînement via des arguments. De cette façon, vous pouvez accéder à vos données et utiliser un script d’entraînement existant.

Un objet estimateur [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) est utilisé afin de soumettre l’exécution pour les expériences scikit-learn. Apprenez-en davantage sur l’entraînement avec l’[estimateur SKlearn](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Récupérer les données dans votre script d’entraînement

Une fois que vous avez envoyé l’exécution, les fichiers de données référencés par le jeu de données `mnist` sont montés sur la cible de calcul. Le code suivant montre comment récupérer les données dans votre script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Exemples de notebooks

Les [notebooks de jeux de données](https://aka.ms/dataset-tutorial) illustrent et développent les concepts abordés dans cet article. 

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner automatiquement des modèles Machine Learning](how-to-auto-train-remote.md) avec des TabularDatasets.

* [Entraîner des modèles de classification d’image](https://aka.ms/filedataset-samplenotebook) avec des FileDatasets.

