---
title: Modifier les clés d’accès au compte de stockage
titleSuffix: Azure Machine Learning service
description: Découvrez comment modifier les clés d’accès du compte Stockage Azure utilisé par votre espace de travail. Le service Azure Machine Learning utilise un compte Stockage Azure pour stocker les données et les modèles. Lorsque vous régénérez la clé d’accès pour le compte de stockage, vous devez mettre à jour le service Azure Machine Learning pour utiliser les nouvelles clés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/16/2019
ms.openlocfilehash: e386e34a8326a51753631ee9ea4215d01ba7ceb3
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558229"
---
# <a name="regenerate-storage-account-access-keys"></a>Régénérer des clés d’accès de compte de stockage

Découvrez comment modifier les clés d’accès des comptes Stockage Azure utilisées par votre service Azure Machine Learning. Azure Machine Learning peut utiliser des comptes de stockage pour stocker des données ou des modèles formés.

Pour des raisons de sécurité, vous devrez peut-être modifier les clés d’accès d’un compte Stockage Azure. Lorsque vous régénérez la clé d’accès, Azure Machine Learning doit être mis à jour pour utiliser la nouvelle clé. Azure Machine Learning peut utiliser le compte de stockage à la fois pour le stockage des modèles et comme banque de données.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez l’article [Créer un espace de travail](how-to-manage-workspace.md).

* Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [L’extension CLI Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Éléments à mettre à jour

Les comptes de stockage peuvent être utilisés par l’espace de travail du service Azure Machine Learning (stockage des journaux, modèles, snapshots, etc.) et comme banque de données. Le processus de mise à jour de l’espace de travail est une commande unique de l’interface de ligne de commande Azure et peut être exécuté après la mise à jour de la clé de stockage. Le processus de mise à jour des banques de données est plus complexe et nécessite de découvrir quelles banques de données utilisent actuellement le compte de stockage, puis de les réinscrire.

> [!IMPORTANT]
> Mettez à jour l’espace de travail à l’aide de l’interface de ligne de commande Azure, et les banques de données à l’aide de Python simultanément. La mise à jour de l’un ou l’autre n’est pas suffisante et peut entraîner des erreurs tant que les deux ne sont pas mis à jour.

Pour découvrir les comptes de stockage qui sont utilisés par vos banques de données, utilisez le code suivant :

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ce code recherche les banques de données inscrites qui utilisent Stockage Azure et répertorie les informations suivantes :

* Nom de la banque de données : nom de la base de données sous laquelle le compte de stockage est inscrit.
* Nom du compte de stockage : Nom du compte Stockage Azure.
* Conteneur : conteneur dans le compte de stockage qui est utilisé par cette inscription.

Il indique également si le magasin de stockage est destiné à un objet blob Azure ou à un partage de fichiers Azure, car il existe différentes méthodes pour réinscrire chaque type de magasin de stockage.

S’il existe une entrée pour le compte de stockage dont vous prévoyez de régénérer les clés d’accès, enregistrez le nom de la banque de données, le nom du compte de stockage et le nom du conteneur.

## <a name="update-the-access-key"></a>Mettre à jour la clé d’accès

Pour mettre à jour le service Azure Machine Learning afin d’utiliser la nouvelle clé, procédez comme suit :

> [!IMPORTANT]
> Effectuez toutes les étapes, en mettant à jour l’espace de travail à l’aide de l’interface de ligne de commande et les bases de données à l’aide de Python. La mise à jour de l’un ou l’autre peut entraîner des erreurs tant que les deux ne sont pas mis à jour.

1. Générez à nouveau la clé. Pour plus d’informations sur la régénération d’une clé d’accès, consultez l’article [Gestion du compte de stockage](/azure/storage/common/storage-account-manage#access-keys). Enregistrez la nouvelle clé.

1. Pour mettre à jour l’espace de travail afin d’utiliser la nouvelle clé, procédez comme suit :

    1. Pour vous connecter à l’abonnement Azure qui contient votre espace de travail en utilisant la commande Azure CLI suivante :

        ```azurecli-interactive
        az login
        ```

    1. Pour mettre à jour l’espace de travail afin d’utiliser la nouvelle clé, utilisez la commande suivante. Remplacez `myworkspace` par le nom de votre espace de travail Azure Machine Learning et remplacez `myresourcegroup` par le nom du groupe de ressources Azure qui contient l’espace de travail.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        Cette commande synchronise automatiquement les nouvelles clés du compte de stockage Azure utilisé par l’espace de travail.

1. Pour réinscrire les banques de données qui utilisent le compte de stockage, utilisez les valeurs de la section [Éléments à mettre à jour](#whattoupdate) et la clé de l’étape 1 avec le code suivant :

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Puisque `overwrite=True` est spécifié, ce code écrase l’inscription existante et la met à jour pour utiliser la nouvelle clé.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’inscription des banques de données, consultez la documentation de référence de la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py).
