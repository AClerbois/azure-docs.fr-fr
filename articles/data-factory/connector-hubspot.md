---
title: Copie des données de HubSpot à l’aide d’Azure Data Factory (préversion) | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de HubSpot vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 066c80f7791bb9699494a09dec12f43dfb383ac6
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090199"
---
# <a name="copy-data-from-hubspot-using-azure-data-factory-preview"></a>Copier des données de HubSpot à l’aide d’Azure Data Factory (préversion)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis HubSpot. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires. Si vous souhaitez établir une dépendance sur les connecteurs en préversion dans votre solution, veuillez contacter le [support Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur HubSpot est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)


Vous pouvez copier les données depuis HubSpot vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur HubSpot.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HubSpot sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Hubspot** | OUI |
| clientId | L’ID client associé à votre application Hubspot.  | OUI |
| clientSecret | La clé secrète client associée à votre application Hubspot. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| accessToken | Jeton d’accès obtenu lors de l’authentification initiale de votre intégration OAuth. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| refreshToken | Jeton d’actualisation obtenu lors de l’authentification initiale de votre intégration OAuth. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non |

**Exemple :**

```json
{
    "name": "HubspotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données HubSpot.

Pour copier des données depuis HubSpot, définissez la propriété type du jeu de données sur **HubspotObject**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **HubspotObject** | OUI |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "HubspotDataset",
    "properties": {
        "type": "HubspotObject",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Hubspot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source HubSpot.

### <a name="hubspotsource-as-source"></a>HubspotSource en tant que source

Pour copier des données depuis HubSpot, définissez le type de source dans l’activité de copie sur **HubspotSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **HubspotSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Companies where Company_Id = xxx"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hubspot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
