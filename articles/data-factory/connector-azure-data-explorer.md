---
title: Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory
description: Découvrez comment copier des données vers ou depuis Azure Data Explorer à l’aide d’une activité de copie dans un pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010268"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers ou depuis [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Data Explorer est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données à partir de tout magasin de données source pris en charge vers Azure Data Explorer. Vous pouvez également copier des données à partir d’Azure Data Explorer vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md).

>[!NOTE]
>La copie de données vers/depuis Azure Data Explorer depuis/vers un magasin de données local à l’aide d’un runtime d’intégration auto-hébergé est prise en charge depuis la version 3.14.

Le connecteur Azure Data Explorer vous permet d’effectuer les opérations suivantes :

* Copier des données en utilisant une authentification de jeton d’application Azure Active Directory (Azure AD) avec un **principal du service**.
* En tant que source, récupérer des données à l’aide d’une requête KQL (Kusto).
* En tant que récepteur, ajouter des données à une table de destination.

## <a name="getting-started"></a>Prise en main

>[!TIP]
>Pour obtenir une description de l’utilisation du connecteur d’Azure Data Explorer, consultez [Copier des données vers/à partir d’Azure Data Explorer à l’aide d’Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur d’Azure Data Explorer utilise l’authentification du principal du service. Suivez ces étapes pour obtenir un principal de service et accorder des autorisations :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation nécessaire au principal de service dans Azure Data Explorer. Consultez l’article [Gérer les autorisations de base de données d’Azure Data Explorer](../data-explorer/manage-database-permissions.md), qui offre des informations détaillées sur les rôles et les autorisations et présente la procédure pas à pas de gestion des autorisations. En règle générale, vous devez :

    - **En tant que source**, accorder au moins le rôle **Observateur de base de données** à votre base de données.
    - **En tant que récepteur**, accorder au moins le rôle **Ingéreur de base de données** à votre base de données.

>[!NOTE]
>Lorsque vous utilisez l’interface utilisateur ADF pour créer, votre compte d’utilisateur de connexion est utilisé pour répertorier les clusters, bases de données et tables Azure Data Explorer. Entrez manuellement le nom si vous n’avez pas d’autorisation pour une telle opération.

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Explorer :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureDataExplorer**. | OUI |
| endpoint | URL de point de terminaison du cluster Azure Data Explorer, avec le format `https://<clusterName>.<regionName>.kusto.windows.net`. | OUI |
| database | Nom de base de données. | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. C’est ce que vous connaissez normalement sous le nom « **ID d’autorité** » dans la [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Récupérez-les en pointant la souris dans l’angle supérieur droit du portail Azure. | OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. C’est ce que vous connaissez normalement sous le nom « **ID d’application cliente AAD** » dans la [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. C’est ce que vous connaissez normalement sous le nom « **Clé d’application AAD** » dans la [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |

**Exemple de Propriétés du service lié :**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Data Explorer.

Pour copier des données vers Azure Data Explorer, définissez la propriété type du jeu de données sur **AzureDataExplorerTable**.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureDataExplorerTable**. | OUI |
| table | Nom de la table dans la base à laquelle le service lié fait référence. | Oui pour le récepteur, Non pour la source |

**Exemple de propriétés du jeu de données**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Explorer en tant que source et récepteur.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer en tant que source

Pour copier des données à partir d’Azure Data Explorer, définissez la propriété **type** dans la source d’activité de copie sur **AzureDataExplorerSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur : **AzureDataExplorerSource** | OUI |
| query | Requête en lecture seule au [format KQL](/azure/kusto/query/). Utilisez la requête KQL personnalisée en tant que référence. | OUI |
| queryTimeout | Temps d’attente avant l’expiration de la demande de requête. La valeur par défaut est 10 minutes (00:10:00) et la valeur maximale autorisée 1 heure (01:00:00). | Non |
| noTruncation | Indique s’il faut tronquer le jeu de résultats retourné. Par défaut, le résultat est tronqué après 500 000 enregistrements ou au-delà de 64 Mo. La troncation est vivement recommandée pour garantir le bon comportement de l’activité. |Non |

>[!NOTE]
>La source par défaut d’Azure Data Explorer a une taille limite de 500 000 enregistrements ou 64 Mo. Pour récupérer tous les enregistrements sans troncation, vous pouvez spécifier `set notruncation;` au début de votre requête. Consultez [limites de requête](https://docs.microsoft.com/azure/kusto/concepts/querylimits) pour plus de détails.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer en tant que récepteur

Pour copier des données vers Azure Data Explorer, définissez la propriété type dans le récepteur d’activité de copie sur **AzureDataExplorerSink**. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du récepteur d’activité de copie doit être définie sur : **AzureDataExplorerSink** | OUI |
| ingestionMappingName | Nom d’un **[mappage](/azure/kusto/management/mappings#csv-mapping)** créé au préalable sur une table Kusto. Pour mapper les colonnes de la source et Azure Data Explorer, ce qui s’applique à **[tous les magasins/formats de source pris en charge](copy-activity-overview.md#supported-data-stores-and-formats)** , notamment les formats CSV/JSON/Avro etc., vous pouvez utiliser l’activité de copie [mappage des colonnes](copy-activity-schema-and-type-mapping.md) (implicitement par nom ou explicitement configurée) et/ou les mappages d’Azure Data Explorer. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

* Pour plus d’informations, consultez [Copier des données d’Azure Data Factory vers Azure Data Explorer](/azure/data-explorer/data-factory-load-data).