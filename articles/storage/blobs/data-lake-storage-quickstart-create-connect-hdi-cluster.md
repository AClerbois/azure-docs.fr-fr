---
title: Configuration du cluster pour Hadoop, Spark, Kafka, HBase ou R Server - Azure HDInsight
description: Configurez Hadoop, Kafka, Spark, HBase, R Server ou les clusters Storm pour HDInsight à partir d’un navigateur, le CLI d’Azure, Azure PowerShell, REST ou le kit de développement logiciel.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: jamesbak
ms.openlocfilehash: 791598da593c25a135c05d72b6846053af3ff344
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353853"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Démarrage rapide : Configurer des clusters dans HDInsight

Dans ce guide de démarrage rapide, vous apprenez à installer et configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka, Interactive Query, HBase, R Server ou Storm. Vous apprenez également à personnaliser les clusters, à les joindre à un domaine et les attacher à un compte de stockage avec la fonctionnalité [Azure Data Lake Storage Gen2 Preview](data-lake-storage-introduction.md) activée.

Un cluster Hadoop se compose de plusieurs machines virtuelles (nœuds) utilisées pour le traitement distribué de tâches. Azure HDInsight prend en charge les détails de mise en œuvre de l’installation et de la configuration des nœuds individuels, de sorte que vous n’avez plus qu’à fournir les informations de configuration générales.

> [!IMPORTANT]
>La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Apprenez comment [supprimer un cluster.](../../hdinsight/hdinsight-delete-cluster.md)

Un compte de stockage avec fonctionnalités Data Lake Storage Gen2 est utilisé en tant que couche de données dans ce guide de démarrage rapide. Avec son service d’espace de noms hiérarchique et un [pilote Hadoop](data-lake-storage-abfs-driver.md), Data Lake Storage Gen2 est optimisé pour l’analyse et le traitement distribués. Les données stockées dans un compte de stockage avec Azure Data Lake Storage Gen2 persistent même après la suppression du cluster HDInsight.

## <a name="prerequisites"></a>Prérequis

- Vous devez créer une identité managée affectée par l’utilisateur, puis affecter le **Rôle Contributeur de Stockage Blob** à l’identité. Consultez [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

## <a name="cluster-setup-methods"></a>Méthodes de configuration du cluster

La table suivante présente les différentes méthodes que vous pouvez utiliser pour configurer un cluster HDInsight.

| Clusters créés avec | un navigateur Web | Ligne de commande | API REST | Foundation | 
| --- |:---:|:---:|:---:|:---:|
| [Portail Azure](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](../../hdinsight/hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI (version 1.0)](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modèles Microsoft Azure Resource Manager](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Création rapide : Configuration d’un cluster de base

Cet article vous guide à travers la configuration du [portail Azure](https://portal.azure.com), où vous pouvez créer un cluster HDInsight en utilisant *Création rapide* ou *Personnaliser*.

![hdinsight options de création personnalisée création rapide](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Suivez les instructions à l’écran pour effectuer une configuration de cluster de base. Les détails sont fournis ci-dessous pour :

* [Nom de groupe ressources](#resource-group-name)
* [Types de cluster et configuration](#cluster-types) 
* [Connexion au cluster et nom d’utilisateur SSH](#cluster-login-and-ssh-user-name)
* [Lieu](#location)

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, reportez-vous à la rubrique [Déclassement de HDInsight 3.3](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Nom de groupe ressources

[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.

## <a name="cluster-types"></a> Types de cluster et configuration

Actuellement, Azure HDInsight propose les types de clusters suivants, chacun avec un ensemble de composants fournissant certaines fonctionnalités.

> [!IMPORTANT]
> Les clusters HDInsight sont disponibles dans différents types, chacun d’eux pour une charge de travail ou une technologie unique. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. Si votre solution nécessite des technologies qui sont réparties sur plusieurs types de clusters HDInsight, un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network) peut connecter les types de cluster requis.

| Type de cluster | Fonctionnalités |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Requête par lot et analyse des données stockées |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |Traitement de grandes quantités de données de NoSQL sans schéma |
| [Interactive Query](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Mise en cache pour les requêtes Hive interactives et plus rapides |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | Plateforme de diffusion en continu distribuée qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Différentes statistiques Big Data, modélisation prédictive et fonctionnalités Machine Learning |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |Traitement d’événements en temps réel |

### <a name="hdinsight-version"></a>Version de HDInsight

Choisissez la version de HDInsight pour ce cluster. Pour plus d’informations, voir [Versions de HDInsight prises en charge](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Pour plus d’informations sur la création d’un cluster HDInsight joint au domaine, consultez [Créer un environnement de bac à sable HDInsight joint à un domaine](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Connexion au cluster et nom d’utilisateur SSH

Les clusters HDInsight vous permettent de configurer deux comptes d’utilisateur lors de la création :

* Utilisateur HTTP : Le nom d’utilisateur par défaut est *admin*. Il utilise la configuration de base sur le portail Azure. Parfois, le nom par défaut est « Utilisateur du cluster ».
* Utilisateur SSH (clusters Linux) : sert à se connecter au cluster à l’aide de SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Emplacement (régions) pour les clusters et le stockage

Vous n’avez pas besoin de spécifier explicitement l’emplacement du cluster : le cluster est au même endroit que le stockage par défaut. Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Points de terminaison de stockage pour les clusters

Bien qu’une installation locale de Hadoop utilise le système de fichiers distribués Hadoop (HDFS) pour le stockage sur le cluster, dans le cloud, vous utilisez des points de terminaison de stockage connectés au cluster. Les clusters HDInsight utilisent [Data Lake Storage Gen2](data-lake-storage-abfs-driver.md) ou des [objets blob dans le Stockage Azure](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). L’utilisation de Stockage Azure ou de Data Lake Storage signifie que vous pouvez supprimer sans risque les clusters HDInsight utilisés pour le calcul tout en conservant vos données.

> [!WARNING]
> L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

Pendant la configuration, vous spécifiez Data Lake Storage pour le point de terminaison de stockage par défaut. Le stockage par défaut contient les journaux des applications et du système. Vous pouvez aussi spécifier des comptes de stockage liés supplémentaires avec Azure Data Lake Storage Gen2 auxquels le cluster peut accéder. Le cluster HDInsight et les comptes de stockage dépendants doivent être situés au même emplacement Azure.

![Paramètres de stockage du cluster : points de terminaison de stockage compatibles HDFS](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage2.png)

Dans l'**identité managée affectée par l'utilisateur**, veillez à sélectionner l'identité managée affectée par l'utilisateur créée en tant que condition préalable de cet article.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Metastores en option

Vous pouvez créer des metastores Hive ou Oozie en option. Toutefois, tous les types de clusters ne prennent pas les metastores et Azure SQL Data Warehouse n’est pas compatible avec les metastores.

Pour plus d’informations, consultez [Utiliser des magasins de métadonnées externes dans Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Lorsque vous créez un metastore personnalisé, n’utilisez pas un nom de base de données contenant des traits d’union, des tirets ou des espaces. Cela risque d’entraîner l’échec du processus de création du cluster.

### <a name="use-hiveoozie-metastore"></a>Metastore Hive

Nous vous recommandons d’utiliser un metastore personnalisé si vous souhaitez conserver vos tables Hive après la suppression de votre cluster HDInsight. Vous pourrez joindre ce metastore à un autre cluster HDInsight.

Un metastore HDInsight créé pour une version de cluster HDInsight ne peut pas être partagé entre différentes versions de cluster HDInsight. Pour obtenir la liste des versions de HDInsight, consultez la section [Versions de HDInsight prises en charge](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metastore Oozie

Pour améliorer les performances avec Oozie, utilisez un metastore personnalisé. Un metastore permet également d’accéder aux données de travail Oozie après la suppression de votre cluster.

> [!IMPORTANT]
> Vous ne pouvez pas réutiliser un metastore Oozie personnalisé. Pour utiliser un metastore Oozie personnalisé, vous devez fournir une base de données SQL Azure vide lors de la création du cluster HDInsight.

## <a name="configure-cluster-size"></a>Configurer la taille du cluster

Vous êtes facturé pour l’utilisation du nœud tant que le cluster existe. La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Les clusters ne peuvent pas être désalloués ou mis en attente.

### <a name="number-of-nodes-for-each-cluster-type"></a>Nombre de nœuds pour chaque type de cluster

Chaque type de cluster possède son propre nombre de nœuds, sa terminologie pour les nœuds et la taille de machine virtuelle par défaut. Dans le tableau suivant, les chiffres entre parenthèses correspondent au nombre de nœuds de chaque type.

| type | Nœuds | Diagramme |
| --- | --- | --- |
| Hadoop |Nœud principal (2), nœud de données (1+) |![Nœuds de cluster Hadoop HDInsight](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |Serveur principal (2), serveur de région (1+), nœud principal/ZooKeeper (3) |![Nœuds de cluster HDInsight HBase](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nœud Nimbus (2), serveur supervisor (1+), nœud ZooKeeper (3) |![Nœuds de cluster HDInsight Storm](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nœud principal (2), nœud worker (1+), nœud ZooKeeper (3) (gratuits pour les machines virtuelles ZooKeeper A1) |![Nœuds de cluster HDInsight Spark](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Pour plus d’informations, consultez [Configuration des nœuds par défaut et tailles des machines virtuelles pour les clusters](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) dans « Quels sont les composants Hadoop et les versions dans HDInsight ? »

Le coût des clusters HDInsight est déterminé par le nombre de nœuds et par la taille des machines virtuelles pour les nœuds.

Les différents types de clusters ont des types de nœuds, nombres de nœuds et tailles de nœuds différents :
* Type de cluster Hadoop par défaut :
    * Deux *nœuds principaux*  
    * Quatre *nœuds de données*
* Type de cluster Storm par défaut :
    * Deux *nœuds Nimbus*
    * Trois *nœuds ZooKeeper*
    * Quatre *nœuds superviseurs*

Si vous essayez simplement out HDInsight, nous vous recommandons d'utiliser un nœud de données. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> La limite de taille du cluster varie selon les abonnements Azure. Contactez le [support de facturation Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) pour augmenter la limite.

Lorsque vous utilisez le portail Azure pour configurer le cluster, la taille de nœud est disponible via le panneau **Niveaux de tarification du nœud**. Dans le portail, vous pouvez également voir le coût associé aux différentes tailles de nœuds.

![Tailles de nœuds de machine virtuelle HDInsight](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Tailles de machines virtuelles

Lorsque vous déployez des clusters, choisissez les ressources de calcul basées sur la solution que vous envisagez de déployer. Les machines virtuelles suivantes sont utilisées pour des clusters HDInsight :

* Machines virtuelles des séries A et D1-4 : [Tailles des machines virtuelles Linux à usage général](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Machine virtuelle de la série D11-14 : [Tailles de machine virtuelle Linux optimisées pour la mémoire](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Pour connaître la valeur que vous devez utiliser pour spécifier une taille de machine virtuelle lors de la création d’un cluster à l’aide de kits de développement logiciel différents ou lorsque vous utilisez Azure PowerShell, consultez les [tailles de machines virtuelles à utiliser pour les clusters HDInsight](../../cloud-services/cloud-services-sizes-specs.md#size-tables). À partir de cet article lié, utilisez la valeur de la colonne **Taille** des tables.

> [!IMPORTANT]
> Si vous avez besoin de plus de 32 nœuds worker dans un cluster, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.

Pour plus d’informations, consultez [Tailles des machines virtuelles](../../virtual-machines/windows/sizes.md). Pour plus d’informations sur la tarification des différentes tailles, consultez [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="custom-cluster-setup"></a>Configuration du cluster personnalisé

La configuration du cluster personnalisé s’appuie sur les paramètres de création rapide et ajoute les options suivantes :

- [Applications HDInsight](#hdinsight-applications)
- [Taille du cluster](#cluster-size)
- Paramètres avancés
  - [Actions de script](#customize-clusters-using-script-action)
  - [Réseau virtuel](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Installer des applications HDInsight sur des clusters

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight sous Linux. Vous pouvez utiliser des applications fournies par Microsoft, des tiers ou que vous développez vous-même. Pour plus d’informations, consultez [Installer des applications Hadoop tierces sur Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

La plupart des applications HDInsight sont installées sur un nœud de périmètre vide.  Un nœud de périmètre vide est une machine virtuelle Linux avec les mêmes outils client installés et configurés comme dans le nœud principal. Vous pouvez utiliser le nœud de périmètre pour accéder au cluster, tester vos applications clientes et héberger vos applications clientes. Pour plus d’informations, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Paramètres avancés : Actions de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la création. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Certains composants Java natifs, comme Mahout et Cascading, peuvent être exécutés sur le cluster en tant que fichiers Java Archive (JAR). Ces fichiers JAR peuvent être distribués au Stockage Azure ou à Azure Data Lake Storage et envoyés aux clusters HDInsight à l’aide des mécanismes d’envoi de travail Hadoop. Pour plus d’informations, consultez la rubrique [Envoi de tâches Hadoop par programme](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> En cas de problèmes lors du déploiement ou de l’appel des fichiers JAR sur les clusters HDInsight, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).
>
> Cascading n’est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight](../../hdinsight/hdinsight-component-versioning.md).

Vous souhaitez parfois configurer des fichiers de configuration suivants pendant le processus de création :

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Pour plus d’informations, consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Paramètres avancés : étendre des clusters avec un réseau virtuel

Si votre solution nécessite des technologies qui sont réparties sur plusieurs types de clusters HDInsight, un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network) peut connecter les types de cluster requis. Cette configuration permet aux clusters, et au code déployé sur ces clusters, de communiquer directement entre eux.

Pour plus d’informations sur l’utilisation du réseau virtuel Azure avec HDInsight, consultez [Étendre HDInsight à l’aide de réseaux virtuels Azure](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Pour voir un exemple d’utilisation de deux types de cluster au sein d’un réseau virtuel Azure, consultez la section [Utiliser Spark Structured Streaming avec Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Extension des capacités de HDInsight à l’aide d’un réseau virtuel Azure](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Résoudre les problèmes de contrôle d’accès

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- [Pilote de système de fichiers ABFS Hadoop pour Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
- [Tutoriel : Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
- [Qu’est-ce que HDInsight, l’écosystème Hadoop et les clusters Hadoop ?](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Prise en main de Hadoop dans HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Travailler à partir d’un PC Windows dans Hadoop sur HDInsight](../../hdinsight/hdinsight-hadoop-windows-tools.md)
