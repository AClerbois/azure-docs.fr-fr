---
title: Créer et explorer des jeux de données avec des étiquettes
titleSuffix: Azure Machine Learning
description: Apprenez à exporter des étiquettes de données à partir de vos projets d’étiquetage Azure Machine Learning, ainsi qu’à les utiliser pour des tâches de Machine Learning.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 05ecce2d6ef0f8a3f241570ba9364c5e38682f3e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319437"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Créer et explorer un jeu de données Azure Machine Learning avec des étiquettes

Cet article explique comment exporter les étiquettes de données d’un projet d’étiquetage de données Azure Machine Learning, et les charger dans des formats populaires tels qu’une tramedonnées pandas pour l’exploration de données ou un jeu de données Torchvision pour la transformation d’image. 

## <a name="what-are-datasets-with-labels"></a>Que sont les jeux de données avec étiquettes 

Les jeux de données Azure Machine Learning avec étiquettes sont des [jeux de données tabulaires](how-to-create-register-datasets.md#dataset-types) avec une propriété d’étiquette que nous appelons jeux de données étiquetés. Ces types spécifiques de jeux de données tabulaires sont créés uniquement en tant que sortie de projets d’étiquetage de données Azure Machine Learning. Créez un projet d’étiquetage des données en suivant [ces étapes](how-to-create-labeling-projects.md). Machine Learning prend en charge les projets d’étiquetage de données pour la classification d’images (comprenant plusieurs étiquettes ou classes), ainsi que l’identification d’objets à l’aide de zones délimitées.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’accès à [Azure Machine Learning studio](https://ml.azure.com/).
    * Installer le package [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).
* Accédez à un projet d’étiquetage de données Azure Machine Learning. Si vous n’avez pas de projet d’étiquetage, créez-en un en suivant [ces étapes](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exporter des étiquettes de données 

Lorsque vous terminez un projet d’étiquetage de données, vous pouvez en exporter les données d’étiquette. Cela vous permet de capturer la référence aux données et ses étiquettes, puis de les exporter au [format COCO](http://cocodataset.org/#format-data) ou en tant que jeu de données Azure Machine Learning. Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage.

### <a name="coco"></a>COCO 

 Le fichier COCO est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé au sein de *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Jeu de données Azure Machine Learning

Vous pouvez accéder au jeu de données Azure Machine Learning exporté dans la section **Jeux de données** de votre studio Azure Machine Learning. La page **Détails** du jeu de données fournit également un exemple de code pour accéder à vos étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explorer les jeux de données étiquetés

Chargez vos jeux de données étiquetés dans une tramedonnées pandas ou un jeu de données torchvision pour tirer parti de bibliothèques open source populaires pour l’exploration de données, ainsi que de bibliothèques fournies par PyTorch pour la transformation et la formation d’images.

### <a name="pandas-dataframe"></a>Tramedonnées Pandas

Vous pouvez charger des jeux de données étiquetés dans une tramedonnées pandas avec la méthode [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) de la classe `azureml-contrib-dataset`. Installez la classe avec la commande d’interpréteur de commandes suivante : 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>L’espace de noms azureml.contrib change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.

Nous proposons les options de gestion de fichiers suivantes pour les flux de fichiers lors de la conversion en tramedonnées pandas.
* Télécharger : Téléchargez vos fichiers de données vers un chemin d’accès local.
* Monter : Montez vos fichiers de données sur un point de montage. Le montage fonctionne uniquement pour le calcul basé sur Linux, incluant les machines virtuelles de notebooks Azure Machine Learning et la capacité de calcul Azure Machine Learning.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Jeux de données Torchvision

Vous pouvez charger des jeux de données étiquetés dans un jeu de données Torchvision avec la méthode [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) également à partir de la classe `azureml-contrib-dataset`. Pour pouvoir utiliser cette méthode, [PyTorch](https://pytorch.org/) doit être installé. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Étapes suivantes

* Pour un exemple d’apprentissage complet, voir le [notebook jeu de données avec étiquettes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
