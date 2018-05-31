---
title: Activité de recherche dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de recherche pour rechercher une valeur à partir d’une source externe. Cette sortie peut être référencée par des activités complémentaires.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: b6c2e2b685855455550612abb58ada6a694bbdff
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011524"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Activité de recherche dans Azure Data Factory

L’activité de recherche peut être utilisée pour récupérer un jeu de données à partir de n’importe quelle source de données compatible ADF.  Elle peut être utilisée dans le scénario suivant :
- Déterminez de manière dynamique quels objets (fichiers, tables, etc.) fonctionnent sur une activité ultérieure, au lieu de coder en dur le nom d’objet

L’activité de recherche peut lire et renvoyer le contenu d’un fichier de configuration, une table de configuration ou le résultat de l’exécution d’une requête ou d’une procédure stockée.  La sortie de l’activité de recherche peut être utilisée dans une activité de transformation ou de copie ultérieure s’il s’agit d’une valeur singleton, ou dans une activité ForEach s’il s’agit d’un tableau d’attributs.

> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Les sources de données suivantes sont prises en charge pour la recherche. Le nombre maximum de lignes pouvant être retournées par l’activité de recherche est de **5000**, et jusqu’à une taille de **2 Mo**. Et la durée maximale pour l’activité de recherche avant l’expiration du délai d’attente est actuellement d’une heure.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propriétés type
NOM | Description | type | Requis ?
---- | ----------- | ---- | --------
dataset | Fournit la référence de jeu de données pour la recherche. Pour plus d’informations, voir la section « Propriétés du jeu de données » dans chaque article traitant du connecteur correspondant. | Paire clé/valeur | OUI
source | Contient des propriétés source spécifiques au jeu de données, identiques à la source de l’activité de copie. Pour plus d’informations, consulter la section « Propriétés de l’activité de copie » dans chaque article traitant du connecteur correspondant. | Paire clé/valeur | OUI
firstRowOnly | Indique s’il faut retourner uniquement la première ligne ou toutes les lignes. | Booléen | Non. La valeur par défaut est `true`.

**Notez les points suivants :**

1. Le colonne Source avec le type ByteArray n’est pas prise en charge.
2. La structure n’est pas prise en charge dans la définition du jeu de données. Pour les fichiers de format texte, vous pouvez utiliser la ligne d’en-tête pour mentionner le nom de la colonne.
3. Si votre source de recherche est un ou plusieurs fichiers JSON, le paramètre `jsonPathDefinition` pour la remise en forme l’objet JSON n’est pas pris en charge. L’ensemble des objets seront récupérés.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Utiliser le résultat de l’activité de recherche dans une activité ultérieure

Le résultat de la recherche est retourné dans la section `output` du résultat de l’exécution d’activité.

* **Quand `firstRowOnly` a la valeur `true` (par défaut)**, le format de la sortie se présente comme dans le code suivant. Le résultat de la recherche se trouve dans une clé `firstRow` fixe. Pour utiliser le résultat dans une activité suivante, utilisez le modèle `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quand `firstRowOnly` a la valeur `false`**, le format de la sortie se présente comme dans le code suivant. Un champ `count` indique le nombre d’enregistrements retournés, tandis que les valeurs détaillées sont affichées dans un tableau `value` fixe. Dans ce cas, l’activité de recherche est généralement suivie d’une [activité Foreach](control-flow-for-each-activity.md). Vous pouvez passer le tableau `value` au champ `items` de l’activité ForEach en utilisant le modèle `@activity('MyLookupActivity').output.value`. Pour accéder aux éléments du tableau `value`, utilisez la syntaxe suivante : `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Voici un exemple : `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exemples
Dans cet exemple, l’activité de copie copie les données d’une table SQL de votre instance Azure SQL Database vers le stockage Blob Azure. Le nom de la table SQL est stocké dans un fichier JSON dans le stockage Blob. L’activité de recherche recherche le nom de la table lors de l’exécution. Cette approche vous permet de modifier JSON de manière dynamique sans avoir à redéployer les pipelines ou les jeux de données. 

Cet exemple illustre une recherche pour la première ligne uniquement. Pour effectuer une recherche portant sur toutes les lignes et chaîner les résultats avec l’activité ForEach, consultez les exemples dans [Copier plusieurs tables en bloc à l’aide d’Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Ce pipeline contient deux activités : *recherche* et *copie*. 

- L’activité de recherche est configurée pour utiliser LookupDataset, qui fait référence à un emplacement dans le stockage d’objets blob Azure. L’activité de recherche lit le nom de la table SQL à partir d’un fichier JSON à cet emplacement. 
- L’activité de copie utilise la sortie de l’activité de recherche (nom de la table SQL). La propriété tableName dans le jeu de données source (SourceDataset) est configurée pour utiliser la sortie de l’activité de recherche. L’activité de copie copie les données de la table SQL vers un emplacement du stockage d’objets blob Azure spécifié par la propriété SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Jeu de données de recherche
Le jeu de données de recherche fait référence au fichier *sourcetable.json* dans le dossier de recherche du stockage Azure spécifié par le type AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Jeu de données source pour l'activité de copie
Le jeu de données source utilise la sortie de l’activité de recherche, qui correspond au nom de la table SQL. L’activité de copie copie les données de cette table SQL vers un emplacement du stockage Blob Azure spécifié par le jeu de données du récepteur. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Jeu de données du récepteur pour l'activité de copie
L’activité de copie copie les données de la table SQL dans le fichier *filebylookup.csv* du dossier *csv* dans le stockage Azure spécifié par la propriété AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure
Ce compte de stockage contient le fichier JSON avec les noms des tables SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Service lié pour base de données SQL Azure
Cette instance Azure SQL Database contient les données à copier dans le stockage Blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Ensemble d’objets

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Tableau d’objets

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité ForEach](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité Web](control-flow-web-activity.md)
