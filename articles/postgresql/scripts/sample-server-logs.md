---
title: 'Script Azure CLI : Télécharger des journaux de serveurs dans Azure Database pour PostgreSQL'
description: Cet exemple de script Azure CLI montre comment activer et télécharger les journaux d’un serveur Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 107c844051ab396fe467a1a629883746b2903125
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731816"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Activer et télécharger les journaux de requêtes lentes d’un serveur Azure Database pour PostgreSQL à l’aide d’Azure CLI
Cet exemple de script CLI montre comment activer et télécharger les journaux de requêtes lentes d’un seul serveur Azure Database pour PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI.

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres. Dans les commandes `az monitor`, remplacez &lt;log_file_name&gt; par le nom de votre fichier journal de serveur.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az postgres server create](/cli/azure/postgres/server) | Crée un serveur PostgreSQL qui héberge les bases de données. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Répertoriez les valeurs de configuration pour un serveur. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Mettez à jour la configuration d’un serveur. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Répertoriez les fichiers journaux pour un serveur. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Téléchargez les fichiers journaux. |
| [az group delete](/cli/azure/group) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- Découvrez-en plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure).
- Essayez d’autres scripts : [Exemples Azure CLI pour base de données pour PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurer et consulter les journaux du serveur sur le Portail Azure](../howto-configure-server-logs-in-portal.md)
