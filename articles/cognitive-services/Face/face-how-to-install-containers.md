---
title: Installer et exécuter des conteneurs - API VISAGE
titleSuffix: Azure Cognitive Services
description: Téléchargez, installez et exécutez des conteneurs pour l’API Visage dans ce tutoriel pas à pas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 93a6d0a42d6d7f07dd8947ce9f8ae99a39d44475
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564145"
---
# <a name="install-and-run-face-containers"></a>Installer et exécuter des conteneurs d’API Visage

L’API Visage d’Azure Cognitive Services fournit un conteneur Linux standardisé pour Docker qui détecte les visages humains dans des images. Cette API identifie également les attributs, y compris les caractéristiques comme le nez et les yeux, le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de ces capacités de détection, l’API Visage peut vérifier si deux visages dans la même image ou dans des images différentes sont identiques à l’aide d’un score de confiance. L’API Visage peut également comparer les visages par rapport à une base de données pour voir si un visage paraissant similaire ou identique existe déjà. Elle peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs de l’API Visage.

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Le moteur Docker doit être installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> Sur Windows, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous avez besoin de connaissances de base des concepts Docker, telles que les registres, les référentiels, les conteneurs et les images conteneur. Vous devez également connaître les commandes `docker` de base.| 
|Ressource visage |Pour utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Une ressource **Visage** Azure, la clé API associée et l’URI de point de terminaison. Les deux valeurs sont disponibles sur les pages **Vue d’ensemble** et **Clés** de la ressource. Elles sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : l’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : le point de terminaison tel qu'il est fourni à la page **Vue d’ensemble**

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur de l’API Visage.

| Conteneur | Minimale | Recommandé | Transactions par seconde<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |10, 20|

* Chaque cœur doit être cadencé à au moins 2,6 GHz.
* Transactions par seconde (TPS).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec docker pull

Des images conteneur sont disponibles pour l’API Visage. 

| Conteneur | Référentiel |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Commande docker pull du conteneur Visage

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Utiliser le conteneur

Une fois que le conteneur est sur [l’ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour utiliser le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec docker run

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants.

| Placeholder | Valeur |
|-------------|-------|
|{API_KEY} | Cette clé, qui permet de démarrer le conteneur, est disponible sur la page **Clés** d’Azure `Cognitive Services`. |
|{ENDPOINT_URI} | La valeur d’URI de point de terminaison de facturation est disponible sur la page **Vue d’ensemble** d’Azure `Cognitive Services`. Par exemple `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Ajoutez le routage `face/v1.0` à l’URI de point de terminaison, comme dans l’exemple ENDPOINT_URI précédent. 

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant :

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur Visage à partir de l’image conteneur.
* Alloue un cœur de processeur et 4 Go de mémoire.
* Expose le port TCP 5 000 et alloue un pseudo TTY pour le conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas. Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `https://localhost:5000`, pour les API de conteneur.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](./face-resource-container-config.md#mount-settings) de sortie et que la journalisation est activée, il génère des fichiers journaux utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 


## <a name="billing"></a>Facturation

Les conteneurs de l’API Visage envoient des informations de facturation à Azure à l’aide d’une ressource API Visage sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs de l’API Visage. En résumé :

* L’API Visage fournit trois conteneurs Linux pour Docker proposant l’extraction de phrases clés, la détection de la langue et l’analyse des sentiments.
* Des images conteneur sont téléchargées à partir d’Azure Container Registry.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel (SDK) pour appeler des opérations dans les conteneurs de l’API Visage en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client, telles que l’image ou le texte analysé, à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, consultez [Configure Face Docker containers](face-resource-container-config.md) (Configurer des conteneurs Docker Visage).
* Pour en savoir plus sur la détection et l’identification des visages, consultez la [vue d’ensemble sur l’API Visage](Overview.md).
* Pour des informations sur les méthodes prises en charge par le conteneur, consultez la documentation sur [l’API Visage](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Pour utiliser plus de conteneurs Cognitive Services, consultez [Container support in Azure Cognitive Services](../cognitive-services-container-support.md) (Prise en charge des conteneurs dans Azure Cognitive Services).
