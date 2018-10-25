---
title: Serveurs logiques SQL Azure | Microsoft Docs
description: En savoir plus sur les serveurs logiques Azure SQL Database et leur gestion.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 6432c6b2c1c224f44b962df63e1502729f69e57f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470657"
---
# <a name="azure-sql-database-logical-servers-and-their-management"></a>Serveurs logiques Azure SQL Database et leur gestion

## <a name="what-is-an-azure-sql-logical-server"></a>Qu’est-ce qu’un serveur logique SQL Azure ?

Un serveur logique agit comme un point d’administration central pour plusieurs bases de données uniques ou [en pool](sql-database-elastic-pool.md), des [connexions](sql-database-manage-logins.md), des [règles de pare-feu](sql-database-firewall-configure.md), des [règlesd’audit](sql-database-auditing.md), des [stratégies de détection des menaces](sql-database-threat-detection.md) et des [groupes de basculement](sql-database-geo-replication-overview.md). Un serveur logique peut se trouver dans une région différente de celle de son groupe de ressources. Avant de pouvoir créer la base de données SQL Azure, vous devez déjà posséder un serveur logique. Toutes les bases de données sur un serveur sont créées au sein de la même région que le serveur logique.

Un serveur logique est une construction logique distincte d’une instance SQL Server que vous connaissez peut-être dans le monde local. Plus précisément, le service SQL Database n’offre aucune garantie en ce qui concerne l’emplacement des bases de données par rapport à leurs serveurs logiques et ne présente aucun accès ni aucune fonctionnalité au niveau de l’instance. Par contraste, un serveur dans une instance gérée SQL Database est similaire à une instance SQL Server que vous connaissez peut-être dans le monde local.

Lorsque vous créez un serveur logique, vous fournissez un compte de connexion du serveur et un mot de passe disposant de droits d’administration pour la base de données MASTER sur ce serveur, ainsi que sur l’ensemble des bases de données qui y sont créées. Ce compte initial est un compte de connexion SQL. Azure SQL Database prend en charge l’authentification SQL et l’authentification Azure Active Directory. Pour en savoir plus sur les connexions et l’authentification, consultez la page [Managing Databases and Logins in Azure SQL Database](sql-database-manage-logins.md) (Gérer les bases de données et les connexions dans Azure SQL Database). L’authentification Windows n’est pas prise en charge.

Un serveur logique de base de données Azure :

- Est créé dans un abonnement Azure, mais peut être déplacé avec les ressources qu’il contient vers un autre abonnement
- Est la ressource parente pour les bases de données, les pools élastiques et les entrepôts de données
- Fournit un espace de noms pour les bases de données, les pools élastiques et les entrepôts de données
- Est un conteneur logique qui possède une forte sémantique de durée de vie : si vous supprimez un serveur, le serveur logique supprimera les bases de données autonomes, les pools élastiques et les entrepôts de données qu’il contient
- Fait partie d’un [contrôle d’accès en fonction du rôle Azure (RBAC)](/azure/role-based-access-control/overview) : les bases de données, les pools élastiques et les entrepôts de données d’un serveur héritent des droits d’accès du serveur
- Est un élément de poids dans l’identité des bases de données, des pools élastiques et des entrepôts de données à des fins de gestion des ressources Azure (voir le schéma d’URL pour les bases de données et les pools)
- Colocalise les ressources d’une région
- Fournit un point de terminaison de connexion pour l’accès aux bases de données (`<serverName>`. database.windows.net)
- Fournit l’accès aux métadonnées concernant l’accès aux ressources contenues via les DMV en vous connectant à une base de données MASTER
- Fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, audit, détection des menaces, etc.
- Est limité par un quota dans l’abonnement parent (six serveurs par abonnement par défaut ; [consultez les Limites d’abonnement ici](../azure-subscription-service-limits.md))
- Fournit l’étendue du quota de base de données et du quota DTU ou vCore pour les ressources qu’il contient (par exemple, 45 000 DTU)
- Est la portée du contrôle de version pour les fonctionnalités activées sur les ressources qu’il contient
- Les connexions principales au niveau du serveur peuvent gérer toutes les bases de données sur un serveur
- Peut contenir des connexions semblables à celles des instances de SQL Server en local qui ont accès à une ou plusieurs bases de données sur le serveur et qui peuvent se voir octroyer des droits d’administration limités. Pour plus d’informations, consultez [Connexions](sql-database-manage-logins.md).
- Le classement par défaut pour toutes les bases de données utilisateur créées sur un serveur logique est `SQL_LATIN1_GENERAL_CP1_CI_AS`, où `LATIN1_GENERAL` est anglais (États-Unis), `CP1` est la page de codes 1252, `CI` ne respecte pas la casse, et `AS` est sensible aux accents.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide du portail Azure

Vous pouvez créer le groupe de ressources de la base de données SQL Azure en avance ou lors de la création du serveur lui-même. Il existe plusieurs méthodes pour accéder à un nouveau formulaire de serveur SQL : en créant un nouveau serveur SQL ou dans le cadre de la création d’une base de données.

### <a name="create-a-blank-sql-server-logical-server"></a>Créer un serveur SQL vide (serveur logique)

Pour créer un serveur Azure SQL Database (sans base de données) à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire de serveur SQL vide (serveur logique).  

### <a name="create-a-blank-or-sample-sql-database"></a>Créer un exemple de base de données SQL ou une base de données SQL vide

Pour créer une base de données SQL Azure à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire SQL Database vide et renseignez les informations demandées. Vous pouvez créer le groupe de ressources de la base de données SQL Azure et le serveur logique en avance ou lors de la création de la base de données elle-même. Vous pouvez créer une base de données vide ou créer un exemple de base de données reposant sur Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Pour plus d’informations sur la sélection du niveau tarifaire de votre base de données, consultez [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores](sql-database-service-tiers-vcore.md).

Pour créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Gérer un serveur SQL existant

Pour gérer un serveur existant, accédez au serveur à l’aide de plusieurs méthodes, à partir d’une page de base de données SQL spécifique, de la page **Serveurs SQL** ou de la page **Toutes les ressources**, par exemple.

Pour gérer une base de données existante, accédez à la page **Bases de données SQL** page, puis cliquez sur la base de données que vous souhaitez gérer. La capture d’écran suivante montre comment commencer à définir un pare-feu au niveau du serveur pour une base de données, à partir de la page **Vue d’ensemble** d’une base de données.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Pour configurer les propriétés de performance d’une base de données, consultez [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores](sql-database-service-tiers-vcore.md).
> [!TIP]
> Pour un démarrage rapide du portail Azure, consultez la page[Créer une base de données SQL Azure à l’aide du portail Azure](sql-database-get-started-portal.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de PowerShell

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Azure PowerShell, utilisez les applets de commande PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour créer et gérer des pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Crée un groupe de ressources|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Crée un serveur|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Renvoie des informations concernant les serveurs|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifie les propriétés d’un serveur|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Supprime un serveur|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Crée une règle de pare-feu au niveau du serveur |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Obtient les règles de pare-feu d’un serveur|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifie une règle de pare-feu sur un serveur|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Supprime une règle de pare-feu d’un serveur|
| New-AzureRmSqlServerVirtualNetworkRule | Crée une [*règle de réseau virtuel*](sql-database-vnet-service-endpoint-rule-overview.md), basée sur un sous-réseau qui est un point de terminaison de service de réseau virtuel. |

> [!TIP]
> Pour un démarrage rapide de PowerShell, consultez la page [Créer une base de données SQL Azure unique à l’aide de PowerShell](sql-database-get-started-portal.md). Pour obtenir des exemples de scripts PowerShell, consultez [Utiliser PowerShell pour créer une base de données SQL Azure et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-powershell.md) et [Surveillance et mise à l’échelle d’une instance SQL Database unique à l’aide de PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de l’interface de ligne de commande Azure

Pour créer et gérer un serveur, des bases de données et des pare-feux SQL Azure avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer des pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Crée une base de données|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Répertorie toutes les bases de données et les entrepôts de données d’un serveur, ou toutes les bases de données d’un pool élastique|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Répertorie les objectifs de service disponibles et les limites de stockage|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Renvoie les données d’utilisation de la base de données|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obtient une base de données ou un entrepôt de données|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Met à jour une base de données|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Supprime une base de données|
|[az group create](/cli/azure/group#az-group-create)|Crée un groupe de ressources|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Crée un serveur|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Répertorie les serveurs|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Renvoie les données d’utilisation d’un serveur|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Obtient un serveur|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Met à jour un serveur|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Supprime un serveur.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Crée la règle de pare-feu d’un serveur|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Répertorie les règles de pare-feu sur un serveur|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Affiche les détails d’une règle de pare-feu|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Met à jour une règle de pare-feu|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Supprime une règle de pare-feu|

> [!TIP]
> Pour un démarrage rapide d’Azure CLI, consultez la page [Créer une base de données SQL Azure unique à l’aide d’Azure CLI](sql-database-cli-samples.md). Pour obtenir des exemples de scripts Azure CLI, consultez [Utiliser CLI pour créer une seule base de données Azure SQL et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-cli.md) et [Utiliser CLI pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de Transact-SQL

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour gérer des pools élastiques, consultez la page [Pools élastiques](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Vous ne pouvez pas créer ou supprimer un serveur à l’aide de Transact-SQL.
>

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifie une base de données SQL Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifie un entrepôt de données Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourne la consommation de mémoire, d’E/S et d’UC d’une base de données Azure SQL Database. Il existe une ligne pour toutes les 15 secondes, même s’il n’existe aucune activité dans la base de données.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Renvoie les données de stockage et l’utilisation d’UC pour une base de données Azure SQL Database. Les données sont collectées et agrégées par intervalles de cinq minutes.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contient des statistiques pour les événements de connectivité de base de données SQL Database, ce qui fournit une vue d’ensemble du nombre d’échecs et de réussites de connexion de base de données. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Renvoie les interblocages les échecs de connexion et les réussites de connexion de base de données Azure SQL Database. Vous pouvez utiliser ces informations pour suivre ou dépanner votre activité de base de données avec SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crée ou met à jour les paramètres de pare-feu au niveau du serveur pour votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur. Une règle de pare-feu au niveau du serveur peut uniquement être créée à l’aide de Transact-SQL, après que la première règle de pare-feu au niveau du serveur a été créée par un utilisateur disposant des autorisations au niveau d’Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau du serveur associés à votre Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau du serveur de votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crée ou met à jour les règles de pare-feu au niveau de la base de données pour votre base de données pour votre Azure SQL Database ou votre entrepôt de données SQL Data Warehouse. Les règles de pare-feu d’une base de données peuvent être configurées pour la base de données MASTER, ainsi que pour les bases de données utilisateur sur SQL Database. Les règles de pare-feu d’une base de données sont utiles lors de l’utilisation d’utilisateurs de base de données autonome. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau de la base de données associés à votre Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau de la base de données de votre Azure SQL Database ou de votre entrepôt de données SQL Data Warehouse. |

> [!TIP]
> Pour un démarrage rapide à l’aide de SQL Server Management Studio sur Microsoft Windows, consultez la page [Azure SQL Database : utilisez SQL Server Management Studio pour vous connecter et interroger des données](sql-database-connect-query-ssms.md). Pour un démarrage rapide à l’aide de Visual Studio Code sur Mac OS, Linux ou Windows, consultez la page [Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de l’API REST

Pour créer et gérer un serveur, des bases de données et des pare-feux Azure SQL, utilisez les requêtes suivantes de l’API REST.

| Commande | Description |
| --- | --- |
|[Serveurs - Create ou Update](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Crée ou met à jour un serveur.|
|[Serveurs - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|Supprime un serveur SQL.|
|[Serveurs - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Obtient un serveur.|
|[Serveurs - List](https://docs.microsoft.com/rest/api/sql/servers/list)|Retourne une liste de serveurs.|
|[Serveurs - List By Resource Group](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retourne une liste de serveurs dans un groupe de ressources.|
|[Serveurs - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|Met à jour un serveur existant.|
|[Bases de données : Create ou Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Supprimer](https://docs.microsoft.com/rest/api/sql/databases/delete)|Supprime une base de données.|
|[Bases de données - Obtenir](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Lister par pool élastique](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par serveur](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)|Met à jour une base de données existante.|
|[Règles de pare-feu - Create ou Update](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Crée ou met à jour une règle de pare-feu.|
|[Règles de pare-feu - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Supprime une règle de pare-feu.|
|[Règles de pare-feu - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Obtient une règle de pare-feu.|
|[Règles de pare-feu - List By Server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Retourne une liste de règles de pare-feu.|

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données SQL](sql-database-cloud-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
