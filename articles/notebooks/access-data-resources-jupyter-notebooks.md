---
title: Accéder aux ressources de données à partir des notebooks Jupyter sur Azure
description: Comment accéder aux fichiers, API REST, bases de données et différentes ressources de stockage Azure à partir d’un notebook Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 17f7e7acc70047119b882a5bb6e60bfdf06f79c0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775456"
---
# <a name="access-cloud-data-in-a-notebook"></a>Accéder aux données de cloud dans un notebook

Pour que votre travail dans un notebook Jupyter soit intéressant, vous devez avoir des données. Les données sont en effet l’élément moteur des notebooks.

Vous pouvez certainement [importer des fichiers de données dans un projet](work-with-project-data-files.md), même à l’aide des commandes telles que `curl` à partir d’un notebook pour télécharger un fichier directement. Toutefois, vous devrez probablement utiliser des données beaucoup plus complètes disponibles dans des sources autres que des fichiers, telles que des API REST, des bases de données relationnelles et le stockage cloud comme les tables Azure.

Cet article décrit brièvement ces différentes options. Étant donné que l’accès aux données est mieux compris en pratique, vous pouvez trouver le code exécutable dans [Exemples Azure Notebooks - Accéder à vos données](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>API REST

En règle générale, la grande quantité de données disponibles sur Internet n’est pas accessible via des fichiers, mais via des API REST. Heureusement, une cellule de notebook pouvant contenir tout le code que vous souhaitez, vous pouvez utiliser du code pour envoyer des demandes et recevoir des données JSON. Vous pouvez ensuite convertir ces données JSON au format que vous voulez utiliser, comme un dataframe Pandas.

Pour accéder aux données à l’aide d’une API REST, utilisez le même code des cellules de code d’un notebook que celui utilisé dans n’importe quel autre application. La structure générale avec la bibliothèque requests se présente comme suit :

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Bases de données SQL Azure

Vous pouvez accéder aux bases de données SQL Server à l’aide des bibliothèques pyodbc ou pymssql.

La page [Utilisation de Python pour interroger une base de données SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) vous fournit des instructions sur la création d’une base de données contenant des données AdventureWorks et montre comment interroger ces données. Le même code est illustré dans l’exemple de notebook pour cet article.

## <a name="azure-storage"></a>Stockage Azure

Le stockage Azure fournit plusieurs types différents de stockage non relationnel, selon le type de données dont vous disposez et la façon dont vous avez besoin d’y accéder :

- Stockage Table : fournit un stockage économique et de volumes élevés pour les données tabulaires, telles que les journaux de capteur collectés, les journaux de diagnostic et ainsi de suite.
- Stockage Blob : fournit un stockage de type fichier pour n’importe quel type de données.

L’exemple de notebook illustre l’utilisation des tables et objets blob, notamment comment utiliser une signature d’accès partagé pour autoriser l’accès en lecture seule aux objets blob.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB fournit un magasin NoSQL entièrement indexé pour les documents JSON. Les articles suivants proposent différentes façons d’utiliser Cosmos DB à partir de Python :

- [Générer une application API SQL avec Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Générer une application Flask avec Azure Cosmos DB pour l’API MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Créer une base de données de graphe avec Python et l’API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Créer une application Cassandra avec Python et Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Créer une application API Table avec Python et Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Quand vous travaillez avec Cosmos DB, vous pouvez utiliser la bibliothèque [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/).

## <a name="other-azure-databases"></a>Autres bases de données Azure

Azure fournit plusieurs autres types de bases de données que vous pouvez utiliser. Les articles ci-dessous fournissent des conseils pour accéder à ces bases de données à partir de Python :

- [Azure Database pour PostgreSQL : Utiliser Python pour se connecter et interroger des données](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Démarrage rapide : Utiliser le cache Azure pour Redis avec Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database pour MySQL : Utiliser Python pour se connecter et interroger des données](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Assistant de copie pour Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser des fichiers de données de projets](work-with-project-data-files.md)
