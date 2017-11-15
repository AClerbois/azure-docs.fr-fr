---
title: "Copier des données à partir d’une source HTTP à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’une source HTTP dans le cloud ou locale vers des banques de données réceptrices prises en charge."
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
ms.date: 10/12/2017
ms.author: jingwang
ms.openlocfilehash: 54afc7d993058ac2b3d2990ba131d334e9332555
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Copier des données d’un point de terminaison HTTP à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-http-connector.md)
> * [Version 2 - Préversion](connector-http.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers un point de terminaison HTTP. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur HTTP dans V1](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une source HTTP vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur HTTP prend en charge :

- La récupération des données à partir du point de terminaison HTTP/HTTPS à l’aide de la méthode HTTP **GET** ou **POST**.
- La récupération de données à l’aide des authentifications suivantes : **Anonyme**, **De base**, **Digest**, **Windows** et **ClientCertificate**.
- La copie de la réponse HTTP en l'état, ou son analyse avec les [formats de fichier et codecs de compression prise en charge](supported-file-formats-and-compression-codecs.md).

Ce connecteur diffère du [connecteur Table web](connector-web-table.md) en ce que celui-ci est utilisé pour extraire le contenu de tables d’une page web HTML.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, voir le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md).

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur HTTP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HTTP sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **FtpServer**. | Oui |
| url | URL de base du serveur web | Oui |
| enableServerCertificateValidation | Spécifiez si vous souhaitez activer la validation du certificat SSL du serveur lors de la connexion au point de terminaison HTTP. | Non, la valeur par défaut est True. |
| authenticationType | Spécifie le type d’authentification. Les valeurs autorisées sont : **Anonymous** (Anonyme), **Basic** (De base), **Digest**, **Windows**, **ClientCertificate** (Certificat client). <br><br> Reportez-vous aux sections suivant ce tableau pour accéder à d’autres propriétés et à des exemples JSON sur ces types d’authentification. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilisation de l’authentification Basic (De base), Digest ou Windows

Définissez la valeur de la propriété « authenticationType » sur **De base**, **Digest** ou **Windows**, spécifiez les propriétés suivantes ainsi que les propriétés génériques décrites dans la section précédente :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| userName | Nom d’utilisateur pour accéder au point de terminaison HTTP. | Oui |
| password | Mot de passe de l’utilisateur (nom d’utilisateur). Marquez ce champ comme SecureString. | Oui |

**Exemple**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Utilisation de l’authentification ClientCertificate (Certificat client)

Pour utiliser l’authentification ClientCertificate, définissez la valeur de la propriété « authenticationType » sur **ClientCertificate**, et spécifiez les propriétés suivantes, ainsi que les propriétés génériques décrites dans la section précédente :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| embeddedCertData | Données du certificat encodé en Base64. | Spécifiez soit la propriété `embeddedCertData`, soit la propriété `certThumbprint`. |
| certThumbprint | Empreinte numérique du certificat installé sur le magasin de certificats de votre ordinateur exécutant le runtime d’intégration auto-hébergé. S’applique uniquement quand un type auto-hébergé du runtime d’intégration est spécifié dans connectVia. | Spécifiez soit la propriété `embeddedCertData`, soit la propriété `certThumbprint`. |
| password | Mot de passe associé au certificat. Marquez ce champ comme SecureString. | Non |

Si vous utilisez « certThumbprint » pour l’authentification et que le certificat est installé dans le magasin personnel de l’ordinateur local, vous devez accorder l’autorisation de lecture à l’ordinateur exécutant le runtime d’intégration auto-hébergé :

1. Lancez Microsoft Management Console (MMC). Ajouter le composant logiciel enfichable **Certificats**ciblant l’**ordinateur local**.
2. Développez **Certificats**, **Personnel**, puis cliquez sur **Certificats**.
3. Cliquez avec le bouton droit sur le certificat du magasin personnel, puis sélectionnez **Toutes les tâches** -> **Gérer les clés privées...**
3. Sous l’onglet **Sécurité**, ajoutez le compte d’utilisateur sous lequel le service hôte du runtime d’intégration (DIAHostService) s’exécute avec l’accès en lecture au certificat.

**Exemple 1 : utilisation de certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : utilisation d’embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données HTTP.

Pour copier des données de HTTP, définissez la propriété type du jeu de données sur **HttpFile**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **HttpFile** | Oui |
| relativeUrl | URL relative de la ressource qui contient les données. Quand cette propriété n’est pas spécifiée, seule l’URL spécifiée dans la définition du service lié est utilisée. | Non |
| requestMethod | Méthode HTTP.<br/>Valeurs autorisées : **GET** (par défaut) ou **POST**. | Non |
| additionalHeaders | En-têtes de requête HTTP supplémentaires. | Non |
| RequestBody | Corps de la requête HTTP. | Non |
| format | Si vous souhaitez **récupérer des données du point de terminaison HTTP en l'état** sans les analyser, et les copier dans un magasin basé sur un fichier, ignorez la section format dans les définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser le contenu de la réponse HTTP pendant la copie, les types de formats de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Texte](supported-file-formats-and-compression-codecs.md#text-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non |

**Exemple 1 : utilisation de la méthode Get (par défaut)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exemple 2 : en utilisant la méthode Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source HTTP.

### <a name="http-as-source"></a>HTTP en tant que source

Pour copier des données de HTTP, définissez le type de source dans l’activité de copie sur **HttpSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur **HttpSource** | Oui |
| httpRequestTimeout | Délai d’expiration (TimeSpan) pour l’obtention d’une réponse par la requête HTTP. Il s’agit du délai d’expiration pour l’obtention d’une réponse, et non du délai d’expiration pour la lecture des données de la réponse.<br/> Valeur par défaut : 00:01:40  | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).