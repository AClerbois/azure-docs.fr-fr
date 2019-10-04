---
title: "Démarrage rapide : Créer un cluster Apache Spark dans Azure HDInsight à l'aide d'Azure CLI"
description: Ce guide de démarrage rapide explique comment utiliser l'interface de ligne de commande Azure (Azure CLI) pour créer un cluster Apache Spark dans Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 72bdab9d7fb5c3019d97ffc4c92257c49ec2b8e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066242"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Démarrage rapide : créer un cluster Apache Spark dans Azure HDInsight à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous allez apprendre à créer un cluster Apache Spark dans Azure HDInsight à l'aide d'Azure CLI. Apache Spark permet une analytique des données et des calculs sur cluster rapides à l’aide du traitement en mémoire. [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) est l'interface de ligne de commande multiplateforme de Microsoft dédiée à la gestion des ressources Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Azure CLI. Si vous n'avez pas installé Azure CLI, consultez [Installer l'interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pour connaître les étapes à suivre.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Créer un cluster Apache Spark

1. Connectez-vous à votre abonnement Azure. Si vous prévoyez d'utiliser Azure Cloud Shell, sélectionnez simplement **Essayer** dans le coin supérieur droit du bloc de code. Sinon, entrez la commande suivante :

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Définir des variables d’environnement Dans ce guide de démarrage rapide, l'utilisation des variables repose sur Bash. De petites modifications seront nécessaires si vous utilisez d'autres environnements. Dans l'extrait de code ci-dessous, remplacez RESSOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME et PASSWORD par les valeurs de votre choix. Entrez ensuite les commandes CLI pour définir les variables d'environnement.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Créez le groupe de ressources en entrant la commande ci-dessous :

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Créez un compte de stockage Azure en entrant la commande ci-dessous :

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrayez la clé primaire du compte de stockage Azure et stockez-la dans une variable en entrant la commande ci-dessous :

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Créez un conteneur de stockage Azure en entrant la commande ci-dessous :

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Créez le cluster Apache Spark en entrant la commande suivante :

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir suivi ce guide de démarrage rapide, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Entrez tout ou partie des commandes suivantes pour supprimer des ressources :

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster Apache Spark dans Azure HDInsight à l'aide d'Azure CLI.  Passez au tutoriel suivant pour apprendre à utiliser un cluster HDInsight Spark pour exécuter des requêtes interactives sur des exemples de données.

> [!div class="nextstepaction"]
> [Exécuter des requêtes interactives sur Apache Spark](./apache-spark-load-data-run-query.md)
