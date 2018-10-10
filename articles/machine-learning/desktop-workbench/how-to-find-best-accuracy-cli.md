---
title: Recherche d’exécutions présentant la précision maximale et la durée minimale dans Azure Machine Learning Workbench | Microsoft Docs
description: Cas d’usage de bout en bout permettant de trouver la meilleure précision par le biais de l’interface de ligne de commande à l’aide d’Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 094fd6d8c6c6d647533cf5409d1a85283c71c80e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953240"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Trouvez des exécutions présentant la précision maximale et la durée minimale
Dans le cas de plusieurs exécutions, l’un des cas d’usage consiste à rechercher les exécutions qui présentent la précision maximale. Une approche possible consiste à utiliser l’interface de ligne de commande (CLI) avec une requête [JMESPath](http://jmespath.org/). Pour plus d’informations sur l’utilisation de JMESPath dans l’interface de ligne de commande Azure, consultez [Utiliser des requêtes JMESPath avec Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). Dans l’exemple ci-après, quatre exécutions sont créés avec des valeurs de précision de 0, 0,98, 1 et 1. Les exécutions sont filtrées si elles figurent dans la plage `[MaxAccuracy-Threshold, MaxAccuracy]`, où `Threshold = .03`.

## <a name="sample-data"></a>Exemples de données
Si vous ne disposez d’aucune exécution dotée d’une valeur `Accuracy`, la procédure ci-après générera des exécutions pour l’interrogation.

Commencez par créer un fichier Python dans Azure Machine Learning Workbench. Nommez-le `log_accuracy.py`, puis collez le code suivant dans le fichier :
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Ensuite, créez un fichier `run.py` et collez-y le code suivant :
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Enfin, ouvrez l’interface de ligne de commande par le biais de Workbench et exécutez la commande `python run.py` pour soumettre quatre expériences. Une fois le script terminé, quatre exécutions supplémentaires doivent apparaître dans l’onglet `Run History`.

## <a name="query-the-run-history"></a>Interrogation de l’historique d’exécution
La première commande recherche la valeur de précision maximale.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

À l’aide de cette valeur de précision maximale de `1` et d’une valeur de seuil de `0.03`, la seconde commande filtre les exécutions au moyen de `Accuracy`, puis trie les exécutions par élément `duration` croissant.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Si vous voulez effectuer une vérification de limite supérieure stricte, le format de requête est le suivant : ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Si vous utilisez PowerShell, le code ci-après utilise les variables locales pour stocker le seuil et la précision maximale :
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la journalisation, consultez l’article [Guide pratique pour utiliser l’historique des exécutions et les métriques de modèles dans Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
