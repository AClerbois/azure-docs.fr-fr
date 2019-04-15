---
title: Audit d’Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment prendre en main l’audit d’Azure SQL Database Managed Instance à l’aide de T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 6ada2a5e505bfe37f4f9a956570d8b6f38f55e55
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357425"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Prendre en main l’audit d’Azure SQL Database Managed Instance

[L’audit Managed Instance](sql-database-managed-instance.md) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure. Par ailleurs, l’audit :

- peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données ainsi qu’à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité ;
- permet et facilite le respect de normes de conformité, même s’il ne garantit pas cette conformité. Pour plus d’informations sur Azure de programmes de la conformité aux normes de cette prise en charge, consultez le [centre de confidentialité Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) où vous trouverez la liste actualisée des certifications de conformité de base de données SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurer l’audit de votre serveur sur Stockage Azure

La section suivante décrit la configuration de l’audit à l’aide de votre instance Managed Instance.

1. Accédez au [portail Azure](https://portal.azure.com).
1. Créez un **conteneur** Stockage Azure où sont stockés les journaux d’audit.

   1. Accédez au stockage Azure où vous souhaitez stocker vos journaux d’audit.

      > [!IMPORTANT]
      > Utilisez un compte de stockage dans la même région que l’instance gérée afin d’éviter des lectures/écritures entre régions.

   1. Dans le compte de stockage, accédez à **Vue d’ensemble**, puis cliquez sur **Objets blob**.

      ![Widget Objets Blob Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Dans le menu supérieur, cliquez sur **+ Conteneur** pour créer un conteneur.

      ![Icône de création d’un conteneur d’objets blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Indiquez un **Nom** pour le conteneur, définissez le niveau d’accès Public sur **Privé**, puis cliquez sur **OK**.

      ![Créer une configuration de conteneur d’objets blob](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Après avoir créé le conteneur pour les journaux d’audit, vous pouvez le configurer comme cible pour ces journaux d’activité de deux façons : [à l’aide de T-SQL](#blobtsql) ou [à l’aide de l’interface utilisateur de SSMS (SQL Server Management Studio)](#blobssms).

   - <a id="blobtsql"></a>Configurer le stockage d’objets blob pour les journaux d’audit à l’aide de T-SQL :

     1. Dans la liste des conteneurs, cliquez sur le conteneur que vous venez de créer, puis sur **Propriétés du conteneur**.

        ![Bouton Propriétés d’un conteneur d’objets blob](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copiez l’URL du conteneur en cliquant sur l’icône Copier, puis enregistrez-la (par exemple, dans le Bloc-notes) pour l’utiliser ultérieurement. Le format d’URL de conteneur doit être `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL du conteneur d’objets blob à copier](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Générez un **jeton SAP** Stockage Azure pour accorder au compte de stockage des droits d’accès à l’audit de Managed Instance :

        - Accédez au compte Stockage Azure dans lequel vous avez créé le conteneur à l’étape précédente.

        - Dans le menu Paramètres de stockage, cliquez sur **Signature d’accès partagé**.

          ![Icône Signature d’accès partagé dans le menu des paramètres de stockage](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configurez la signature d’accès partagé comme suit :

          - **Services autorisés** : Blob

          - **Date de début** : pour éviter tout problème lié au fuseau horaire, il est recommandé d’utiliser la date de la veille.

          - **Date de fin** : choisissez la date à laquelle ce jeton SAP arrive à expiration.

            > [!NOTE]
            > À l’expiration, renouvelez le jeton afin d’éviter les échecs d’audit.

          - Cliquez sur **Générer une signature d’accès partagé**.
            
            ![Configuration de SAP](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Après que vous avez cliqué sur Générer une signature d’accès partagé, le jeton SAP s’affiche en bas de la fenêtre. Copiez le jeton en cliquant sur l’icône Copier, puis enregistrez-le (par exemple, dans le Bloc-notes) pour l’utiliser ultérieurement.

          ![Copier le jeton SAP](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Supprimez le point d’interrogation (« ? ») au début du jeton.

     1. Connectez-vous à votre instance gérée à l’aide de SSMS (SQL Server Management Studio) ou de tout autre outil pris en charge.

     1. Exécutez l’instruction T-SQL suivante pour **créer des informations d’identification** à l’aide de l’URL du conteneur et du jeton SAP que vous avez créés dans les étapes précédentes :

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Exécutez l’instruction T-SQL suivante pour créer un audit de serveur (choisissez votre propre nom d’audit et utilisez l’URL du conteneur que vous avez créée au cours des étapes précédentes). Si aucune valeur n’est spécifiée, la valeur par défaut de `RETENTION_DAYS` est 0 (conservation illimitée) :

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Continuez en [créant une spécification d’audit du serveur ou une spécification d’audit de la base de données](#createspec).

   - <a id="blobssms"></a>Configurez le stockage d’objets blob pour les journaux d’audit à l’aide de SQL Server Management Studio (SSMS) 18 (préversion) :

     1. Connectez-vous à l’instance gérée à l’aide de l’interface utilisateur de SSMS (SQL Server Management Studio).

     1. Développez le nœud racine de l’Explorateur d’objets.

     1. Développez le nœud **Sécurité**, cliquez avec le bouton droit sur le nœud **Audits**, puis cliquez sur Nouvel audit :

        ![Développer les nœuds Sécurité et Audit](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Veillez à sélectionner « URL » dans **Destination de l’audit**, puis cliquez sur **Parcourir** :

        ![Parcourir le stockage Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Facultatif) Connectez-vous à votre compte Azure :

        ![Connexion à Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Sélectionnez un abonnement, un compte de stockage et un conteneur d’objets blob dans les menus déroulants, ou cliquez sur **Créer** pour créer votre propre conteneur. Une fois terminé, cliquez sur **OK** :

        ![Sélectionnez l’abonnement Azure, compte de stockage et conteneur d’objets blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Cliquez sur **OK** dans la boîte de dialogue Créer un audit.

1. <a id="createspec"></a>Après avoir configuré le conteneur d’objets blob comme cible pour les journaux d’audit, créez une spécification d’audit du serveur ou une spécification d’audit de la base de données comme vous le feriez pour SQL Server :

   - [Créer le repère de T-SQL Server audit specification](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Créer le repère de T-SQL de base de données d’audit specification](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Activez l’audit du serveur que vous avez créé à l’étape 6 :

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Pour toute information supplémentaire :

- [Audit des différences entre les bases de données uniques, pool élastique, s et des instances gérées dans la base de données SQL Azure et les bases de données SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CRÉATION D’AUDIT DE SERVEUR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurer l’audit pour votre serveur dans les journaux d’Event Hub ou Azure Monitor

Journaux d’audit à partir d’une instance gérée peuvent être envoyés à Event Hubs ou journaux Azure Monitor. Cette section décrit comment configurer cela :

1. Accédez dans le [portail Azure](https://portal.azure.com/) à l’instance gérée.

2. Cliquez sur **Paramètres de diagnostic**.

3. Cliquez sur **Activer les diagnostics**. Si les diagnostics sont déjà activés, l’option *+Ajouter un paramètre de diagnostic* s’affiche à la place.

4. Sélectionnez **SQLSecurityAuditEvents** dans la liste des journaux d’activité.

5. Sélectionnez une destination pour les événements d’audit - Hub d’événements, journaux d’Azure Monitor ou les deux. Configurez les paramètres requis (par exemple, espace de travail Log Analytics) pour chaque cible.

6. Cliquez sur **Enregistrer**.

    ![Configurer les paramètres de diagnostic](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Connectez-vous à l’instance gérée à l’aide de **SQL Server Management Studio (SSMS)** ou de tout autre client pris en charge.

8. Exécutez l’instruction T-SQL suivante pour créer un audit du serveur :

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Créez une spécification d’audit du serveur ou une spécification d’audit de la base de données comme vous le feriez pour SQL Server :

   - [Créer le repère de T-SQL Server audit specification](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Créer le repère de T-SQL de base de données d’audit specification](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Activer l’audit du serveur créé à l’étape 8 :
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Utiliser les journaux d’audit

### <a name="consume-logs-stored-in-azure-storage"></a>Utiliser les journaux d’activité stockés dans Stockage Azure

Plusieurs méthodes vous permettent d’afficher des journaux d’activité d’audit d’objets blob.

- Utilisez la fonction système `sys.fn_get_audit_file` pour retourner les données du journal d’audit dans un format tabulaire. Pour plus d’informations sur l’utilisation de cette fonction, consultez la [documentation sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Vous pouvez explorer les journaux d’audit avec un outil comme [l’Explorateur de stockage Azure](https://azure.microsoft.com/features/storage-explorer/). Dans Stockage Azure, les journaux d’audit sont enregistrés sous la forme d’une collection de fichiers blob dans un conteneur défini pour stocker les journaux d’audit. Pour plus d’informations sur la hiérarchie du dossier de stockage, sur les conventions de nommage et sur le format des journaux, consultez le [document de référence sur le format des journaux d’audit d’objets blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Pour obtenir la liste complète des méthodes de consommation du journal d’audit, reportez-vous à l’article [Bien démarrer avec l’audit de bases de données SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Utiliser les journaux d’activité stockés dans Event Hub

Pour utiliser les données des journaux d’audit à partir d’un hub d’événements, vous devez configurer un flux de données destiné à consommer les événements et à les écrire dans une cible. Pour plus d’informations, voir la Documentation concernant Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consommer et d’analyser les journaux stockés dans les journaux Azure Monitor

Si les journaux d’audit sont écrits dans les journaux d’Azure Monitor, ils sont disponibles dans l’espace de travail Analytique de journal, où vous pouvez exécuter des recherches avancées sur les données d’audit. En tant que point de départ, accédez à l’espace de travail Analytique de journal et sous *général* section cliquez *journaux* et entrez une requête simple, tel que : `search "SQLSecurityAuditEvents"` pour afficher l’audit des journaux.  

Journaux d’Azure Monitor vous donne des informations opérationnelles en temps réel à l’aide de la recherche intégrée et des tableaux de bord personnalisés permettant d’analyser facilement des millions d’enregistrements toutes vos charges de travail et serveurs. Pour plus d’informations utiles sur le langage de recherche de journaux Azure Monitor et les commandes, consultez [Azure Monitor enregistre la référence de recherche](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Audit des différences entre les bases de données dans Azure SQL Database et les bases de données dans SQL Server

Les principales différences entre l’audit des bases de données dans Azure SQL Database et des bases de données dans SQL Server sont les suivantes :

- Avec l’option de déploiement d’instance gérée dans Azure SQL Database, l’audit s’effectue au niveau du serveur et stocke les fichiers journaux `.xel` dans Stockage Blob Azure.
- Avec les options de déploiement de base de données unique et de pool élastique dans Azure SQL Database, l’audit fonctionne au niveau de la base de données.
- Dans SQL Server (en local ou sur machines virtuelles), l’audit fonctionne au niveau du serveur, mais stocke les événements dans les journaux des événements du système de fichiers/Windows.

L’audit XEvent d’une instance gérée prend en charge les cibles de Stockage Blob Azure. Les journaux d’activité de fichiers et de Windows ne sont **pas pris en charge**.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azure sont :

- Une nouvelle syntaxe `TO URL` est fournie et vous permet de spécifier l’URL du conteneur du Stockage Blob Azure où les fichiers `.xel` sont placés.
- Une nouvelle syntaxe `TO EXTERNAL MONITOR` est fournie pour activer les cibles de journaux même Hub et Azure Monitor.
- La syntaxe `TO FILE` **n’est pas prise en charge**, car SQL Database ne peut pas accéder aux partages de fichiers Windows.
- L’option d’arrêt n’est **pas prise en charge**.
- `queue_delay` 0 est **ne pas pris en charge**.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir la liste complète des méthodes de consommation du journal d’audit, reportez-vous à l’article [Bien démarrer avec l’audit de bases de données SQL](sql-database-auditing.md).
- Pour plus d’informations sur Azure de programmes de la conformité aux normes de cette prise en charge, consultez le [centre de confidentialité Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) où vous trouverez la liste actualisée des certifications de conformité de base de données SQL.

<!--Image references-->









