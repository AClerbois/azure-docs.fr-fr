---
title: 'Didacticiel : Entraîner votre premier modèle Azure ML en Python'
titleSuffix: Azure Machine Learning
description: Dans ce tutoriel, vous allez découvrir les modèles de conception fondamentaux d’Azure Machine Learning et comment effectuer l’entraînement d’un modèle scikit-learn simple basé sur le jeu de données diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11eaafec750b2ca35933b431866def51c25e8961
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582471"
---
# <a name="tutorial-train-your-first-ml-model"></a>Didacticiel : Entraîner votre premier modèle ML

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ce tutoriel est le **deuxième d’une série de deux**. Dans le didacticiel précédent, [vous avez créé un espace de travail et choisi un environnement de développement](tutorial-1st-experiment-sdk-setup.md). Dans ce tutoriel, vous allez découvrir les modèles de conception fondamentaux d’Azure Machine Learning et comment effectuer l’entraînement d’un modèle scikit-learn simple basé sur le jeu de données diabetes. À l’issue de ce didacticiel, vous aurez acquis une connaissance pratique du Kit de développement logiciel (SDK) permettant de monter en puissance pour passer au développement d’expériences et de flux de travail plus complexes.

Dans ce tutoriel, vous avez appris à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Connecter votre espace de travail et créer une expérience
> * Charger des données et effectuer l’apprentissage de modèles scikit-Learn
> * Afficher les résultats de l’apprentissage sur le portail
> * Récupérer le meilleur modèle

## <a name="prerequisites"></a>Prérequis

Le seul prérequis est d’avoir suivi la première partie de ce tutoriel, [Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md).

Dans cette partie du tutoriel, vous exécutez le code dans l’exemple de notebook Jupyter `tutorials/tutorial-1st-experiment-sdk-train.ipynb`, ouvert à la fin de la première partie. Cet article vous guide tout au long du code qui se trouve dans le notebook.

## <a name="open-the-notebook"></a>Ouvrir le notebook

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).

1. Ouvrez le fichier **tutorial-1st-experiment-sdk-train.ipynb** dans votre dossier comme indiqué dans la [première partie](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> Ne créez **pas** un *autre* notebook dans l’interface Jupyter ! Le notebook `tutorials/tutorial-1st-experiment-sdk-train.ipynb` comprend l’**ensemble du code et des données nécessaires** pour ce tutoriel.

## <a name="connect-workspace-and-create-experiment"></a>Connecter un espace de travail et créer une expérience

> [!Important]
> Le reste de cet article contient le même contenu que ce que vous voyez dans le notebook.  
>
> Basculez maintenant vers le notebook Jupyter si vous voulez lire le code à mesure que vous l’exécutez. 
> Pour exécuter une seule cellule de code dans un notebook, cliquez sur celle-ci et appuyez sur **Maj + Entrée**. Sinon, exécutez l’intégralité du notebook en choisissant **Run all** (Tout exécuter) dans la barre d’outils supérieure.

Importez la classe `Workspace` et chargez les informations de votre abonnement à partir du fichier `config.json` à l’aide de la fonction `from_config().`. Par défaut, celle-ci recherche le fichier JSON dans le répertoire actif, mais vous pouvez également spécifier un paramètre de chemin d’accès pour pointer vers le fichier en utilisant `from_config(path="your/file/path")`. Sur un serveur de notebook cloud, le fichier est automatiquement dans le répertoire racine.

Si le code suivant demande une authentification supplémentaire, collez simplement le lien dans un navigateur, puis entrez le jeton d’authentification.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Créez à présent une expérience dans votre espace de travail. Une expérience est une autre ressource cloud fondamentale qui représente une collection d’essais (cycles de modèles individuels). Dans ce tutoriel, vous allez utiliser l’expérience pour créer des cycles et suivre la formation de votre modèle dans Azure Machine Learning Studio. Les paramètres incluent la référence de votre espace de travail et un nom de chaîne pour l’expérience.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Charger des données et préparer l’apprentissage

Pour ce tutoriel, vous utilisez le jeu de données Diabète, qui utilise des caractéristiques telles que l’âge, le sexe et l’IMC pour prédire l’évolution du diabète. Chargez les données à partir de la classe [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) et fractionnez-les en jeux de formation et de test à l’aide de `train_test_split()`. Cette fonction sépare les données afin que le modèle dispose de données non consultées à utiliser pour les tests après l’apprentissage.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Effectuer l’apprentissage d’un modèle

Il est facile d’effectuer l’apprentissage d’un modèle scikit-learn simple en local pour un apprentissage à petite échelle mais, lors de l’apprentissage de nombreuses itérations avec des dizaines de permutations de fonctionnalités et de définitions d’hyperparamètres différentes, vous risquez de perdre la trace des modèles dont vous avez effectué l’apprentissage et de la manière dont vous l’avez fait. Le modèle de conception suivant montre comment tirer parti du Kit de développement logiciel (SDK) pour conserver facilement la trace de votre apprentissage dans le cloud.

Créez un script qui effectue l’apprentissage en boucle de modèles de crête en utilisant différentes valeurs alpha d’hyperparamètre.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Le code ci-dessus effectue les opérations suivantes :

1. Pour chaque valeur d’hyperparamètre alpha dans le tableau `alphas`, un nouveau cycle est cré dans l’expérience. La valeur alpha est journalisée afin de pouvoir faire la distinction entre les différents cycles.
1. À chaque cycle, un modèle d’arête est instancié, formé et utilisé pour effectuer des prédictions. La mesure root-mean-squared-error (racine carrée de l’erreur quadratique moyenne) est calculée pour les valeurs prévues et observées, puis journalisée dans le cycle. À ce stade, le cycle génère des métadonnées qui sont jointes tant pour la valeur alpha que pour la précision de la mesure RMSE.
1. Ensuite, le modèle de chaque cycle est sérialisé et chargé dans le cycle. Cela vous permet de télécharger le fichier de modèle à partir du cycle sur le portail.
1. À la fin de chaque itération, le cycle s’achève par l’appel de `run.complete()`.

Une fois l’apprentissage terminé, appelez la variable `experiment` pour extraire un lien vers l’expérience dans le portail.

```python
experiment
```

<table style="width:100%"><tr><th>Nom</th><th>Espace de travail</th><th>Page du rapport</th><th>Page des documents</th></tr><tr><td>diabetes-experiment</td><td>your-workspace-name</td><td>Lien vers le portail Azure</td><td>Lien vers la documentation</td></tr></table>

## <a name="view-training-results-in-portal"></a>Afficher les résultats de l’apprentissage sur le portail

En suivant le **Lien vers le portail Azure**, vous accédez à la page de l’expérience principale. Vous voyez ici toutes les cycles individuels dans l’expérience. Toutes les valeurs journalisées de manière personnalisée (`alpha_value` et `rmse` dans ce cas) deviennent des champs pour chaque cycle, et deviennent également disponibles pour les graphiques et les vignettes en haut de la page de l’expérience. Pour ajouter une métrique journalisée à un graphique ou à une vignette, pointez dessus, cliquez sur le bouton Modifier, puis recherchez votre métrique journalisée de manière personnalisée.

Lorsque vous effectuez l’apprentissage de modèles à grande échelle sur des centaines et des milliers de cycles, cette page facilite la visualisation de tous les modèles que vous avez formés, et singulièrement de la façon dont ils ont été formés ainsi que de l’évolution de vos métriques uniques au fil du temps.

![Page d’expérience principale sur le portail](./media/tutorial-quickstart/experiment-main.png)

Cliquez sur un lien de numéro de cycle dans la colonne `RUN NUMBER` pour accéder à la page du cycle correspondant. L’onglet par défaut **Détails** affiche des informations plus détaillées sur chaque exécution. Accédez à l’onglet **Sorties** pour voir le fichier `.pkl` pour le modèle chargé dans le cycle durant chaque itération d’apprentissage. Ici, vous pouvez télécharger le fichier de modèle au lieu de devoir ré-effectuer son apprentissage manuellement.

![Page Détails de l’exécution sur le portail](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Obtenir le meilleur modèle

En plus de la possibilité de télécharger des fichiers de modèle à partir de l’expérience du portail, vous pouvez les télécharger par programme. Le code suivant effectue une itération à chaque cycle de l’expérience, et accède tant aux métriques d’exécution journalisées qu’aux détails de l’exécution (qui contiennent l’identificateur du cycle). Cela permet de suivre le meilleur cycle, en l’occurrence, celui dont la mesure root-mean-squared-error est la plus faible.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Utilisez l’ID de meilleure exécution pour extraire l’exécution individuelle à l’aide du constructeur `Run` et de l’objet d’expérience. Appelez ensuite `get_file_names()` pour voir tous les fichiers disponibles en téléchargement à partir de ce cycle. Dans ce cas, vous avez chargé un seul fichier pour chaque cycle durant l’apprentissage.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Appelez `download()` sur l’objet cycle, en spécifiant le nom du fichier de modèle à télécharger. Par défaut, cette fonction télécharge vers le répertoire actif.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Sautez cette section si vous prévoyez d’exécuter d’autres tutoriels Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Arrêter la machine virtuelle Notebook

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Tout supprimer

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce didacticiel, vous avez accompli les tâches suivantes :

> [!div class="checklist"]
> * Connexion de votre espace de travail et création d’une expérience
> * Chargement de données et apprentissage des modèles scikit-Learn
> * Affichage des résultats de l’apprentissage sur le portail et récupération des modèles

[Déployez votre modèle](tutorial-deploy-models-with-aml.md) avec Azure Machine Learning.
Découvrez comment développer des expériences de [Machine Learning automatisé](tutorial-auto-train-models.md).
