---
title: "Création d’une instance d’Azure Database pour PostgreSQL à l’aide de la CLI Azure | Microsoft Docs"
description: "Guide de démarrage rapide pour créer et gérer un serveur Azure Database pour PostgreSQL à l’aide de la CLI (interface de ligne de commande) Azure."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start create
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 793d948b25a2b6c408359de03433746a9494e1d1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Création d’une instance d’Azure Database pour PostgreSQL à l’aide de la CLI Azure

Base de données Azure pour PostgreSQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide de démarrage rapide vous montre comment créer un serveur Azure Database pour PostgreSQL dans un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) avec la CLI Azure.

Pour suivre ce guide de démarrage rapide, assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.
```azurecli
az login
```
Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée. Sélectionnez un ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#set).
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un [serveur Azure Database pour PostgreSQL](overview.md) avec la commande [az sql server create](/cli/azure/postgres/server#create). Un serveur contient un groupe de bases de données gérées en tant que groupe. 

L’exemple suivant crée un serveur nommé `mypgserver-20170401` dans votre groupe de ressources `myresourcegroup` avec l’identifiant d’administrateur serveur `mylogin`. Le nom du serveur correspond au nom DNS et doit ainsi être globalement unique dans Azure. Remplacez `<server_admin_password>` avec votre propre valeur.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

Par défaut, la base de données **postgres** est créée sous le serveur. La base de données [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) est une base de données par défaut destinée aux utilisateurs, utilitaires et applications tierces. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Créez une règle de pare-feu au niveau du serveur Azure PostgreSQL avec la commande [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). Une règle de pare-feu au niveau du serveur permet à une application externe, comme [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), de se connecter à votre serveur via le pare-feu du service Azure PostgreSQL. 

Vous pouvez définir une règle de pare-feu qui couvre une plage d’adresses IP pour pouvoir vous connecter à partir de votre réseau. L’exemple suivant utilise [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) pour créer une règle de pare-feu `AllowAllIps` pour une plage d’adresses IP. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
>

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Le résultat est au format JSON. Prenez note du **administratorLogin** et du **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Se connecter à la base de données PostgreSQL avec psql

Si votre ordinateur client a PostgreSQL installé, vous pouvez utiliser une instance locale de [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) pour vous connecter à un serveur Azure PostgreSQL. Nous allons maintenant utiliser l’utilitaire de ligne de commande psql pour nous connecter au serveur Azure pour PostgreSQL.

1. Exécutez la commande psql suivante pour vous connecter à un serveur Azure Database pour PostgreSQL
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Par exemple, la commande suivante se connecte à la base de données par défaut appelée **postgres** sur le serveur PostgreSQL **mypgserver-20170401.postgres.database.azure.com** à l’aide des informations d’identification d’accès. Saisissez le `<server_admin_password>` que vous avez choisi à l’invite de mot de passe.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Une fois que vous êtes connecté au serveur, créez une base de données vide à l’invite.
```bash
CREATE DATABASE mypgsqldb;
```

3.  À l’invite, exécutez la commande suivante pour basculer la connexion sur la base de données nouvellement créée **mypgsqldb** :
```bash
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Se connecter à la base de données PostgreSQL avec pgAdmin

Pour se connecter au serveur Azure PostgreSQL à l’aide de l’outil d’interface graphique _pgAdmin_
1.    Lancez l’application _pgAdmin_ sur votre ordinateur client. Vous pouvez installer _pgAdmin_ à partir de http://www.pgadmin.org/.
2.    Choisissez **Ajouter un nouveau serveur** à partir du menu **Liens rapides**.
3.    Dans la boîte de dialogue **Créer - Serveur**, onglet **Général**, entrez un nom convivial unique pour le serveur. Par exemple **Serveur Azure PostgreSQL**.
 ![Outil pgAdmin - Créer - Serveur](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.    Dans la boîte de dialogue **Créer - Serveur**, onglet **Connexion** :
    - Saisissez le nom complet du serveur (par exemple **mypgserver-20170401.postgres.database.azure.com**) dans la zone **Nom/Adresse de l’hôte**. 
    - Entrez le port 5432 dans la zone **Port**. 
    - Entrez **l’identifiant d’administrateur serveur (user@mypgserver)** obtenu précédemment dans ce guide de démarrage rapide et le mot de passe que vous avez saisi lors de la création du serveur dans les zones **Nom d’utilisateur** et **Mot de passe**, respectivement.
    - Sélectionnez **Requis** pour **Mode SSL**. Par défaut, tous les serveurs Azure PostgreSQL sont créés avec l’application SSL activée. Pour désactiver l’application de SSL, consultez les détails dans [Application de SSL](./concepts-ssl-connection-security.md).

    ![pgAdmin - Créer - Serveur](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.    Cliquez sur **Save**.
6.    Dans le volet gauche Navigateur, développez **Groupes de serveurs**. Choisissez votre serveur **Serveur Azure PostgreSQL**.
7.  Choisissez le **serveur** auquel vous vous êtes connecté, puis choisissez **Bases de données** sous ce dernier. 
8.    Cliquez avec le bouton droit sur **Bases de données** pour créer une base de données.
9.    Choisissez un nom de base de données **mypgsqldb** et son propriétaire en tant qu’administrateur serveur **mylogin**.
10. Cliquez sur **Enregistrer** pour créer une base de données vide.
11. Dans le **Navigateur**, développez le groupe **Serveurs**. Développez le serveur que vous avez créé, et consultez la base de données **mypgsqldb** se trouvant en dessous.
 ![pgAdmin - Créer - Base de données](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

Nettoyez toutes les ressources vous avez créées au cours de ce démarrage rapide en supprimant le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans la CLI Azure.

```azurecli
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande [az postgres server delete](/cli/azure/postgres/server#delete).
```azurecli
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Étapes suivantes
- Migrez votre base de données avec [Exportation et importation](./howto-migrate-using-export-and-import.md) ou [Vidage et restauration](./howto-migrate-using-dump-and-restore.md).
- Pour une présentation technique de ce service, consultez la page [Qu’est-ce qu’Azure Database pour PostgreSQL ?](overview.md)
