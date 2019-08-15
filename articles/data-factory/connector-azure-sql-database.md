---
title: Copier des données vers ou à partir d’Azure SQL Database à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de magasins de données sources pris en charge vers Azure SQL Database ou de SQL Database vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 905d208dccf54ac34e3f832d4d0c5b98a6121757
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827509"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copier des données depuis/vers Azure SQL Database en utilisant Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [Version actuelle](connector-azure-sql-database.md)

Cet article explique comment copier des données vers et depuis Azure SQL Database. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure SQL Database est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec tableau de [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)

Plus précisément, ce connecteur Azure SQL Database prend en charge les fonctions suivantes :

- Copie de données à l’aide de l’authentification SQL et de l’authentification du jeton de l’application Azure Active Directory (Azure AD) avec un principal de service ou l’identité managée pour les ressources Azure.
- En tant que source, la récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, l’ajout de données à une table de destination ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie.

>[!NOTE]
>Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) n’est actuellement pas pris en charge par ce connecteur. Pour contourner ce problème, vous pouvez utiliser un [connecteur ODBC générique](connector-odbc.md) et un pilote SQL Server ODBC via un runtime d’intégration auto-hébergé. Suivez [ces instructions](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) relatives au téléchargement du pilote ODBC et à la configuration des chaînes de connexion.

> [!IMPORTANT]
> Si vous copiez des données via le runtime d’intégration Azure Data Factory, configurez un [pare-feu Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) de façon à ce que les services Azure puissent accéder au serveur.
> Si vous copiez des données à l’aide d’un runtime d’intégration auto-hébergé, configurez le pare-feu Azure SQL Server pour qu’il autorise la plage IP appropriée. Cette plage inclut l’adresse IP de l’ordinateur utilisé pour se connecter à Azure SQL Database.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Azure Data Factory propres à un connecteur Azure SQL Database.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour un service lié Azure SQL Database sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **AzureSqlDatabase**. | OUI |
| connectionString | Spécifiez les informations requises pour la connexion à l’instance Azure SQL Database pour la propriété **connectionString**. <br/>Marquez ce champ en tant que **SecureString** pour le stocker en toute sécurité dans Azure Data Factory. Vous pouvez également placer un mot de passe ou une clé de principal de service dans Azure Key Vault. En cas d’authentification SQL, vous pouvez extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez l’exemple JSON figurant après le tableau et l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Azure Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| locataire | Spécifiez les informations de locataire, comme le nom de domaine ou l’ID de locataire, dans lequel votre application se trouve. Récupérez-les en pointant la souris dans le coin supérieur droit du Portail Azure. | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| connectVia | Ce [runtime d'intégration](concepts-integration-runtime.md) permet de se connecter au magasin de données. Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, le runtime d’intégration par défaut est utilisé. | Non |

Pour en savoir plus sur les autres types d’authentification, consultez les sections suivantes sur les prérequis et les exemples JSON, respectivement :

- [Authentification SQL](#sql-authentication)
- [Authentification par jeton d’application Azure AD : Principal du service](#service-principal-authentication)
- [Authentification par jeton d’application Azure AD : Identités managées pour les ressources Azure](#managed-identity)

>[!TIP]
>Si vous rencontrez une erreur avec le code d’erreur « UserErrorFailedToConnectToSqlServer » et un message tel que « La limite de session pour la base de données est XXX et a été atteinte. », ajoutez `Pooling=false` à votre chaîne de connexion, puis réessayez.

### <a name="sql-authentication"></a>Authentification SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemple de service lié qui utilise l’authentification SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Mot de passe dans Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour utiliser l’authentification du jeton d’application Azure AD basée sur le principal de service, effectuez les étapes suivantes :

1. [Créez une application Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) dans le Portail Azure. Prenez note du nom de l’application et des valeurs suivantes qui définissent le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. [Provisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pour votre serveur Azure SQL Server sur le Portail Azure, si ce n’est pas déjà fait. L’administrateur Azure AD doit être un utilisateur Azure AD ou un groupe Azure AD, mais il ne doit pas s’agir d’un principal de service. Vous devez effectuer cette étape pour qu’à l’étape suivante vous puissiez utiliser une identité Azure AD pour créer un utilisateur de base de données autonome pour le principal de service.

3. [Créez des utilisateurs de base de données autonome](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pour le principal de service. Connectez-vous à la base de données vers ou à partir de laquelle vous souhaitez copier des données à l’aide d’outils tels que SQL Server Management Studio, avec une identité Azure AD qui a au moins l’autorisation ALTER ANY USER. Exécutez le code T-SQL suivant : 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Accordez les autorisations requises par le principal de service comme vous le feriez d’habitude pour des utilisateurs SQL ou autres. Exécutez le code suivant. Pour plus d’options, consultez [ce document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Dans Azure Data Factory, configurez un service lié Azure SQL Database.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemple de service lié qui utilise l’authentification du principal de service

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identités managées pour authentifier les ressources Azure

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser cette identité managée pour l’authentification Azure SQL Database. La fabrique désignée peut accéder et copier des données vers ou à partir de votre base de données à l’aide de cette identité.

Pour utiliser l’authentification par identité managée, effectuez les étapes suivantes.

1. [Provisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) pour votre serveur Azure SQL Server sur le Portail Azure, si ce n’est pas déjà fait. L’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe Azure AD. Si vous accordez au groupe avec identité managée un rôle d’administrateur, ignorez les étapes 3 et 4. L’administrateur dispose d’un accès complet à la base de données.

2. [Créer des utilisateurs de base de données autonome](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) pour l’identité managée d’Azure Data Factory. Connectez-vous à la base de données vers ou à partir de laquelle vous souhaitez copier des données à l’aide d’outils tels que SQL Server Management Studio, avec une identité Azure AD qui a au moins l’autorisation ALTER ANY USER. Exécutez le code T-SQL suivant : 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Accordez les autorisations requises par l’identité managée Data Factory comme vous le feriez d’habitude pour des utilisateurs SQL et autres. Exécutez le code suivant. Pour plus d’options, consultez [ce document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Dans Azure Data Factory, configurez un service lié Azure SQL Database.

**Exemple**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article consacré aux [jeux de données](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database.

Pour copier des données depuis ou vers Azure SQL Database, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du jeu de données doit être définie sur **AzureSqlTable**. | OUI |
| tableName | Nom de la table ou de la vue dans l’instance Azure SQL Database à laquelle le service lié fait référence. | Non pour Source, Oui pour Récepteur |

#### <a name="dataset-properties-example"></a>Exemple de propriétés du jeu de données

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure SQL Database en tant que source et récepteur.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database en tant que source

Pour copier des données d’Azure SQL Database, affectez la valeur **SqlSource** à la propriété **type** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source de l’activité de copie doit être définie sur **SqlSource**. | OUI |
| sqlReaderQuery | Cette propriété utilise la requête SQL personnalisée pour lire les données. Par exemple `select * from MyTable`. | Non |
| sqlReaderStoredProcedureName | Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. | Non |
| storedProcedureParameters | Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de procédure stockée. | Non |

**Points à noter :**

- Si **sqlReaderQuery** est spécifié pour **SqlSource**, l’activité de copie exécute cette requête sur la source Azure SQL Database pour obtenir les données. Vous pouvez également spécifier une procédure stockée en spécifiant **sqlReaderStoredProcedureName** et **storedProcedureParameters** si la procédure stockée accepte des paramètres.
- Si vous ne spécifiez pas **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, les colonnes définies dans la section « structure » du JSON de jeu de données sont utilisées pour créer une requête. La requête `select column1, column2 from mytable` est exécutée sur Azure SQL Database. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.

#### <a name="sql-query-example"></a>Exemple de requête SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>Exemple de procédure stockée

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>Définition de la procédure stockée

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database en tant que récepteur

> [!TIP]
> Pour en savoir plus sur les meilleures pratiques, les configurations et les comportements d’écriture pris en charge, consultez l’article [Meilleures pratiques de chargement de données dans Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Pour copier des données vers Azure SQL Database, affectez la valeur **SqlSink** à la propriété **type** dans le récepteur d’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du récepteur de l’activité de copie doit être définie sur **SqlSink**. | OUI |
| writeBatchSize | Nombre de lignes à insérer dans le tableau SQL *par lot*.<br/> La valeur autorisée est **integer** (nombre de lignes). Par défaut, Azure Data Factory détermine de façon dynamique la taille de lot appropriée selon la taille de ligne. | Non |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/> La valeur autorisée est **timespan**. Par exemple : « 00:30:00 » (30 minutes). | Non |
| preCopyScript | Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Azure SQL Database. Elle n'est appelée qu'une seule fois par copie. Utilisez cette propriété pour nettoyer les données préchargées. | Non |
| sqlWriterStoredProcedureName | Nom de la procédure stockée qui définit comment appliquer des données sources dans une table cible. <br/>Cette procédure stockée est *appelée par lot*. Pour les opérations qui ne s’exécutent qu’une seule fois et qui n’ont rien à voir avec les données sources (par exemple, supprimer ou tronquer), utilisez la propriété `preCopyScript`. | Non |
| storedProcedureTableTypeParameterName |Nom du paramètre du type de table spécifié dans la procédure stockée.  |Non |
| sqlWriterTableType |Nom du type de table à utiliser dans la procédure stockée. L'activité de copie rend les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms et de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non |

**Exemple 1 : Ajout de données**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Meilleures pratiques de chargement de données dans Azure SQL Data

Lors de la copie de données dans Azure SQL Database, vous pourriez avoir besoin d’un comportement d’écriture différent :

- [Ajouter](#append-data) : Mes données sources contiennent uniquement de nouveaux enregistrements.
- [Upsert](#upsert-data) : Mes données sources contiennent à la fois des insertions et des mises à jour.
- [Remplacer](#overwrite-the-entire-table) : Je veux recharger une table de dimension entière à chaque fois.
- [Écrire avec une logique personnalisée](#write-data-with-custom-logic) : J’ai besoin d’un traitement supplémentaire avant l’insertion finale dans la table de destination.

Reportez-vous aux sections correspondantes pour savoir comment configurer cela dans Azure Data Factory et pour connaître les meilleures pratiques associées.

### <a name="append-data"></a>Ajout de données

L’ajout de données est le comportement par défaut de ce connecteur de récepteur Azure SQL Database. Azure Data Factory effectue une insertion en bloc pour écrire efficacement dans votre table. Vous pouvez configurer la source et le récepteur en conséquence dans l’activité de copie.

### <a name="upsert-data"></a>Effectuer un upsert de données

**Option 1 :** Quand vous avez une grande quantité de données à copier, utiliser l’approche suivante pour effectuer un upsert : 

- Tout d’abord, utilisez une [table temporaire de niveau base de données](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) pour le chargement en bloc de tous les enregistrements via l’activité de copie. Étant donné que les opérations sur des tables temporaires de niveau base de données ne sont pas journalisées, vous pouvez charger des millions d’enregistrements en quelques secondes.
- Exécutez une activité de procédure stockée dans Azure Data Factory pour appliquer une instruction [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Utilisez la table temporaire comme source pour exécuter toutes les mises à jour ou insertions en tant que transaction unique. De cette façon, le nombre d’allers-retours et d’opérations de journalisation est réduit. À la fin de l’activité de procédure stockée, la table temporaire peut être tronquée pour être prête pour le prochain cycle d’upsert.

Par exemple, dans Azure Data Factory, vous pouvez créer un pipeline avec une **activité de copie** chaînée avec une **activité de procédure stockée**. La première activité copie des données à partir de votre banque source dans une table temporaire Azure SQL Database, par exemple **##UpsertTempTable**, comme nom de table dans le jeu de données. La seconde activité appelle une procédure stockée pour fusionner les données sources de la table temporaire dans la table cible et nettoyer la table temporaire.

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

### <a name="overwrite-the-entire-table"></a>Remplacer la table entière

Vous pouvez configurer la propriété **preCopyScript** dans le récepteur de l’activité de copie. Dans ce cas, pour chaque activité de copie qui s’exécute, Azure Data Factory exécute d’abord le script. Il exécute ensuite la copie pour insérer les données. Par exemple, pour remplacer l’intégralité de la table par les données les plus récentes, spécifiez un script pour d’abord supprimer tous les enregistrements avant de charger en bloc les nouvelles données à partir de la source.

### <a name="write-data-with-custom-logic"></a>Écrire des données avec une logique personnalisée

Les étapes permettant d’écrire des données à l’aide d’une logique personnalisée sont semblables à celles décrites dans la section [Effectuer un upsert de données](#upsert-data). Lorsque vous devez appliquer un traitement supplémentaire avant l’insertion finale des données sources dans la table de destination, à grande échelle, vous pouvez effectuer l’une de ces deux actions :

- Charger les données dans une table temporaire de niveau base de données, puis appeler une procédure stockée. 
- Appeler une procédure stockée pendant la copie.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Appel d'une procédure stockée à partir d'un récepteur SQL

Quand vous copiez des données dans Azure SQL Database, vous pouvez également configurer et appeler une procédure stockée spécifiée par l’utilisateur avec des paramètres supplémentaires. La fonction de procédure stockée tire parti des [paramètres table](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Appeler une procédure stockée traite les données ligne par ligne, et non pas en bloc, ce qui n’est pas recommandé pour la copie à grande échelle. Pour en savoir plus, consultez l’article [Meilleures pratiques de chargement de données dans Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Par exemple, lorsque vous souhaitez appliquer un traitement supplémentaire avant l’insertion finale des données sources dans la table de destination. Fusionner des colonnes, rechercher des valeurs supplémentaires et effectuer des insertions dans plusieurs tables sont des exemples de traitement supplémentaire.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert dans une table Azure SQL Database. En supposant que les données d’entrée et la table réceptrice **Marketing** ont trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération upsert basée sur la colonne **ProfileID** et appliquez-la uniquement à une catégorie spécifique appelée « ProductA ».

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

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la section sur le mappage de flux de données.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mappage de type de données pour Azure SQL Database

Quand des données sont copiées vers ou à partir d’Azure SQL Database, les mappages suivants sont utilisés entre les types de données Azure Data Factory et les types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database | Type de données intermédiaires Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Pour les types de données mappés avec le type intermédiaire Decimal, Azure Data Factory prend actuellement en charge une précision maximale de 28. Si les données ont une précision supérieure à 28, envisagez de les convertir en chaîne dans la requête SQL.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Banques de données et formats pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
