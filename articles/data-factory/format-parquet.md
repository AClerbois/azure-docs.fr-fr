---
title: Format Parquet dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format Parquet dans Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9047e82713b709027275c75b17eb955877c62f08
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674785"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format Parquet dans Azure Data Factory

Suivez cet article si vous souhaitez **analyser des fichiers Parquet ou écrire des données au format Parquet**. 

Le format Parquet est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Blob Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Stockage Fichier Azure](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données Parquet.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | La propriété type du jeu de données doit être définie sur **Parquet**. | OUI      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`. **Consultez les détails dans l’article du connecteur -> section des propriétés du jeu de données**. | OUI      |
| compressionCodec | Codec de compression à utiliser lors de l’écriture dans des fichiers Parquet. Lors de la lecture de fichiers Parquet, Data Factory détermine automatiquement le codec de compression sur la base des métadonnées de fichier.<br>Les types pris en charge sont « **none** », « **gzip** », « **snappy** » (valeur par défaut) et « **lzo** ». Notez que l’activité de copie ne prend actuellement pas en charge LZO. | Non       |

> [!NOTE]
> Les espaces blancs dans le nom de colonne ne sont pas pris en charge pour les fichiers Parquet.

Voici un exemple de jeu de données Parquet sur Stockage Blob Azure :

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Parquet en tant que source et récepteur.

### <a name="parquet-as-source"></a>Parquet en tant que source

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | La propriété type de la source de l’activité de copie doit être définie sur **ParquetSource**. | OUI      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

### <a name="parquet-as-sink"></a>Parquet en tant que récepteur

Les propriétés prises en charge dans la section ***\*récepteur\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | La propriété type de la source de l’activité de copie doit être définie sur **ParquetSink**. | OUI      |
| storeSettings | Groupe de propriétés sur la méthode d’écriture de données dans un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres d’écriture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la section sur le mappage de flux de données.

## <a name="data-type-support"></a>Prise en charge des types de données

Les types de données complexes Parquet ne sont actuellement pas pris en charge (par ex., MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Utilisation du runtime d’intégration auto-hébergé

> [!IMPORTANT]
> Dans le cas de copies permises par Integration Runtime (auto-hébergé), par exemple, entre des magasins de données locaux et cloud, si vous ne copiez pas les fichiers Parquet **tels quels**, vous devez installer **JRE 8 64 bits (Java Runtime Environment) ou OpenJDK** sur votre machine de runtime d’intégration. Consultez le paragraphe suivant pour plus de détails.

Dans le cas de copies s’exécutant sur l’IR auto-hébergé avec sérialisation/désérialisation des fichiers Parquet, ADF localise le runtime Java en vérifiant d’abord le registre *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pour JRE puis, s’il ne le trouve pas, en vérifiant la variable système *`JAVA_HOME`* pour OpenJDK.

- **Pour utiliser JRE** : Le runtime d’intégration de 64 bits requiert la version 64 bits de JRE. Vous pouvez la récupérer [ici](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Pour utiliser OpenJDK** : il est pris en charge depuis la version 3.13 du runtime d’intégration. Empaquetez jvm.dll avec tous les autres assemblys requis d’OpenJDK dans la machine d’IR auto-hébergé et définissez la variable d’environnement système JAVA_HOME en conséquence.

> [!TIP]
> Si, en copiant des données au format Parquet avec le runtime d’intégration auto-hébergé, vous obtenez une erreur indiquant « An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space** », vous pouvez ajouter une variable d’environnement `_JAVA_OPTIONS` sur l’ordinateur qui héberge le runtime d’intégration auto-hébergé afin d’ajuster la taille de segment de mémoire minimale/maximale nécessaire pour que la machine virtuelle Java puisse effectuer une copie de ce type, puis réexécuter le pipeline.

![Définir la taille de segment de mémoire JVM sur le runtime d’intégration auto-hébergé](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemple : donnez la valeur `-Xms256m -Xmx16g` à la variable `_JAVA_OPTIONS`. L’indicateur `Xms` spécifie le pool d’allocation de mémoire initial pour une Machine virtuelle Java (JVM), tandis que `Xmx` spécifie le pool d’allocation de mémoire maximal. En d’autres termes, JVM démarrera avec la quantité de mémoire `Xms` et pourra au maximum utiliser la quantité de mémoire `Xmx`. Par défaut, ADF applique un minimum de 64 Mo et un maximum de 1 Go.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
