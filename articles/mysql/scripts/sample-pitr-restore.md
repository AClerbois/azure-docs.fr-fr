---
title: 'Script Azure CLI : Restaurer un serveur Azure Database pour MySQL à un point antérieur dans le temps'
description: Cet exemple de script Azure CLI permet de restaurer un serveur Azure Database pour MySQL à un point antérieur dans le temps.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 5e59468897b04dd5f017480bcf7b5abc4656a5c2
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582169"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Restauration d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI
Cet exemple de script Azure CLI permet de restaurer un seul serveur Azure Database pour MySQL à un point antérieur dans le temps.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet exemple. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur. Remplacez l’ID d’abonnement utilisé dans les commandes az monitor par votre propre ID d’abonnement.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server restore](/cli/azure/mysql/server#restore) | Restaure un serveur à partir de la sauvegarde. |
| [az group delete](/cli/azure/group#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour MySQL](../sample-scripts-azure-cli.md)
