---
title: 'Démarrage rapide : Lancer une application Java Spring en utilisant Azure CLI'
description: Dans ce guide de démarrage rapide, vous déployez un exemple d’application sur Azure Spring Cloud en utilisant Azure CLI.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 3bc1bfcf58d622151f0af9c6da693c5533bcf966
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721611"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Démarrage rapide : Lancer une application Java Spring en utilisant Azure CLI

Azure Spring Cloud vous permet d’exécuter facilement une application de microservices basée sur Spring Boot sur Azure.

Ce guide de démarrage rapide vous montre comment déployer une application Spring Cloud Java sur Azure. Quand vous aurez terminé, vous pourrez continuer à gérer l’application via Azure CLI ou passer à l’utilisation du portail Azure.

Dans ce guide de démarrage rapide, vous découvrez comment :

> [!div class="checklist"]
> * Provisionner une instance de service
> * Définir un serveur de configuration pour une instance
> * Générer localement une application de microservices
> * Déployer chaque microservice
> * Affecter un point de terminaison public pour votre application

## <a name="prerequisites"></a>Prérequis

>[!Note]
> Azure Spring Cloud est actuellement disponible en préversion publique. Les offres de préversion publique permettent aux clients de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services de la préversion publique ne sont pas destinés à une utilisation de production.  Pour en savoir plus sur le support offert lors des préversions, consultez notre [FAQ](https://azure.microsoft.com/support/faq/) ou soumettez une [Demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Pour en savoir plus, [lisez notre documentation sur Azure Cloud Shell](../cloud-shell/overview.md).

Pour suivre ce guide de démarrage rapide :

1. [Installez Git](https://git-scm.com/)
2. [Installez JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installez Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
4. [Installez Azure CLI version 2.0.67 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installez l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Provisionnez une instance de service sur Azure CLI

1. Connectez-vous à Azure CLI et choisissez votre abonnement actif. Veillez à choisir l’abonnement actif qui est dans la liste verte pour Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Préparez un nom pour votre service Azure Spring Cloud.  Le nom doit comporter entre 4 et 32 caractères, et contenir uniquement des lettres minuscules, des chiffres et des traits d’union.  Le premier caractère du nom du service doit être une lettre, et le dernier doit être une lettre ou un chiffre.

3. Créez un groupe de ressources destiné à contenir votre service Azure Spring Cloud.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).

4. Ouvrez une fenêtre Azure CLI et exécutez les commandes suivantes pour provisionner une instance d’Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    Le déploiement de l’instance de service prend environ cinq minutes.

5. Définissez le nom du groupe de ressources et le nom du cluster par défaut en utilisant les commandes suivantes :

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>Configurer votre serveur de configuration

Mettez à jour votre serveur de configuration avec l’emplacement du dépôt Git pour notre projet :

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>Générer localement les applications de microservices

1. Créez un dossier et clonez le dépôt de l’exemple d’application sur votre compte Cloud Azure.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Changez de répertoire et générez le projet.

    ```azurecli
        cd piggymetrics
        mvn clean package -D skipTests
    ```

La compilation du projet prend environ 5 minutes.  Une fois l’opération terminée, vous devez avoir des fichiers JAR individuels pour chaque service dans leurs dossiers respectifs.

## <a name="create-the-microservices"></a>Créer les microservices

Créez des microservices Spring Cloud en utilisant les fichiers JAR générés à l’étape précédente. Vous allez créer trois microservices : **gateway**, **auth-service** et **account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Déployer des applications et définir des variables d’environnement

Nous devons déployer nos applications sur Azure. Utilisez les commandes suivantes pour déployer les trois applications :

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Affecter un point de terminaison public à la passerelle

Nous avons besoin d’un moyen d’accéder à l’application via un navigateur web. Notre application de passerelle a besoin d’un point de terminaison public, qui peut être affecté avec la commande suivante :

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Enfin, interrogez l’application de **passerelle** pour obtenir son adresse IP publique, afin de pouvoir vérifier que l’application est en cours d’exécution :

```azurecli
az spring-cloud app show --name gateway | grep url
```

Accédez à l’URL fournie par la commande précédente pour voir l’application PiggyMetrics en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une application Spring Cloud à partir d’Azure CLI.  Pour découvrir plus d’informations sur Azure Spring Cloud, passez au tutoriel sur la préparation de votre application pour le déploiement.

> [!div class="nextstepaction"]
> [Préparer votre application Azure Spring Cloud pour le déploiement](spring-cloud-tutorial-prepare-app-deployment.md)
