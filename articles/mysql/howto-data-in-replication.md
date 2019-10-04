---
title: Configurer la réplication de données entrantes pour répliquer des données dans Azure Database pour MySQL.
description: Cet article décrit comment configurer la réplication des données entrantes pour Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: 7d56d7f8fcbd53d4f69863d260591ef80f3d7188
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459077"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Comment configurer Azure Database pour MySQL pour la réplication de données entrantes MySQL

Dans cet article, vous allez apprendre à configurer la réplication des données entrantes dans le service Azure Database pour MySQL en configurant les serveurs réplica et maître. La réplication des données entrantes permet de synchroniser les données provenant d’un serveur MySQL maître qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans un réplica au sein du service Azure Database pour MySQL. 

Cet article suppose que vous ayez déjà utilisé les serveurs et base de données MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Créer un serveur MySQL à utiliser en tant que réplica

1. Créer un nouveau serveur Azure Database pour MySQL

   Créez un nouveau serveur MySQL (par exemple, « replica.mysql.database.azure.com »). Reportez-vous à [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md) pour la création de serveurs. Ce serveur est le serveur « réplica » dans la réplication de données entrantes.

   > [!IMPORTANT]
   > Ce serveur Azure Database pour MySQL doit être créé dans les niveaux de tarification à usage général ou à mémoire optimisée.
   > 

2. Créer les mêmes comptes d’utilisateur et les privilèges correspondants

   Les comptes d’utilisateur ne sont pas répliqués à partir du serveur maître vers le serveur réplica. Si vous prévoyez de fournir aux utilisateurs un accès au serveur réplica, vous devez créer manuellement tous les comptes et privilèges correspondants sur ce nouveau serveur Azure Database pour MySQL.

## <a name="configure-the-master-server"></a>Configurer le serveur maître
Les étapes suivantes servent à préparer et à configurer le serveur MySQL hébergé localement, dans une machine virtuelle ou par un service de base de données hébergé par d’autres fournisseurs de cloud pour la réplication de données entrantes. Ce serveur est le « maître » dans la réplication de données entrantes. 

1. Activer la journalisation binaire

   Vérifiez si la journalisation binaire a été activée sur le serveur maître en exécutant la commande suivante : 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Si la variable [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) est renvoyée avec la valeur « ON », la journalisation binaire est activée sur votre serveur. 

   Si `log_bin` est renvoyé avec la valeur « OFF », activez la journalisation binaire en modifiant votre fichier my.cnf ainsi que `log_bin=ON` et redémarrez le serveur pour que la modification prenne effet.

2. Paramètres du serveur maître

   La réplication des données entrantes requiert que le paramètre `lower_case_table_names` soit cohérent entre les serveurs maître et réplica. Par défaut, ce paramètre est 1 dans Azure Database pour MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Créer un nouveau rôle de réplication et définir une autorisation

   Créez un compte d’utilisateur sur le serveur maître configuré avec des privilèges de réplication. Cela est possible via des commandes SQL ou un outil tel que MySQL Workbench. Si vous prévoyez une réplication avec SSL, cela doit être spécifié lors de la création de l’utilisateur. Pour comprendre comment [ajouter des comptes d’utilisateur](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) sur votre serveur maître, reportez-vous à la documentation MySQL. 

   Dans les commandes ci-dessous, le nouveau rôle de réplication créé peut accéder au serveur maître à partir de n’importe quelle machine, et pas seulement de celle qui héberge le serveur maître. Cela s’effectue en spécifiant « syncuser@’%’ » dans la commande de création d’un utilisateur. Consultez la documentation MySQL pour en savoir plus sur la [définition des noms de compte](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Commande SQL**

   *Réplication avec SSL*

   Pour exiger le protocole SSL pour toutes les connexions utilisateur, utilisez la commande suivante pour créer un utilisateur : 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Réplication sans SSL*

   Si le protocole SSL n’est pas requis pour toutes les connexions, utilisez la commande suivante pour créer un utilisateur :

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Pour créer le rôle de réplication dans MySQL Workbench, ouvrez le panneau **Utilisateurs et privilèges** à partir du panneau **Gestion**. Cliquez ensuite sur **Ajouter un compte**. 
 
   ![Utilisateurs et privilèges](./media/howto-data-in-replication/users_privileges.png)

   Tapez le nom d’utilisateur dans le champ **Nom de connexion**. 

   ![Synchroniser l’utilisateur](./media/howto-data-in-replication/syncuser.png)
 
   Cliquez sur le panneau **Rôles administratifs**, puis sélectionnez **Subordonné de réplication** dans la liste **Privilèges globaux**. Cliquez ensuite sur **Appliquer** pour créer le rôle de réplication.

   ![Subordonné de réplication](./media/howto-data-in-replication/replicationslave.png)


4. Définir le serveur maître en mode en lecture seule

   Avant de commencer à vider la base de données, le serveur doit être placé en mode lecture seule. En mode lecture seule, le serveur maître ne peut traiter aucune transaction d’écriture. Évaluer l’impact sur votre entreprise et planifiez la fenêtre de lecture seule lors d’une période creuse, si nécessaire.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenir le nom du fichier du journal binaire et le décalage

   Exécutez la commande [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) afin de déterminer le nom de fichier du journal binaire actuel et le décalage.
    
   ```sql
   show master status;
   ```
   Les résultats doivent être comme suit. Assurez-vous de noter le nom du fichier binaire, tel qu’il sera utilisé lors des étapes ultérieures.

   ![Résultats de l’état récapitulatif](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Vider et restaurer le serveur maître

1. Vider toutes les bases de données du serveur maître

   Vous pouvez utiliser mysqldump pour vider les bases de données de votre serveur maître. Pour plus d’informations, reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer). Il n’est pas nécessaire de vider les bibliothèques MySQL et de test.

2. Définir le serveur maître en mode lecture/écriture

   Une fois la base de données vidée, remettez le serveur MySQL maître en mode de lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurer le fichier de vidage sur le nouveau serveur

   Restaurez le fichier de vidage sur le serveur créé dans le service Azure Database pour MySQL. Reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer) pour savoir comment restaurer un fichier de vidage sur un serveur MySQL. Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le serveur Azure Database pour MySQL à partir de la machine virtuelle.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs maître et réplica pour démarrer la réplication des données entrantes

1. Définir un serveur maître

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Data-in Replication Stored Procedures](reference-data-in-stored-procedures.md) (Procédures stockées de réplication de données entrantes). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench. 

   Pour lier deux serveurs et démarrer une réplication, connectez-vous au serveur réplica cible dans le service Azure Database pour MySQL, et définissez l’instance externe en tant que serveur maître. Pour ce faire, utilisez la procédure stockée `mysql.az_replication_change_master` sur le serveur Azure DB pour MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host : nom d’hôte du serveur maître
   - master_user : nom d’utilisateur pour le serveur maître
   - master_password : mot de passe pour le serveur maître
   - master_log_file : nom de fichier du journal binaire à partir de l’exécution de `show master status`
   - master_log_pos : position du journal binaire à partir de l’exécution de `show master status`
   - master_ssl_ca : contexte du certificat d’autorité de certification. Si vous n’utilisez pas le protocole SSL, transmettez une chaîne vide.
       - Il est recommandé de transmettre ce paramètre en tant que variable. Pour plus d’informations, consultez les exemples suivants.

> [!NOTE]
> Si le serveur maître est hébergé dans une machine virtuelle Azure, activez l’option « Autoriser l’accès aux services Azure » pour autoriser les serveurs maîtres et de réplica à communiquer entre eux. Ce paramètre peut être modifié dans les options de **sécurité de la connexion**. Pour plus d’informations, consultez la [gestion des règles de pare-feu à l’aide du portail](howto-manage-firewall-using-portal.md).

   **Exemples**

   *Réplication avec SSL*

   La variable `@cert` est créée en exécutant les commandes MySQL suivantes : 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   La réplication avec SSL est définie entre un serveur maître hébergé dans le domaine « companya.com », et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Réplication sans SSL*

   La réplication sans SSL est définie entre un serveur maître hébergé dans le domaine « companya.com », et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Démarrer la réplication

   Appelez la procédure stockée `mysql.az_replication_start` pour amorcer la réplication.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Vérifier l’état de la réplication

   Appelez la commande [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) sur le serveur réplica pour afficher l’état de réplication.
    
   ```sql
   show slave status;
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est « yes » et la valeur de `Seconds_Behind_Master` est « 0 », cela signifie que la réplication fonctionne correctement. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de « 0 », cela signifie que le réplica traite les mises à jour. 

## <a name="other-stored-procedures"></a>Autres procédures stockées

### <a name="stop-replication"></a>Arrêter la réplication

Pour arrêter la réplication entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Supprimer la relation de réplication

Pour supprimer la relation entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorer l’erreur de réplication

Pour ignorer une erreur de réplication et autoriser la poursuite de la réplication, utilisez la procédure stockée suivante :
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [Réplication des données entrantes](concepts-data-in-replication.md) pour Azure Database pour MySQL. 
