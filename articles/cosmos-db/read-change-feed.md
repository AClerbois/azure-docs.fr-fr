---
title: Accès au flux de modifications dans Azure Cosmos DB
description: Cet article décrit les différentes options disponibles pour accéder au flux de modification et le lire dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242037"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lire le flux de modification Azure Cosmos DB

Vous pouvez accéder au flux de modification Azure Cosmos DB à l'aide d'une des options suivantes :

* Utilisation d’Azure Functions
* Utilisation de la bibliothèque du processeur de flux de modification
* Utilisation du Kit de développement logiciel (SDK) de l'API SQL Azure Cosmos DB

## <a name="using-azure-functions"></a>Utilisation d’Azure Functions

Azure Functions est l'option la plus simple. Il s'agit aussi de l'option recommandée. Lorsque vous créez un déclencheur Azure Cosmos DB dans une application Azure Functions, vous pouvez sélectionner le conteneur auquel vous souhaitez vous connecter, et la fonction Azure se déclenche chaque fois qu'une modification est apportée au conteneur. Les déclencheurs peuvent être créés via le portail Azure Functions, via le portail Azure Cosmos DB ou par programmation avec des Kits de développement logiciel (SDK). Visual Studio et VS Code fournissent un accompagnement pour l'écriture des fonctions Azure. Vous pouvez même utiliser l'interface CLI d'Azure Functions pour le développement multiplateforme. Vous pouvez écrire et déboguer le code sur votre bureau, puis déployer la fonction avec un seul clic. Pour en savoir plus, consultez les articles [Traitement de base de données serverless à l'aide d'Azure Functions](serverless-computing-database.md) et [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Utilisation de la bibliothèque du processeur de flux de modification

La bibliothèque du processeur de flux de modification masque la complexité tout en vous offrant un contrôle total sur le flux de modification. La bibliothèque suit le modèle Observateur, dans lequel la bibliothèque appelle votre fonction de traitement. Si vous disposez d’un flux de modification à débit élevé, vous pouvez instancier plusieurs clients pour lire le flux de modification. Comme vous utilisez la bibliothèque du processeur de flux de modification, la charge est automatiquement répartie entre les différents clients sans que vous ayez à implémenter cette logique. La bibliothèque gère toute la complexité. Si vous souhaitez utiliser votre propre équilibreur de charge, vous pouvez implémenter `IPartitionLoadBalancingStrategy` afin de mettre en place une stratégie de partition personnalisée pour traiter le flux de modification. Pour en savoir plus, consultez [Utilisation de la bibliothèque du processeur de flux de modification](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Utilisation du Kit de développement logiciel (SDK) de l'API SQL Azure Cosmos DB

Le Kit de développement logiciel (SDK) vous offre un contrôle de niveau inférieur sur le flux de modification. Vous pouvez gérer le point de contrôle, accéder à une clé de partition logique spécifique, etc. Si vous disposez de plusieurs lecteurs, vous pouvez utiliser `ChangeFeedOptions` pour répartir la charge de lecture sur plusieurs threads ou clients. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
* [Utilisation de la bibliothèque du processeur de flux de modification](change-feed-processor.md)
