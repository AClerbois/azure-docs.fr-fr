---
title: Configurer le stockage à l’aide d’Azure Files
description: Comment configurer et vous connecter à Azure Files dans un conteneur Windows sur App Service.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297221"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurer Azure Files dans un conteneur Windows sur App Service

> [!NOTE]
> Cet article s’applique aux conteneurs Windows personnalisés. Pour déployer sur App Service sur _Linux_, consultez [Distribuer du contenu depuis le stockage Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Ce guide montre comment accéder au stockage Azure dans des conteneurs Windows. Seuls les [partages de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) et [partages de fichiers Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) sont pris en charge. Vous utiliserez des partages de fichiers Azure dans ce guide pratique. Les avantages sont multiples : sécurisation et portabilité du contenu, accès à plusieurs applications et multiplication des méthodes de transfert.

## <a name="prerequisites"></a>Prérequis

- [Azure CLI](/cli/azure/install-azure-cli) 2.0.46 (ou version ultérieure).
- [Une application de conteneur Windows existante dans Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Création d’un partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Charger des fichiers dans un partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files, facturé séparément et non fourni avec l’application web, n’est pas le stockage par défaut. Il ne prend pas en charge la configuration du pare-feu en raison des limitations de l’infrastructure.
>

## <a name="link-storage-to-your-web-app-preview"></a>Lier le stockage à une application web (préversion)

 Pour monter un partage Azure Files dans un répertoire de votre application App Service, utilisez la commande [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Le type de stockage doit être AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Répétez cette opération pour tous les autres annuaires que vous souhaitez lier à un partage Azure Files.

## <a name="verify"></a>Vérifier

Vous pouvez vérifier qu’un partage Azure Files est lié à une application web en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Étapes suivantes

- [Migrer une application ASP.NET vers Azure App Service à l’aide d’un conteneur Windows (préversion)](app-service-web-tutorial-windows-containers-custom-fonts.md).
