---
title: "Didacticiel Kubernetes sur Azure - Déployer une application | Microsoft Docs"
description: "Didacticiel ACS - Déployer une application"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 73b943709fb3846058d4b1c09bc76b09460855ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Exécuter des applications dans Azure Container Service (ACS)

Dans ce didacticiel (quatrième d’une série de huit), un exemple d’application est déployé dans un cluster Kubernetes. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Mise à jour des fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Test de l'application

Dans les didacticiels suivants, cette application est mise à l’échelle et Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

Ce didacticiel suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. 

Pour effectuer ce didacticiel, vous avez besoin du fichier manifeste Kubernetes `azure-vote-all-in-one-redis.yml`. Ce fichier a été téléchargé avec le code source de l’application dans un didacticiel précédent. Vérifiez que vous avez cloné le référentiel et que vous avez modifié des répertoires dans le référentiel cloné.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Mettre à jour le fichier manifeste

Dans ce didacticiel, Azure Container Registry (ACR) a été utilisé pour stocker une image conteneur. Avant d’exécuter l’application, le nom de serveur de connexion ACR doit être mis à jour dans le fichier manifeste Kubernetes.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Le fichier manifeste a été précréé avec le nom de serveur de connexion `microsoft`. Ouvrez le fichier avec un éditeur de texte. Dans cet exemple, le fichier est ouvert avec `vi`.

```console
vi azure-vote-all-in-one-redis.yml
```

Remplacez `microsoft` par le nom de serveur de connexion ACR. Cette valeur se trouve sur la ligne **47** du fichier manifeste.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Enregistrez et fermez le fichier.

## <a name="deploy-application"></a>Déployer l’application

Utilisez la commande [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) pour exécuter l’application. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Tester l’application

Un [service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) est créé et expose l’application à Internet. Ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.7/#get) avec l’argument `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Au début, *EXTERNAL-IP* pour le service *azure-vote-front* apparaît *En attente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Une fois que l’adresse *EXTERNAL-IP* est passée du statut *En attente* à *Adresse IP*, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Pour afficher l’application, accédez à l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application de vote Azure a été déployée sur un cluster Kubernetes dans ACS. Les tâches accomplies sont les suivantes :  

> [!div class="checklist"]
> * Téléchargement des fichiers manifeste Kubernetes
> * Exécution de l’application dans Kubernetes
> * Test de l’application

Passez au didacticiel suivant pour en savoir plus sur la mise à l’échelle d’une application Kubernetes et de l’infrastructure Kubernetes sous-jacente. 

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes](./tutorial-kubernetes-scale.md)
