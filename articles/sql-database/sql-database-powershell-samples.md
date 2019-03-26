---
title: Exemples de scripts Azure PowerShell pour SQL Database | Microsoft Docs
description: Exemples de scripts Azure PowerShell vous permettant de créer et de gérer des serveurs, des pools élastiques, des bases de données et des pare-feu Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3a22caa39dad3ce296c43015f324270b7dc55ae8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862027"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemples Azure PowerShell pour Azure SQL Database

Azure SQL Database vous permet de configurer vos bases de données, instances et pools à l’aide d’Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous aurez besoin de la version 1.4.0 d’AZ PowerShell ou d’une version ultérieure pour suivre ce didacticiel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="single-database-and-elastic-pools"></a>Base de données unique et pools élastiques

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure SQL Database.

| |  |
|---|---|
|**Créer et configurer des bases de données uniques et des pools élastiques**||
| [Créer une base de données unique et configurer une règle de pare-feu de serveur de base de données](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell crée une base de données SQL Azure unique et configure une règle de pare-feu au niveau du serveur. |
| [Créer des pools élastiques et déplacer les bases de données mises en pool](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell crée des pools élastiques Azure SQL Database, déplace des bases de données mises en pool et modifie les tailles de calcul.|
|**Configurer la géoréplication et le basculement**||
| [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure la géoréplication active pour une base de données SQL Azure unique et la fait basculer vers le réplica secondaire. |
| [Configurer et basculer une base de données regroupée à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure la géoréplication active pour une base de données SQL Azure dans un pool élastique SQL et la fait basculer vers le réplica secondaire. |
| [Configurer et basculer un groupe de basculement pour une base de données unique](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure un groupe de basculement pour une instance de serveur de base de données SQL Azure, ajoute une base de données au groupe de basculement et le bascule vers le serveur secondaire |
|**Mettre à l’échelle une base de données unique et un pool élastique**||
| [Mettre à l’échelle une base de données unique](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell surveille les mesures de performances d’une base de données SQL Azure, l’adapte à une taille de calcul supérieure et crée une règle d’alerte sur l’une des mesures de performances. |
| [Mettre à l’échelle un pool élastique](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell surveille les mesures de performances d’un pool élastique de base de données SQL Azure, l’adapte à une taille de calcul supérieure et crée une règle d’alerte sur l’une des mesures de performances.  |
| **Audit et détection des menaces** |
| [Configurer l’audit et la détection des menaces](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure les stratégies d’audit et de détection des menaces pour une base de données SQL Azure. |
| **Restaurer, copier et importer une base de données**||
| [Restaurer une base de données](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell restaure une base de données SQL Azure à partir d’une sauvegarde géoredondante et restaure une base de données SQL Azure supprimée dans la dernière sauvegarde. |
| [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell crée une copie d’une base de données SQL Azure existante sur un nouveau serveur SQL Azure. |
| [Importer une base de données à partir d’un fichier bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell importe une base de données vers un serveur SQL Azure à partir d’un fichier bacpac. |
| **Synchroniser des données entre des bases de données**||
| [Synchroniser des données entre des bases de données SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure la synchronisation des données entre plusieurs bases de données Azure SQL. |
| [Synchroniser des données entre une base de données SQL et un serveur SQL local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure la synchronisation des données entre une base de données Azure SQL et une base de données SQL Server locale. |
| [Mettre à jour le schéma de synchronisation SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell ajoute ou supprime des éléments du schéma de synchronisation Data Sync. |
|||

Découvrez-en plus sur l’[API de base de données unique Azure PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instance gérée

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure SQL Database - Managed Instance.

| |  |
|---|---|
|**Créer et configurer des instances managées**||
| [Créer et gérer une instance gérée](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Ce script PowerShell vous montre comment créer et gérer une instance gérée à l’aide d’Azure PowerShell. |
| [Créer et gérer une instance managée à l’aide du modèle Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell vous montre comment créer et gérer une instance managée à l’aide du modèle Azure PowerShell et Azure Resource Manager.|
| **Configurer Transparent Data Encryption (TDE)**||
| [Gérer Transparent Data Encryption dans une instance gérée à l’aide de votre propre clé Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure Transparent Data Encryption (TDE) dans le scénario Bring Your Own Key pour Azure SQL Managed Instance, à l’aide d’une clé Azure Key Vault.|
|||

Découvrez-en plus sur l’[API Managed Instance Azure PowerShell](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).
