---
title: Exécuter une image Azure Container Registry à partir d’une application
description: Utilisez la commande AZ ACR Pack Build pour créer une image conteneur à partir d’une application et effectuer une transmission de type envoi (push) vers Azure Container Registry, sans utiliser de fichier dockerfile.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/06/2019
ms.author: danlep
ms.openlocfilehash: 4e41bcaff8faef2c4eaec9ae852955d4b7ce354b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839898"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Générer et envoyer (push) une image à partir d’une application à l’aide d’un Cloud Native Buildpack

La commande Azure CLI `az acr pack build` utilise l'outil CLI [`pack`](https://github.com/buildpack/pack), de [Buildpacks](https://buildpacks.io/), pour générer une application et envoyer son image dans un registre de conteneurs Azure. Cette fonctionnalité fournit une option permettant de créer rapidement une image conteneur à partir du code source de votre application dans Node.js, Java et d’autres langages sans avoir à définir un fichier dockerfile.

Pour utiliser les exemples de cet article, vous pouvez utiliser Azure Cloud Shell ou une installation locale d’Azure CLI. Si vous souhaitez l’utiliser en local, la version 2.0.70 ou ultérieure est requise. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="use-the-build-command"></a>Utiliser la commande Build

Pour générer et envoyer (push) une image de conteneur à l’aide de Cloud Native Buildpacks, exécutez la commande [az acr pack build][az-acr-pack-build]. Tandis que la commande [az acr build][az-acr-build] génère et envoie (push) une image à partir d’une source fichier dockerfile et du code associé, avec `az acr pack build` vous spécifiez directement une arborescence source d’application.

Au minimum, spécifiez les éléments suivants lorsque vous exécutez `az acr pack build` :

* Un registre de conteneurs Azure dans lequel vous exécutez la commande
* Un nom d’image et étiquette pour l’image résultante
* L’un des [emplacements de contexte pris en charge](container-registry-tasks-overview.md#quick-task) pour les tâches ACR, comme un répertoire local, un GitHub référentiel ou un tarball distant
* Le nom d’une image du générateur Buildpack, telle que `cloudfoundry/cnb:bionic`.  

`az acr pack build` prend en charge d’autres fonctionnalités des commandes de tâches ACR, notamment les [variables d’exécution](container-registry-tasks-reference-yaml.md#run-variables) et les [journaux d’exécution des tâches](container-registry-tasks-overview.md#view-task-logs) qui sont diffusés en continu et également enregistrés pour une récupération ultérieure.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemple : Créer une image Node.js avec Cloud Foundry Builder

L’exemple suivant crée une image conteneur à partir de l’application Node.js dans le référentiel [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world), à l’aide du générateur `cloudfoundry/cnb:bionic` :

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Cet exemple génère l'image `node-app` avec la balise `1.0` et l'envoie (push) au registre de conteneurs *myregistry*. Ici, le nom du registre cible est explicitement ajouté au nom de l’image. Si cette valeur n’est pas spécifiée, l’URL du registre est automatiquement ajoutée au nom de l’image.

Le paramètre `--pull` spécifie que la commande extrait la dernière image du générateur.

La sortie de la commande affiche la progression de la génération et de l’envoi de l’image. 

Une fois l’image créée, vous pouvez l’exécuter avec Docker, si vous l’avez installé. Connectez-vous d’abord à votre registre :

```azurecli
az acr login --name myregistry
```

Exécuter l’image :

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Accédez à `localhost:1337` dans votre navigateur favori pour voir l’exemple d’application Web. Appuyez sur `[Ctrl]+[C]` pour arrêter le conteneur.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemple : Créer une image Java avec Heroku Builder

L’exemple suivant génère une image de conteneur à partir de l’application Java dans le référentiel [buildpack/sample-java-App](https://github.com/buildpack/sample-java-app), à l’aide du générateur `heroku/buildpacks:18` :

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Cet exemple génère l'image `java-app` marquée avec l’ID d’exécution de la commande et l'envoie (push) au registre de conteneurs *myregistry*.

Le paramètre `--pull` spécifie que la commande extrait la dernière image du générateur.

La sortie de la commande affiche la progression de la génération et de l’envoi de l’image. 

Une fois l’image créée, vous pouvez l’exécuter avec Docker, si vous l’avez installé. Connectez-vous d’abord à votre registre :

```azurecli
az acr login --name myregistry
```

Exécutez l’image en remplaçant la balise d'image pour *runid* :

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Accédez à `localhost:8080` dans votre navigateur favori pour voir l’exemple d’application Web. Appuyez sur `[Ctrl]+[C]` pour arrêter le conteneur.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez généré et envoyé une image conteneur avec `az acr pack build`, vous pouvez la déployer comme n’importe quelle image vers une cible de votre choix. Les options de déploiement Azure incluent l'exécution de cette opération dans [App Service](../app-service/containers/tutorial-custom-docker-image.md) ou le [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), entre autres.

Pour plus d’informations sur les fonctionnalités des tâches ACR, consultez [Automatiser les builds d’image de conteneur et la maintenance avec les tâches ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
