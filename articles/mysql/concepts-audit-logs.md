---
title: Journaux d’audit pour Azure Database pour MySQL
description: Décrit les journaux d’audit disponibles dans Azure Database pour MySQL et les paramètres disponibles pour l’activation des niveaux de journalisation.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443838"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Journaux d’audit dans Azure Database pour MySQL

Dans Azure Database pour MySQL, le journal d’audit est disponible pour les utilisateurs. Le journal d’audit peut être utilisé pour suivre l’activité au niveau de la base de données et est couramment utilisé à des fins de conformité.

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité Journal d’audit n’existe qu’en préversion.

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

Par défaut, le journal d’audit est désactivé. Pour l’activer, définissez `audit_log_enabled` sur Activé.

Les autres paramètres que vous pouvez ajuster incluent :

- `audit_log_events` : contrôle les événements à consigner. Consultez le tableau ci-dessous pour des événements d’audit spécifiques.
- `audit_log_exclude_users`: utilisateurs MySQL à exclure de la journalisation. Quatre utilisateurs maximum sont autorisés. La longueur maximale du paramètre est de 256 caractères.

| **Event** | **Description** |
|---|---|
| `CONNECTION` | - Initialisation de la connexion (réussite ou échec) <br> - Réauthentification d’utilisateur avec un autre utilisateur/mot de passe en cours de session <br> - Arrêt de la connexion |
| `DML_SELECT`| Requêtes SELECT |
| `DML_NONSELECT` | Requêtes INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Requêtes telles que « DROP DATABASE » |
| `DCL` | Requêtes telles que « GRANT PERMISSION » |
| `ADMIN` | Requêtes telles que « SHOW STATUS » |
| `GENERAL` | Tout dans DML_SELECT, DML_NONSELECT, DML, DDL, DCL et ADMIN |
| `TABLE_ACCESS` | - Uniquement disponible pour MySQL 5.7 <br> - Instructions de lecture de table, telles que SELECT ou INSERT INTO... SELECT <br> - Instructions de suppression de table, telles que DELETE ou TRUNCATE TABLE <br> - Instructions d’insertion de table, telles que INSERT ou REPLACE <br> - Instructions de mise à jour de table, telles que UPDATE |

## <a name="access-audit-logs"></a>Accéder aux journaux d’audit

Les journaux d’audit sont intégrés aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux d’audit sur votre serveur MySQL, vous pouvez les transmettre vers des journaux Azure Monitor, des Event Hubs ou Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic dans le portail Azure, consultez l’[article sur le portail de journal d’audit](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémas des journaux de diagnostic

Les sections suivantes décrivent la sortie des journaux d’audit MySQL en fonction du type d’événement. En fonction de la méthode de sortie, les champs inclus et l’ordre dans lequel ils apparaissent peuvent varier.

### <a name="connection"></a>Connexion

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nom du serveur |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (uniquement disponible pour MySQL 5.7) |
| `connection_id_d` | ID de connexion unique généré par MySQL |
| `host_s` | Vide |
| `ip_s` | Adresse IP du client qui se connecte à MySQL |
| `user_s` | Nom de l’utilisateur qui exécute la requête |
| `db_s` | Nom de la base de données connectée |
| `\_ResourceId` | URI de ressource |

### <a name="general"></a>Généralités

Le schéma ci-dessous s’applique aux types d’événements GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL et ADMIN.

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nom du serveur |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (uniquement disponible pour MySQL 5.6) |
| `event_time` | Secondes de démarrage de requête en horodatage UNIX |
| `error_code_d` | Code d’erreur en cas d’échec de la requête. `0` signifie aucune erreur |
| `thread_id_d` | ID du thread qui a exécuté la requête |
| `host_s` | Vide |
| `ip_s` | Adresse IP du client qui se connecte à MySQL |
| `user_s` | Nom de l’utilisateur qui exécute la requête |
| `sql_text_s` | Texte de la requête complète |
| `\_ResourceId` | URI de ressource |

### <a name="table-access"></a>Accès à la table

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nom du serveur |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de connexion unique généré par MySQL |
| `db_s` | Nom de la base de données accédée |
| `table_s` | Nom de la table accédée |
| `sql_text_s` | Texte de la requête complète |
| `\_ResourceId` | URI de ressource |

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour configurer les journaux d’audit dans le portail Azure](howto-configure-audit-logs-portal.md)