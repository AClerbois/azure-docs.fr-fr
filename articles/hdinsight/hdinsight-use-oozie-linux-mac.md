---
title: Utiliser des workflows Hadoop Oozie dans Azure HDInsight basé sur Linux
description: Utilisation de Hadoop Oozie dans HDInsight basé sur Linux. Découvrez comment définir un workflow Oozie et envoyer un travail Oozie.
services: hdinsight
ms.service: hdinsight
ms.custom: hdinsightactive
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: dfbf9a3a9b800fec5df4cf527ddd4ec8e3f55b37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853237"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Utiliser Apache Oozie avec Apache Hadoop pour définir et exécuter un workflow sur Azure HDInsight Linux

Découvrez comment utiliser Apache Oozie avec Apache Hadoop sur Azure HDInsight. Oozie est un workflow et un système de coordination qui gère les travaux Hadoop. Oozie est intégré dans la pile Hadoop et prend en charge les travaux suivants :

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Vous pouvez également utiliser Oozie pour planifier des travaux propres à un système, comme des programmes Java ou des scripts de l’interpréteur de commandes.

> [!NOTE]  
> Une autre option pour définir des workflows avec HDInsight consiste à utiliser Azure Data Factory. Pour plus d’informations sur Data Factory, consultez [Utiliser Apache Pig et Apache Hive avec Data Factory][azure-data-factory-pig-hive]. Pour utiliser Oozie sur les clusters avec le Pack Sécurité Entreprise, consultez la section [Exécuter Apache Oozie dans des clusters HDInsight Hadoop avec le Pack Sécurité Entreprise](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Conditions préalables

* **Un cluster Hadoop sur HDInsight**. Consultez [prise en main HDInsight sous Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un client SSH**. Consultez [se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Une base de données SQL Azure**.  Consultez [créer une base de données SQL Azure dans le portail Azure](../sql-database/sql-database-get-started.md).  Cet article utilise une base de données nommée `oozietest`.

* **Changement possible pour la configuration du stockage.**  Consultez [configuration du stockage](#storage-configuration) si vous utilisez le type de compte de stockage `BlobStorage`.

## <a name="storage-configuration"></a>Configuration du stockage
Aucune action n’est requise si le compte de stockage utilisé est du genre `Storage (general purpose v1)` ou `StorageV2 (general purpose v2)`.  Le processus dans l’article produira la sortie vers au moins `/mapreducestaging`.  Une configuration de hadoop par défaut contiendra `/mapreducestaging` dans le `fs.azure.page.blob.dir` variable de configuration dans `core-site.xml` pour service `HDFS`.  Cette configuration entraîne la sortie vers le répertoire d’objets BLOB de pages, qui n’est pas pris en charge pour le type de compte de stockage `BlobStorage`.  Pour utiliser `BlobStorage` pour cet article, vous devez supprimer `/mapreducestaging` à partir de la `fs.azure.page.blob.dir` variable de configuration.  La configuration est accessible à partir de la [Ambari UI](hdinsight-hadoop-manage-ambari.md).  Sinon, vous recevrez le message d’erreur : `Page blob is not supported for this account type.`

> [!NOTE]  
> Le compte de stockage utilisé dans cet article a [transfert sécurisé](../storage/common/storage-require-secure-transfer.md) activé et par conséquent `wasbs` plutôt que `wasb` est utilisée tout au long de l’article.

## <a name="example-workflow"></a>Exemple de flux de travail

Le workflow utilisé dans ce document comporte deux actions. Les actions sont des définitions de tâches, telles que l’exécution de Hive, Sqoop, MapReduce ou d’autres processus :

![Diagramme du workflow][img-workflow-diagram]

1. Une action Hive exécute un script HiveQL pour extraire des enregistrements à partir de la `hivesampletable` incluse avec HDInsight. Chaque ligne de données décrit un accès depuis un appareil mobile spécifique. Le format d’enregistrement ressemble à ce qui suit :

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Le script Hive utilisé dans ce document comptabilise le nombre total d’accès pour chaque plateforme (par exemple, Android ou iPhone) et stocke ces nombres dans une nouvelle table Hive.

    Pour plus d’informations sur Hive, consultez [Utiliser Apache Hive avec HDInsight][hdinsight-use-hive].

2. Une action Sqoop exporte le contenu de la nouvelle table Hive vers une table créée dans Azure SQL Database. Pour plus d’informations sur Sqoop, consultez [Utiliser Apache Sqoop avec HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Pour obtenir la liste des versions Oozie prises en charge sur les clusters HDInsight, voir [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Création du répertoire de travail

Oozie s’attend à ce que vous stockiez toutes les ressources requises pour un travail dans le même répertoire. Cet exemple utilise `wasbs:///tutorials/useoozie`. Pour créer ce répertoire, suivez les étapes ci-dessous :

1. Modifier le code ci-dessous pour remplacer `sshuser` avec SSH utilisateur nom pour le cluster, puis remplacez `clustername` avec le nom du cluster.  Entrez le code pour se connecter au cluster HDInsight par [à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Pour créer le répertoire, utilisez la commande suivante :

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Le paramètre `-p` a provoqué la création de tous les répertoires dans le chemin d’accès. Le `data` directory est utilisé pour contenir les données utilisées par le `useooziewf.hql` script.

3. Modifier le code ci-dessous pour remplacer `username` avec votre nom d’utilisateur SSH.  Pour vous assurer qu’Oozie peut emprunter l’identité de votre compte d’utilisateur, utilisez la commande suivante :

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Vous pouvez ignorer les erreurs indiquant que l’utilisateur est déjà membre du groupe `users`.

## <a name="add-a-database-driver"></a>Ajout d’un pilote de base de données

Étant donné que ce workflow utilise Sqoop pour exporter des données vers la base de données SQL, vous devez fournir une copie du pilote JDBC utilisé pour interagir avec la base de données SQL. Pour copier le pilote JDBC dans le répertoire de travail, utilisez la commande suivante à partir de la session SSH :

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Vérifiez que le pilote JDBC réels qui existe à `/usr/share/java/`.

Si votre workflow utilisait d’autres ressources, par exemple un fichier jar contenant une application MapReduce, vous devez également les ajouter.

## <a name="define-the-hive-query"></a>Définition de la requête Hive

Suivez les étapes ci-après pour créer un script de langage de requête Hive (HiveQL) qui définit une requête. Vous allez utiliser la requête dans un workflow Oozie plus loin dans ce document.

1. À partir de la connexion SSH, utilisez la commande suivante pour créer un fichier nommé `useooziewf.hql` :

    ```bash
    nano useooziewf.hql
    ```

3. Une fois que l’éditeur nano GNU est ouvert, utilisez la requête suivante comme contenu du fichier :

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Deux variables sont utilisées dans le script :

   * `${hiveTableName}`: contient le nom de la table à créer.

   * `${hiveDataFolder}`: contient l’emplacement où stocker les fichiers de données pour la table.

     Le fichier de définition du workflow (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l’exécution.

4. Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.  

5. Utilisez la commande suivante pour copier `useooziewf.hql` à `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Cette commande stocke le fichier `useooziewf.hql` sur le stockage compatible avec HDFS pour le cluster.

## <a name="define-the-workflow"></a>Définition du flux de travail

Les définitions de workflow Oozie sont écrites en langage de définition de processus Hadoop (hPDL), qui est un langage de définition de processus XML. Pour définir le flux de travail, procédez comme suit :

1. Pour créer et modifier un fichier, utilisez l’instruction suivante :

    ```bash
    nano workflow.xml
    ```

2. Une fois que l’éditeur nano est ouvert, saisissez le code XML suivant comme contenu du fichier :

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Deux actions sont définies dans le flux de travail :

   * `RunHiveScript`: il s’agit de l’action de départ, qui exécute le script Hive `useooziewf.hql`.

   * `RunSqoopExport`: cette action exporte les données créées à partir du script Hive vers une base de données SQL à l’aide de Sqoop. Elle n’est exécutée que si l’action `RunHiveScript` a abouti.

     Le workflow a plusieurs entrées, telle que `${jobTracker}`. Vous allez remplacer ces entrées par les valeurs que vous utilisez dans la définition du travail. Vous allez créer la définition du travail plus tard dans ce document.

     Notez également l’entrée `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` dans la section Sqoop. Celle-ci indique à Oozie de rendre cette archive disponible pour Sqoop lors de l’exécution de cette action.

3. Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.  

4. Utilisez la commande suivante pour copier le fichier `workflow.xml` vers `/tutorials/useoozie/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Création d’une table

> [!NOTE]  
> Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.

1. Utilisez la commande suivante pour installer FreeTDS sur le cluster HDInsight :

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Modifier le code ci-dessous pour remplacer `<serverName>` avec votre nom de serveur SQL Azure et `<sqlLogin>` avec la connexion de serveur SQL Azure.  Entrez la commande pour vous connecter à la configuration requise de la base de données SQL.  Entrez le mot de passe à l’invite.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Vous obtenez un résultat qui ressemble à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. À l’invite de commandes `1>` , entrez les lignes suivantes :

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Une fois l’instruction `GO` entrée, les instructions précédentes sont évaluées. Ces instructions créent une table nommée `mobiledata`, qui est utilisé par le flux de travail.

    Pour vérifier que la table a été créée, utilisez les commandes suivantes :

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Vous pouvez voir un résultat qui ressemble à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Quittez l’utilitaire tsql en entrant `exit` à la `1>` invite.

## <a name="create-the-job-definition"></a>Création de la définition de travail

La définition du travail indique où se trouve le fichier workflow.xml. Elle indique également où se trouvent les autres fichiers utilisés par le workflow (par exemple, `useooziewf.hql`). En outre, elle définit les valeurs des propriétés utilisées dans le workflow et les fichiers associés.

1. Pour obtenir l’adresse complète du stockage par défaut, utilisez la commande suivante. Cette adresse est utilisée dans le fichier de configuration que vous allez créer.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Cette commande renvoie des informations similaires au code XML suivant :

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Si le cluster HDInsight utilise le stockage Azure comme stockage par défaut, le contenu de l’élément `<value>` commence par `wasbs://`. Si Azure Data Lake Storage Gen1 est utilisé, il commence par `adl://`. Si Azure Data Lake Storage Gen2 est utilisé, il commence par `abfs://`.

    Enregistrez le contenu de l’élément `<value>`, car il est utilisé dans les prochaines étapes.

2. Modifiez le fichier xml ci-dessous comme suit :

    |Valeur d’espace réservé| Valeur remplacée|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Valeur reçue de l’étape 1.|
    |admin| Votre nom de connexion pour le cluster de HDInsight si ce n’est pas administrateur.|
    |serverName| Nom du serveur de base de données SQL Azure.|
    |sqlLogin| Connexion au serveur de base de données SQL Azure.|
    |sqlPassword| Azure SQL database server connexion mot de passe.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    La plupart des informations de ce fichier sont utilisées pour remplir les valeurs utilisées dans les fichiers workflow.xml ou ooziewf.hql (comme `${nameNode}`).  Si le chemin d’accès est un chemin `wasbs`, vous devez utiliser le chemin d’accès complet. Ne le limitez pas simplement à `wasbs:///`. L’entrée `oozie.wf.application.path` définit où trouver le fichier workflow.xml. Ce fichier contient le workflow qui a été exécuté par ce travail.

3. Pour créer la configuration de définition de travail Oozie, utilisez la commande suivante :

    ```bash
    nano job.xml
    ```

4. Une fois que l’éditeur nano s’ouvre, collez le code XML modifié en tant que le contenu du fichier.

5. Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.

## <a name="submit-and-manage-the-job"></a>Soumission et gestion du travail

Les étapes suivantes utilisent la commande Oozie pour soumettre et gérer des flux de travail Oozie sur le cluster. La commande Oozie est une interface conviviale sur l’ [API REST Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Lorsque vous utilisez la commande Oozie, vous devez utiliser le nom de domaine complet pour le nœud principal HDInsight. Ce nom de domaine complet est uniquement accessible à partir du cluster, ou, si le cluster se trouve sur un réseau virtuel Azure, à partir des autres machines sur le même réseau.

1. Pour obtenir l’URL du service Oozie, utilisez la commande suivante :

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Cette commande renvoie des informations similaires au code XML suivant :

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    La partie `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` correspond à l’URL à utiliser avec la commande Oozie.

2. Modifier le code pour remplacer l’URL par celle reçue précédemment. Pour créer une variable d’environnement pour l’URL, utilisez l’élément suivant afin de ne pas être obligé de le taper pour chaque commande :

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Pour envoyer le travail, utilisez la commande suivante :

    ```bash
    oozie job -config job.xml -submit
    ```

    Cette commande charge les informations du travail à partir de `job.xml` et les envoie à Oozie, mais n’exécute pas le travail.

    Une fois la commande terminée, elle renvoie l’ID du travail (par exemple, `0000005-150622124850154-oozie-oozi-W`). Cet ID est utilisé pour gérer le travail.

4. Modifier le code ci-dessous pour remplacer `<JOBID>` par l’ID renvoyé à l’étape précédente.  Pour vérifier le statut du travail, utilisez la commande suivante :

    ```bash
    oozie job -info <JOBID>
    ```

    Cette commande renvoie des informations qui ressemblent à ce qui suit :

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Ce travail a le statut `PREP`, Cet état indique que le travail a été créé, mais qu’il n’a pas commencé.

5. Modifier le code ci-dessous pour remplacer `<JOBID>` avec l’ID renvoyé précédemment.  Pour démarrer le travail, utilisez la commande suivante :

    ```bash
    oozie job -start JOBID
    ```

    Si vous vérifiez l’état après cette commande, il sera en cours d’exécution et des informations pour les actions au sein du travail seront renvoyées.  Le travail prendra quelques minutes pour terminer.

6. Modifier le code ci-dessous pour remplacer `<serverName>` avec votre nom de serveur SQL Azure et `<sqlLogin>` avec la connexion de serveur SQL Azure.  Une fois que la tâche se termine avec succès, vous pouvez vérifier que les données a été générées et exportées vers la table de base de données SQL à l’aide de la commande suivante.  Entrez le mot de passe à l’invite.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    À l’invite de commandes `1>` , entrez la requête suivante :

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Les informations renvoyées sont semblables à ce qui suit :

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Pour plus d’informations sur la commande Oozie, consultez [Outil en ligne de commande Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API REST Oozie

L’API REST Oozie vous permet de créer vos propres outils fonctionnant avec Oozie. Les informations suivantes sont des informations spécifiques de HDInsight sur l’utilisation de l’API REST Oozie :

* **URI** : vous pouvez accéder à l’API REST depuis l’extérieur du cluster à l’adresse `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Authentification** : pour l’authentification, utilisez l’API, le compte HTTP (admin) et le mot de passe du cluster. Par exemple : 

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Pour plus d’informations sur l’utilisation de l’API REST Oozie, consultez [API des services web Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interface utilisateur Web Oozie

L’interface utilisateur web Oozie fournit une vue web de l’état des travaux Oozie sur le cluster. Avec l’interface utilisateur web, vous pouvez afficher les informations suivantes :

   * Statut de tâche
   * Définition du travail
   * Configuration
   * Un graphique des actions exécutées sur le travail
   * Les journaux du travail

Vous pouvez également afficher les détails pour les actions au sein d’un travail.

Pour accéder à l’interface utilisateur web Oozie, procédez comme suit :

1. Créez un tunnel SSH vers le cluster HDInsight. Pour plus d’informations, consultez [Utilisation du tunneling SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Une fois que vous créez un tunnel, ouvrez l’interface utilisateur web d’Ambari dans votre navigateur web à l’aide d’URI `http://headnodehost:8080`.

3. Sur le côté gauche de la page, sélectionnez **Oozie** > **Liens rapides** > **Interface utilisateur web Oozie**.

    ![Image des menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. L’interface utilisateur web Oozie affiche par défaut les travaux du workflow en cours d’exécution. Pour voir tous les travaux du workflow, sélectionnez **Tous les travaux**.

    ![Tous les travaux affichés](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Pour afficher plus d’informations sur un travail, sélectionnez le travail.

    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Sous l’onglet **Infos travail**, vous pouvez voir les informations de base sur le travail, ainsi que les actions individuelles au sein du travail. Vous pouvez utiliser les onglets en haut de la page pour afficher la **définition du travail**, la **configuration du travail**, accéder au **journal du travail** ou afficher un graphe orienté acyclique (DAG) du travail sous **Job DAG** (DAG du travail).

   * **Journal de la tâche** : Cliquez sur le bouton **Obtenir les journaux** pour obtenir tous les journaux de la tâche, ou utilisez le champ **Entrer un filtre de recherche** pour filtrer les journaux.

       ![Journal du travail](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **DAG de la tâche** : le graphique orienté acyclique est une représentation graphique des chemins de données utilisés à travers le workflow.

       ![Graphique non cyclique dirigé du travail](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Si vous sélectionnez l’une des actions sous l’onglet **Infos travail**, des informations sur l’action s’affichent. Par exemple, sélectionnez l’action **RunSqoopExport**.

    ![Informations sur l’action](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Vous pouvez voir les détails de l’action, notamment un lien vers **l’URL de la console** Utilisez ce lien pour afficher les informations de suivi pour le travail.

## <a name="schedule-jobs"></a>Planifier les travaux

Vous pouvez utiliser le coordinateur pour spécifier un début, une fin et la fréquence d’occurrence des travaux. Pour définir une planification pour le workflow, procédez comme suit :

1. Utilisez la commande suivante pour créer un fichier nommé **coordinator.xml** :

    ```bash
    nano coordinator.xml
    ```

    Utilisez le code XML suivant comme contenu du fichier :

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Les variables `${...}` sont remplacées par des valeurs dans la définition du travail lors de l’exécution. Les variables sont les suivantes :
    >
    > * `${coordFrequency}`: délai entre les instances de la tâche en cours d’exécution.
    > * `${coordStart}`: heure de début de la tâche.
    > * `${coordEnd}`: heure de fin de la tâche.
    > * `${coordTimezone}`: les tâches du coordinateur se trouvent dans un fuseau horaire fixe sans passage à l’heure d’été, généralement représenté par UTC. Ce fuseau horaire est appelé le *fuseau horaire du traitement d’Oozie*.
    > * `${wfPath}`: chemin du fichier workflow.xml.

2. Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.

3. Pour copier le fichier dans le répertoire de travail pour ce travail, utilisez la commande suivante :

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Pour modifier le `job.xml` fichier que vous avez créé précédemment, utilisez la commande suivante :

    ```bash
    nano job.xml
    ```

    Effectuez les modifications suivantes :

   * Pour ordonner à Oozie d’exécuter le fichier coordinateur au lieu du fichier de workflow, remplacez `<name>oozie.wf.application.path</name>` par `<name>oozie.coord.application.path</name>`.

   * Pour définir la variable `workflowPath` utilisée par le coordinateur, ajoutez le code XML suivant :

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Remplacez le texte `wasbs://mycontainer@mystorageaccount.blob.core.windows` par la valeur utilisée dans les autres entrées du fichier job.xml.

   * Pour définir le début, la fin et la fréquence correspondant au coordinateur, ajoutez le code XML suivant :

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Ces valeurs définissent l’heure de début sur 12 h 00 le 10 mai 2018 et l’heure de fin pour le 12 mai 2018. L’intervalle d’exécution de ce travail est défini sur quotidien. La fréquence est exprimée en minutes, par conséquent, 24 heures x 60 minutes = 1 440 minutes. Enfin, le fuseau horaire est défini sur UTC.

5. Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.

6. Pour soumettre et démarrer le travail, utilisez la commande suivante :

    ```bash
    oozie job -config job.xml -run
    ```

7. Si vous accédez à l’interface utilisateur web Oozie et sélectionnez l’onglet **Travaux du coordinateur**, vous obtenez des informations semblables à l’image suivante :

    ![Onglet Travaux du coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    L’entrée **Next Materialization** (Matérialisation suivante) indique l’heure de la prochaine exécution du travail.

8. De même que pour le travail de workflow précédent, si vous sélectionnez l’entrée du travail dans l’interface utilisateur web, des informations sur le travail s’affichent :

    ![Informations sur le travail du coordinateur](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]  
    > L’image affiche uniquement les exécutions réussies du travail, et non les actions individuelles dans le workflow planifié. Pour afficher les actions individuelles, sélectionnez l’une des entrées **Action**.

    ![Informations sur l’action](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Résolution de problèmes

Avec l’interface utilisateur Oozie, vous pouvez afficher les journaux Oozie. L’interface utilisateur Oozie contient également des liens vers les journaux JobTracker pour les tâches MapReduce démarrées par le workflow. Le modèle pour la résolution des problèmes doit être le suivant :

   1. Affichez le travail dans l’interface utilisateur web Oozie.

   2. En cas d’erreur ou d’échec d’une action spécifique, sélectionnez l’action pour voir si le champ **Message d’erreur** fournit plus d’informations sur l’échec.

   3. Si elle est disponible, utilisez l’URL de l’action pour afficher des détails supplémentaires (tels que les journaux JobTracker) pour l’action.

Voici des erreurs spécifiques que vous pouvez rencontrer avec une description de la marche à suivre pour les résoudre.

### <a name="ja009-cannot-initialize-cluster"></a>JA009 : Impossible d’initialiser le cluster

**Symptômes** : l’état de la tâche passe à **SUSPENDED** (Suspendu). Les détails du travail montrent que l’état de `RunHiveScript` est défini sur **START_MANUAL**. Lorsque vous sélectionnez l’action, le message d’erreur suivant apparaît :

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Cause** : les adresses de Stockage Blob Azure utilisées dans le fichier **job.xml** ne contiennent pas le conteneur de stockage ou le nom du compte de stockage. Le format d’adresse de stockage blob doit être `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Résolution** : changez les adresses de Stockage Blob utilisées par la tâche.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002 : Oozie n’est pas autorisé à emprunter l’identité &lt;utilisateur&gt;

**Symptômes** : l’état de la tâche passe à **SUSPENDED** (Suspendu). Les détails du travail montrent que l’état de `RunHiveScript` est défini sur **START_MANUAL**. Lorsque vous sélectionnez l’action, le message d’erreur suivant s’affiche :

    JA002: User: oozie is not allowed to impersonate <USER>

**Cause** : les paramètres d’autorisation actuels ne permettent pas à Oozie d’emprunter l’identité du compte d’utilisateur spécifié.

**Résolution** : Oozie peut emprunter l’identité des utilisateurs du groupe **users**. Utilisez le `groups USERNAME` pour voir les groupes dont le compte d’utilisateur est membre. Si l’utilisateur n’est pas membre du groupe **users** , utilisez la commande suivante pour ajouter l’utilisateur au groupe :

    sudo adduser USERNAME users

> [!NOTE]  
> Il peut se passer plusieurs minutes avant que HDInsight reconnaisse que l’utilisateur a été ajouté au groupe.

### <a name="launcher-error-sqoop"></a>Launcher ERROR (Sqoop) (Erreur du lanceur, Sqoop)

**Symptômes** : l’état de la tâche passe à **KILLED** (Supprimé). Les détails du travail montrent que l’état de `RunSqoopExport` est défini sur **ERROR**. Lorsque vous sélectionnez l’action, le message d’erreur suivant s’affiche :

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Cause** : Sqoop ne peut pas charger le pilote de base de données nécessaire pour accéder à la base de données.

**Résolution** : quand vous utilisez Sqoop à partir d’une tâche Oozie, vous devez inclure le pilote de base de données avec les autres ressources (comme workflow.xml) utilisées par la tâche. Vous devez également référencer l’archive contenant le pilote de base de données à partir de la section `<sqoop>...</sqoop>` de workflow.xml.

Par exemple, pour le travail de ce document, vous utiliseriez les étapes suivantes :

1. Copiez le fichier `mssql-jdbc-7.0.0.jre8.jar` dans le répertoire **/tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modifiez le fichier `workflow.xml` pour ajouter le code XML suivant sur une nouvelle ligne au-dessus de `</sqoop>` :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment définir un flux de travail Oozie et comment exécuter un travail Oozie. Pour en savoir plus sur l’utilisation de HDInsight, consultez les articles suivants :

* [Utiliser le coordinateur Apache Oozie basé sur le temps avec Hadoop dans HDInsight][hdinsight-oozie-coordinator-time]
* [Charger des données pour des tâches Apache Hadoop dans HDInsight][hdinsight-upload-data]
* [Utiliser Apache Sqoop avec Apache Hadoop dans HDInsight][hdinsight-use-sqoop]
* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight][hdinsight-use-hive]
* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
