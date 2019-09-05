---
title: Copier des données à partir de sources Microsoft Access à l’aide de Azure Data Factory | Microsoft Docs
description: Découvrez comment copier des données à partir de sources Microsoft Access vers des banques de données réceptrices prises en charge à l’aide d’une activité de copie dans un pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: jingwang
ms.openlocfilehash: 796d2a4730f5de8d2f294fb2b30a0616b81f3e59
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276239"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Copier des données depuis/vers des banques de données Microsoft Access à l’aide de Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’un magasin de données Microsoft Access. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de la source Microsoft Access vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur Microsoft Access, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installez le pilote ODBC Microsoft Access pour le magasin de données sur la machine exécutant le runtime d’intégration.

>[!NOTE]
>La version 2016 du pilote ODBC de Microsoft Access ne fonctionne pas avec ce connecteur. Utilisez plutôt la version de pilote 2013 ou 2010.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir les entités Data Factory spécifiques au connecteur Microsoft Access.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Microsoft Access sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **MicrosoftAccess** | OUI |
| connectionString | Chaîne de connexion ODBC excluant la partie informations d’identification. Vous pouvez spécifier la chaîne de connexion ou utiliser le système DSN (nom de la source de données) que vous avez configuré sur la machine exécutant le runtime d’intégration (vous devez toujours spécifier la partie informations d’identification dans le service lié en conséquence).<br>Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md).| OUI |
| authenticationType | Type d’authentification utilisé pour se connecter au magasin de données Microsoft Access.<br/>Les valeurs autorisées sont les suivantes : **De base** et **Anonyme**. | OUI |
| userName | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. | Non |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non |
| credential | Partie de la chaîne de connexion contenant les informations d’accès, spécifiée dans un format de valeurs de propriété spécifique au pilote. Marquez ce champ comme SecureString. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple :**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Microsoft Access.

Pour copier des données à partir de Microsoft Access, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **MicrosoftAccessTable** | OUI |
| tableName | Nom de la table dans Microsoft Access. | Non pour la source (si « query » est spécifié dans la source de l’activité) ;<br/>Oui pour le récepteur |

**Exemple**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Microsoft Access.

### <a name="microsoft-access-as-source"></a>Microsoft Access en tant que source

Pour copier des données à partir d’une banque de données compatible avec Microsoft Access, les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **MicrosoftAccessSource** | OUI |
| query | Utilise la requête personnalisée pour lire des données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
