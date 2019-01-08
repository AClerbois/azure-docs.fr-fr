---
title: Didacticiel Kubernetes sur Azure - Préparer une application
description: Dans ce didacticiel Azure Kubernetes Service (AKS), vous découvrez comment préparer et créer une application à plusieurs conteneurs avec Docker Compose que vous pouvez ensuite déployer sur AKS.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b529af1db6d72d87abc25eb37f2f1c39216a0ba4
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724162"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Didacticiel : Préparer une application pour Azure Kubernetes Service (AKS)

Dans ce didacticiel (le premier d’une série de sept), vous allez préparer une application à plusieurs conteneurs à son utilisation dans Kubernetes. Les outils de développement existants tels que Docker Compose servent à créer et tester en local une application. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Cloner un exemple de source de l’application à partir de GitHub
> * Créer une image conteneur à partir de l’exemple de source de l’application
> * Test l’application à plusieurs conteneurs dans un environnement Docker local

Une fois ces étapes effectuées, l’application suivante s’exécute dans votre environnement de développement local :

![Image du cluster Kubernetes sur Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

Dans d’autres tutoriels, l’image conteneur est chargée dans un registre Azure Container Registry, puis déployée dans un cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel présuppose une compréhension de base des concepts Docker essentiels, tels que les conteneurs, les images de conteneur et les commandes `docker`. Pour apprendre les principes de base des conteneurs, consultez [Bien démarrer avec Docker][docker-get-started].

Pour suivre ce tutoriel, vous avez besoin d’un environnement de développement Docker local exécutant des conteneurs Linux. Docker fournit des packages qui le configurent sur un système [Mac][docker-for-mac], [Windows][docker-for-windows] ou [Linux][docker-for-linux].

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ces didacticiels. Par conséquent, nous recommandons d’utiliser un environnement de développement Docker complet.

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple d’application utilisé dans ce didacticiel est une application de votes de base. L’application est constituée d’un composant web frontal et d’une instance Redis principale. Le composant web est empaqueté dans une image conteneur personnalisée. L’instance Redis utilise une image non modifiée de Docker Hub.

Utilisez [git][] pour cloner l’exemple d’application dans votre environnement de développement :

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Passez au répertoire cloné.

```console
cd azure-voting-app-redis
```

Dans le répertoire se trouvent le code source de l’application, un fichier Docker Compose précréé et un fichier manifeste Kubernetes. Ces fichiers sont utilisés tout au long de ce didacticiel.

## <a name="create-container-images"></a>Créer des images de conteneur

Vous pouvez utiliser [Docker Compose][docker-compose] pour automatiser la création d’images de conteneur et le déploiement d’applications à plusieurs conteneurs.

Utilisez l’exemple de fichier `docker-compose.yaml` pour créer l’image conteneur, téléchargez l’image Redis, puis démarrez l’application :

```console
docker-compose up -d
```

Une fois terminé, utilisez la commande [docker images][docker-images] pour afficher les images créées. Trois images ont été téléchargées ou créées. L’image *azure-vote-front* contient l’application de serveur frontal et utilise l’image `nginx-flask` comme base. L’image `redis` est utilisée pour démarrer une instance Redis.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Exécutez la commande [docker ps][docker-ps] pour voir les conteneurs en cours d’exécution :

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Tester l’application localement

Pour voir l’application en cours d’exécution, entrez http://localhost:8080 dans un navigateur web local. L’exemple d’application est chargée, comme indiqué dans l’exemple suivant :

![Image du cluster Kubernetes sur Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Maintenant que la fonctionnalité de l’application a été validée, les conteneurs en cours d’exécution peuvent être arrêtés et supprimés. Ne supprimez pas les images de conteneur. Dans le didacticiel suivant, l’image *azure-vote-front* est chargée dans une instance Azure Container Registry.

Arrêtez et supprimez les instances de conteneur et les ressources avec la commande [docker-compose down][docker-compose-down] :

```console
docker-compose down
```

Lorsque l’application locale a été supprimée, vous disposez d’une image Docker qui contient l’application Azure Vote, *azure-front-front*, à utiliser avec le didacticiel suivant.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, une application a été testée et les images de conteneur créées pour l’application. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Cloner un exemple de source de l’application à partir de GitHub
> * Créer une image conteneur à partir de l’exemple de source de l’application
> * Test l’application à plusieurs conteneurs dans un environnement Docker local

Passez au didacticiel suivant pour savoir comment stocker des images de conteneur dans Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
