---
title: Exemple de script Azure CLI - Supprimer un Cache Azure pour Redis | Microsoft Docs
description: Exemple de script Azure CLI - Supprimer un Cache Azure pour Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: d02d3196c2cbc130a2e88061df514b0bf681b1bf
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238009"
---
# <a name="delete-an-azure-cache-for-redis"></a>Supprimer un Cache Azure pour Redis

Dans ce scénario, vous apprenez comment supprimer un Cache Azure pour Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour supprimer une instance du Cache Azure pour Redis. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Supprimez une instance du Cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).
