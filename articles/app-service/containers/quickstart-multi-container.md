---
title: Créer une application multiconteneur (préversion) dans Azure Web App pour conteneurs à l’aide d’une configuration Docker Compose
description: Déployer votre première application multiconteneur dans Azure Web App pour conteneurs en quelques minutes
keywords: service d’application Azure, application web, Linux, Docker, Compose, multiconteneur, plusieurs conteneurs, Web App pour conteneurs, plusieurs conteneurs, conteneurs, Kubernetes, Wordpress, base de données Azure pour MySQL, base de données de production avec des conteneurs
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: bf567402a66f9152c7eb9b97925fec2a159ffe56
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127418"
---
# <a name="create-a-multi-container-preview-app-using-web-app-for-containers"></a>Créer une application multiconteneur (préversion) à l’aide d’Azure Web App pour conteneurs

[Web App pour conteneurs](app-service-linux-intro.md) fournit une solution souple d’utilisation des images Docker. Ce guide de démarrage rapide montre comment déployer une application multiconteneur sur Web App pour conteneurs dans [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) à l’aide d’une configuration Docker Compose. Pour Kubernetes et une solution de bout en bout complète qui utilise une base de données Azure pour MySQL, suivez le [didacticiel sur les multiconteneurs](tutorial-multi-container-app.md).

Vous allez effectuer les étapes de ce guide de démarrage rapide dans Cloud Shell, mais vous pouvez également exécuter ces commandes en local avec [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 ou ultérieur). 

![Exemple d’application à plusieurs conteneurs sur Web App pour conteneurs][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Pour ce guide de démarrage rapide, vous utilisez le fichier Composer de [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Le fichier de configuration se trouve dans les [exemples Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Dans Cloud Shell, créez un répertoire de démarrage rapide, puis utilisez-le.

```bash
mkdir quickstart

cd quickstart
```

Exécutez ensuite la commande suivante pour cloner le référentiel de l’exemple d’application sur votre répertoire de démarrage rapide. Accédez ensuite au répertoire `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Dans Cloud Shell, créez un groupe de ressources avec la commande [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *USA Centre Sud*. Pour afficher tous les emplacements pris en charge pour App Service sous Linux au niveau **Standard**, exécutez la commande [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous.

Une fois la commande terminée, une sortie JSON affiche les propriétés du groupe de ressources.

## <a name="create-an-azure-app-service-plan"></a>Créer un plan Azure App Service

Dans Cloud Shell, créez un plan App Service dans le groupe de ressources avec la commande [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` dans le niveau tarifaire **Standard** (`--sku S1`) et un conteneur Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Création d’une application Docker Compose

Dans Cloud Shell, créez une [application web](app-service-linux-intro.md) multiconteneur dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). N’oubliez pas de remplacer _\<app_name>_ par un nom d’application unique.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`). Le chargement de l’application peut prendre plusieurs minutes. Si vous recevez une erreur, attendez quelques minutes supplémentaires, puis actualisez le navigateur.

![Exemple d’application à plusieurs conteneurs sur Web App pour conteneurs][1]

**Félicitations**, vous avez créé une application à plusieurs conteneurs dans Web App pour conteneurs.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application WordPress multiconteneur dans Web App pour conteneurs](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png