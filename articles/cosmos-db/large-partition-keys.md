---
title: Créer des conteneurs Azure Cosmos avec une grande clé de partition à l'aide du portail Azure et de différents kits de développement logiciel (SDK).
description: Découvrez comment créer un conteneur dans Azure Cosmos DB avec une grande clé de partition à l’aide du portail Azure et de différents kits de développement logiciel (SDK).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: e2ea934140322a13f07a90f4246bacd3f9dbe6c9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721092"
---
# <a name="create-containers-with-large-partition-key"></a>Créer des conteneurs avec une grande clé de partition

Azure Cosmos DB utilise le partitionnement basé sur le hachage pour mettre à l'échelle horizontale les données. Tous les conteneurs Azure Cosmos créés avant le 3 mai 2019 utilisent une fonction de hachage calculant le hachage en fonction des 100 premiers octets de la clé de partition. Si les 100 premiers octets de plusieurs clés de partition sont identiques, ces partitions logiques sont considérées comme identiques par le service. Cela peut entraîner des problèmes tels qu'un quota erroné ou l'application d'index uniques aux clés de partition. Les grandes clés de partition permettent d'y remédier. Azure Cosmos DB prend désormais en charge les grandes clés de partition avec des valeurs jusqu'à 2 Ko.

Les grandes clés de partition sont prises en charge moyennant une version améliorée de la fonction de hachage, qui peut générer un hachage unique à partir de grande clés de partition jusqu'à 2 Ko. Cette version de hachage est également recommandée pour les scénarios avec cardinalité des clés de partition élevée, indépendamment de la taille de la clé de partition. Une cardinalité de clé de partition correspond au nombre de partitions logiques uniques (de l'ordre d'environ 30 000, par exemple) d'un conteneur. Cet article explique comment créer un conteneur avec une grande clé de partition à l'aide du portail Azure et des kits de développement logiciel (SDK).

## <a name="create-a-large-partition-key-azure-portal"></a>Créer une grande clé de partition (portail Azure)

Pour créer une grande clé de partition, quand vous créez un conteneur à l’aide du portail Azure, activez l’option **Ma clé de partition est supérieure à 100 octets**. Désactivez la case à cocher si vous n’avez pas besoin de grandes clés de partition ou si vos applications s’exécutent sur des kits de développement logiciel (SDK) antérieurs à la version 1.18.

![Créer de grandes clés de partition à l’aide du portail Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Créer une grande clé de partition (PowerShell)

Pour créer un conteneur avec prise en charge d'une grande clé de partition, consultez

* [Créer un conteneur Azure Cosmos avec une grande taille de clé de partition](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Créer une grande clé de partition (kit de développement logiciel (SDK) .Net)

Pour créer un conteneur avec une grande clé de partition à l’aide du kit de développement logiciel (SDK) .NET, vous devez spécifier la propriété `PartitionKeyDefinitionVersion.V2`. L’exemple suivant montre comment spécifier la propriété Version de l’objet PartitionKeyDefinition et la définir sur PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>Kit SDK .NET v3

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>Kit SDK .NET v2

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Versions prises en charge du kit de développement logiciel (SDK)

Les grandes clés de partition sont prises en charge avec les versions minimales suivantes des kits de développement logiciel (SDK) :

|Type de kit de développement logiciel (SDK)  | Version minimum   |
|---------|---------|
|.Net     |    1.18     |
|Java sync     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | version ultérieure à `2017-05-03` à l’aide de l'en-tête de requête `x-ms-version`.|
| Modèle Resource Manager | version 2 à l’aide de la propriété `"version":2` dans l’objet `partitionKey`. |

Actuellement, vous ne pouvez pas utiliser de conteneurs avec de grandes clés de partition dans Power BI et Azure Logic Apps. Seuls les conteneurs sans grande clé de partition sont possibles pour ces applications.

## <a name="next-steps"></a>Étapes suivantes

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Utiliser un compte Azure Cosmos](account-overview.md)
