---
title: Environnements de calcul pris en charge par Azure Data Factory | Microsoft Docs
description: "En savoir plus sur environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory pour transformer/traiter les données."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f9f29cd20020ec5e6538bf1dd31e89c2f7adcc92
ms.lasthandoff: 04/03/2017


---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Environnements de calcul pris en charge par Azure Data Factory
Cet article décrit les différents environnements de calcul que vous pouvez utiliser pour traiter ou transformer des données. Il fournit également des détails sur les différentes configurations (à la demande ou de type « apporter votre propre configuration ») prises en charge par Data Factory lors de la configuration des services liés qui relient ces environnements de calcul à Azure Data Factory.

Le tableau suivant fournit une liste d’environnements de calcul pris en charge par Data Factory et les activités qui peuvent s’exécuter sur ces derniers. 

| Environnement de calcul | activités |
| --- | --- |
| [Cluster HDInsight à la demande](#azure-hdinsight-on-demand-linked-service) ou [votre propre cluster HDInsight](#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) |[Activités Machine Learning : exécution de lot et mise à jour de ressource](data-factory-azure-ml-batch-execution-activity.md) |
| [Service Analytique Azure Data Lake](#azure-data-lake-analytics-linked-service) |[Langage U-SQL du service Analytique Data Lake](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) |[Procédure stockée](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versions de HDInsight prises en charge dans Azure Data Factory
Azure HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version crée une version spécifique de la distribution de la plateforme de données Hortonworks (HDP) et un ensemble de composants qui sont contenus dans cette distribution. Microsoft met systématiquement à jour la liste des versions prises en charge de HDInsight pour fournir les derniers correctifs et composants de l’écosystème Hadoop. HDInsight 3.2 est déconseillé depuis le 01/04/2017 ; pour plus d’informations, consultez la page [Versions de HDInsight prises en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Ceci influe sur les fabriques de données Azure Data Factory existantes, où des activités sont en cours d’exécution sur des clusters HDInsight 3.2. Nous recommandons aux utilisateurs de suivre les instructions ci-dessous pour mettre à jour les fabriques de données concernées.

### <a name="for-linked-services-pointing-to-your-own-hdinsight-clusters"></a>Pour les services liés qui pointent vers vos propres clusters HDInsight
* **Services liés HDInsight qui pointent vers vos propres clusters HDInsight version 3.2 ou antérieure :**

  Azure Data Factory prend en charge l’envoi de travaux à vos propres clusters HDInsight entre HDI 3.1 et [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Toutefois, vous ne pouvez plus créer de clusters HDInsight 3.2 depuis le 01/04/2017 selon la stratégie de dépréciation documentée sur la page [Versions de HDInsight prises en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).  

  **Recommandations :** 
  * Effectuez des tests pour vérifier la compatibilité des activités qui font référence à ces services liés avec [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) selon les informations documentées sur les pages [Composants Hadoop disponibles avec différentes versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) et [Notes de publication de Hortonworks associées aux versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * Mettez à niveau votre cluster HDInsight 3.2 vers [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) pour obtenir les derniers correctifs et composants de l’écosystème Hadoop. 

* **Services liés HDInsight qui pointent vers vos propres clusters HDInsight version 3.3 ou ultérieure :**

  Azure Data Factory prend en charge l’envoi de travaux à vos propres clusters HDInsight entre HDI 3.1 et [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). 
  
  **Recommandations :** 
  * Aucune action n’est requise du point de vue de Data Factory. Toutefois, si vous utilisez une version antérieure de HDInsight, nous vous recommandons quand même de passer à [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) pour obtenir les derniers correctifs et composants de l’écosystème Hadoop.

### <a name="for-hdinsight-on-demand-linked-services"></a>Pour les services liés à la demande HDInsight
* **La version 3.2 ou antérieure est spécifiée dans la définition JSON des services liés à la demande HDInsight :**
  
  Azure Data Factory permettra la prise en charge de la création de clusters HDInsight version 3.3 ou ultérieure à la demande à partir du **05/05/2017**. Par ailleurs, la fin du support des services liés HDInsight 3.2 à la demande sera étendue jusqu’au **05/07/2017**.  

  **Recommandations :** 
  * Effectuez des tests pour vérifier la compatibilité des activités qui font référence à ces services liés avec [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) selon les informations documentées sur les pages [Composants Hadoop disponibles avec différentes versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) et [Notes de publication de Hortonworks associées aux versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * Avant le **05/07/2017**, mettez à jour la propriété Version de la définition JSON des services liés HDI à la demande vers [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) pour obtenir les derniers correctifs et composants de l’écosystème Hadoop. Vous trouverez la définition JSON détaillée sur la page [Exemple de service lié à la demande Azure HDInsight](#azure-hdinsight-on-demand-linked-service). 

* **Version non spécifiée dans les services liés HDInsight à la demande :**
  
  Azure Data Factory permettra la prise en charge de la création de clusters HDInsight version 3.3 ou ultérieure à la demande à partir du **05/05/2017**. Par ailleurs, la fin du support des services liés HDInsight 3.2 à la demande sera étendue jusqu’au **05/07/2017**. 

  Avant le **05/05/2017**, la valeur par défaut des propriétés version et osType, si elles sont vides, est la suivante : 

  | Propriété | Valeur par défaut | Requis |
  | --- | --- | --- |
  Version    | HDI 3.1 pour un cluster Windows et HDI 3.2 pour un cluster Linux.| Non
  osType | Windows (valeur par défaut) | Non

  Après le **05/05/2017**, la valeur par défaut des propriétés version et osType, si elles sont vides, est la suivante :

  | Propriété | Valeur par défaut | Requis |
  | --- | --- | --- |
  Version    | HDI 3.3 pour un cluster Windows et HDI 3.5 pour un cluster Linux.    | Non
  osType | Linux (valeur par défaut)    | Non

  **Recommandations :** 
  * Avant le **05/05/2017**, mettez à jour le service lié de façon à définir explicitement la combinaison version - osType attendue dans la définition JSON des services liés HDInsight à la demande. Vous pouvez spécifier la version 3.2 pour assurer une compatibilité descendante. 
  * Entre le**05/05/2017** et le **05/07/2017**, effectuez des tests pour vérifier la compatibilité des activités qui font référence à ces services liés avec [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) selon les informations documentées sur les pages [Composants Hadoop disponibles avec différentes versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) et [Notes de publication de Hortonworks associées aux versions de HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).  
  * Avant le **07/05/2017**, définissez la propriété Version de la définition JSON des services liés HDInsight à la demande sur [la dernière version de HDInsight prise en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) ou utilisez la valeur par défaut, HDInsight 3.5, pour obtenir les derniers correctifs et composants de l’écosystème Hadoop. Vous trouverez la définition JSON détaillée sur la page [Exemple de service lié à la demande Azure HDInsight](#azure-hdinsight-on-demand-linked-service).

>[!Note]
>Actuellement, Azure Data Factory ne prend pas en charge les clusters HDInsight avec Azure Data Lake Store comme magasin principal. Vous devrez utiliser le Stockage Azure comme magasin principal des clusters HDInsight. 
>  
>  

## <a name="on-demand-compute-environment"></a>Environnement de calcul à la demande
Dans ce type de configuration, l'environnement de calcul est entièrement géré par le service Azure Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez créer un service lié pour un environnement de calcul à la demande, le configurer et contrôler les paramètres granulaires pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.

> [!NOTE]
> La configuration à la demande est actuellement prise en charge uniquement pour les clusters Azure HDInsight.
> 
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Service lié à la demande Azure HDInsight
Le service Azure Data Factory peut automatiquement créer un cluster HDInsight à la demande sous Windows/Linux pour traiter les données. Le cluster est créé dans la même région que celle du compte de stockage (propriété linkedServiceName dans JSON) associé au cluster.

Notez les points **importants** suivants sur le service lié HDInsight à la demande :

* Vous ne voyez pas le cluster HDInsight à la demande créé dans votre abonnement Azure. Le service Azure Data Factory gère le cluster HDInsight à la demande à votre place.
* Les journaux des tâches exécutées sur un cluster HDInsight à la demande sont copiés dans le compte de stockage associé au cluster HDInsight. Vous pouvez accéder à ces journaux à partir du portail Azure dans le panneau **Détails sur l’exécution d’activité** . Pour plus de détails, consultez l'article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md) .
* Vous êtes facturé uniquement lorsque le cluster HDInsight est actif et exécute des tâches.

> [!IMPORTANT]
> Il faut généralement au moins **20 minutes** pour mettre en service un cluster Azure HDInsight à la demande.
> 
> 

### <a name="example"></a>Exemple
Le JSON suivant définit un service lié HDInsight à la demande sous Linux. Le service Data Factory crée automatiquement un cluster HDInsight **sous Linux** lors du traitement d’une tranche de données. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 4,
            "timeToLive": "00:05:00",
            "osType": "linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Pour utiliser un cluster HDInsight Windows, définissez **osType** sur **windows**, ou n’utilisez pas la propriété car la valeur par défaut est windows.  

> [!IMPORTANT]
> Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il n’existe un cluster activé (**timeToLive**), et est supprimé une fois le traitement activé. 
> 
> Comme un nombre croissant de tranches sont traitées, vous voyez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « **nomdevotrefabriquededonnéesadf**-**nomduservicelié**-horodatage ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.
> 
> 

### <a name="properties"></a>Propriétés
| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur **HDInsightOnDemand**. |Oui |
| clusterSize |Nombre de nœuds worker/données dans le cluster. Le cluster HDInsight est créé avec 2 nœuds principaux et le nombre de nœuds worker que vous spécifiez pour cette propriété. Les nœuds étant de taille Standard_D3 à 4 cœurs, un cluster à 4 nœuds de travail prend 24 cœurs (4\*4 = 16 nœuds pour les nœuds de travail + 2\*4 = 8 cœurs pour les nœuds principaux). Pour plus d’informations sur le niveau Standard_D3, voir [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). |yes |
| timetolive |La durée d’inactivité autorisée pour le cluster HDInsight à la demande. Spécifie la durée pendant laquelle le cluster HDInsight à la demande reste actif après l’achèvement d’une exécution d’activité s’il n’existe aucun autre travail actif dans le cluster.<br/><br/>Par exemple, si une exécution d’activité prend 6 minutes et si la propriété TimeToLive est définie sur 5 minutes, le cluster reste actif pendant 5 minutes après les 6 minutes du traitement de l’exécution d’activité. Si une autre exécution d’activité intervient dans la fenêtre de 6 minutes, elle est traitée par le même cluster.<br/><br/>La création d’un cluster HDInsight à la demande étant une opération coûteuse (elle peut prendre du temps), utilisez ce paramètre selon le besoin pour améliorer les performances d’une fabrique de données en réutilisant un cluster HDInsight à la demande.<br/><br/>Si vous définissez la valeur de la propriété TimeToLive sur 0, le cluster est supprimé dès que l’exécution d’activité est traitée. En revanche, si vous définissez une valeur élevée, le cluster peut rester inactif inutilement entraînant des coûts élevés. Par conséquent, il est important de définir la valeur appropriée en fonction de vos besoins.<br/><br/>Plusieurs pipelines peuvent partager la même instance du cluster HDInsight à la demande si la valeur de la propriété TimeToLive est correctement définie. |Oui |
| version |Version du cluster HDInsight. La valeur par défaut est 3.1 pour le cluster Windows et 3.2 pour le cluster Linux. |Non |
| linkedServiceName |Le service lié Azure Storage utilisé par le cluster à la demande pour le stockage et le traitement des données. <p>Actuellement, vous ne pouvez pas créer un cluster HDInsight à la demande qui utilise un Azure Data Lake Store en guise de stockage. Si vous souhaitez stocker les données de résultat à partir du traitement HDInsight dans un Azure Data Lake Store, utilisez une activité de copie pour copier les données du stockage Blob Azure dans Azure Data Lake Store.</p>  | Oui |
| additionalLinkedServiceNames |Spécifie les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les enregistrer en votre nom. |Non |
| osType |Type de système d'exploitation. Les valeurs autorisées sont Windows (par défaut) et Linux. |Non |
| hcatalogLinkedServiceName |Le nom du service lié à SQL Azure pointant vers la base de données HCatalog. Le cluster HDInsight à la demande est créé en utilisant Azure SQL Database en tant que metastore. |Non |

#### <a name="additionallinkedservicenames-json-example"></a>Exemple JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriétés avancées
Vous pouvez également spécifier les propriétés suivantes pour la configuration granulaire du cluster HDInsight à la demande.

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| coreConfiguration |Spécifie les paramètres de configuration de base (par exemple, core-site.xml) pour le cluster HDInsight à créer. |Non |
| hBaseConfiguration |Spécifie les paramètres de configuration HBase (hbase-site.xml) pour le cluster HDInsight. |Non |
| hdfsConfiguration |Spécifie les paramètres de configuration HDFS (hdfs-site.xml) pour le cluster HDInsight. |Non |
| hiveConfiguration |Spécifie les paramètres de configuration Hive (hive-site.xml) pour le cluster HDInsight. |Non |
| mapReduceConfiguration |Spécifie les paramètres de configuration MapReduce (mapred-site.xml) pour le cluster HDInsight. |Non |
| oozieConfiguration |Spécifie les paramètres de configuration Oozie (oozie-site.xml) pour le cluster HDInsight. |Non |
| stormConfiguration |Spécifie les paramètres de configuration Storm (storm-site.xml) pour le cluster HDInsight. |Non |
| yarnConfiguration |Spécifie les paramètres de configuration Yarn (yarn-site.xml) pour le cluster HDInsight. |Non |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemple : configuration de cluster HDInsight à la demande avec les propriétés avancées

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Tailles de nœuds
Vous pouvez spécifier les tailles du nœud principal, du nœud de données et du nœud zookeeper en utilisant les propriétés suivantes : 

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| headNodeSize |Spécifie la taille du nœud principal. La valeur par défaut est Standard_D3. Consultez la section **Spécification des tailles de nœud** ci-dessous pour plus d’informations. |Non |
| dataNodeSize |Spécifie la taille du nœud de données. La valeur par défaut est Standard_D3. |Non |
| zookeeperNodeSize |Spécifie la taille du nœud ZooKeeper. La valeur par défaut est Standard_D3. |Non |

#### <a name="specifying-node-sizes"></a>Spécification des tailles de nœud
Pour connaître les valeurs de chaîne à spécifier pour les propriétés ci-dessus, voir [Tailles des machines virtuelles dans Azure](../virtual-machines/linux/sizes.md). Les valeurs doivent être conformes aux **applets de commande et API** référencées dans l’article. Comme vous pouvez le voir dans l’article, le nœud de données de grande taille (par défaut) possède 7 Go de mémoire, ce qui risque de s’avérer insuffisant pour votre scénario. 

Si vous souhaitez créer des nœuds principaux et des nœuds de travail de taille D4, vous devez spécifier la valeur **Standard_D4** pour les propriétés headNodeSize et dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Si vous spécifiez une valeur incorrecte pour ces propriétés, vous risquez de rencontrer l’ **erreur** suivante : Impossible de créer le cluster. Exception: Unable to complete the cluster create operation. Operation failed with code ’400’. (L’opération a échoué avec le code « 400 ».) Cluster left behind state: 'Error'. Message: 'PreClusterCreationValidationFailure'. Lorsque vous recevez ce message d’erreur, vérifiez que vous utilisez les noms d’**applet de commande et d’API** figurant dans le tableau référencé dans l’article ci-dessus.  

## <a name="bring-your-own-compute-environment"></a>Apportez votre propre environnement de calcul
Dans ce type de configuration, les utilisateurs peuvent inscrire un environnement de calcul existant en tant que service lié dans Data Factory. L'environnement de calcul est géré par l'utilisateur et le service Data Factory l'utilise pour exécuter les activités.

Ce type de configuration est pris en charge pour les environnements de calcul suivants :

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Service Analytique Azure Data Lake
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Service lié Azure HDInsight
Vous pouvez créer un service lié Azure HDInsight pour inscrire votre propre cluster HDInsight avec Data Factory.

### <a name="example"></a>Exemple

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Propriétés
| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur **HDInsight**. |Oui |
| clusterUri |L'URI du cluster HDInsight. |Oui |
| username |Spécifiez le nom de l'utilisateur à utiliser pour se connecter à un cluster HDInsight existant. |Oui |
| password |Spécifiez le mot de passe du compte d'utilisateur. |Oui |
| linkedServiceName | Nom du service lié de stockage Azure faisant référence au stockage Blob Azure utilisé par le cluster HDInsight. <p>Actuellement, vous ne pouvez pas spécifier un service lié Azure Data Lake Store pour cette propriété. Vous pouvez accéder aux données d’Azure Data Lake Store à partir de scripts Hive/Pig si le cluster HDInsight a accès à Data Lake Store. </p>  |Oui |

## <a name="azure-batch-linked-service"></a>Service lié Azure Batch
Vous pouvez créer un service lié Azure Batch pour inscrire un pool de machines virtuelles (VM) Batch à une fabrique de données. Vous pouvez exécuter des activités personnalisées .NET à l'aide d'Azure Batch ou Azure HDInsight.

Consultez les rubriques suivantes si vous ne connaissez pas le service Azure Batch :

* [Présentation de base d’Azure Batch](../batch/batch-technical-overview.md) pour une vue d’ensemble du service Azure Batch.
* Applet de commande [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) pour créer un compte Azure Batch (ou) [Portail Azure](../batch/batch-account-create-portal.md) pour créer le compte Azure Batch à l’aide du portail Azure. Pour obtenir des instructions détaillées sur l'utilisation de l'applet de commande, consultez la rubrique [Utilisation de PowerShell pour gérer un compte Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) pour créer un pool Azure Batch.

### <a name="example"></a>Exemple

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Ajoutez « **.\<nom région\>** » au nom de votre compte Batch pour la propriété **accountName**. Exemple :

```json
"accountName": "mybatchaccount.eastus"
```

Une autre option consiste à fournir le point de terminaison batchUri comme indiqué ci-dessous.  

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriétés
| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur **AzureBatch**. |Oui |
| accountName |Nom du compte Azure Batch. |Oui |
| accessKey |Clé d'accès du compte Azure Batch. |Oui |
| poolName |Nom du pool de machines virtuelles. |Oui |
| linkedServiceName |Nom du service lié Azure Storage associé à ce service lié Azure Batch. Ce service lié est utilisé pour présenter les fichiers nécessaires à l'exécution de l'activité et stocker les journaux d'exécution de l'activité. |Oui |

## <a name="azure-machine-learning-linked-service"></a>Service lié Microsoft Azure Machine Learning
Vous créez un service lié Azure Machine Learning pour inscrire un point de terminaison de notation par lot Machine Learning pour une fabrique de données.

### <a name="example"></a>Exemple

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Propriétés
| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur **AzureML**. |Oui |
| mlEndpoint |L'URL de la notation par lot. |Oui |
| apiKey |L'API du modèle d'espace de travail publié. |Oui |

## <a name="azure-data-lake-analytics-linked-service"></a>Service lié Analytique Azure Data Lake
Vous créez un service lié **Analytique Azure Data Lake** pour lier un service de calcul Analytique Azure Data Lake Analytics à une fabrique de données Azure avant d’utiliser l’ [activité U-SQL Analytique Data Lake](data-factory-usql-activity.md) dans un pipeline.

L’exemple suivant présente la définition JSON pour le service lié Analytique Azure Data Lake.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

Le tableau suivant décrit les propriétés utilisées dans la définition JSON.

| Propriété | Description | Requis |
| --- | --- | --- |
| Type |La propriété de type doit être définie sur **AzureDataLakeAnalytics**. |Oui |
| accountName |Nom du compte du service Analytique Azure Data Lake. |Oui |
| dataLakeAnalyticsUri |URI du service Analytique Azure Data Lake. |Non |
| autorisation |Le code d’autorisation est automatiquement récupéré après un clic sur le bouton **Autoriser** dans l’éditeur de la fabrique de données et une fois la connexion OAuth effectuée. |Oui |
| subscriptionId |ID d’abonnement Azure |Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). |
| resourceGroupName |Nom du groupe de ressources Azure |Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé). |
| sessionId |ID de session issu de la session d'autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Il est généré automatiquement dans l’éditeur de la fabrique de données. |Oui |

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Le message d’erreur suivant peut s’afficher à **l’expiration du jeton** d’authentification : Erreur de l’opération d’informations d’identification : invalid_grant - AADSTS70002: Erreur de validation des informations d’identification. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z ».

| Type d’utilisateur | Expire après |
|:--- |:--- |
| Comptes d’utilisateurs NON gérés par Azure Active Directory (@hotmail.com, @live.com, @outlook.com, par exemple) |12 heures |
| Comptes d’utilisateurs gérés par Azure Active Directory (AAD) |14 jours après la dernière exécution de tranche de données. <br/><br/>90 jours, si une tranche basée sur un service lié OAuth est exécutée au moins une fois tous les 14 jours. |

Pour éviter ou résoudre cette erreur, vous devez accorder une nouvelle autorisation à l’aide du bouton **Autoriser** au moment de l’**expiration du jeton**, puis redéployer le service lié. Vous pouvez également générer des valeurs pour les propriétés sessionId et authorization à l’aide du code fourni dans la section suivante. 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Pour générer les valeurs des propriétés sessionId et authorization au moyen d’un programme
Le code suivant génère les valeurs des propriétés **sessionId** et **authorization**.  

```CSharp

if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Pour plus d’informations sur les classes Data Factory utilisées dans le code, consultez les rubriques [AzureDataLakeStoreLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Vous devez ajouter une référence à Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog. 

## <a name="azure-sql-linked-service"></a>Service lié Azure SQL
Créez un service lié Azure SQL et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) .

## <a name="azure-sql-data-warehouse-linked-service"></a>Service lié Azure SQL Data Warehouse
Créez un service lié Azure SQL Data Warehouse et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

## <a name="sql-server-linked-service"></a>Service SQL Server lié
Créez un service lié à SQL Server et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .


