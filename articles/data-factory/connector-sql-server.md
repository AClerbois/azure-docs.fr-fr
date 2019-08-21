---
title: Copier des données depuis et vers SQL Server à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment déplacer des données vers et depuis une base de données SQL Server qui est locale ou dans une machine virtuelle Azure à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: f5ddd9928194c477d8f8b6f4c9569a8fe58f39d3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967382"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copier des données vers et depuis SQL Server à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version d’Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Version actuelle](connector-sql-server.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis une base de données SQL Server. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md) qui offre une présentation générale de l’activité de copie.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données SQL Server vers tout magasin de données récepteur pris en charge. Vous pouvez aussi copier des données à partir de tout magasin de données source pris en charge vers une base de données SQL Server. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SQL Server prend en charge :

- SQL Server versions 2016, 2014, 2012, 2008 R2, 2008 et 2005.
- La copie des données à l'aide de l'authentification SQL ou Windows
- En tant que source, la récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, l'ajout de données à une table de destination ou l'appel d'une procédure stockée avec une logique personnalisée pendant la copie

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) n’est pas pris en charge.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) n’est actuellement pas pris en charge par ce connecteur. Pour contourner ce problème, vous pouvez utiliser un [connecteur ODBC générique](connector-odbc.md) et un pilote SQL Server ODBC. Suivez [ces instructions](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) relatives au téléchargement du pilote ODBC et à la configuration des chaînes de connexion.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur de base de données SQL Server.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié SQL Server :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SqlServer**. | OUI |
| connectionString |Spécifiez les informations **connectionString** nécessaires pour établir une connexion à la base de données SQL Server à l’aide de l’authentification SQL ou de l’authentification Windows. Consultez les exemples suivants.<br/>Marquez ce champ comme **SecureString** pour le stocker de manière sécurisée dans Azure Data Factory. Vous pouvez également placer un mot de passe dans Azure Key Vault. En cas d’authentification SQL, extrayez la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez l’exemple JSON figurant après le tableau et l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| userName |Spécifiez un nom d’utilisateur si vous utilisez l’authentification Windows. Exemple : **domainname\\username**. |Non |
| password |Spécifiez un mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ comme **SecureString** pour le stocker de manière sécurisée dans Azure Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Non |
| connectVia | Ce [runtime d'intégration](concepts-integration-runtime.md) permet de se connecter au magasin de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

>[!TIP]
>Si vous rencontrez une erreur avec le code d’erreur « UserErrorFailedToConnectToSqlServer » et un message tel que « La limite de session de la base de données XXX a été atteinte », ajoutez `Pooling=false` à votre chaîne de connexion, puis réessayez.

**Exemple 1 : Utilisation de l'authentification SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utiliser l’authentification SQL avec un mot de passe dans Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 3 : Utilisation de l'authentification Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données SQL Server.

Pour copier des données depuis et vers la base de données SQL Server, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **SqlServerTable**. | OUI |
| tableName |Cette propriété est le nom de la table ou de la vue dans l’instance de base de données SQL Server à laquelle le service lié fait référence. | Non pour Source, Oui pour Récepteur |

**Exemple**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste de propriétés prises en charge par la source et le récepteur SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server en tant que source

Pour copier des données à partir de SQL Server, définissez **SqlSource** comme type source dans l’activité de copie. Les propriétés prises en charge dans la section source de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l'activité de copie doit être définie sur **SqlSource**. | OUI |
| sqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Par exemple `select * from MyTable`. |Non |
| sqlReaderStoredProcedureName |Cette propriété est le nom de la procédure stockée qui lit les données dans la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non |
| storedProcedureParameters |Ces paramètres concernent la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de procédure stockée. |Non |

**Points à noter :**

- Si **sqlReaderQuery** est spécifié pour **SqlSource**, l’activité de copie exécute cette requête sur la source SQL Server pour obtenir les données. Vous pouvez également spécifier une procédure stockée en spécifiant **sqlReaderStoredProcedureName** et **storedProcedureParameters** si la procédure stockée accepte des paramètres.
- Si vous ne spécifiez pas **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, les colonnes définies dans la section « structure » du jeu de données JSON sont utilisées pour créer une requête. La requête `select column1, column2 from mytable` s’exécute sur l’instance SQL Server. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.

**Exemple : Utiliser une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple : Utilisation d'une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Définition de la procédure stockée**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server en tant que récepteur

> [!TIP]
> Pour en savoir plus sur les bonnes pratiques, les configurations et les comportements d’écriture pris en charge, consultez l’article [Bonnes pratiques de chargement de données dans SQL Server](#best-practice-for-loading-data-into-sql-server).

Pour copier des données vers SQL Server, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section récepteur de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur de l'activité de copie doit être définie sur **SqlSink**. | OUI |
| writeBatchSize |Nombre de lignes à insérer dans la table SQL *par lot*.<br/>Les valeurs autorisées sont des entiers pour le nombre de lignes. Par défaut, Azure Data Factory détermine de façon dynamique la taille de lot appropriée en fonction de la taille de ligne. |Non |
| writeBatchTimeout |Cette propriété spécifie le délai d'attente avant expiration de l'opération d'insertion de lot.<br/>Les valeurs autorisées sont celles qui expriment un intervalle de temps. Exemple : « 00:30:00 » pour 30 minutes. |Non |
| preCopyScript |Cette propriété spécifie une requête SQL que l’activité de copie doit exécuter avant l’écriture de données dans SQL Server. Elle n'est appelée qu'une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |
| sqlWriterStoredProcedureName | Nom de la procédure stockée qui définit comment appliquer des données sources dans une table cible. <br/>Cette procédure stockée est *appelée par lot*. Pour les opérations qui ne s’exécutent qu’une seule fois et qui n’ont rien à voir avec les données sources (par exemple, supprimer ou tronquer), utilisez la propriété `preCopyScript`. | Non |
| storedProcedureTableTypeParameterName |Nom du paramètre du type de table spécifié dans la procédure stockée.  |Non |
| sqlWriterTableType |Nom du type de table à utiliser dans la procédure stockée. L'activité de copie rend les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms et de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non |

**Exemple 1 : Ajout de données**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemple 2 : Appeler une procédure stockée pendant la copie**

Pour en savoir plus, consultez [Appel d'une procédure stockée à partir d'un récepteur SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Bonnes pratiques de chargement de données dans SQL Server

Lors de la copie de données dans SQL Server, vous pouvez exiger un comportement d’écriture différent :

- [Ajouter](#append-data) : Mes données sources contiennent uniquement de nouveaux enregistrements.
- [Upsert](#upsert-data) : Mes données sources contiennent à la fois des insertions et des mises à jour.
- [Remplacer](#overwrite-the-entire-table) : Je veux recharger la totalité de la table de dimension à chaque fois.
- [Écrire avec une logique personnalisée](#write-data-with-custom-logic) : J’ai besoin d’un traitement supplémentaire avant l’insertion finale dans la table de destination.

Consultez les sections correspondantes pour savoir comment effectuer des configurations dans Azure Data Factory et connaître les bonnes pratiques associées.

### <a name="append-data"></a>Ajouter des données

L’ajout de données est le comportement par défaut de ce connecteur de récepteur SQL Server. Azure Data Factory effectue une insertion en bloc pour écrire efficacement dans votre table. Vous pouvez configurer la source et le récepteur en conséquence dans l’activité de copie.

### <a name="upsert-data"></a>Effectuer un upsert de données

**Option 1 :** Quand vous avez une grande quantité de données à copier, utilisez l’approche suivante pour effectuer un upsert : 

- Tout d’abord, utilisez une [table temporaire](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) pour charger en bloc tous les enregistrements par le biais de l’activité de copie. Étant donné que les opérations sur des tables temporaires ne sont pas journalisées, vous pouvez charger des millions d’enregistrements en quelques secondes.
- Exécutez une activité de procédure stockée dans Azure Data Factory pour appliquer une instruction [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Utilisez une table temporaire comme source pour effectuer toutes les mises à jour ou insertions sous la forme d’une transaction unique. De cette façon, le nombre d’allers-retours et d’opérations de journalisation est réduit. À la fin de l’activité de procédure stockée, la table temporaire peut être tronquée pour être prête pour le prochain cycle d’upsert.

Par exemple, dans Azure Data Factory, vous pouvez créer un pipeline avec une **activité de copie** chaînée avec une **activité de procédure stockée**. La première activité copie des données à partir de votre magasin source dans une table temporaire de base de données, par exemple **##UpsertTempTable**, comme nom de table dans le jeu de données. La seconde activité appelle ensuite une procédure stockée pour fusionner les données sources de la table temporaire dans la table cible et nettoyer la table temporaire.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Dans votre base de données, définissez une procédure stockée avec la logique MERGE, comme dans l’exemple suivant, qui est pointée à partir de l’activité de procédure stockée précédente. Supposons que la cible est la table **Marketing** comportant trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération d’upsert sur la colonne **ProfileID**.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Option 2 :** Vous pouvez également choisir d’[appeler une procédure stockée dans l’activité de copie](#invoke-a-stored-procedure-from-a-sql-sink). Cette approche exécute chaque ligne de la table source au lieu d’utiliser l’insertion en bloc comme approche par défaut dans l’activité de copie, ce qui n’est pas approprié pour les opérations d’upsert à grande échelle.

### <a name="overwrite-the-entire-table"></a>Remplacer l’intégralité de la table

Vous pouvez configurer la propriété **preCopyScript** dans un récepteur d’activité de copie. Dans le cas présent, pour chaque activité de copie qui s’exécute, Azure Data Factory exécute d’abord le script. Il exécute ensuite la copie pour insérer les données. Par exemple, pour remplacer l’intégralité de la table par les données les plus récentes, spécifiez un script pour d’abord supprimer tous les enregistrements avant de charger en bloc les nouvelles données à partir de la source.

### <a name="write-data-with-custom-logic"></a>Écrire des données avec une logique personnalisée

Les étapes permettant d’écrire des données à l’aide d’une logique personnalisée sont semblables à celles décrites dans la section [Effectuer un upsert de données](#upsert-data). Quand vous devez appliquer un traitement supplémentaire avant l’insertion finale des données sources dans la table de destination, à grande échelle, vous pouvez effectuer l’une de ces deux actions : 

- Charger les données dans une table temporaire, puis appeler une procédure stockée. 
- Appeler une procédure stockée pendant la copie.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Appel d'une procédure stockée à partir d'un récepteur SQL

Quand vous copiez des données dans une base de données SQL Server, vous pouvez également configurer et appeler une procédure stockée spécifiée par l’utilisateur avec des paramètres supplémentaires. La fonction de procédure stockée tire parti des [paramètres table](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Appeler une procédure stockée traite les données ligne par ligne, et non pas en bloc, ce qui n’est pas recommandé pour la copie à grande échelle. Pour en savoir plus, consultez l’article [Bonnes pratiques de chargement de données dans SQL Server](#best-practice-for-loading-data-into-sql-server).

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Par exemple, quand vous souhaitez appliquer un traitement supplémentaire avant l’insertion finale de données sources dans la table de destination. Fusionner des colonnes, rechercher des valeurs supplémentaires et insérer des données dans plusieurs tables sont des exemples de traitement supplémentaire.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table de la base de données SQL Server. Supposons que les données d’entrée et la table réceptrice **Marketing** ont trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération upsert basée sur la colonne **ProfileID** et appliquez-la uniquement à une catégorie spécifique appelée « ProductA ».

1. Dans votre base de données, définissez le type de table avec le même nom que **sqlWriterTableType**. Le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Dans votre base de données, définissez la procédure stockée avec le même nom que **SqlWriterStoredProcedureName**. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom de paramètre du type de table de la procédure stockée doit être identique au **tableName** défini dans le jeu de données.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Dans Azure Data Factory, définissez la section **Récepteur SQL** de l’activité de copie comme suit :

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mappage de type de données pour SQL Server

Quand vous copiez des données depuis et vers SQL Server, les mappages suivants sont utilisés de types de données SQL Server en types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données SQL Server | Type de données intermédiaires Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Pour les types de données mappés avec le type intermédiaire Decimal, Azure Data Factory prend actuellement en charge une précision maximale de 28. Si vous disposez de données qui nécessitent une précision supérieure à 28, pensez à les convertir en chaîne dans une requête SQL.

## <a name="troubleshoot-connection-issues"></a>Résoudre les problèmes de connexion

1. Configurez votre instance SQL Server pour qu’elle accepte les connexions à distance. Démarrez **SQL Server Management Studio**, cliquez avec le bouton droit sur **Serveur**, puis sélectionnez **Propriétés**. Sélectionnez **Connexions** dans la liste, puis cochez la case **Autoriser les accès distants à ce serveur**.

    ![Activation des connexions à distance](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Pour obtenir des instructions détaillées, consultez [Configurer l’option de configuration du serveur d’accès à distance](https://msdn.microsoft.com/library/ms191464.aspx).

2. Démarrez le **Gestionnaire de configuration SQL Server**. Développez **Configuration du réseau SQL Server** pour l’instance souhaitée, puis sélectionnez **Protocoles pour MSSQLSERVER**. Des protocoles s’affichent dans le volet droit. Activez TCP/IP en cliquant avec le bouton droit sur **TCP/IP**, puis en sélectionnant **Activer**.

    ![Activation de TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Pour obtenir des informations supplémentaires et découvrir d’autres façons d’activer le protocole TCP/IP, consultez [Activer ou désactiver un protocole réseau de serveur](https://msdn.microsoft.com/library/ms191294.aspx).

3. Dans la même fenêtre, double-cliquez sur **TCP/IP** pour lancer la fenêtre des **propriétés TCP/IP**.
4. Allez sous l’onglet **Adresses IP** . Faites défiler l’écran vers le bas jusqu’à la section **IPAll**. Notez le **port TCP**. La valeur par défaut est **1433**.
5. Créez une **règle de Pare-feu Windows** sur l’ordinateur pour autoriser le trafic à entrer par ce port. 
6. **Vérifiez la connexion** : Pour vous connecter à SQL Server en utilisant un nom complet, utilisez SQL Server Management Studio à partir d’un autre ordinateur. Par exemple `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l'activité de copie d'Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
