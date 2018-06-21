---
title: Clés uniques dans Azure Cosmos DB | Microsoft Docs
description: Découvrez comment utiliser des clés uniques dans votre base de données Azure Cosmos DB.
services: cosmos-db
keywords: contrainte de clé unique, violation de contrainte de clé unique
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: d12109efbb157b1e0c15b1a4c0d005fa98c44858
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261098"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Clés uniques dans Azure Cosmos DB

Les clés uniques permettent aux développeurs d’ajouter une couche d’intégrité des données à leur base de données. En créant une stratégie de clé unique durant la création d’un conteneur, vous garantissez l’unicité d’une ou de plusieurs valeurs par [clé de partition](partition-data.md). Une fois qu’un conteneur a été créé avec une stratégie de clé unique, il empêche la création de tout nouvel élément ou de tout élément mis à jour avec des valeurs dupliquant des valeurs spécifiées par la contrainte de clé unique.   

> [!NOTE]
> Les clés uniques sont prises en charge par les dernières versions des SDK SQL [.NET](sql-api-sdk-dotnet.md) et [.NET Core](sql-api-sdk-dotnet-core.md) et de [l’API MongoDB](mongodb-feature-support.md#unique-indexes). L’API Table et l’API Graph ne prennent pas en charge les clés uniques à l’heure actuelle. 
> 
>

## <a name="use-case"></a>Cas d’utilisation

Examinons, par exemple, comment une base de données utilisateur associée à une [application sociale](use-cases.md#web-and-mobile-applications) pourrait tirer avantage d’une stratégie de clé unique appliquée aux adresses e-mail. En convertissant l’adresse e-mail de l’utilisateur en clé unique, vous êtes sûr que chaque enregistrement possède une adresse e-mail unique et qu’aucun nouvel enregistrement ne peut être créé avec des adresses e-mail dupliquées. 

Si vous souhaitez que les utilisateurs soient autorisés à créer plusieurs enregistrements avec la même adresse e-mail, mais pas avec les mêmes prénom, nom et adresse e-mail, vous pouvez ajouter d’autres chemins d’accès à la stratégie de clé unique. Par conséquent, au lieu de créer une clé unique basée sur une adresse e-mail, vous pouvez créer une clé unique combinant à la fois le prénom, le nom et l’adresse e-mail. Dans ce cas, chaque combinaison unique des trois chemins d’accès est autorisée, ce qui permet à la base de données de contenir des éléments ayant les valeurs de chemin d’accès suivantes. Chacun de ces enregistrements passerait la stratégie de clé unique.  

**Valeurs de clé unique autorisées pour firstName (prénom), lastName (nom) et email**

|Prénom|Nom|Adresse de messagerie|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Si vous tentez d’ajouter un autre enregistrement avec l’une des combinaisons répertoriées dans le tableau ci-dessus, vous recevrez une erreur indiquant que la contrainte de clé unique n’est pas respectée. L’erreur Azure Cosmos DB retournée est de type « La ressource avec l’ID ou le nom spécifié existe déjà. » ou « La ressource avec l’ID, le nom ou l’index unique spécifié existe déjà. » 

## <a name="using-unique-keys"></a>Utilisation de clés uniques

Des clés uniques doivent être définies durant la création du conteneur, et la clé unique est incluse dans la clé de partition. Pour reprendre l’exemple précédent, si vous partitionnez en fonction du code postal, les enregistrements de la table pourraient se trouver dupliqués dans chaque partition.

Vous ne pouvez pas mettre à jour les conteneurs existants pour utiliser des clés uniques.

Une fois un conteneur créé avec une stratégie de clé unique, la stratégie ne peut pas être modifiée, sauf si vous recréez le conteneur. Si vous disposez de données existantes sur lesquelles implémenter des clés uniques, créez le conteneur, puis utilisez l’outil de migration de données approprié pour déplacer les données vers le nouveau conteneur. Pour les conteneurs SQL, utilisez [l’outil de migration de données](import-data.md). Pour les conteneurs MongoDB, utilisez [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md).

Un maximum de 16 valeurs de chemin d’accès (par exemple /firstName, /lastName, /address/zipCode, etc.) peuvent être incluses dans chaque clé unique. 

Chaque stratégie de clé unique peut inclure jusqu’à 10 contraintes ou combinaisons de clé unique, et les chemins d’accès combinés de toutes les propriétés d’index unique ne doivent pas dépasser 60 caractères. Ainsi, l’exemple précédent qui utilise le prénom, le nom et l’adresse e-mail représente une seule contrainte et utilise trois des 16 chemins d’accès disponibles. 

Les frais unitaires de demande de création, de mise à jour et de suppression d’un élément sont légèrement supérieurs quand il existe une stratégie de clé unique appliquée au conteneur. 

Les clés uniques partiellement allouées ne sont pas prises en charge. Si les valeurs de certains chemins d’accès uniques sont manquantes, ces dernières sont traitées comme une valeur null spéciale, qui fait partie intégrante de la contrainte d’unicité.

## <a name="sql-api-sample"></a>Exemple d’API SQL

L’exemple de code suivant montre comment créer un conteneur SQL avec deux contraintes de clé unique. La première contrainte est la contrainte firstName, lastName, email décrite dans l’exemple précédent. La deuxième contrainte est la contrainte address/zipCode des utilisateurs. Un exemple de fichier JSON utilisant les chemins d’accès de cette stratégie de clé unique est fourni après l’exemple de code. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Exemple de document JSON.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Veuillez noter que le nom de clé unique respecte la casse. Comme indiqué dans à l’exemple ci-dessus, le nom unique est défini pour /address/zipcode. Si vos données auront une valeur ZipCode, alors « null » est inséré dans la clé unique, car zipcode ne correspond pas à ZipCode. Et en raison de ce respect de la casse, tous les autres enregistrements contenant ZipCode ne pourront pas être insérés, car une valeur « null » dupliquée viole la contrainte de clé unique.

## <a name="mongodb-api-sample"></a>Exemple d’API MongoDB

L’exemple de commande suivant montre comment créer un index unique sur les champs firstName, lastName et email de la collection d’utilisateurs pour l’API MongoDB. Cela garantit l’unicité de la combinaison des trois champs dans tous les documents de la collection. Pour les collections de l’API MongoDB, l’index unique est créé après la création de la collection, mais avant le remplissage de cette dernière.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Configurer les clés uniques à l’aide du portail Azure

Dans les sections ci-dessus, vous trouverez des exemples de code illustrant comment vous pouvez définir des contraintes de clé unique lors de la création d’une collection à l’aide de l’API SQL ou de l’API MongoDB. Mais il est également possible de définir des clés uniques lorsque vous créez une collection via l’interface utilisateur web dans le portail Azure. 

- Accédez à **l’Explorateur de données** dans votre compte Cosmos DB
- Cliquez sur **Nouvelle collection**
- Dans la section Clés uniques, ** vous pouvez ajouter les contraintes de clé unique souhaitées en cliquant sur **Ajouter une clé unique**

![Définir des clés uniques dans l’Explorateur de données](./media/unique-keys/unique-keys-azure-portal.png)

- Si vous souhaitez créer une contrainte de clé unique sur le chemin d’accès lastName, ajoutez `/lastName`.
- Si vous souhaitez créer une contrainte de clé unique pour la combinaison lastName firstName, ajoutez `/lastName,/firstName`.

Lorsque vous avez terminé, cliquez sur **OK** pour créer la collection.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des clés uniques pour les éléments d’une base de données. Si vous créez un conteneur pour la première fois, consultez [Partitionnement de données Azure Cosmos DB](partition-data.md), car les clés uniques et les clés de partition dépendent les unes des autres. 


