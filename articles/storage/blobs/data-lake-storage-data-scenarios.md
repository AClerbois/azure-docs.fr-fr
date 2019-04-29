---
title: Scénarios de données impliquant Azure Data Lake Storage Gen2 | Microsoft Docs
description: Comprendre les différents scénarios et outils à l’aide desquels les données peuvent être ingérées, traitées, téléchargées et affichées dans Data Lake Storage Gen2 (anciennement Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: 9deaa2f1e381dffbd85b0ee150c5782098a9db6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628215"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Utilisation d’Azure Data Lake Storage Gen2 pour le Big Data

Il existe quatre étapes principales dans traitement des données Big Data :

> [!div class="checklist"]
> * Réception de grandes quantités de données dans un magasin de données, en temps réel ou par lots
> * Traitement des données
> * Téléchargement des données
> * Visualisation des données

Commencez par créer un compte de stockage et un système de fichiers. Puis, octroyez l'accès aux données. Les premières sections de cet article vous aideront à accomplir ces tâches. Dans les sections suivantes, nous nous pencherons sur les options et outils à utiliser lors de chacune des phases de traitement.

## <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

Un compte Data Lake Storage Gen2 est un compte de stockage qui possède un espace de noms hiérarchique. 

Pour créer un compte de ce type, consultez [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un *système de fichiers* est un conteneur de dossiers et de fichiers. Il vous en faut au moins un pour commencer à ingérer des données dans votre compte de stockage.  Voici une liste d'outils que vous pouvez utiliser pour les créer.

|Outil | Assistance |
|---|--|
|Explorateur de stockage Azure | [Créer un système de fichiers à l'aide de l'Explorateur Stockage](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [Créer un conteneur d'objets blob ou un partage de fichiers à l'aide d'AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Interface de ligne de commande (CLI) Hadoop File System (HDFS) avec HDInsight |[Créer un système de fichiers en utilisant HDFS avec HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Code dans un notebook Azure Databricks|[Créer un système de fichiers de compte de stockage (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Créer un système de fichiers et le monter (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Pour créer des systèmes de fichiers, le plus simple consiste à utiliser l'Explorateur Stockage ou AzCopy. La création de systèmes de fichiers à l'aide de HDInsight et Databricks nécessite un peu plus de travail. Si vous prévoyez malgré tout d'utiliser des clusters HDInsight ou Databricks pour traiter vos données, commencez par créer vos clusters, puis utilisez l'interface CLI de HDFS pour créer vos systèmes de fichiers.  

## <a name="grant-access-to-the-data"></a>Octroyer l'accès aux données

Configurez les autorisations d'accès appropriées pour votre compte et les données qu'il contient avant d'entamer l'ingestion des données.

Pour octroyer l'accès, trois méthodes sont disponibles :

* Attribuez l'un de ces rôles à un utilisateur, à un groupe, à une identité gérée par l'utilisateur ou à un principal de service :

  [Lecteur des données blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Utilisez un jeton de signature d'accès partagé (SAP).

* Utilisez une clé de compte de stockage.

Ce tableau montre comment octroyer l'accès à chacun des services ou outils Azure.

|Outil | Pour octroyer l'accès | Assistance |
|---|--|---|
|Explorateur Stockage| Attribuer un rôle aux utilisateurs et aux groupes | [Attribuer des rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Attribuer un rôle aux utilisateurs et aux groupes <br>**ou**<br> Utiliser un jeton SAS| [Attribuer des rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Créer facilement une signature d'accès partagé (SAS) pour télécharger un fichier à partir du service Stockage Azure - Utiliser l'Explorateur Stockage Azure](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Affecter un rôle à une identité managée affectée par l’utilisateur | [Créer un cluster HDInsight avec Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Attribuer un rôle à une identité managée affectée par l'utilisateur<br>**ou**<br> Attribuer un rôle à un principal de service<br>**ou**<br> Utiliser une clé de compte de stockage | [Propriétés du service lié](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Affecter un rôle à une identité managée affectée par l’utilisateur | [Créer un cluster HDInsight avec Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Attribuer un rôle à un principal de service | [Guide pratique : Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Pour octroyer l'accès à des fichiers et dossiers spécifiques, consultez ces articles.

* [Définir des autorisations au niveau de fichiers et de répertoires à l'aide de l'Explorateur Stockage Azure avec Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listes de contrôle d'accès sur les fichiers et répertoires](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Pour en savoir plus sur la configuration d'autres aspects liés à la sécurité, consultez [Guide de sécurité Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Ingérer les données

Cette section présente les différentes sources de données et les différentes manières d’ingérer ces données dans un compte Data Lake Storage Gen2.

![Ingérer des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingérer des données dans Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Données ad hoc

Ceci représente les petits jeux de données qui sont utilisés pour créer un prototype d’une application de Big Data. Il existe différentes façons de recevoir des données ad hoc en fonction de la source de données. 

Voici une liste d'outils que vous pouvez utiliser pour ingérer des données ad hoc.

| source de données | Réception avec |
| --- | --- |
| Ordinateur local |[Explorateur Stockage](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Outil AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Outil AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp en cours d’exécution sur un cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Flux de données

Ceci représente les données qui peuvent être générées par diverses sources, telles que des applications, des appareils, des capteurs, etc. Ces données peuvent être ingérées dans Data Lake Storage Gen2 par des outils divers. En général, ces outils capturent et traitent les données sur la base de l’événement en temps réel, puis ils écrivent les événements par lots dans Data Lake Storage Gen2 afin qu’ils puissent être traités.

Voici une liste d'outils que vous pouvez utiliser pour ingérer des données diffusées en continu.

|Outil | Assistance |
|---|--|
|Azure HDInsight Storm | [Écrire dans Apache Hadoop HDFS à partir d'Apache Storm sur HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Données relationnelles

Les bases de données relationnelles peuvent également être utilisées comme sources des données. Sur une période donnée, les bases de données relationnelles collectent de grandes quantités de données qui peuvent fournir des informations clés si elles sont traitées via un pipeline de Big Data. Vous pouvez utiliser les outils suivants pour déplacer ces données vers Data Lake Storage Gen2.

Voici une liste d'outils que vous pouvez utiliser pour ingérer des données relationnelles.

|Outil | Assistance |
|---|--|
|Azure Data Factory | [Activité Copy dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Données de journal de serveur web (téléchargement à l’aide d’applications personnalisées)

Ce type de jeu de données est spécifiquement indiqué, car l’analyse des données de journal de serveur web constitue un cas d’usage courant pour les applications de Big Data et nécessite le chargement d’importants volumes de fichiers journaux sur Data Lake Storage Gen2. Vous pouvez utiliser les outils suivants pour écrire vos propres scripts ou applications pour télécharger ces données.

Voici une liste d'outils que vous pouvez utiliser pour ingérer des données de journal de serveur web.

|Outil | Assistance |
|---|--|
|Azure Data Factory | [Activité Copy dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Pour télécharger des données de journal de serveur web, et également pour télécharger d’autres types de données (par exemple, les données relatives aux sentiments sociaux), il est préférable d’écrire vos propres scripts/applications personnalisés, car cela vous donne la possibilité d’inclure votre composant de téléchargement de données dans le cadre de votre application de Big Data plus étendue. Dans certains cas, ce code peut prendre la forme d’un script ou d’un utilitaire de ligne de commande simple. Dans d’autres cas, le code peut être utilisé pour intégrer le traitement de Big Data dans une solution ou une application métier.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Données associées aux clusters Azure HDInsight

La plupart des types de clusters HDInsight (Hadoop, HBase, Storm) prennent en charge Data Lake Storage Gen2 comme référentiel de stockage des données. Les clusters HDInsight accèdent aux données à partir des objets blob d’Azure Storage (WASB). Pour optimiser les performances, vous pouvez copier les données à partir de WASB sur un compte Data Lake Storage Gen2 associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

Voici une liste d'outils que vous pouvez utiliser pour ingérer des données associées à des clusters HDInsight.

|Outil | Assistance |
|---|--|
|Apache DistCp | [Utiliser DistCp pour copier des données entre Azure Storage Blob et Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Outil AzCopy | [Transférer des données avec AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Copier des données vers ou à partir d’Azure Data Lake Storage Gen2 à l’aide d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Données stockées localement ou dans des clusters IaaS Hadoop

De grandes quantités de données peuvent être stockées dans des clusters Hadoop existants, localement sur les ordinateurs à l’aide de HDFS. Les clusters Hadoop peuvent être inclus dans un déploiement local ou au sein d’un cluster IaaS sur Azure. Il peut être nécessaire de copier ces données dans Azure Data Lake Storage Gen2 afin de bénéficier d’un accès unique ou périodique. Différentes options vous permettent d’y parvenir. Voici une liste de ces options et des compromis correspondants.

| Approche | Détails | Avantages | Considérations |
| --- | --- | --- | --- |
| Utiliser Azure Data Factory (ADF) pour copier des données directement à partir de clusters Hadoop dans Azure Data Lake Storage Gen2 |[ADF prend en charge HDFS comme source de données](../../data-factory/connector-hdfs.md) |ADF offre une prise en charge immédiate de HDFS ainsi qu’une gestion et une surveillance de bout en bout de premier ordre |Nécessite que la passerelle de gestion des données soit déployée localement ou dans le cluster IaaS |
| Utilisez Distcp pour copier les données de Hadoop dans Azure Storage. Copiez ensuite les fichiers de Stockage Azure vers Data Lake Storage Gen2 à l’aide du mécanisme approprié. |Vous pouvez copier les données de Stockage Azure vers Data Lake Storage Gen2 en utilisant : <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Outil AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[pache DistCp en cours d’exécution sur des clusters HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Vous pouvez utiliser des outils open source. |Processus en plusieurs étapes qui implique différentes technologies |

### <a name="really-large-datasets"></a>Jeux de données très volumineux

Pour télécharger des jeux de données qui comptent plusieurs téraoctets, l’utilisation des méthodes décrites ci-dessus peut parfois être lente et coûteuse. Dans ce cas, vous pouvez utiliser Azure ExpressRoute.  

Azure ExpressRoute vous permet de créer des connexions privées entre les infrastructures et les centres de données Azure dans votre environnement local. Ceci constitue une option fiable pour le transfert de grandes quantités de données. Pour en savoir plus, consultez la [Documentation Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Traiter les données

Une fois que les données sont disponibles dans Data Lake Storage Gen2, vous pouvez exécuter une analyse sur ces données à l’aide des applications de Big Data prises en charge. 

![Analyser des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analyser des données dans Data Lake Storage Gen2")

Voici une liste d'outils que vous pouvez utiliser pour exécuter des travaux d'analyse sur les données stockées dans Data Lake Storage Gen2.

|Outil | Assistance |
|---|--|
|Azure HDInsight | [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Démarrage rapide : Analyser des données dans Azure Data Lake Storage Gen2 à l'aide d'Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutoriel : Extraire, transformer et charger des données à l'aide d'Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualiser les données

Vous pouvez utiliser une combinaison de services pour créer des représentations visuelles des données stockées dans Data Lake Storage Gen2.

![Visualiser des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualiser des données dans Data Lake Storage Gen2")

* Vous pouvez commencer par utiliser [Azure Data Factory pour déplacer les données de Data Lake Storage Gen2 vers Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Ensuite, vous pouvez [intégrer Power BI à Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) pour créer une représentation visuelle des données.

## <a name="download-the-data"></a>Télécharger les données

Vous pouvez également être amené à télécharger ou à déplacer les données à partir d’Azure Data Lake Storage Gen2 dans certains cas, tels que :

* Déplacer des données vers d’autres référentiels pour créer une interface avec vos pipelines de traitement des données existantes. Par exemple, vous pourriez vouloir déplacer des données de Data Lake Storage Gen2 vers Azure SQL Database ou vers SQL Server local.

* Télécharger des données sur votre ordinateur local pour le traitement dans des environnements IDE lors de la création de prototypes d’applications.

![Sortir des données à partir de Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Sortir des données à partir de Data Lake Storage Gen2")

Voici une liste d'outils que vous pouvez utiliser pour télécharger des données à partir de Data Lake Storage Gen2.

|Outil | Assistance |
|---|--|
|Azure Data Factory | [Activité Copy dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Utiliser DistCp pour copier des données entre Azure Storage Blob et Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
