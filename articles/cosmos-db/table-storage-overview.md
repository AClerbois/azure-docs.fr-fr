---
title: Vue d’ensemble de Stockage Table Azure
description: Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 37249d904343a4eddb0d1e82f451c3b9e95a479d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953504"
---
# <a name="azure-table-storage-overview"></a>Vue d’ensemble du stockage de table Azure

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Le Stockage Table Azure est un service qui stocke des données NoSQL structurées dans le cloud, en fournissant une conception sans schéma à un magasin de clés/attributs. Comme le stockage de tables est sans schéma, il est aisé d’adapter vos données en fonction des besoins de votre application. L’accès aux données du Stockage Table est rapide et économique pour de nombreux types d’applications, et généralement moins coûteux que le SQL traditionnel pour des volumes de données similaires.

Vous pouvez utiliser le Stockage Table pour stocker des jeux de données flexibles, comme des données utilisateur pour des applications Web, des carnets d’adresses, des informations sur les périphériques ou d’autres types de métadonnées requis par votre service. Vous pouvez stocker un nombre quelconque d'entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la limite de capacité du compte de stockage.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.

* [Bien démarrer avec l’API Table d’Azure Cosmos DB et le stockage Table Azure à l’aide du SDK .NET](table-storage-how-to-use-dotnet.md)

* Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table :

    * [Référence de la bibliothèque cliente de stockage pour .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Référence d’API REST](https://msdn.microsoft.com/library/azure/dd179355)
