---
title: Présentation d'Azure Data Lake Storage Gen2
description: Fournit une vue d'ensemble d'Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: daa5efcf1be075e81e402edff56239a7cf21db18
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939227"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Présentation d'Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l'analytique du Big Data et intégrées au service [Stockage Blob Azure](storage-blobs-introduction.md). La préversion de Data Lake Storage Gen2 rassemble les fonctionnalités de nos deux services de stockage existants : Stockage Blob Azure et Azure Data Lake Storage Gen1. Les fonctionnalités [d’Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), comme la sémantique des systèmes de fichiers, le répertoire, la sécurité au niveau du fichier et la mise à l'échelle, sont combinées à celles du [stockage Blob Azure](storage-blobs-introduction.md), comme le stockage hiérarchisé économique et la haute disponibilité/reprise après sinistre.

## <a name="designed-for-enterprise-big-data-analytics"></a>Conçu pour l’analytique du Big Data d’entreprise

Data Lake Storage Gen2 fait du stockage Azure la base pour créer des dépôts Data Lake d’entreprise sur Azure. Conçu dès le départ pour traiter plusieurs téraoctets d’informations tout en assurant des centaines de gigaoctets de débit, Data Lake Storage Gen2 vous permet de facilement gérer d'importants volumes de données.

Une caractéristique fondamentale de Data Lake Storage Gen2 est l’ajout d’un [espace de noms hiérarchique](data-lake-storage-namespace.md) au stockage Blob. L’espace de noms hiérarchique organise les objets/fichiers dans une hiérarchie de répertoires pour offrir un accès efficace aux données. Une convention de nommage de magasin d’objets commune utilise des barres obliques dans le nom pour reproduire une structure hiérarchique de dossiers. Cette structure devient réelle avec Data Lake Storage Gen2. Des opérations comme le renommage ou la suppression d’un répertoire deviennent des opérations atomiques uniques de métadonnées sur le répertoire au lieu d’énumérer et de traiter tous les objets qui partagent le préfixe du nom de répertoire.

Dans le passé, l’analytique cloud devait trouver le meilleur compromis entre les performances, la gestion et la sécurité. Data Lake Storage Gen2 traite chacun de ces aspects de plusieurs manières :

-   Les **performances** sont optimisées, car vous n’avez pas besoin de copier ou transformer les données avant l’analyse. L’espace de noms hiérarchique améliore considérablement les performances des opérations de gestion de répertoires, et donc les performances générales du travail.

-   La **gestion** est plus facile, car vous pouvez organiser et manipuler les fichiers dans des répertoires et des sous-répertoires.

-   La **sécurité** est applicable, car vous pouvez définir des autorisations POSIX sur des répertoires ou des fichiers spécifiques.

-   La **rentabilité** est réelle, car Data Lake Storage Gen2 repose sur le [Stockage Blob Azure](storage-blobs-introduction.md) dont le coût est faible. Les fonctionnalités supplémentaires réduisent encore le coût total de possession de l’analytique du Big Data sur Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principales fonctionnalités de Data Lake Storage Gen2

-   **Accès compatible Hadoop** : Azure Data Lake Storage Gen2 vous permet de gérer les données et d’y accéder comme vous le feriez avec un [système de fichiers HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Le nouveau [pilote ABFS](data-lake-storage-abfs-driver.md) est disponible dans tous les environnements Apache Hadoop, y compris [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) et [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) pour accéder aux données stockées dans Data Lake Storage Gen2.

-   **Surensemble d’autorisations POSIX** : Le modèle de sécurité pour Data Lake Gen2 prend en charge les autorisations ACL et POSIX, ainsi que certaines granularités supplémentaires spécifiques à Data Lake Storage Gen2. Les paramètres peuvent être configurés via l’Explorateur Stockage ou des infrastructures telles que Hive et Spark.

-   **Rentabilité** : Data Lake Storage Gen2 offre une capacité de stockage et des transactions à bas coût. Tout au long du cycle de vie des données, les taux de facturation changent pour maintenir les coûts au minimum par le biais de fonctionnalités intégrées comme le [cycle de vie du Stockage Blob Azure](storage-lifecycle-management-concepts.md).

-   **Pilote optimisé** : Le pilote ABFS est [optimisé spécifiquement](data-lake-storage-abfs-driver.md) pour l’analytique des données volumineuses. Les API REST correspondantes sont exposés via le point de terminaison `dfs.core.windows.net`.

### <a name="scalability"></a>Extensibilité

Le Stockage Azure est nativement scalable, que vous y accédiez via les interfaces Data Lake Storage Gen2 ou de stockage Blob. Il peut stocker et traiter *plusieurs exaoctets de données*. Ce volume de stockage est disponible avec un débit mesuré en gigabits par seconde (Gbits/s) et des niveaux élevés d’opérations d’entrée/sortie par seconde (IOPS). En plus de la simple persistance, le traitement est exécuté à des latences par demande quasi constantes, qui sont mesurées au niveau du service, du compte et du fichier.

### <a name="cost-effectiveness"></a>Rentabilité

Un des nombreux avantages de la conception de Data Lake Storage Gen2 sur le stockage Blob Azure est le faible coût de la capacité de stockage et des transactions. Contrairement à d’autres services de stockage cloud, les données stockées dans Data Lake Storage Gen2 n’ont pas à être déplacées ni transformées avant d’effectuer l’analyse. Pour plus d’informations sur la tarification, consultez la page [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Par ailleurs, des fonctionnalités comme [l’espace de noms hiérarchique](data-lake-storage-namespace.md) améliorent considérablement les performances générales de nombreuses tâches analytiques. Cette amélioration des performances signifie que vous avez besoin d’une puissance de calcul moindre pour traiter la même quantité de données, ce qui contribue à la baisse du coût total de possession (TCO) pour le travail analytique de bout en bout.

### <a name="one-service-multiple-concepts"></a>Un seul service, plusieurs concepts

Data Lake Storage Gen2 est une fonctionnalité supplémentaire pour l’analytique Big Data, reposant sur le stockage Blob Azure. L’exploitation des composants de plateforme existants des objets Blob présente de nombreux avantages, dans le cadre de la création et du fonctionnement des lacs de données pour l’analytique. Grâce à cela, plusieurs concepts sont générés pour décrire les mêmes éléments partagés.

Les éléments suivants sont des entités équivalentes, décrites par différents concepts. Sauf indication contraire, ces entités sont directement synonymes :

| Concept                                | Organisation de niveau supérieur | Organisation de niveau inférieur                                            | Conteneur de données |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objets BLOB – Stockage d’objets à usage général | Conteneur              | Répertoire virtuel (Kit de développement logiciel uniquement – ne fournit aucune manipulation atomique) | Blob           |
| ADLS Gen2 – Stockage de l’analytique          | Système de fichiers             | Répertoire                                                           | Fichier           |

## <a name="supported-open-source-platforms"></a>Plateformes open source prises en charge

Plusieurs plateformes open source prennent en charge le stockage Data Lake Gen2. Ces plateformes apparaissent dans le tableau suivant.

> [!NOTE]
> Seules les versions qui s’affichent dans ce tableau sont prises en charge.

| Plateforme |  Version(s) prise(s) en charge | Informations complémentaires |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6+ | [Quels sont les composants et versions Apache Hadoop disponibles avec HDInsight ?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2+ | [Archive de publication Apache Hadoop](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1+ | [Notes de publication de Cloudera Enterprise 6.x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1+ | [Versions de Databricks Runtime](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x++ | [Configuration de l’accès des données dans le Cloud](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants décrivent certains des principaux concepts de Data Lake Storage Gen2 et détaillent comment stocker et gérer les données, y accéder, et les utiliser pour obtenir des insights :

-   [Espace de noms hiérarchique](data-lake-storage-namespace.md)
-   [Créez un compte de stockage](data-lake-storage-quickstart-create-account.md)
-   [Utiliser un compte Azure Data Lake Storage Gen2 dans Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
