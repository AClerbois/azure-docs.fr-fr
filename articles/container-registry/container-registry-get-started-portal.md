---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec le portail Azure"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec le portail Azure."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 033ef0cdd607ced5de6c975e071e0ce37e677201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Créer un registre de conteneur à l’aide du portail Azure

Un registre Azure Container Registry est un registre Docker privé dans Azure, dans lequel vous pouvez stocker et gérer vos images conteneurs Docker privées. Dans ce guide de démarrage rapide, vous créez un registre de conteneurs avec le portail Azure.

Pour suivre cette procédure de démarrage rapide, Docker doit être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse https://portal.azure.com.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Sélectionnez **Nouveau** > **Conteneurs** > **Azure Container Registry**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-01]

Entrez les valeurs **Nom du registre** et **Groupe de ressources**. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Créez un groupe de ressources nommé `myResourceGroup`, et pour **Référence SKU**, sélectionnez « Classique ». Sélectionnez **Créer** pour déployer l’instance ACR.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-03]

Azure Container Registry est disponible dans plusieurs références SKU : `Classic`, `Basic`, `Standard` et `Premium`. Bien que `Basic`, `Standard` et `Premium` offrent des fonctions avancées telles que le stockage géré et les Webhooks, elles sont actuellement en préversion et ne sont pas disponibles dans certaines régions Azure. Pour ce démarrage rapide, nous sélectionnons la référence SKU `Classic`, car elle est disponible dans toutes les régions.

Lorsque le message **Déploiement réussi** s’affiche, sélectionnez le registre de conteneurs dans le portail, puis sélectionnez **Clés d’accès**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-05]

Sous **Utilisateur administrateur**, sélectionnez **Activer**. Notez les valeurs suivantes :

* Serveur de connexion
* Nom d’utilisateur
* password

Vous utilisez ces valeurs dans les étapes suivantes lorsque vous travaillez avec votre registre dans l’interface de ligne de commande Docker.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/). Remplacez le *nom d’utilisateur*, le *mot de passe* et le *serveur de connexion* par les valeurs notées à l’étape précédente.

```
docker login --username <username> --password <password> <login server>
```

Après son exécution, la commande retourne le message Login Succeeded (Connexion réussie).

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans votre registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si nécessaire, exécutez la commande suivante pour extraire une image existante du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Avant d’envoyer l’image dans votre registre, vous devez l’étiqueter avec le nom du serveur de connexion à l’ACR. Étiquetez l’image en utilisant la commande [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Remplacez la valeur du *serveur de connexion* par le nom du serveur de connexion enregistré précédemment.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/) pour envoyer l’image à l’instance ACR. Remplacez la valeur du *serveur de connexion* par le nom du serveur de connexion de votre instance ACR.

```
docker push <login server>/aci-helloworld:v1
```

La sortie d’une commande `docker push` réussie ressemble à :

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Répertorier les images conteneur

Pour répertorier les images de votre instance ACR, accédez à votre registre dans le portail, sélectionnez **Référentiels**, puis sélectionnez le référentiel que vous avez créé avec `docker push`.

Dans cet exemple, nous sélectionnons le référentiel **aci-helloworld** et nous pouvons voir l’image étiquetée `v1` sous **BALISES**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-09]

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources **myResourceGroup**. Cela supprimera le groupe de ressources, l’instance ACR et toutes les images conteneurs.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-08]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre Azure Container Registry avec Azure CLI. Si vous souhaitez utiliser Azure Container Registry avec des instances Azure Container Instances, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiels Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png