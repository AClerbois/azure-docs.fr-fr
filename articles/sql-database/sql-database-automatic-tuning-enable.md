---
title: Activer le réglage automatique pour Azure SQL Database | Microsoft Docs
description: Vous pouvez facilement activer le réglage automatique sur Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 02fae39325e632c087fcd2969fbaf5dd705994ba
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867641"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Activer le réglage automatique pour surveiller les requêtes et améliorer les performances de la charge de travail

Azure SQL Database est un service de données géré automatiquement qui surveille vos requêtes en permanence et identifie les actions que vous pouvez effectuer pour améliorer les performances de votre charge de travail. Vous pouvez consulter les recommandations et les appliquer manuellement ou laisser Azure SQL Database appliquer automatiquement des actions correctives : il s’agit du **mode de réglage automatique**.

Le réglage automatique peut être activé au niveau du serveur ou de la base de données par le biais du [portail Azure](sql-database-automatic-tuning-enable.md#azure-portal), des appels d’[API REST](sql-database-automatic-tuning-enable.md#rest-api) et des commandes [T-SQL](sql-database-automatic-tuning-enable.md#t-sql).

## <a name="enable-automatic-tuning-on-server"></a>Activer le réglage automatique sur le serveur
Au niveau du serveur, vous pouvez choisir d’hériter de la configuration du réglage automatique à partir de « Valeurs Azure par défaut » ou de ne pas hériter de la configuration. Les valeurs Azure par défaut sont FORCE_LAST_GOOD_PLAN activé, CREATE_INDEX activé et DROP_INDEX désactivé.

### <a name="azure-portal"></a>Portail Azure
Pour activer le réglage automatique sur le **serveur** logique Azure SQL Database, accédez au serveur dans le portail Azure, puis sélectionnez **Paramétrage automatique** dans le menu.

![Serveur](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Notez que l’option **DROP_INDEX** n’est pas compatible avec les applications utilisant la commutation de partition et les indicateurs d’index, et ne doit pas être activée dans ces cas.
>

Sélectionnez les options de réglage automatique que vous souhaitez activer et sélectionnez **Appliquer**.

Les options de réglage automatique sur un serveur sont appliquées à toutes les bases de données de ce serveur. Par défaut, toutes les bases de données héritent de la configuration de leur serveur parent, mais celle-ci peut être remplacée et spécifiée individuellement pour chaque base de données.

### <a name="rest-api"></a>API REST

Découvrez-en davantage sur l’utilisation de l’API REST pour activer le réglage automatique sur un serveur en consultant [Méthodes UPDATE et GET HTTP du réglage automatique SQL Server](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).


## <a name="enable-automatic-tuning-on-an-individual-database"></a>Activer le réglage automatique sur une base de données individuelle

Azure SQL Database vous permet de spécifier individuellement la configuration de réglage automatique pour chaque base de données. Au niveau de la base de données, vous pouvez choisir d’hériter de la configuration du réglage automatique du serveur parent, des valeurs Azure par défaut ou de ne pas hériter de la configuration. Les valeurs Azure par défaut sont définies sur FORCE_LAST_GOOD_PLAN activé, CREATE_INDEX activé et DROP_INDEX désactivé.

> [!NOTE]
> Il est généralement recommandé de gérer la configuration du réglage automatique au **niveau du serveur**, afin que les mêmes paramètres de configuration soient appliqués automatiquement à chaque base de données. Configurez le paramétrage automatique sur une base de données individuelle seulement si vous avez besoin que celle-ci ait des paramètres différents de ceux dont les autres ont hérité du même serveur.
>

### <a name="azure-portal"></a>Portail Azure

Pour activer le réglage automatique sur **une seule base de données**, accédez à la base de données dans le portail Azure et sélectionnez **Paramétrage automatique**.

Des paramètres de réglage automatique individuels peuvent être configurés séparément pour chaque base de données. Vous pouvez configurer manuellement une option de réglage automatique individuelle ou spécifier qu’une option hérite de ses paramètres du serveur.

![Base de données](./media/sql-database-automatic-tuning-enable/database.png)

Notez que l’option DROP_INDEX n’est pas compatible avec les applications utilisant la commutation de partition et les indicateurs d’index, et ne doit pas être activée dans ces cas.

Une fois que vous avez sélectionné la configuration voulue, cliquez sur **Appliquer**.

### <a name="rest-api"></a>API REST

Découvrez-en davantage sur l’utilisation de l’API REST pour activer le réglage automatique sur une base de données unique en consultant [Méthodes UPDATE et GET HTTP du réglage automatique SQL Database](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Pour activer le réglage automatique sur une base de données par le biais de T-SQL, connectez-vous à la base de données et exécutez la requête suivante :

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Si vous affectez la valeur AUTO au réglage automatique, les Valeurs Azure par défaut sont appliquées. Si vous affectez la valeur INHERIT, la configuration du réglage automatique est héritée du serveur parent. Si vous choisissez CUSTOM, vous devez configurer manuellement le réglage automatique.

Pour configurer des options de réglage automatique individuelles par le biais de T-SQL, connectez-vous à la base de données et exécutez une requête telle que celle-ci :

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Si vous affectez la valeur ON à l’option de réglage, les paramètres héritées par la base de données sont substitués et l’option de réglage est activée. Si vous affectez la valeur OFF, les paramètres héritées par la base de données sont également substitués et l’option de réglage est désactivée. L’option de réglage automatique pour laquelle DEFAULT est spécifié hérite de la configuration du paramètre de réglage automatique de niveau base de données.  

Découvrez-en davantage sur les options T-SQL pour configurer le réglage automatique en consultant [Options ALTER DATABASE SET (Transact-SQL) pour le serveur logique SQL Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Désactivée par le système
Le réglage automatique surveille toutes les actions effectuées sur la base de données et, dans certains cas, il peut déterminer que le réglage automatique ne peut pas fonctionner correctement sur la base de données. Dans ce cas, l’option de réglage est désactivée par le système. Dans la plupart des cas, cela est dû au fait que le Magasin des requêtes n’est pas activé ou est en lecture seule sur une base de données spécifique.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurer les notifications par e-mail sur l’ajustement automatique

Consultez le guide [Notifications par e-mail du réglage automatique](sql-database-automatic-tuning-email-notifications.md).

## <a name="next-steps"></a>Étapes suivantes
* Lisez l’[article Réglage automatique](sql-database-automatic-tuning.md) pour en savoir plus sur le réglage automatique et sur la manière dont il peut vous aider à améliorer vos performances.
* Consultez [Recommandations en matière de performances](sql-database-advisor.md) pour obtenir une vue d’ensemble des recommandations relatives aux performances Azure SQL Database.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md).
