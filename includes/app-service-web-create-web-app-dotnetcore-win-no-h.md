---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3275fb0e40e7cdd6f02ec96fb949ec4e4e673f26
ms.sourcegitcommit: 902aae1b5f872993185cbf1e35a4f7bb93748226
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2018
ms.locfileid: "35656315"
---
Créer une [application web](../articles/app-service/containers/app-service-linux-intro.md) dans le plan App Service `myAppServicePlan`. 

Dans Cloud Shell, vous pouvez utiliser la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Dans l’exemple suivant, remplacez `<app_name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>
