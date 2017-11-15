---
title: "Didacticiel Azure Container Instances - Préparer votre application"
description: "Préparer une application pour le déploiement vers Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5de53266e1dbadecb9fabb1649615fa9f4ba8b5f
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Créer un conteneur à déployer dans Azure Container Instances

Azure Container Instances permet de déployer des conteneurs Docker sur l’infrastructure Azure sans configurer de machines virtuelles ni adopter de service de niveau supérieur. Dans ce didacticiel, vous allez générer une petite application web dans Node.js et vous allez l’empaqueter dans un conteneur pouvant être exécuté avec Azure Container Instances. Le programme est le suivant :

> [!div class="checklist"]
> * Le clonage de la source de l’application à partir de GitHub
> * la création des images de conteneur à partir de la source de l’application
> * Le test des images dans un environnement Docker local

Dans les didacticiels suivants, vous chargez votre image dans un conteneur Azure Container Registry, et vous la déployez dans Azure Container Instances.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.20 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

Ce didacticiel présuppose une compréhension de base des concepts Docker essentiels, tels que les conteneurs, les images de conteneur et les commandes `docker` de base. Si besoin, consultez [Bien démarrer avec Docker]( https://docs.docker.com/get-started/) pour apprendre les principes de base des conteneurs.

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Par conséquent, nous recommandons une installation locale de l’interface Azure CLI et de l’environnement de développement Docker.

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple dans ce didacticiel inclut une application web basique générée dans [Node.js](http://nodejs.org). L’application se présente sous forme de page HTML statique et ressemble à ceci :

![Application du didacticiel affichée dans le navigateur][aci-tutorial-app]

Utilisez Git pour télécharger l’exemple :

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Générer l’image de conteneur

Le fichier Dockerfile fourni dans le référentiel de l’exemple montre comment le conteneur est généré. La génération du conteneur commence par une [image officielle Node.js][dockerhub-nodeimage] sur [Linux Alpine](https://alpinelinux.org/), une petite distribution parfaitement adaptée aux conteneurs. Les fichiers d’application sont ensuite copiés dans le conteneur, les dépendances sont installées à l’aide de Node Package Manager, et l’application démarre.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Utilisez la commande `docker build` pour créer l’image de conteneur, et balisez-la *aci-tutorial-app* :

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Le résultat de la commande `docker build` est similaire à ce qui suit (tronqué pour une meilleure lisibilité) :

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Utilisez `docker images` pour voir l’image ainsi générée :

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Exécutez localement le conteneur

Avant d’essayer de déployer le conteneur dans Azure Container Instances, exécutez-le localement pour vous assurer qu’il fonctionne. Le commutateur `-d` permet l’exécution du conteneur en arrière-plan, tandis que `-p` vous permet de mapper un port arbitraire sur votre ordinateur vers le port 80 du conteneur.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Allez à l’adresse http://localhost:8080 dans votre navigateur pour confirmer l’exécution du conteneur.

![Exécution locale de l’application dans le navigateur][aci-tutorial-app-local]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une image de conteneur qui peut être déployée dans Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Clonage de la source de l’application à partir de GitHub
> * Création des images de conteneur à partir de la source de l’application
> * Test de l’exécution locale du conteneur

Passez au didacticiel suivant pour en savoir plus sur le stockage d’images de conteneur dans Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png