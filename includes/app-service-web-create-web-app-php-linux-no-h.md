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
ms.openlocfilehash: c876c60f89ecf0d7be01f70dedec70a6c84b1b91
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550948"
---
Créer une [application web](../articles/app-service/containers/app-service-linux-intro.md) dans le plan App Service `myAppServicePlan`. 

Dans Cloud Shell, vous pouvez utiliser la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Dans l’exemple suivant, remplacez `<app-name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). Le runtime est défini sur `PHP|7.0`. Pour voir tous les runtimes, exécutez [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.0" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.0" --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vous avez créé une application web vide, avec le déploiement Git activé.

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>
