---
title: "Copier des données à partir de Sybase à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier des données de Sybase vers des magasins de données récepteurs pris en charge à l’aide d’une activité de copie dans un pipeline Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 1170ee7232e1046e194f5223f7b7bf582ef18dfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copier des données à partir de Sybase à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - GA](v1/data-factory-onprem-sybase-connector.md)
> * [Version 2 - Préversion](connector-sybase.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une base de données Sybase. Il s’appuie sur l’article de [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, qui est actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Sybase dans V1](v1/data-factory-onprem-sybase-connector.md).

## <a name="supported-scenarios"></a>Scénarios pris en charge

Vous pouvez copier des données d’une base de données Sybase vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Sybase prend en charge :

- Sybase **version 16 et ultérieures**
- Copie des données avec l’authentification **De base** ou **Windows**.

## <a name="prerequisites"></a>Composants requis

Pour utiliser ce connecteur Sybase, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le [fournisseur de données pour Sybase iAnywhere.Data.SQLAnywhere](http://go.microsoft.com/fwlink/?linkid=324846) 16 ou version ultérieure sur l’ordinateur Runtime d’intégration.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Sybase.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Sybase :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **Sybase**. | Oui |
| server | Nom du serveur Sybase. |Oui |
| database | Nom de la base de données Sybase. |Oui |
| schema | Nom du schéma dans la base de données. |Non |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données Sybase.<br/>Les valeurs autorisées sont **De base** et **Windows**. |Oui |
| username | Spécifiez le nom d’utilisateur associé à la connexion à la base de données Sybase. |Oui |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ comme SecureString. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple :**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données Sybase.

Pour copier des données à partir de Sybase, affectez la valeur **RelationalTable** à la propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **RelationalTable** | Oui |
| TableName | Nom de la table dans la base de données Sybase. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "SybaseDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source Sybase.

### <a name="sybase-as-source"></a>Sybase en tant que source

Pour copier des données à partir de Sybase, définissez **RelationalSource** comme type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sybase"></a>Mappage de type de données pour Sybase

Lors de la copie des données à partir de Sybase, les mappages suivants sont utilisés entre les types de données Sybase et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma source et le type de données au récepteur, consultez [Mappages de types de données et de schémas](copy-activity-schema-and-type-mapping.md).

Sybase prend en charge les types T-SQL. Pour obtenir une table de mappage entre les types SQL et les types de données intermédiaires d’Azure Data Factory, consultez la section [Connecteur Azure SQL Database - Mappage de type de données](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).