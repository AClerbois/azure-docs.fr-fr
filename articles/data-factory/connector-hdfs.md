---
title: Copier des données de HDFS à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’une source HDFS dans le cloud ou locale vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 627d0a305268b6814a3f48a0f9eee88c097627a6
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876783"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copier des données depuis HDFS à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Version actuelle](connector-hdfs.md)

Cet article explique comment copier des données à partir du serveur HDFS. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur HDFS est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [pris en charge de la matrice de source/récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Plus précisément, ce connecteur HDFS prend en charge ce qui suit :

- Copie de fichiers en utilisant une authentification **Windows** (Kerberos) ou **Anonyme**.
- Copie de fichiers en utilisant le protocole **webhdfs** ou la prise en charge de **DistCp intégré**.
- Copie de fichiers en l'état ou analyse/génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Prérequis

Pour copier des données depuis un HDFS qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations, voir l’article [Runtime d’intégration auto-hébergé](concepts-integration-runtime.md).

> [!NOTE]
> Assurez-vous que le runtime d'intégration peut accéder à **TOUS** les éléments [serveur du nœud de nom]:[port du nœud de nom] et [serveurs du nœud de données]:[port du nœud de données] du cluster Hadoop. Le [port du nœud de nom] par défaut est 50070 et le [port du nœud de données] par défaut est 50075.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de HDFS :

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HDFS sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Hdfs**. | Oui |
| url |URL vers le système HDFS |OUI |
| authenticationType | Les valeurs autorisées sont les suivantes : **Anonyme**  ou **Windows**. <br><br> Pour utiliser l’**authentification Kerberos** pour le connecteur HDFS, reportez-vous à [cette section](#use-kerberos-authentication-for-hdfs-connector) pour configurer votre environnement local en conséquence. |OUI |
| userName |Nom d’utilisateur de l’authentification Windows Pour l’authentification Kerberos, spécifiez `<username>@<domain>.com`. |Oui (pour l’authentification Windows) |
| password |Mot de passe de l’authentification Windows Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui (pour l’authentification Windows) |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple : utilisation d’une authentification anonyme**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : utilisation d’une authentification Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). 

- Pour **Parquet et le format de texte délimité**, reportez-vous à [jeu de données de format Parquet et texte délimité](#parquet-and-delimited-text-format-dataset) section.
- Pour les autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre jeu de données de format](#other-format-dataset) section.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet et le jeu de données de format texte délimité

Pour copier des données à partir de HDFS dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) l’article sur le jeu de données en fonction du format et la prise en charge Paramètres. Les propriétés suivantes sont prises en charge de fichiers HDFS sous `location` paramètres dans le jeu de données en fonction du format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **HdfsLocation**. | Oui      |
| folderPath | Le chemin d’accès au dossier. Si vous souhaitez utiliser le caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |
| fileName   | Le nom du fichier sous le paramètre folderPath donné. Si vous souhaitez utiliser le caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |

> [!NOTE]
> **Partage de fichiers** type de jeu de données avec le format Parquet/texte mentionné dans la section suivante est toujours prise en charge-est de l’activité de copie/recherche pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Autre jeu de données de format

Pour copier des données à partir de HDFS dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **FileShare** |Oui |
| folderPath | Chemin d'accès au dossier. Le filtre de caractères génériques est pris en charge, et les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de fichier contient effectivement ce caractère d’échappement ou générique. <br/><br/>Exemples : dossier_racine/sous-dossier/ ; consultez d’autres exemples dans [Exemples de filtres de dossier et de fichier](#folder-and-file-filter-examples). |Oui |
| fileName |  **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d'échappement si le nom réel de votre dossier contient des caractères génériques ou ce caractère d'échappement. |Non  |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous voulez analyser des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques.

**Exemple :**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source HDFS.

### <a name="hdfs-as-source"></a>HDFS en tant que source

- Pour la copie depuis **Parquet et le format de texte délimité**, reportez-vous à [Parquet et source de format de texte délimité](#parquet-and-delimited-text-format-source) section.
- Pour copier à partir d’autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre source de format](#other-format-source) section.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet et source de format de texte délimité

Pour copier des données à partir de HDFS dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) article sur la source d’activité de copie basée sur le format et paramètres pris en charge. Les propriétés suivantes sont prises en charge de fichiers HDFS sous `storeSettings` paramètres de source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Type                     | La propriété de type sous `storeSettings` doit être définie sur **HdfsReadSetting**. | Oui                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                             |
| wildcardFolderPath       | Le chemin d’accès de dossier avec des caractères génériques pour filtrer les dossiers de code source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                             |
| wildcardFileName         | Le nom de fichier avec des caractères génériques sous folderPath/wildcardFolderPath donné pour filtrer les fichiers source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                             |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                             |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de stockage simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                             |

> [!NOTE]
> Pour Parquet/texte délimité par des **FileSystemSource** source d’activité de copie type mentionné dans la section suivante est toujours prise en charge-concerne pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Autre source de format

Pour copier des données à partir de HDFS dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge dans l’activité de copie **source** section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **HdfsSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |
| distcpSettings | Groupe de propriétés lors de l’utilisation de HDFS DistCp. | Non  |
| resourceManagerEndpoint | Point de terminaison de Yarn ResourceManager | Oui, en cas d’utilisation de DistCp |
| tempScriptPath | Chemin d’accès du dossier utilisé pour stocker le script de commande DistCp temporaire. Le fichier de script est généré par Data Factory et supprimé une fois le travail de copie terminé. | Oui, en cas d’utilisation de DistCp |
| distcpOptions | Options supplémentaires fournies à la commande DistCp. | Non  |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de stockage simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non  |

**Exemple : Source HDFS dans l’activité de copie à l’aide de DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Apprenez-en davantage sur l’utilisation de DistCp pour copier efficacement des données de HDFS en lisant la section suivante.

### <a name="folder-and-file-filter-examples"></a>Exemples de filtres de dossier et de fichier

Cette section décrit le comportement résultant de l’utilisation de filtres de caractères génériques dans les noms de fichier et les chemins de dossier.

| folderPath | fileName             | recursive | Structure du dossier source et résultat du filtrage (les fichiers en **gras** sont récupérés) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vide, utiliser la valeur par défaut) | false     | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*`  | (vide, utiliser la valeur par défaut) | true      | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*`  | `*.csv`              | false     | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*`  | `*.csv`              | true      | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utiliser DistCp pour copier des données de HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) est un outil en ligne de commande Hadoop natif permettant d’effectuer une copie distribuée dans un cluster Hadoop. Lors de l’exécution d’une commande Distcp, celle-ci affiche tous les fichiers copiés et crée plusieurs travaux de mappage dans le cluster Hadoop, puis chaque travail de mappage effectue une copie binaire de la source au récepteur.

L’activité de copie prend en charge l’utilisation de DistCp pour copier des fichiers en l'état vers un objet blob Azure (y compris une [copie intermédiaire](copy-activity-performance.md)) ou Azure Data Lake Store, auquel cas elle peut tirer pleinement parti de la puissance de votre cluster au lieu de s’exécuter sur le runtime d’intégration auto-hébergé. Le débit de la copie est ainsi amélioré, en particulier si votre cluster est très puissant. Selon votre configuration dans Azure Data Factory, l’activité de copie construit automatiquement une commande distcp, l’envoie à votre cluster Hadoop et surveille l’état de la copie.

### <a name="prerequisites"></a>Conditions préalables

Pour utiliser DistCp pour copier des fichiers en l’état de HDFS vers un objet blob Azure (y compris une copie intermédiaire) ou Azure Data Lake Store, assurez-vous que votre cluster Hadoop présente la configuration requise suivante :

1. Les services MapReduce et Yarn sont activés.
2. La version minimale de Yarn est 2.5.
3. Le serveur HDFS est intégré avec votre banque de données cible (objet blob Azure ou Azure Data Lake Store) :

    - Le système de fichiers d’objet blob Azure est pris en charge en mode natif depuis Hadoop 2.7. Vous devez uniquement spécifier le chemin d’accès du fichier jar dans Hadoop env config.
    - Le système de fichiers d’Azure Data Lake Store est empaqueté depuis Hadoop 3.0.0-alpha1. Si votre cluster Hadoop est d’une version antérieure, vous devez importer manuellement les packages jar liés à Azure Data Lake Store (azure-datalake-store.jar) dans un cluster à partir d’[ici](https://hadoop.apache.org/releases.html), et spécifier le chemin d’accès du fichier jar dans Hadoop env config.

4. Préparez un dossier temporaire dans HDFS. Ce dossier temporaire est utilisé pour stocker le script d'interpréteur de commandes DistCp afin qu’il occupe un espace exprimable en Ko.
5. Assurez-vous que le compte d’utilisateur fourni dans le service lié HDFS est autorisé à : (a) soumettre une application dans Yarn ; (b) créer un sous-dossier, et lire/écrire des fichiers dans le dossier temporaire situé au-dessus.

### <a name="configurations"></a>Configurations

Consultez DistCp liés des configurations et des exemples dans [HDFS en tant que source](#hdfs-as-source) section.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilisation de l’authentification Kerberos pour le connecteur HDFS

Il existe deux options de configuration de l’environnement local afin d’utiliser l’authentification Kerberos dans le connecteur HDFS. Vous pouvez choisir celle qui correspond mieux à votre situation.
* Option 1 : [Joindre un ordinateur exécutant le runtime d’intégration auto-hébergé dans le domaine Kerberos](#kerberos-join-realm)
* Option 2 : [Activer l’approbation mutuelle entre le domaine Windows et le domaine Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Option 1 : Joindre un ordinateur exécutant le runtime d’intégration auto-hébergé dans le domaine Kerberos

#### <a name="requirements"></a>Configuration requise

* L’ordinateur exécutant le runtime d’intégration auto-hébergé doit joindre le domaine Kerberos et ne peut pas joindre un domaine Windows.

#### <a name="how-to-configure"></a>Comment configurer

**Sur un ordinateur exécutant le runtime d’intégration auto-hébergé :**

1.  Exécutez l’utilitaire **Ksetup** pour configurer le serveur Kerberos KDC et le domaine.

    L’ordinateur doit être configuré en tant que membre d’un groupe de travail, car un domaine Kerberos est différent d’un domaine Windows. Pour ce faire, définissez le domaine Kerberos et ajoutez un serveur KDC comme suit. Remplacez *REALM.COM* par votre propre domaine respectif en fonction des besoins.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Redémarrez** l’ordinateur après avoir exécuté ces 2 commandes.

2.  Vérifiez la configuration avec la commande **Ksetup**. La sortie doit être semblable à :

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Dans Azure Data Factory :**

* Configurez le connecteur HDFS à l’aide de l’**authentification Windows** avec votre nom principal Kerberos et le mot de passe pour vous connecter à la source de données HDFS. Vérifiez les détails de configuration dans la section sur les [propriétés du service lié HDFS](#linked-service-properties).

### <a name="kerberos-mutual-trust"></a>Option 2 : Activer l’approbation mutuelle entre le domaine Windows et le domaine Kerberos

#### <a name="requirements"></a>Configuration requise

*   L’ordinateur exécutant le runtime d’intégration auto-hébergé doit joindre un domaine Windows.
*   Vous avez besoin d’autorisations pour mettre à jour les paramètres du contrôleur de domaine.

#### <a name="how-to-configure"></a>Comment configurer

> [!NOTE]
> Remplacez REALM.COM et AD.COM dans le didacticiel suivant par votre propre domaine et contrôleur de domaine respectifs en fonction des besoins.

**Sur le serveur KDC :**

1. Modifiez la configuration KDC dans le fichier **krb5.conf** afin de permettre au KDC d’approuver le domaine Windows faisant référence au modèle de configuration suivant. Par défaut, la configuration se trouve dans **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Redémarrez** le service KDC après la configuration.

2. Préparez un principal nommé **krbtgt/REALM.COM\@AD.COM** dans le serveur KDC avec la commande suivante :

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Dans le fichier de configuration du service HDFS **hadoop.security.auth_to_local**, ajoutez `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Sur le contrôleur de domaine :**

1.  Exécutez les commandes **Ksetup** suivantes pour ajouter une entrée de domaine :

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Établir l’approbation entre le domaine Windows et le domaine Kerberos. [password] est le mot de passe pour le principal **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Sélectionnez l’algorithme de chiffrement utilisé dans Kerberos.

    1. Accédez à Gestionnaire de serveur > Gestion des stratégies de groupe > Domaine > Objets de stratégie de groupe > Stratégie de domaine par défaut ou actif, puis Modifier.

    2. Dans la fenêtre contextuelle **Éditeur de gestion des stratégies de groupe**, accédez à Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies locales > Options de sécurité, puis configurez **Sécurité réseau : Configurer les types de chiffrement autorisés pour Kerberos**.

    3. Sélectionnez l’algorithme de chiffrement à utiliser pour vous connecter à KDC. En règle générale, vous pouvez simplement sélectionner toutes les options.

        ![Configuration des types de chiffrement pour Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilisez la commande **Ksetup** pour spécifier l’algorithme de chiffrement à utiliser dans le domaine spécifique.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Créez le mappage entre le compte de domaine et le principal Kerberos afin de pouvoir utiliser le principal Kerberos dans un domaine Windows.

    1. Démarrez Outils d’administration > **Utilisateurs et ordinateurs Active Directory**.

    2. Pour configurer les fonctionnalités avancées, cliquez sur **Affichage** > **Fonctionnalités avancées**.

    3. Recherchez le compte pour lequel vous souhaitez créer des mappages, cliquez avec le bouton droit pour afficher **Mappages des noms** > cliquez sur l’onglet **Noms Kerberos**.

    4. Ajoutez un principal provenant du domaine.

        ![Mappage des identités de sécurité](media/connector-hdfs/map-security-identity.png)

**Sur un ordinateur exécutant le runtime d’intégration auto-hébergé :**

* Exécutez les commandes **Ksetup** suivantes pour ajouter une entrée de domaine.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Dans Azure Data Factory :**

* Configurez le connecteur HDFS à l’aide de l’**authentification Windows** avec votre compte de domaine ou le principal Kerberos pour vous connecter à la source de données HDFS. Vérifiez les détails de configuration dans la section sur les [propriétés du service lié HDFS](#linked-service-properties).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
