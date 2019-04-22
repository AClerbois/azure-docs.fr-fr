---
title: Intégration d’Azure Data Lake Storage Gen1 à d’autres services Azure | Microsoft Docs
description: Présentation de l’intégration d’Azure Data Lake Storage Gen1 à d’autres services Azure
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: e28863f9980d6403bef1f88de01b7a9b5271b444
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879299"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Intégration d’Azure Data Lake Storage Gen1 à d’autres services Azure
Azure Data Lake Storage Gen1 peut être utilisé conjointement à d’autres services Azure afin d’augmenter le nombre de scénarios. L’article suivant liste les services auxquels Data Lake Storage Gen1 peut être intégré.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Utiliser Data Lake Storage Gen1 avec Azure HDInsight
Vous pouvez approvisionner un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) qui utilise Data Lake Storage Gen1 comme stockage compatible HDFS. Pour cette version, pour les clusters Hadoop et Storm sous Windows et Linux, vous ne pouvez utiliser Data Lake Storage Gen1 que comme stockage supplémentaire. Ces clusters utilisent toujours Azure Storage (WASB) comme stockage par défaut. Toutefois, pour les clusters HBase sous Windows et Linux, vous pouvez utiliser Data Lake Storage Gen1 comme stockage par défaut, comme stockage supplémentaire ou les deux.

Pour savoir comment approvisionner un cluster HDInsight avec Data Lake Storage Gen1, consultez les rubriques suivantes :

* [Approvisionner un cluster HDInsight avec Data Lake Storage Gen1 à l’aide du portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Approvisionner un cluster HDInsight avec Data Lake Storage Gen1 comme stockage par défaut à l’aide d’Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Approvisionner un cluster HDInsight avec Data Lake Storage Gen1 comme stockage supplémentaire à l’aide d’Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Utiliser Data Lake Storage Gen1 avec Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) vous permet de travailler avec le Big Data à l'échelle du cloud. Il approvisionne dynamiquement des ressources et vous permet d’effectuer des analyses sur des téraoctets, voire des exaoctets, de données stockées dans plusieurs sources de données prises en charge, parmi lesquelles Data Lake Storage Gen1. Data Lake Analytics est spécialement optimisé pour fonctionner avec Data Lake Storage Gen1, fournissant ainsi le plus haut niveau de performances, de débit et de parallélisation pour vos charges de travail de Big Data.

Pour obtenir des instructions sur l’utilisation de Data Lake Analytics avec Data Lake Storage Gen1, consultez la rubrique [Prise en main de Data Lake Analytics avec Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Utiliser Data Lake Storage Gen1 avec Azure Data Factory
Vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour recevoir des données à partir de tables Azure, de bases de données SQL Azure, d'Azure SQL DataWarehouse, d'objets Blob Azure Storage et de bases de données locales. Jouissant d’un statut de premier ordre dans l’écosystème Azure, Azure Data Factory peut être utilisé pour orchestrer l’ingestion de données de ces sources vers Data Lake Storage Gen1.

Pour obtenir des instructions sur l’utilisation d’Azure Data Factory avec Data Lake Storage Gen1, consultez la rubrique [Déplacer des données vers et depuis Data Lake Storage Gen1 avec Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copier des données Stockage Blob Azure dans Data Lake Storage Gen1
Azure Data Lake Storage Gen1 fournit un outil de ligne de commande, AdlCopy, qui permet de copier les données Stockage Blob Azure vers un compte Data Lake Storage Gen1. Pour plus d’informations, consultez la rubrique [Copier des données Stockage Blob Azure vers Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copier des données entre Azure SQL Database et Data Lake Storage Gen1
Vous pouvez utiliser Apache Sqoop pour importer et exporter des données entre Azure SQL Database et Data Lake Storage Gen1. Pour plus d’informations, consultez la rubrique [Copier des données entre Data Lake Storage Gen1 et Azure SQL Database à l’aide de Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Utiliser Data Lake Storage Gen1 avec Stream Analytics
Vous pouvez utiliser Data Lake Storage Gen1 en tant que sortie pour stocker les données diffusées en continu à l’aide d’Azure Stream Analytics. Pour plus d’informations, consultez la rubrique [Diffuser des données à partir d’Azure Storage Blob dans Data Lake Storage Gen1 à l’aide d’Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Utiliser Data Lake Storage Gen1 avec Power BI
Vous pouvez utiliser Power BI pour importer des données à partir d’un compte Data Lake Storage Gen1 en vue de les analyser et de les visualiser. Pour plus d’informations, consultez la rubrique [Analyse des données dans Data Lake Storage Gen1 à l’aide de Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Utiliser Data Lake Storage Gen1 avec Data Catalog
Vous pouvez inscrire des données issues de Data Lake Storage Gen1 dans Azure Data Catalog pour qu’elles puissent être découvertes à l’échelle de l’organisation. Pour plus d’informations, consultez la rubrique [Inscrire des données Data Lake Storage Gen1 dans Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Utiliser Data Lake Storage Gen1 avec SQL Server Integration Services (SSIS)
Vous pouvez utiliser le Gestionnaire de connexions Data Lake Storage Gen1 dans SSIS pour connecter un paquet SSIS à Data Lake Storage Gen1. Pour obtenir plus d’informations, consultez la rubrique [Utiliser Data Lake Storage Gen1 avec SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Utiliser Data Lake Storage Gen1 avec SQL Data Warehouse
Vous pouvez utiliser Polybase pour charger des données à partir de Data Lake Storage Gen1 vers SQL Data Warehouse. Pour obtenir plus d’informations, consultez la rubrique [Utiliser Data Lake Storage Gen1 avec SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Utiliser Data Lake Storage Gen1 avec Azure Event Hubs
Vous pouvez utiliser Azure Data Lake Storage Gen1 pour archiver et capturer les données reçues par Azure Event Hubs. Pour plus d’informations, consultez la rubrique [Utiliser Data Lake Storage Gen1 avec Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble d’Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Prise en main de Data Lake Storage Gen1 à l’aide du portail](data-lake-store-get-started-portal.md)
* [Prise en main de Data Lake Storage Gen1 à l’aide de PowerShell](data-lake-store-get-started-powershell.md)  

