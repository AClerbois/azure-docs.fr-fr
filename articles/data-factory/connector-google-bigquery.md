---
title: Copier des données à partir de Google BigQuery avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline de fabrique de données pour copier des données de Google BigQuery vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c9320c8d0cf512bc9145accc07ab4c79630a7c84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808889"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copier des données à partir de Google BigQuery avec Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Google BigQuery. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis Google BigQuery vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Data Factory fournit un pilote intégré pour permettre la connectivité. Vous n’avez donc pas besoin d’installer manuellement un pilote pour utiliser ce connecteur.

>[!NOTE]
>Ce connecteur Google BigQuery repose sur les API BigQuery. N’oubliez pas que BigQuery limite le taux maximal de requêtes entrantes et applique des quotas appropriés sur une base par projet. Reportez-vous à [Quotas et limites - requêtes d’API](https://cloud.google.com/bigquery/quotas#api_requests). Assurez-vous que vous ne déclenchez pas trop de demandes simultanées pour le compte.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google BigQuery.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google BigQuery sont les suivantes.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **GoogleBigQuery**. | Oui |
| project | L’ID du projet BigQuery par défaut sur lequel exécuter la requête.  | Oui |
| additionalProjects | Liste séparée par des virgules des ID de projets BigQuery publics accessibles.  | Non  |
| requestGoogleDriveScope | Pour demander l’accès à Google Drive. Autoriser l’accès à Google Drive active la prise en charge des tables fédérées qui combinent les données BigQuery avec les données issues de Google Drive. La valeur par défaut est **false**.  | Non  |
| authenticationType | Mécanisme d’authentification OAuth 2.0 utilisé pour l’authentification. ServiceAuthentication ne peut être utilisé que sur un runtime d’intégration auto-hébergé. <br/>Les valeurs autorisées sont **UserAuthentication** et **ServiceAuthentication**. Reportez-vous aux sections suivant ce tableau pour accéder à d’autres propriétés et à des exemples JSON sur ces types d’authentification. | Oui |

### <a name="using-user-authentication"></a>Utiliser l’authentification utilisateur

Définissez la valeur de la propriété « authenticationType » sur **UserAuthentication** et spécifiez les propriétés suivantes ainsi que les propriétés génériques décrites dans la section précédente :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| clientId | ID de l’application utilisée pour générer le jeton d’actualisation. | Non  |
| clientSecret | Secret de l’application utilisée pour générer le jeton d’actualisation. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| refreshToken | Le jeton d’actualisation obtenu de Google servant à autoriser l’accès à BigQuery. Découvrez comment en obtenir un en consultant [Obtention de jetons d’accès OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) et [ce blog de communauté](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |

**Exemple :**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Utiliser l’authentification du service

Définissez la valeur de la propriété « authenticationType » sur **ServiceAuthentication** et spécifiez les propriétés suivantes ainsi que les propriétés génériques décrites dans la section précédente. Ce type d’authentification ne peut être utilisé que sur un runtime d’intégration auto-hébergé.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| email | ID d’e-mail du compte de service utilisé pour ServiceAuthentication. Il ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non  |
| keyFilePath | Chemin complet du fichier de clé .p12 utilisé pour authentifier l’adresse e-mail du compte de service. | Non  |
| trustedCertPath | Chemin complet du fichier .pem qui contient les certificats d’autorité de certification approuvés utilisés pour vérifier le serveur quand vous vous connectez via SSL. Cette propriété ne peut être définie que quand vous utilisez SSL sur le runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non  |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier .pem spécifié. La valeur par défaut est **false**.  | Non  |

**Exemple :**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Google BigQuery.

Pour copier des données à partir de Google BigQuery, définissez la propriété type du jeu de données sur **GoogleBigQueryObject**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **GoogleBigQueryObject** | Oui |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par le type de source Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource en tant que type de source

Pour copier des données à partir de Google BigQuery, définissez le type de source sur **GoogleBigQuerySource** dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **GoogleBigQuerySource**. | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
