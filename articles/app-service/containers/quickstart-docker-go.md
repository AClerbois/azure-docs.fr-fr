---
title: Créer une application Docker/Go sur Linux - Azure App Service
description: Comment déployer une image Docker exécutant une application Go dans Web App pour conteneurs.
keywords: azure app service, application web, go, docker, conteneur
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 88c9996ce3f2d89ae58881c913f6bd4e549b5814
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547302"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>Exécuter un conteneur Linux personnalisé dans Azure App Service

[App Service Linux](app-service-linux-intro.md) fournit des piles d’applications prédéfinies sur Linux avec la prise en charge de langages comme .NET, PHP, Node.js, etc. Vous pouvez également utiliser une image Docker personnalisée pour exécuter votre application web sur une pile d’applications qui n’est pas encore définie dans Azure. Ce guide de démarrage rapide vous montre comment créer une application web et déployer une image Go depuis Docker Hub. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exemple d’application s’exécutant dans Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Créer une application web

Créez une [application web](../overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). N’oubliez pas de remplacer `<app name>` par un nom d’application globalement unique.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

Dans la commande précédente, `--deployment-container-image-name` pointe vers l’image publique Docker Hub [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Accéder à l’application

```bash
http://<app_name>.azurewebsites.net/hello
```

![Exemple d’application s’exécutant dans Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Félicitations !** Vous avez déployé une image Docker personnalisée exécutant une application Go dans Web App pour conteneurs.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer à partir du référentiel de conteneurs privé](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Configurer un conteneur personnalisé](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)
