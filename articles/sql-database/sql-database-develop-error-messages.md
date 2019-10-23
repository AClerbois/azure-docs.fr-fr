---
title: 'Codes d’erreur SQL : erreur de connexion de base de données | Microsoft Docs'
description: 'En savoir plus sur les codes d’erreur SQL pour les applications clientes SQL Database, tels que les erreurs de connexion de base de données courantes, les problèmes de copie de base de données et les erreurs générales. '
keywords: code d’erreur sql, accès sql, erreur de connexion de base de données, codes d’erreur sql
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 8d4e7fa314ce3a5f8534e7742880114ccc7f7144
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598142"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Codes d’erreur SQL pour les applications clientes SQL Database : erreurs de connexion de base de données et autres problèmes

Cet article répertorie les codes d’erreur SQL pour les applications clientes SQL Database, y compris les erreurs de connexion de base de données, les erreurs temporaires, les erreurs de gouvernance des ressources, les problèmes de copie de base de données, le pool élastique et d’autres erreurs. La plupart des catégories sont spécifiques à Azure SQL Database et ne s'appliquent pas à Microsoft SQL Server. Voir aussi [Messages d’erreur système](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erreurs de connexion de base de données et erreurs temporaires

Le tableau suivant décrit les codes d’erreur SQL pour les erreurs de perte de connexion, et autres erreurs temporaires, que vous pouvez rencontrer quand votre application tente d’accéder à SQL Database. Pour des tutoriels de démarrage sur la façon de se connecter à Azure SQL Database, consultez [Connexion à Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erreurs de connexion de base de données et erreurs temporaires les plus courantes

L’infrastructure Azure a la capacité de reconfigurer dynamiquement les serveurs quand des charges de travail importantes sont à traiter dans le service SQL Database.  Ce comportement dynamique peut entraîner la perte par votre programme client de sa connexion à SQL Database. Ce type d’erreur état est connu sous le nom d’ *erreur temporaire*.

Il est fortement recommandé que votre programme client possède une logique de nouvelle tentative afin qu’il puisse rétablir une connexion après avoir donné à l’erreur temporaire le temps de se corriger elle-même.  Nous vous recommandons de patienter 5 secondes avant votre première tentative. Si vous effectuez une nouvelle tentative avant 5 secondes, vous risquez de submerger le service cloud. Pour chaque nouvelle tentative, le délai doit augmenter de manière exponentielle, sans dépasser 60 secondes.

Les erreurs temporaires se manifestent généralement comme l'un des messages d'erreur suivants à partir de vos programmes clients :

* La base de données &lt;db_name&gt; sur le serveur &lt;Azure_instance&gt; n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. Si le problème persiste, contactez le support technique en indiquant l’ID de suivi de session &lt;session_id&gt;.
* La base de données &lt;db_name&gt; sur le serveur &lt;Azure_instance&gt; n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. Si le problème persiste, contactez le support technique en indiquant l’ID de suivi de session &lt;session_id&gt;. (Microsoft SQL Server, erreur : 40613)
* Une connexion existante a été fermée de force par l'hôte distant.
* System.Data.Entity.Core.EntityCommandExecutionException : Une erreur s’est produite lors de la définition de la commande. Consultez l'exception interne pour plus d'informations. ---> System.Data.SqlClient.SqlException : Une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur. (fournisseur : Fournisseur de session, erreur : 19 - connexion physique inutilisable)
* Une tentative de connexion à une base de données secondaire a échoué car la base de données est en cours de reconfiguration et qu’elle est occupée à appliquer de nouvelles pages tout en gérant une transaction active sur la base de données primaire. 

Pour obtenir des exemples de code de logique de nouvelle tentative, voir :

* [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md) 
* [Actions servant à corriger les erreurs de connexion et les erreurs temporaires dans la base de données SQL](sql-database-connectivity-issues.md)

Pour en savoir plus sur la *période de blocage* des clients qui utilisent ADO.NET, consultez la page [Regroupement de connexions SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Codes d’erreur pour les erreurs temporaires

Les erreurs suivantes sont temporaires et doivent être relancées dans la logique d’application : 

| Code d'erreur | severity | Description |
| ---:| ---:|:--- |
| 4060 |16 |Impossible d'ouvrir de base de données "%.&#x2a;ls" demandée par la connexion. La connexion a échoué. Pour plus d’informations, consultez [Erreurs 4000 à 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Le service a rencontré une erreur lors du traitement de votre demande. Réessayez. Code d'erreur % d.<br/><br/>Vous recevez cette erreur lorsque le service est arrêté en raison de mises à niveau logicielles ou matérielles, de pannes de matériel ou tout autre problème de basculement. Le code d’erreur (%d) [incorporé au message d’erreur 40197](sql-database-develop-error-messages.md#embedded-error-codes) fournit des informations supplémentaires sur le type de défaillance ou de basculement survenu. 40020, 40143, 40166 et 40540 sont des exemples de codes d'erreur incorporés au message d'erreur 40197.<br/><br/>La reconnexion à votre serveur SQL Database vous reconnecte automatiquement à une copie saine de votre base de données. Votre application doit détecter l'erreur 40197, consigner le code d'erreur incorporé (%d) dans le message pour la résolution des problèmes, et essayer de se reconnecter à la base de données SQL jusqu'à ce que les ressources soient disponibles et que votre connexion soit rétablie. Pour plus d’informations, consultez [Erreurs temporaires](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Le service est actuellement occupé. Relancez la demande dans 10 secondes. ID de l'incident : %ls. Code : %d. Pour plus d'informations, consultez les pages suivantes : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md)|
| 40613 |17 |La base de données ’%.&#x2a;ls’ sur le serveur ’%.&#x2a;ls’ n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. Si le problème persiste, contactez le support technique en indiquant l'ID de suivi de session ’%.&#x2a;ls’’.<br/><br/> Cette erreur peut se produire s’il existe déjà une connexion d’administrateur dédiée à la base de données. Pour plus d’informations, consultez [Erreurs temporaires](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Impossible de traiter la requête. Ressources insuffisantes pour traiter la demande.<br/><br/>Le service est actuellement occupé. Relancez la requête ultérieurement. Pour plus d'informations, consultez les pages suivantes : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) |
| 49919 |16 |Processus ne peut pas créer ou mettre à jour de la demande. Opérations de mise à jour ou de création en cours pour l'abonnement « % ld » trop nombreuses.<br/><br/>Le service est occupé à traiter plusieurs demandes de création ou de mise à jour pour votre abonnement ou le serveur. Les requêtes sont actuellement bloquées pour l’optimisation des ressources. Requête [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pour les opérations en attente. Patientez jusqu’à ce que les demandes de création ou de mise à jour soient terminées ou supprimez l’une de vos requêtes en cours et réessayez votre requête ultérieurement. Pour plus d'informations, consultez les pages suivantes : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) |
| 49920 |16 |Impossible de traiter la requête. Opérations en cours pour l'abonnement « % ld » trop nombreuses.<br/><br/>Le service est occupé à traiter plusieurs demandes pour cet abonnement. Les requêtes sont actuellement bloquées pour l’optimisation des ressources. Requête [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pour l'état de l'opération. Patientez jusqu’à ce que les requêtes soient terminées ou supprimez l’une de vos requêtes en cours et réessayez votre requête ultérieurement. Pour plus d'informations, consultez les pages suivantes : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) |
| 4221 |16 |La connexion au serveur de lecture secondaire a échoué en raison d’une longue attente sur 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. Le réplica n’est pas disponible pour la connexion, car il manque des versions de ligne pour les transactions qui étaient en cours lorsque le réplica a été recyclé. Le problème peut être résolu en restaurant ou en validant les transactions actives au niveau du réplica principal. Les occurrences de cette erreur peuvent être réduites en évitant les transactions d’écriture longues sur le serveur principal. |

## <a name="embedded-error-codes"></a>Codes d’erreur incorporés

Les erreurs suivantes sont incorporées dans le code d’erreur plus général 40197 :

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Code d'erreur | severity | Description | 
| ---:| ---:|:---|
|  1104 |17 |Espace insuffisant pour TEMPDB pendant le dépassement. Créez de l’espace en supprimant des objets et/ou réécrivez la requête pour qu’elle occupe moins de lignes. Si le problème persiste, passez à un objectif de niveau service supérieur.|
| 40020 |16 |La base de données est en cours de transition et les transactions sont arrêtées.|
| 40143 |16 |Le réplica que le nœud de données héberge pour la partition demandée n’est pas le réplica principal.|
| 40166 |16 |Une reconfiguration CloudDB est en cours et toutes les nouvelles transactions utilisateur sont abandonnées.|
| 40540 |16 |La transaction a été abandonnée, car la base de données a été placée en mode de lecture seule. Cette situation est temporaire. Recommencez l’opération.|

Vous trouverez plus d’informations sur les autres erreurs incorporées en interrogeant `sys.messages` :

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Erreurs de copie de base de données

Les erreurs suivantes peuvent survenir lors de la copie d’une base de données dans Azure SQL Database. Pour en savoir plus, consultez [Copie d’une base de données Azure SQL](sql-database-copy.md).

| Code d'erreur | severity | Description |
| ---:| ---:|:--- |
| 40635 |16 |Le client avec l'adresse IP’%.&#x2a;ls’ est temporairement désactivé. |
| 40637 |16 |La copie de base de données est actuellement désactivée. |
| 40561 |16 |La copie de base de données a échoué. La base de données source ou cible n'existe pas. |
| 40562 |16 |La copie de base de données a échoué. La base de données source a été supprimée. |
| 40563 |16 |La copie de base de données a échoué. La base de données cible a été supprimée. |
| 40564 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40565 |16 |La copie de base de données a échoué. Pas plus d'une copie de base de données simultanée de la même source est autorisée. Supprimez la base de données cible et réessayez ultérieurement. |
| 40566 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40567 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40568 |16 |La copie de base de données a échoué. La base de données source n'est plus disponible. Supprimez la base de données cible et réessayez. |
| 40569 |16 |La copie de base de données a échoué. La base de données cible n'est plus disponible. Supprimez la base de données cible et réessayez. |
| 40570 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement. |
| 40571 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement. |

## <a name="resource-governance-errors"></a>Erreurs de gouvernance des ressources

Les erreurs suivantes sont causées par une utilisation excessive des ressources avec Azure SQL Database. Par exemple :

* Votre transaction a été ouverte trop longtemps.
* Votre transaction comporte trop de verrous.
* Une application consomme trop de mémoire.
* Une application consomme trop de ressources d’espace `TempDb` .

Rubriques connexes :

* Pour plus d'informations, consultez les pages suivantes :
  * [Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)
  * [Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)
  * [Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)
  * [Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)
  * [Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) 

| Code d'erreur | severity | Description |
| ---:| ---:|:--- |
| 10928 |20 |ID de la ressource : %d. %d, la limite %s de la base de données a été atteinte. Pour en savoir plus, consultez [Limites de ressources de SQL Database pour les bases de données uniques et mises en pool](sql-database-resource-limits-database-server.md).<br/><br/>L’ID de ressource indique la ressource qui a atteint la limite. Pour les threads de travail, ID de la ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>Pour en savoir plus sur cette erreur et sa résolution, consultez : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) |
| 10929 |20 |ID de la ressource : %d. La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. L’ID de ressource indique la ressource qui a atteint la limite. Pour les threads de travail, ID de la ressource = 1. Pour les sessions, l’ID de ressource = 2. Pour plus d'informations, consultez les pages suivantes : <br/>&bull; &nbsp;[Limites de ressources du serveur de base de données](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limites de DTU pour les bases de données uniques](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md) <br/>Sinon, réessayez plus tard. |
| 40544 |20 |La base de données a atteint son quota de taille. Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles. Pour plus d’informations sur la mise à l’échelle des bases de données, consultez [Mettre à l’échelle des ressources de base de données unique](sql-database-single-database-scale.md) et [Mettre à l’échelle des ressources de pool élastique](sql-database-elastic-pool-scale.md).|
| 40549 |16 |La session est arrêtée, car l’une des transactions est de longue durée. Essayez de la raccourcir. Pour plus d’informations sur le traitement par lot, consultez [Comment utiliser le traitement par lot pour améliorer les performances des applications de base de données SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |La session a été arrêtée, car elle a acquis trop de verrous. Essayez de lire ou de modifier moins de lignes dans une transaction unique. Pour plus d’informations sur le traitement par lot, consultez [Comment utiliser le traitement par lot pour améliorer les performances des applications de base de données SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |La session a été arrêtée en raison de l’utilisation excessive de `TEMPDB` . Essayez de modifier votre requête pour réduire l’espace utilisé par la table temporaire.<br/><br/>Si vous utilisez des objets temporaires, conservez de l’espace dans la base de données `TEMPDB` en supprimant des objets temporaires une fois qu’ils ne sont plus nécessaires à la session. Pour plus d’informations sur l’utilisation de tempdb dans SQL Database, consultez [Base de données tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |La session a été arrêtée en raison de l’utilisation excessive de l’espace réservé au journal des transactions. Essayez de modifier moins de lignes dans une transaction unique. Pour plus d’informations sur le traitement par lot, consultez [Comment utiliser le traitement par lot pour améliorer les performances des applications de base de données SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Si vous effectuez des insertions en bloc à l’aide de l’utilitaire `bcp.exe` ou de la classe `System.Data.SqlClient.SqlBulkCopy`, essayez d’utiliser les options `-b batchsize` ou `BatchSize` permettant de limiter le nombre de lignes copiées sur le serveur à chaque transaction. Si vous reconstruisez un index en utilisant l’instruction `ALTER INDEX`, essayez d’utiliser l’option `REBUILD WITH ONLINE = ON`. Pour plus d’informations sur les tailles des journaux des transactions pour le modèle d’achat vCore, consultez : <br/>&bull; &nbsp;[Limites de vCores pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de ressources d’une instance gérée](sql-database-managed-instance-resource-limits.md)|
| 40553 |16 |La session a été arrêtée en raison d’une utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>La diminution du nombre d’opérations `ORDER BY` et `GROUP BY` dans votre code Transact-SQL réduit les besoins en mémoire de votre requête. Pour plus d’informations sur la mise à l’échelle des bases de données, consultez [Mettre à l’échelle des ressources de base de données unique](sql-database-single-database-scale.md) et [Mettre à l’échelle des ressources de pool élastique](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Erreurs relatives au pool élastique

Les erreurs suivantes sont liées à la création et à l’utilisation de pools élastiques :

| Code d'erreur | severity | Description | Action corrective |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Le pool élastique a atteint sa limite de stockage. Le taux d’utilisation du stockage pour le pool élastique ne doit pas dépasser (%d) Mo. Tentative d’écriture de données dans une base de données alors que la limite de stockage du pool élastique a été atteinte. Pour plus d’informations sur les limites de ressources, consultez : <br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |Envisagez si possible d’augmenter le nombre de DTU du pool élastique ou d’ajouter de la capacité de stockage à ce dernier afin d’accroître sa limite de stockage. Vous pouvez aussi réduire l’espace de stockage utilisé par les bases de données individuelles qu’il contient ou supprimer certaines de ses bases de données. Pour plus d’informations sur la mise à l’échelle d’un pool élastique, consultez [Mettre à l’échelle des ressources de pool élastique](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. Pour plus d’informations sur les limites de ressources, consultez : <br/>&bull; &nbsp;[Limites de DTU pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limites de vCores pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md) <br/> Sinon, réessayez plus tard. Nombre minimal de DTU/vCore par base de données ; nombre maximal de DTU/vCore par base de données. Le nombre total d’ouvriers simultanés (demandes) dans toutes les bases de données du pool élastique a failli dépasser la limite du pool. |Envisagez si possible d’augmenter le nombre de DTU ou de vCore du pool élastique afin d’accroître sa limite de rôles de travail, ou supprimez des bases de données du pool élastique. |
| 40844 | 16 |La base de données '%ls' sur le serveur '%ls' est une base de données présentant l’édition '%ls' dans un pool élastique. Elle ne peut pas présenter de relation de copie continue.  |N/A |
| 40857 | 16 |Pool élastique introuvable pour le serveur : '%ls'. Nom du pool élastique: '%ls'. Le pool élastique spécifié n’existe pas sur le serveur spécifié. | Indiquez un nom de pool élastique valide. |
| 40858 | 16 |Le pool élastique '%ls' existe déjà sur le serveur : '%ls'. Le pool élastique spécifié existe déjà sur le serveur SQL Database spécifié. | Saisissez un nouveau nom pour le pool élastique. |
| 40859 | 16 |Le pool élastique ne prend pas en charge le niveau de service '%ls'. Le niveau de service spécifié n’est pas pris en charge pour le provisioning du pool élastique. |Saisissez l’édition correcte, ou laissez le champ du niveau de service vide afin d’utiliser le niveau de service par défaut. |
| 40860 | 16 |La combinaison du pool élastique '%ls' et de l’objectif de service '%ls' n’est pas valide. Le pool élastique et le niveau de service ne peuvent être spécifiés ensemble que si le type de ressource est spécifié comme étant un « ElasticPool ». |Spécifiez la combinaison de pool élastique et de niveau de service adéquate. |
| 40861 | 16 |L’édition de base de données '%. **ls' ne peut pas être différente du niveau de service du pool élastique, qui correspond à '%.* *ls'. L’édition de base de données est différente du niveau de service du pool élastique. |Indiquez une édition de base de données identique au niveau de service du pool élastique.  Remarque : l’édition de base de données n’a pas besoin d’être spécifiée. |
| 40862 | 16 |Si l’objectif de service du pool élastique est spécifié, le nom du pool élastique doit l’être également. L’objectif de service du pool élastique n’identifie pas de manière unique un pool élastique. |Si vous utilisez l’objectif de service du pool élastique, spécifiez le nom du pool élastique. |
| 40864 | 16 |Le nombre de DTU du pool élastique doit se monter au minimum à (%d) pour le niveau de service '%.*ls'. Tentative de définition du nombre de DTU du pool élastique au-dessous de la limite minimale. |Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite minimale ou plus. |
| 40865 | 16 |Le nombre de DTU du pool élastique ne doit pas dépasser (%d) pour le niveau de service '%.*ls'. Tentative de définition du nombre de DTU du pool élastique au-dessus de la limite maximale. |Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite maximale ou moins. |
| 40867 | 16 |Le nombre maximal de DTU par base de données doit être au minimum (%d) pour le niveau de service '%.*ls'. Tentative de définition du nombre maximal de DTU par base de données en dessous de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40868 | 16 |Le nombre maximal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. Tentative de définition du nombre maximal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40870 | 16 |Le nombre minimal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. Tentative de définition du nombre minimal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40873 | 16 |Le nombre de bases de données (%d) et le nombre minimal de DTU par base de données (%d) ne peuvent pas dépasser le nombre de DTU du pool élastique (%d). Tentative de spécification d’un nombre minimal de DTU pour les bases de données dans le pool élastique dépassant le nombre de DTU du pool élastique. | Envisagez d’augmenter le nombre de DTU du pool élastique, ou de réduire le nombre minimal de DTU par base de données ou le nombre de bases de données dans le pool élastique. |
| 40877 | 16 |Impossible de supprimer un pool élastique, sauf s’il ne contient aucune base de données. Le pool élastique contient une ou plusieurs bases de données et ne peut donc pas être supprimé. |Supprimez les bases de données du pool élastique afin de pouvoir supprimer ce dernier. |
| 40881 | 16 |Le pool élastique '%.*ls' a atteint le nombre limite de bases de données.  Le nombre limite de bases de données ne peut pas dépasser (%d) pour un pool élastique incluant (%d) DTU. Tentative de création ou d’ajout d’une base de données au pool élastique alors que le nombre limite de bases de données du pool élastique a été atteint. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître le nombre limite de bases de données, ou de supprimer des bases de données du pool élastique. |
| 40889 | 16 |La limite de stockage ou relative aux DTU pour le pool élastique '%.*ls' ne peut pas être abaissée, car l’espace de stockage risque de ne pas être suffisant pour les bases de données de ce pool. Tentative de définition de la limite de stockage du pool élastique en dessous du taux d’utilisation de son espace de stockage. | Envisagez de réduire le taux d’utilisation du stockage des bases de données individuelles dans le pool élastique, ou de supprimer des bases de données dans le pool afin de réduire sa limite de stockage ou celles des DTU. |
| 40891 | 16 |Le nombre minimal de DTU par base de données (%d) ne peut pas dépasser le nombre maximal de DTU par base de données (%d). Tentative de définition d’un nombre minimal de DTU par base de données supérieur au nombre maximal de DTU par base de données. |Vérifiez que le nombre minimal de DTU par base de données ne dépasse pas le nombre maximal de DTU par base de données. |
| TBD | 16 |La taille de l’espace de stockage d’une base de données individuelle dans un pool élastique ne peut pas dépasser la taille maximale autorisée par le pool élastique de niveau de service '%.*ls'. La taille maximale de la base de données dépasse la taille maximale autorisée par le niveau de service du pool élastique. |Définissez la taille maximale de la base de données dans les limites de la taille maximale autorisée par le niveau de service du pool élastique. |

Rubriques connexes :

* [Créer un pool élastique (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Gérer un pool élastique (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Créer un pool élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Surveiller et gérer un pool élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Erreurs générales.

Les erreurs suivantes n’entrent dans aucune des catégories précédentes.

| Code d'erreur | severity | Description |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(Connexion de l’administrateur) n’est pas un nom valide, car il contient des caractères non valides.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |La connexion a échoué. La connexion provient d'un domaine non approuvé et ne peut pas être utilisée avec l’authentification Windows.%.&#x2a;ls (Les connexions Windows ne sont pas prises en charge dans cette version de SQL Server.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Échec de la connexion pour l'utilisateur ’%.&#x2a;ls’.%.&#x2a;ls%.&#x2a;ls(Échec de la connexion pour l’utilisateur "%.&#x2a;ls".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Échec de la connexion pour l'utilisateur '%.&#x2a;ls'. Raison : le compte est désactivé.%.&#x2a;ls |
| 40014 |16 |Plusieurs bases de données ne peuvent pas être utilisées dans la même transaction. |
| 40054 |16 |Les tables sans index de cluster ne sont pas prises en charge dans cette version de SQL Server. Créez un index de cluster et réessayez. |
| 40133 |15 |Cette opération n'est pas prise en charge dans cette version de SQL Server. |
| 40506 |16 |Le SID spécifié n'est pas valide pour cette version de SQL Server. |
| 40507 |16 |'%.&#x2a;ls' ne peut pas être appelé avec des paramètres dans cette version de SQL Server. |
| 40508 |16 |L'instruction USE n'est pas prise en charge pour basculer d'une base de données à une autre. Utilisez une nouvelle connexion pour vous connecter à une autre base de données. |
| 40510 |16 |L'instruction '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server |
| 40511 |16 |La fonction intégrée '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server. |
| 40512 |16 |La fonctionnalité déconseillée '%ls' n'est pas prise en charge dans cette version de SQL Server. |
| 40513 |16 |La variable de serveur '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server. |
| 40514 |16 |'%.ls' n'est pas pris en charge dans cette version de SQL Server. |
| 40515 |16 |La référence au nom de base de données et/ou serveur dans '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server. |
| 40516 |16 |Les objets temporaires globaux ne sont pas pris en charge dans cette version de SQL Server. |
| 40517 |16 |Le mot clé ou l'option d'instruction '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server. |
| 40518 |16 |La commande DBCC '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server. |
| 40520 |16 |La classe sécurisable '%S_MSG' n'est pas prise en charge dans cette version de SQL Server. |
| 40521 |16 |La classe sécurisable '%S_MSG' n'est pas prise en charge dans le champ d'application du serveur pour cette version de SQL Server. |
| 40522 |16 |Le type de base de données principal '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server. |
| 40523 |16 |La création de l'utilisateur implicite '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server. Créez explicitement l'utilisateur avant de l'utiliser. |
| 40524 |16 |Le type de données '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server. |
| 40525 |16 |WITH '%.ls' n'est pas en charge cette version de SQL Server. |
| 40526 |16 |Le fournisseur d'ensemble de lignes '%.&#x2a;ls' n'est pris en charge dans cette version de SQL Server. |
| 40527 |16 |Les serveurs liés ne sont pas pris en charge dans cette version de SQL Server. |
| 40528 |16 |Les utilisateurs ne peuvent pas être mappés à des certificats, clés asymétriques ou connexions Windows dans cette version de SQL Server. |
| 40529 |16 |La fonction intégrée '%.&#x2a;ls' dans le contexte d'emprunt d'identité n'est pas prise en charge dans cette version de SQL Server. |
| 40532 |11 |Impossible d'ouvrir le serveur "%.&#x2a;ls" demandé par la connexion. La connexion a échoué. |
| 40553 |16 |La session a été arrêtée en raison d’une utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/> La diminution du nombre d’opérations `ORDER BY` et `GROUP BY` dans votre code Transact-SQL réduit les besoins en mémoire de votre requête. |
| 40604 |16 |Opération CREATE/ALTER DATABASE impossible car elle dépasse le quota du serveur. |
| 40606 |16 |L'association de bases de données n'est pas prise en charge dans cette version de SQL Server. |
| 40607 |16 |Les connexions Windows ne sont pas prises en charge dans cette version de SQL Server. |
| 40611 |16 |Les serveurs peuvent avoir au maximum 128 règles de pare-feu définies. |
| 40614 |16 |L'adresse IP de début de la règle de pare-feu ne peut pas dépasser l'adresse IP de fin. |
| 40615 |16 |Impossible d'ouvrir le serveur '{0}' demandé par la connexion. Le client avec l'adresse IP '{1}' n'est pas autorisé à accéder au serveur.<br /><br />Pour activer l’accès, utilisez le portail de la base de données SQL Database ou exécutez l’élément sp\_set\_firewall\_rule sur la base de données MASTER, afin de créer une règle de pare-feu pour cette adresse IP ou cette plage d’adresses. Cela peut prendre jusqu’à cinq minutes pour que cette modification prenne effet. |
| 40617 |16 |Le nom de la règle de pare-feu qui commence par (nom de règle) est trop long. La longueur maximale est 128. |
| 40618 |16 |Le nom de la règle de pare-feu ne peut pas être vide. |
| 40620 |16 |Échec de la connexion pour l'utilisateur "%.&#x2a;ls". La modification du mot de passe a échoué. La modification du mot de passe lors de la connexion n'est pas prise en charge dans cette version de SQL Server. |
| 40627 |20 |L'opération sur le serveur '{0}' et la base de données '{1}' est en cours. Veuillez patienter quelques minutes avant de réessayer. |
| 40630 |16 |Échec de la validation de mot de passe. Le mot de passe ne respecte pas les exigences de la stratégie car il est trop court. |
| 40631 |16 |Le mot de passe que vous avez spécifié est trop long. Le mot de passe doit avoir au maximum 128 caractères. |
| 40632 |16 |Échec de la validation de mot de passe. Le mot de passe ne respecte pas les exigences de la stratégie car il n'est pas assez complexe. |
| 40636 |16 |Impossible d'utiliser un nom de base de données réservé '%.&#x2a;ls' dans cette opération. |
| 40638 |16 |ID d’abonnement (id d’abonnement) non valide. L'abonnement n'existe pas. |
| 40639 |16 |La requête n’est pas conforme au schéma : (erreur de schéma). |
| 40640 |20 |Le serveur a rencontré une exception inattendue. |
| 40641 |16 |L'emplacement spécifié n'est pas valide. |
| 40642 |17 |Le serveur est actuellement trop occupé. Veuillez réessayer plus tard. |
| 40643 |16 |La valeur d'en-tête x-ms-version spécifiée n'est pas valide. |
| 40644 |14 |Impossible d'autoriser l'accès à l'abonnement spécifié. |
| 40645 |16 |Le nom de serveur (nom de serveur) ne peut pas être vide ou nul. Il peut uniquement contenir les lettres minuscules 'a' à 'z', les chiffres 0 à 9 et le trait d'union. Le trait d'union ne peut pas figurer au début ou à la fin du nom. |
| 40646 |16 |L'ID d'abonnement ne peut pas être vide. |
| 40647 |16 |L’abonnement (id d’abonnement) ne contient pas le serveur (nom de serveur). |
| 40648 |17 |Trop de demandes ont été effectuées. Veuillez réessayer ultérieurement. |
| 40649 |16 |Un type de contenu non valide est spécifié. Seul application/xml est pris en charge. |
| 40650 |16 |L’abonnement (id d’abonnement) n’existe pas ou n’est pas prêt pour l’opération. |
| 40651 |16 |Impossible de créer le serveur, car l’abonnement (id d’abonnement) est désactivé. |
| 40652 |16 |Impossible de déplacer ou de créer le serveur. L’abonnement (id d’abonnement) va dépasser le quota du serveur. |
| 40671 |17 |Échec de la communication entre la passerelle et le service de gestion. Veuillez réessayer ultérieurement. |
| 40852 |16 |Impossible d’ouvrir la base de données '%.\*ls' on server '%.\*ls' demandée par la connexion. L’accès à la base de données est autorisé uniquement à l’aide d’une chaîne de connexion sécurisée. Pour accéder à cette base de données, modifiez vos chaînes de connexion pour y insérer « secure » sur le serveur de nom de domaine complet  -  ’nom de servur’.database.windows.net doit être transformé en ’nom de serveur’.database`secure`.windows.net. |
| 40914 | 16 | Impossible d’ouvrir le serveur « *[nom-serveur]* » demandé par la connexion. Le client n’est pas autorisé à accéder au serveur.<br /><br />Pour résoudre le problème, envisagez d’ajouter une [règle de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |Le système Azure SQL est en cours de chargement et place une limite supérieure sur les opérations DB CRUD simultanées pour un serveur SQL Database unique (par exemple, créer la base de données). Le serveur spécifié dans le message d'erreur a dépassé le nombre maximal de connexions simultanées. Réessayez ultérieurement. |
| 45169 |16 |Le système Azure SQL est en cours de chargement et place une limite supérieure sur les opérations CRUD simultanées pour un abonnement unique (par exemple, créer le serveur). L'abonnement spécifié dans le message d'erreur a dépassé le nombre maximal de connexions simultanées, et la demande a été rejetée. Réessayez ultérieurement. |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Fonctionnalités Azure SQL Database](sql-database-features.md).
* Découvrez le [modèle d’achat DTU](sql-database-service-tiers-dtu.md).
* En savoir plus sur le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

