---
title: "Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Cet article décrit comment créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide de la ligne de commande Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 66d192287eeaaaa82c0f61f8aa13b8bf7bf8cd47
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs de gérer l’accès à un serveur Azure Database pour MySQL à partir d’une adresse IP spécifique ou d’une plage d’adresses IP. À l’aide de commandes d’interface de ligne de commande Azure pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles de pare-feu pour gérer votre serveur. Pour une vue d’ensemble des pare-feu Azure Database pour MySQL, consultez la rubrique [Règles de pare-feu d’Azure Database pour MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Composants requis
* [Installez Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Installez le SDK Python Azure pour les services PostgreSQL et MySQL.
* Installez le composant Azure CLI pour les services PostgreSQL et MySQL.
* Créez un serveur de base de données Azure pour MySQL.

## <a name="firewall-rule-commands"></a>Commandes de règle de pare-feu :
La commande **az mysql server firewall-rule** est utilisée à partir de l’interface de ligne de commande Azure pour créer, supprimer, répertorier, afficher et mettre à jour des règles de pare-feu.

Commandes :
- **create** : créez une règle de pare-feu du serveur Azure MySQL.
- **delete** : supprimez une règle de pare-feu du serveur Azure MySQL.
- **list** : répertoriez les règles de pare-feu du serveur Azure MySQL.
- **show** : affichez les détails d’une règle de pare-feu du serveur Azure MySQL.
- **update**: mettez à jour une règle de pare-feu du serveur Azure MySQL.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Se connecter à Azure et répertorier vos serveurs Azure Database pour MySQL
Connectez-vous en toute sécurité à Azure CLI à l’aide de votre compte Azure en exécutant la commande **az login**.

1. Exécutez la commande suivante à partir de la ligne de commande :
```azurecli
az login
```
Cette commande génère un code à utiliser lors de l’étape suivante.

2. Ouvrez la page [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans un navigateur web et entrez le code.

3. À l’invite, connectez-vous à l’aide de vos informations d’identification Azure.

4. Une fois votre connexion autorisée, une liste des abonnements s’affiche dans la console. Copiez l’ID de l’abonnement souhaité pour définir l’abonnement actif à utiliser.
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. Si vous ne connaissez pas le nom de votre abonnement et du groupe de ressources, affichez la liste des serveurs Azure Database for MySQL.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Notez l’attribut de nom dans la liste ; il servira à spécifier le serveur MySQL sur lequel vous allez travailler. Si nécessaire, vérifiez les informations permettant d’utiliser l’attribut de nom et ce serveur pour vérifier que le nom est correct :

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Répertorier les règles de pare-feu d’un serveur Azure Database pour MySQL 
À l’aide du nom du serveur et du nom de groupe de ressources, répertoriez les règles de pare-feu existantes sur le serveur. Notez que l’attribut de nom de serveur est spécifié dans le commutateur **-server** et non dans le commutateur **--name**.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
La sortie répertorie les règles éventuelles au format JSON (par défaut). Vous pouvez utiliser le commutateur **--output table** pour générer les résultats dans un format tabulaire plus lisible.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Créer une règle de pare-feu sur un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MyQL et du nom du groupe de ressources, créez une nouvelle règle de pare-feu sur le serveur. Indiquez le nom de la règle, ainsi que les adresses IP de début et de fin de la règle (pour fournir un accès à une plage d’adresses IP).
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Pour autoriser une seule adresse IP à accéder au serveur, fournissez des adresses IP de début et de fin identiques, comme dans cet exemple.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez créée au format JSON (par défaut). En cas d’échec, la sortie affiche un texte de message d’erreur.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Mettre à jour une règle de pare-feu sur un serveur Azure Database pour MySQL 
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, mettez à jour une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée, ainsi que les adresses IP de début et de fin à mettre à jour.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez mise à jour au format JSON (par défaut). En cas d’échec, la sortie affiche un texte de message d’erreur.

> [!NOTE]
> Si la règle de pare-feu n’existe pas, elle est créée par la commande de mise à jour.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Afficher les détails d’une règle de pare-feu sur un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, affichez les détails d’une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez spécifiée au format JSON (par défaut). En cas d’échec, la sortie affiche un texte de message d’erreur.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Supprimer une règle de pare-feu sur un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, supprimez une règle de pare-feu existante du serveur. Indiquez le nom de la règle de pare-feu existante.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
En cas de réussite, aucun résultat ne s’affiche. En cas d’échec, le texte du message d’erreur s’affiche.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [règles de pare-feu du serveur Azure Database pour MySQL](./concepts-firewall-rules.md).
- [Créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure](./howto-manage-firewall-using-portal.md).
