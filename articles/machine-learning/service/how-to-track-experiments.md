---
title: Consigner les métriques pendant les exécutions d’entraînement
titleSuffix: Azure Machine Learning service
description: Vous pouvez effectuer le suivi de vos expérimentations et superviser les métriques pour améliorer le processus de création de modèle. Découvrez comment ajouter la journalisation à votre script d’entraînement, envoyer l’expérimentation, vérifier la progression d’une tâche en cours d’exécution et afficher les résultats consignés d’une exécution.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: edc0da77fc1c2813c2485fca18d50952e3060db8
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370471"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Consigner les métriques pendant les exécutions d’entraînement dans Azure Machine Learning

Améliorez le processus de création de modèle avec le suivi de vos expériences et la supervision des métriques. Dans cet article, découvrez comment ajouter la journalisation à votre script d’entraînement, soumettre une exécution d’expérience, surveiller l’exécution et afficher les résultats d’une exécution dans le service Azure Machine Learning.

## <a name="list-of-training-metrics"></a>Liste des métriques d’entraînement 

Les métriques suivantes peuvent être ajoutées à une exécution pendant l’entraînement d’une expérimentation. Pour afficher une liste plus détaillée des éléments qui peuvent être suivis lors d’une exécution, consultez la [documentation de référence sur l’exécution de la classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Fonction Python | Notes|
|----|:----|:----|
|Valeurs scalaires |Fonction :<br>`run.log(name, value, description='')`<br><br>Exemple :<br>run.log("accuracy", 0.95) |Journalisez une valeur numérique ou de chaîne dans l’exécution avec le nom donné. La journalisation d’une métrique dans une exécution entraîne le stockage de cette métrique dans l’enregistrement d’exécution dans l’expérimentation.  Vous pouvez consigner la même métrique plusieurs fois pendant une exécution, le résultat étant considéré comme un vecteur de cette métrique.|
|Listes|Fonction :<br>`run.log_list(name, value, description='')`<br><br>Exemple :<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Journalisez une liste de valeurs dans l’exécution avec le nom donné.|
|Ligne|Fonction :<br>`run.log_row(name, description=None, **kwargs)`<br>Exemple :<br>run.log_row("Y over X", x=1, y=0.4) | L’utilisation de *log_row* crée une métrique avec plusieurs colonnes, comme décrit dans kwargs. Chaque paramètre nommé génère une colonne avec la valeur spécifiée.  Vous pouvez appeler *log_row* une seule fois pour consigner un tuple arbitraire ou plusieurs fois dans une boucle pour générer une table complète.|
|Table|Fonction :<br>`run.log_table(name, value, description='')`<br><br>Exemple :<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Journalisez un objet dictionnaire dans l’exécution avec le nom donné. |
|Images|Fonction :<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemple :<br>`run.log_image("ROC", plt)` | Journalisez une image dans l’enregistrement d’exécution. Utilisez log_image pour consigner un fichier image ou un tracé matplotlib dans l’exécution.  Ces images seront visibles et comparables dans l’enregistrement d’exécution.|
|Étiqueter une exécution|Fonction :<br>`run.tag(key, value=None)`<br><br>Exemple :<br>run.tag("selected", "yes") | Étiquetez l’exécution avec une clé de chaîne et une valeur de chaîne facultative.|
|Charger un fichier ou un répertoire|Fonction :<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemple :<br>run.upload_file("best_model.pkl", "./model.pkl") | Chargez un fichier sur l’enregistrement d’exécution. Les exécutions capturent automatiquement le fichier dans le répertoire de sortie spécifié, par défaut « ./outputs » pour la plupart des types d’exécutions.  Utilisez upload_file uniquement quand des fichiers supplémentaires doivent être chargés ou qu’aucun répertoire de sortie n’est spécifié. Nous vous suggérons d’ajouter `outputs` au nom afin qu’il soit chargé sur le répertoire outputs. Vous pouvez répertorier tous les fichiers qui sont associés à cet enregistrement d’exécution en appelant `run.get_file_names()`|

> [!NOTE]
> Les métriques pour les valeurs scalaires, listes, lignes et tables peuvent être de type float, integer ou string.

## <a name="start-logging-metrics"></a>Démarrer les métriques de journalisation

Si vous souhaitez suivre ou superviser votre expérimentation, vous devez ajouter du code pour démarrer la journalisation quand vous envoyez l’exécution. Voici comment déclencher l’envoi d’exécution :
* __Run.start_logging__ : ajoutez des fonctions de journalisation à votre script d’entraînement et démarrez une session de journalisation interactive dans l’expérimentation spécifiée. **start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.
* __ScriptRunConfig__ : ajoutez des fonctions de journalisation à votre script d’entraînement et chargez l’intégralité du dossier de script avec l’exécution.  **ScriptRunConfig** est une classe pour la définition des configurations pour les exécutions de script. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.

## <a name="set-up-the-workspace"></a>Configurer l’espace de travail
Avant d’ajouter la journalisation et d’envoyer une expérience, vous devez configurer l’espace de travail.

1. Chargez l’espace de travail. Pour en savoir plus sur la définition de la configuration de l’espace de travail, suivez les étapes sous [Créer un espace de travail Azure Machine Learning service](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Option 1 : Utiliser start_logging

**start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.

L’exemple suivant entraîne un simple modèle Ridge sklearn localement dans un notebook Jupyter local. Pour en savoir plus sur l’envoi d’expérimentations dans différents environnements, consultez [Configurer des cibles de calcul pour l’entraînement de modèle avec le service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Créez un script d’entraînement dans un notebook Jupyter local. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Ajoutez le suivi d’expérimentation à l’aide du kit SDK du service Azure Machine Learning et chargez un modèle persistant sur l’enregistrement d’exécution de l’expérimentation. Le code suivant ajoute des balises, des journaux d’activité, puis charge un fichier de modèle sur l’exécution de l’expérimentation.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Le script se termine par ```run.complete()```, ce qui marque l’exécution comme terminée.  Cette fonction est généralement utilisée dans les scénarios de notebooks interactifs.

## <a name="option-2-use-scriptrunconfig"></a>Option 2 : Utiliser ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) est une classe destinée à la définition des configurations pour les exécutions de script. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.

Cet exemple s’appuie sur le modèle Ridge sklearn de base ci-dessus. Il effectue un simple balayage des paramètres sur les valeurs alpha du modèle pour capturer les métriques et les modèles entraînés dans les exécutions sous l’expérimentation. L’exemple est exécuté localement dans un environnement géré par l’utilisateur. 

1. Créez un script d’entraînement `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Le script `train.py` fait référence à `mylib.py`, qui permet d’obtenir la liste des valeurs alpha à utiliser dans le modèle Ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configurez un environnement local géré par l’utilisateur.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Envoyez le script ```train.py``` à exécuter dans l’environnement géré par l’utilisateur. L’intégralité de ce dossier de script est envoyée pour l’entraînement, dont le fichier ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Gérer une exécution

L’article [Start, monitor and cancel training runs (Démarrer, surveiller et annuler des exécutions d’entraînement)](how-to-manage-runs.md) met en avant les flux de travail Azure Machine Learning spécifiques pour la gestion de vos expériences.

## <a name="view-run-details"></a>Afficher les détails de l’exécution

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Superviser l’exécution avec le widget de notebook Jupyter
Quand vous utilisez la méthode **ScriptRunConfig** pour envoyer des exécutions, vous pouvez vérifier la progression de l’exécution avec un [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Comme l’envoi de l’exécution, le widget est asynchrone et fournit des mises à jour automatiques toutes les 10 à 15 secondes jusqu’à ce que la tâche soit terminée.

1. Affichez le widget Jupyter en attendant la fin de l’exécution.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Capture d’écran du widget de notebook Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Vous pouvez également obtenir un lien vers le même affichage dans votre espace de travail.

```python
print(run.get_portal_url())
```

2. **[Pour l’exécution de Machine Learning automatisé]**  Pour accéder aux graphiques à partir d’une exécution précédente. Remplacez `<<experiment_name>>` par le nom d’une expérience appropriée :

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de notebook Jupyter pour le Machine Learning automatisé](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Pour afficher d’autres détails sur un pipeline, cliquez sur le pipeline que vous souhaitez explorer dans la table. Les graphiques apparaissent dans une fenêtre contextuelle du Portail Azure.

### <a name="get-log-results-upon-completion"></a>Obtenir des résultats du journal lors de l’achèvement

L’entraînement et la supervision du modèle se produisent en arrière-plan afin que vous puissiez exécuter d’autres tâches pendant que vous attendez. Vous pouvez également attendre que l’entraînement du modèle soit terminé avant d’exécuter davantage de code. Quand vous utilisez **ScriptRunConfig**, vous pouvez employer ```run.wait_for_completion(show_output = True)``` pour indiquer quand l’entraînement du modèle est terminé. L’indicateur ```show_output``` vous donne une sortie détaillée. 


### <a name="query-run-metrics"></a>Interroger les métriques d’exécution

Vous pouvez afficher les métriques d’un modèle entraîné à l’aide de ```run.get_metrics()```. Vous pouvez désormais obtenir toutes les métriques qui ont été consignées dans l’exemple ci-dessus pour déterminer le meilleur modèle.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Afficher l’expérimentation dans le portail Azure

Une fois l’exécution d’une expérimentation terminée, vous pouvez accéder à l’enregistrement d’exécution de l’expérimentation. Vous pouvez accéder à l’historique de deux manières :

* Obtenez l’URL vers l’exécution directement ```print(run.get_portal_url())```
* Affichez les détails de l’exécution en envoyant son nom (dans ce cas, ```run```). Cette méthode vous oriente vers le nom de l’expérience, l’ID, le type, l’état, la page de détails, un lien vers le Portail Azure et un lien vers la documentation.

Le lien pour l’exécution vous amène directement à la page de détails de l’exécution dans le portail Azure. Ici, vous pouvez voir les propriétés, les métriques suivies, les images et les graphiques consignés dans l’expérimentation. Dans ce cas, nous avons consigné MSE et les valeurs alpha.

  ![Détails de l’exécution dans le Portail Azure](./media/how-to-track-experiments/run-details-page.png)

Vous pouvez également afficher les sorties ou les journaux d’activité de l’exécution, ou télécharger la capture instantanée de l’expérimentation que vous avez envoyée afin de pouvoir partager le dossier de l’expérimentation avec d’autres utilisateurs.

### <a name="viewing-charts-in-run-details"></a>Affichage de graphiques dans des détails d’exécution

Il existe différentes manières d’utiliser les API de journalisation pour enregistrer différents types de métriques en cours d’exécution, et les afficher sous forme de graphiques dans le portail Azure. 

|Valeur connectée|Exemple de code| Afficher dans le portail|
|----|----|----|
|Journaliser un tableau de valeurs numériques| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|graphique en courbes à variable unique|
|Journaliser une valeur numérique avec le même nom de métrique utilisé à plusieurs reprises (comme à l’intérieur d’une boucle for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Graphique en courbes à variable unique|
|Journaliser une ligne avec 2 colonnes numériques à plusieurs reprises|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Graphique en courbes à deux variables|
|Journaliser un table avec 2 colonnes numériques|`run.log_table(name='Sine Wave', value=sines)`|Graphique en courbes à deux variables|


## <a name="example-notebooks"></a>Exemples de notebooks
Les notebooks suivants illustrent les concepts de cet article :
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Essayez de suivre les étapes suivantes pour savoir comment utiliser le kit SDK Azure Machine Learning pour Python :

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

* Découvrez comment [entraîner des modèles PyTorch avec Azure Machine Learning](how-to-train-pytorch.md).
