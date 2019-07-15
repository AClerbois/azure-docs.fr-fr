---
title: Prise en main de l’audit Azure SQL Database | Microsoft Docs
description: Utilisez la fonctionnalité d’audit d’Azure SQL Database pour effectuer le suivi des événements de base de données dans un journal d’audit.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 1b3a6a18d10b9d9f6ab6456ae2911e54f5c56a71
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544084"
---
# <a name="get-started-with-sql-database-auditing"></a>Bien démarrer avec l’audit de bases de données SQL

L’audit pour Azure [SQL Database](sql-database-technical-overview.md) et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure, votre espace de travail OMS ou Event Hubs. Par ailleurs, l’audit :

- peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données ainsi qu’à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité ;

- permet et facilite le respect de normes de conformité, même s’il ne garantit pas cette conformité. Pour plus d’informations sur les programmes Azure qui prennent en charge la conformité aux normes, consultez le [Centre de confidentialité Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) où vous trouverez la liste actualisée des certifications de conformité SQL Database.


> [!NOTE] 
> Cette rubrique s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Vue d’ensemble de l’audit de bases de données Azure SQL

Vous pouvez utiliser l’audit de bases de données SQL pour :

- **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
- **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d’activités et d’événements.
- **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

> [!IMPORTANT]
> Les journaux d’audit sont écrits dans des **Blobs d’ajout** dans un stockage blob Azure avec votre abonnement Azure.
>
> - Tous les types de stockage (v1, v2, blob) sont pris en charge.
> - Toutes les configurations de réplication de stockage sont prises en charge.
> - Le **stockage Premium** n'est actuellement **pas pris en charge**.
> - Le **stockage dans un réseau virtuel** n’est actuellement **pas pris en charge**.
> - Le **stockage derrière un pare-feu** n’est actuellement **pas pris en charge**

## <a id="subheading-8"></a>Définir une stratégie d’audit au niveau du serveur ou au niveau de la base de données

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut :

- Une stratégie de serveur s’applique aux bases de données existantes et à celles qui sont nouvellement créées sur le serveur.

- Si *l’audit d’objets blob serveur est activé*, il *s’applique toujours à la base de données*. La base de données est auditée, quels que soient les paramètres d’audit de la base de données.

- L’activation de l’audit d’objets blob dans la base de données ou l’entrepôt de données, en plus de son activation sur le serveur, ne remplace *pas* et ne modifie pas non plus les paramètres de l’audit d’objets blob serveur. Les deux audits coexistent. En d’autres termes, la base de données est auditée deux fois en parallèle (une fois par la stratégie du serveur et une autre fois par la stratégie de la base de données).

   > [!NOTE]
   > Vous devez éviter d’activer à la fois l’audit d’objets blob du serveur et l’audit d’objets blob de la base de données, sauf si :
    > - Vous voulez utiliser un autre *compte de stockage* ou une autre *période de rétention* pour une base de données spécifique.
    > - Vous souhaitez auditer des types ou des catégories d’événements pour une base de données qui sont différents de ceux qui sont audités pour les autres bases de données du serveur. Par exemple, il est possible que des insertions de table doivent être auditées uniquement pour une base de données spécifique.
   >
   > Sinon, nous vous recommandons d’activer uniquement l’audit d’objets blob au niveau du serveur et de laisser l’audit au niveau de la base de données désactivé pour toutes les bases de données.

## <a id="subheading-2"></a>Configuration de l’audit pour votre base de données

La section suivante décrit la configuration de l’audit à l’aide du portail Azure.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez à **Audit** sous l’en-tête Sécurité dans votre volet de serveur/base de données SQL.

    <a id="auditing-screenshot"></a>![Volet de navigation][1]

3. Si vous préférez définir une stratégie d’audit de serveur, vous pouvez sélectionner le lien **Afficher les paramètres du serveur** dans la page d’audit de la base de données. Vous pouvez alors afficher ou modifier les paramètres d’audit du serveur. Les stratégies d’audit de serveur s’appliquent aux bases de données existantes et à celles qui sont nouvellement créées sur le serveur.

    ![Volet de navigation][2]

4. Si vous préférez activer l’audit au niveau base de données, définissez **Audit** sur **ACTIVÉ**.

    Si l’audit d’objets du serveur est activé, l’audit configuré pour la base de données coexiste avec celui-ci.

    ![Volet de navigation][3]

5. **Nouveau** : Vous disposez désormais de plusieurs options pour configurer l’emplacement d’écriture des journaux d’audit. Vous pouvez écrire des journaux d’activité dans un compte de stockage Azure ou dans un espace de travail Log Analytics pour qu’ils soient consommés par des journaux Azure Monitor, ou dans un hub d’événements pour qu’ils soient consommés par ce hub. Vous pouvez associer ces options comme vous le souhaitez. Les journaux d’audit seront écrits dans chacun des emplacements choisis.

   > [!WARNING]
   > L’activation de l’audit sur Log Analytics implique des frais selon les taux d’ingestion. Notez le coût associé à l’utilisation de cette [option](https://azure.microsoft.com/pricing/details/monitor/), ou envisagez de stocker les journaux d’audit dans un compte de stockage Azure.

    ![Options de stockage](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Pour configurer l’écriture des journaux d’audit dans un compte de stockage, sélectionnez **Stockage**, puis ouvrez **Détails du stockage**. Sélectionnez le compte de stockage Azure dans lequel les journaux d’activité seront enregistrés, puis sélectionnez la période de rétention. Les anciens journaux d’activité seront supprimés. Cliquez ensuite sur **OK**.

    ![compte de stockage](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Pour configurer l’écriture des journaux d’audit dans un espace de travail Log Analytics, sélectionnez **Log Analytics (préversion)** , puis ouvrez **Détails de Log Analytics**. Sélectionnez ou créez l’espace de travail Log Analytics où les journaux d’activité doivent être écrits, puis cliquez sur **OK**.

    ![Espace de travail Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Pour configurer l’écriture des journaux d’audit dans un hub d’événements, sélectionnez **Hub d’événements (préversion)** , puis ouvrez **Détails du hub d’événements**. Sélectionnez le hub d’événements dans lequel les journaux d’activité doivent être écrits, puis cliquez sur **OK**. Veillez à ce que le hub d’événements se trouve dans la même région que votre base de données et votre serveur.

    ![Event Hub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Cliquez sur **Enregistrer**.
10. Si vous souhaitez personnaliser les événements audités, vous pouvez le faire avec des [applets de commande PowerShell](#subheading-7) ou [l’API REST](#subheading-9).
11. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité de détection des menaces et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Pour plus d’informations, consultez [Bien démarrer avec la détection des menaces](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> L’activation de l’audit sur un Azure SQL Data Warehouse interrompu est impossible. Pour l’activer, annulez l’interruption de l’entrepôt de données.

> [!WARNING]
> L’activation de l’audit sur un serveur avec un entrepôt Azure SQL Data Warehouse, **entraîne la reprise de l’entrepôt Data Warehouse**, même s’il avait précédemment été interrompu, ce qui peut entraîner des frais de facturation.

## <a id="subheading-3"></a>Analyse des journaux et des rapports d’audit

Si vous avez choisi d’écrire les journaux d’audit dans des journaux Azure Monitor :

- Utilisez le [portail Azure](https://portal.azure.com).  Ouvrez la base de données appropriée. En haut de la page **Audit** de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Afficher les journaux d’audit](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Ensuite, cliquez sur **Ouvrir dans OMS** en haut de la page **Enregistrements d’audit** pour ouvrir la vue Journaux d’activité dans Log Analytics, où vous pouvez personnaliser la plage de temps et la requête de recherche.

    ![Ouvrir dans Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Vous pouvez également accéder aux journaux d’audit à partir du panneau Log Analytics. Ouvrez votre espace de travail Log Analytics, puis, dans la section **Général**, cliquez sur **Journaux d’activité**. Vous pouvez démarrer par une requête simple, telle que : *search "SQLSecurityAuditEvents"* pour afficher les journaux d’audit.
    Vous pouvez également utiliser les [journaux Azure Monitor](../log-analytics/log-analytics-log-search.md) pour exécuter des recherches avancées sur les données de votre journal d’audit. Les journaux Azure Monitor vous donnent des insights opérationnels en temps réel à l’aide d’une recherche intégrée et de tableaux de bord personnalisés permettant d’analyser facilement des millions d’enregistrements dans l’ensemble de vos charges de travail et serveurs. Pour plus d’informations utiles sur le langage et les commandes de recherche des journaux Azure Monitor, consultez [Informations de référence sur la recherche dans les journaux Azure Monitor](../log-analytics/log-analytics-log-search.md).

Si vous avez choisi d’écrire les journaux d’audit dans un hub d’événements :

- Pour utiliser les données des journaux d’audit à partir d’un hub d’événements, vous devez configurer un flux de données destiné à consommer les événements et à les écrire dans une cible. Pour plus d’informations, consultez la [documentation Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
- Les journaux d’audit d’Event Hub sont capturés dans le corps d’événements [Apache Avro](https://avro.apache.org/) et stockés en format JSON avec l’encodage UTF-8. Pour lire les journaux d’audit, vous pouvez utiliser les [outils Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou des outils similaires qui traitent ce format.

Si vous choisissez d’écrire les journaux d’audit dans un compte de stockage Azure, plusieurs méthodes existent pour afficher les journaux d’activité :

- Les journaux d’audit sont agrégés dans le compte choisi lors de la configuration. Vous pouvez explorer les journaux d’audit avec un outil comme [l’Explorateur de stockage Azure](https://storageexplorer.com/). Dans le stockage Azure, les journaux d’activité d’audit sont enregistrés sous la forme d’une collection de fichiers d’objets blob dans un conteneur nommé **sqldbauditlogs**. Pour plus d’informations sur la hiérarchie du dossier de stockage, sur les conventions d’affectation de noms et sur le format de journal, consultez la documentation relative au [Format des journaux d’audit SQL Database](https://go.microsoft.com/fwlink/?linkid=829599).

- Utilisez le [portail Azure](https://portal.azure.com).  Ouvrez la base de données appropriée. En haut de la page **Audit** de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][7]

    La page **Enregistrements d’audit** s’ouvre et vous y voyez les journaux d’activité.

  - Vous pouvez afficher des dates spécifiques en cliquant sur **Filtrer** en haut de la page **Enregistrements d’audit**.
  - Vous pouvez basculer entre les enregistrements d’audit qui ont été créés par la *stratégie d’audit de serveur* et la *stratégie d’audit de base de données* en choisissant la **Source de l’audit**.
  - Vous pouvez afficher uniquement les enregistrements d’audit liés aux injections SQL en cochant la case **Afficher uniquement les enregistrements d’audit pour les injections SQL**.

       ![Volet de navigation][8]

- Utilisez la fonction système **sys.fn_get_audit_file** (T-SQL) pour retourner les données du journal d’audit sous un format tabulaire. Pour plus d’informations sur l’utilisation de cette fonction, consultez [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Utilisez **Fusionner les fichiers d’audit** dans SQL Server Management Studio (à partir de SSMS 17) :
    1. Dans le menu SSMS, sélectionnez **Fichier** > **Ouvrir** > **Fusionner les fichiers d’audit**.

        ![Volet de navigation][9]
    2. La boîte de dialogue **Ajouter des fichiers d’audit** s’ouvre. Sélectionnez l’une des options **Ajouter** pour choisir de fusionner les fichiers d’audit à partir d’un disque local ou de les importer à partir du stockage Azure. Vous devrez fournir vos informations de stockage Azure et la clé de compte.

    3. Une fois que tous les fichiers à fusionner ont été ajoutés, cliquez sur **OK** pour terminer l’opération de fusion.

    4. Le fichier fusionné s’ouvre dans SSMS, où vous pouvez l’afficher et l’analyser, ainsi que l’exporter vers un fichier XEL ou CSV, ou vers une table.

- Utilisez Power BI. Vous pouvez afficher et analyser les données du journal d’audit dans Power BI. Pour plus d’informations et pour accéder à un modèle téléchargeable, consultez [Analyze audit log data in Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/) (Analyser les données des journaux d’audit dans Power BI).
- Téléchargez les fichiers journaux à partir de votre conteneur Azure Storage Blob via le portail ou avec un outil comme [l’Explorateur de stockage Azure](https://storageexplorer.com/).
  - Une fois que vous avez téléchargé localement un fichier journal, double-cliquez sur le fichier pour ouvrir, afficher et analyser les journaux d’activité dans SSMS.
  - Vous pouvez également télécharger plusieurs fichiers simultanément avec l’Explorateur de stockage Azure. Pour cela, cliquez avec le bouton droit sur un sous-dossier, puis sélectionnez **Enregistrer en tant que** pour l’enregistrer dans un dossier local.

- Autres méthodes :

  - Après avoir téléchargé plusieurs fichiers (ou un sous-dossier contenant des fichiers journaux), vous pouvez les fusionner localement en suivant les instructions relatives à l’option Fusionner les fichiers d’audit dans SSMS, décrites précédemment.
  - Affichez des journaux d’activité d’audit d’objets blob par programmation :

    - [Interrogez des fichiers d’événements étendus](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) avec PowerShell.

## <a id="subheading-5"></a>Pratiques de production

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Audit des bases de données géorépliquées</a>

Avec les bases de données géorépliquées, lorsque vous activez l’audit dans la base de données primaire, la même stratégie d’audit est appliquée à la base de données secondaire. Il est également possible de configurer l’audit dans la base de données secondaire en activant l’audit dans le **serveur secondaire**, indépendamment de la base de données primaire.

- Au niveau du serveur (**recommandé**) : activez l’audit sur le **serveur principal** et le **serveur secondaire**. Les bases de données primaire et secondaire sont auditées, indépendamment l’une de l’autre, en fonction de leur stratégie de niveau serveur respective.
- Au niveau de la base de données : l’audit au niveau de la base de données ne peut être configuré pour les bases de données secondaires qu’à partir des paramètres d’audit de la base de données primaire.
  - L’audit doit être activé sur la *base de données primaire elle-même*, et non pas sur le serveur.
  - Une fois que l’audit est activé sur la base de données primaire, il est également activé sur la base de données secondaire.

    >[!IMPORTANT]
    >Avec l’audit au niveau de la base de données, les paramètres de stockage de la base de données secondaire sont identiques à ceux de la base de données primaire, ce qui entraîne un trafic entre régions. Il est conseillé d’activer uniquement l’audit d’objets blob au niveau du serveur et de laisser l’audit au niveau de la base de données désactivé pour toutes les bases de données.
    > [!WARNING]
    > L’utilisation d’Event Hub ou des journaux Azure Monitor comme cibles des journaux d’audit au niveau du serveur n’est pas prise en charge pour le moment pour les bases de données secondaires géorépliquées.

### <a id="subheading-6">Régénération des clés de stockage</a>

Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Si vous écrivez les journaux d’audit dans le stockage Azure, vous devez réenregistrer votre stratégie d’audit lors de l’actualisation de vos clés. Pour ce faire, procédez comme suit :

1. Ouvrez **Détails du stockage**. Dans la zone **Clé d’accès de stockage**, sélectionnez **Secondaire**, puis cliquez sur **OK**. Cliquez ensuite sur **Enregistrer** en haut de la page de configuration de l’audit.

    ![Volet de navigation][5]
2. Accédez à la page de configuration du stockage, puis regénérez la clé d’accès primaire.

    ![Volet de navigation][6]
3. Revenez à la page de configuration de l’audit, remplacez la clé d’accès de stockage secondaire par la clé primaire, puis cliquez sur **OK**. Cliquez ensuite sur **Enregistrer** en haut de la page de configuration de l’audit.
4. Revenez à la page de configuration du stockage, puis regénérez la clé d’accès secondaire (en préparation du cycle suivant d’actualisation des clés).

## <a name="additional-information"></a>Informations supplémentaires

- Pour plus d’informations sur le format du journal, la hiérarchie du dossier de stockage et les conventions d’affectation de nom,consultez le [document de référence sur le format des journaux d’audit d’objets blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > L’audit Azure SQL Database stocke 4 000 caractères de données pour des champs de caractères dans un enregistrement d’audit. Lorsque l’**instruction** ou les valeurs **data_sensitivity_information** retournées à partir d’une action pouvant être auditée contiennent plus de 4 000 caractères, toutes les données au-delà des 4 000 premiers caractères sont  **tronquées et ne sont pas auditées**.

- Les journaux d’audit sont écrits dans des **Blobs d’ajout** dans un stockage Blob Azure avec votre abonnement Azure :
  - **Stockage Premium** n’est actuellement  **pas pris en charge** par l’ajout d’objets blob.
  - Le **stockage dans un réseau virtuel** n’est actuellement **pas pris en charge**.

- La stratégie d’audit par défaut inclut toutes les actions et l’ensemble suivant de groupes d’actions, qui feront l’audit de toutes les requêtes et procédures stockées exécutées sur la base de données, ainsi que des connexions réussies et échouées :

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Vous pouvez configurer l’audit pour différents types d’actions et groupes d’actions à l’aide de PowerShell, comme décrit dans la section [Gérer l’audit de base de données SQL avec Azure PowerShell](#subheading-7).

- Quand vous utilisez l’authentification AAD, les échecs de connexion ne sont *pas* enregistrés dans le journal d’audit SQL. Pour voir les enregistrements d’audit des échecs de connexion, accédez au [portail Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), qui affiche les détails de ces événements.


## <a id="subheading-7"></a>Gérer l’audit de base de données SQL avec Azure PowerShell

**Applets de commande PowerShell (y compris prise en charge de la clause WHERE pour un filtrage supplémentaire)**  :

- [Créer ou mettre à jour une stratégie d’audit de base de données (Set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Créer ou mettre à jour une stratégie de serveur (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Obtenir une stratégie d’audit de base de données (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obtenir une stratégie d’audit de serveur (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Supprimer une stratégie d’audit de base de données (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Supprimer une stratégie d’audit de serveur (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Pour obtenir un exemple de script, consultez [Configurer l’audit et la détection des menaces avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Gérer l’audit de base de données SQL à l’aide de l’API REST

**API REST** :

- [Create or Update Database Auditing Policy](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Create or Update Server Auditing Policy](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Get Database Auditing Policy](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Get Server Auditing Policy](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Prise en charge de la stratégie étendue avec la clause WHERE pour un filtrage supplémentaire :

- [Créer ou mettre à jour la stratégie d’audit *étendue* de base de données](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Créer ou mettre à jour la stratégie d’audit *étendue* de serveur](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obtenir la stratégie d’audit *étendue* de base de données](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obtenir la stratégie d’audit *étendue* de serveur](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Gérer l’audit de base de données SQL à l’aide de modèles ARM

Vous pouvez gérer l’audit de bases de données Azure SQL à l’aide de modèles [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), comme indiqué dans ces exemples :

- [Déployer un serveur Azure SQL Server avec l’audit activé pour écrire des journaux d’audit dans le compte de stockage blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Déployer un serveur SQL Azure avec l’audit activé pour écrire des journaux d’audit dans Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Déployer un serveur SQL Azure avec l’audit activé pour écrire des journaux d’audit dans Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Les exemples liés se trouvent sur un référentiel public externe et sont fournis « en l’état », sans garantie et ne sont pas pris en charge dans n’importe quel service/programme de support Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
