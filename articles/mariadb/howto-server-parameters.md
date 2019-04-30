---
title: Guide pratique pour configurer les paramètres de serveur dans Azure Database for MariaDB
description: Cet article explique comment configurer les paramètres de serveur MariaDB dans Azure Database for MariaDB à l’aide du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c618a4035e9ec9b1ca1986e898ea1060ac05712d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922526"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Guide pratique pour configurer des paramètres de serveur dans Azure Database for MariaDB à l’aide du portail Azure

Azure Database for MariaDB prend en charge la configuration de certains paramètres de serveur. Cet article décrit comment configurer ces paramètres à l’aide du portail Azure. Les paramètres du serveur ne sont pas tous modifiables.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Accéder à Paramètres du serveur sur le portail Azure

1. Connectez-vous au portail Azure, puis localisez votre serveur Azure Database for MariaDB.
2. Dans la section **PARAMÈTRES**, cliquez sur **Paramètres du serveur** pour ouvrir la page de paramètres du serveur Azure Database for MariaDB.
![Page Paramètres du serveur du portail Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Recherchez les paramètres que vous devez ajuster. Examinez la colonne **Description** pour comprendre la fonction et les valeurs autorisées.
![Bouton déroulant Énumérer](./media/howto-server-parameters/3-toggle_parameter.png)
4. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
![Enregistrer ou annuler les modifications](./media/howto-server-parameters/4-save_parameters.png)
5. Si vous avez enregistré de nouvelles valeurs pour les paramètres, vous pouvez toujours rétablir toutes les valeurs par défaut en sélectionnant **Rétablir toutes les valeurs par défaut**.
![Rétablir toutes les valeurs par défaut](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste des paramètres de serveur configurables

La liste des paramètres de serveur pris en charge s’allonge en permanence. Utilisez l’onglet Paramètres du serveur dans le portail Azure pour obtenir la définition et configurer les paramètres du serveur en fonction des besoins de votre application.

## <a name="non-configurable-server-parameters"></a>Paramètres de serveur non configurables

Le pool de mémoires tampons InnoDB et le nombre maximal de connexions ne sont pas configurables et dépendent de votre [niveau tarifaire](concepts-pricing-tiers.md).

|**Niveau tarifaire**| **vCore(s)**|**Pool de mémoires tampons InnoDB (Mo)**| **Nombre maximal de connexions**|
|---|---|---|---|
|De base| 1| 1 024| 50|
|De base| 2| 2560| 100|
|Usage général| 2| 3584| 300|
|Usage général| 4| 7680| 625|
|Usage général| 8| 15360| 1250|
|Usage général| 16| 31232| 2 500|
|Usage général| 32| 62976| 5 000|
|Usage général| 64| 125952| 10000|
|Mémoire optimisée| 2| 7168| 600|
|Mémoire optimisée| 4| 15360| 1250|
|Mémoire optimisée| 8| 30720| 2 500|
|Mémoire optimisée| 16| 62464| 5 000|
|Mémoire optimisée| 32| 125952| 10000|

Ces paramètres de serveur ne sont pas configurables dans le système :

|**Paramètre**|**Valeur fixe**|
| :------------------------ | :-------- |
|innodb_file_per_table dans le niveau de base|ÉTEINT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 Mo|

Tous les autres paramètres de serveur sont définis sur leurs valeurs MariaDB fournies par défaut pour [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Utilisation avec le paramètre de fuseau horaire

### <a name="populating-the-time-zone-tables"></a>Remplissage des tables de fuseaux horaires

Les tables de fuseaux horaires sur votre serveur peuvent être remplies en appelant la procédure stockée `az_load_timezone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench.

> [!NOTE]
> Si vous exécutez la commande `az_load_timezone` à partir de MySQL Workbench, vous devrez peut-être d’abord désactiver le mode de mise à jour sécurisée à l’aide de `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Pour afficher les valeurs de fuseau horaire disponibles, exécutez la commande suivante :

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Définition du fuseau horaire global

Le fuseau horaire global peut être défini dans la page **Paramètres du serveur** sur le portail Azure. L’exemple ci-dessous définit le fuseau horaire global sur la valeur « US/Pacific ».

![Définir le paramètre de fuseau horaire](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Définition du fuseau horaire de la session

Le fuseau horaire de la session peut être défini en exécutant la commande `SET time_zone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench. L’exemple ci-dessous définit le fuseau horaire **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Reportez-vous à la documentation de MariaDB relative aux [fonction de date et heure](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
