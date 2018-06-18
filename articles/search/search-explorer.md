---
title: Interroger un index (portail - Recherche Azure) | Microsoft Docs
description: Émettez une requête de recherche dans l’Explorateur de recherche du portail Azure.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: heidist
ms.openlocfilehash: a3592bd0c304dfb78374eeba432c0d28203980c9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790506"
---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Interrogation d’un index Recherche Azure à l’aide de l’Explorateur de recherche dans le Portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-query-overview.md)
> * [Portail](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Cet article vous explique comment interroger un index Recherche Azure à l’aide de l’**Explorateur de recherche** dans le Portail Azure. Vous pouvez utiliser l’Explorateur de recherche pour envoyer des chaînes de requête Lucene simples ou complètes à un index existant dans votre service.

## <a name="open-the-service-dashboard"></a>Ouverture du tableau de bord des services
1. Cliquez sur **Toutes les ressources** dans la barre de raccourcis de gauche du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Sélectionnez votre service Recherche Azure.

## <a name="select-an-index"></a>Sélection d’un index

Sélectionnez l’index dans lequel vous souhaitez effectuer la recherche à partir de la mosaïque **Index**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Ouverture de l’Explorateur de recherche

Cliquez sur la vignette de l’Explorateur de recherche pour ouvrir par glissement la barre de recherche et le volet des résultats.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Commencez la recherche

Lorsque vous utilisez l’Explorateur de recherche, vous pouvez spécifier les [paramètres de requête](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) pour formuler la requête.

1. Saisissez une requête dans **Chaîne de requête** puis appuyez sur **Rechercher**. 

   La chaîne de requête est automatiquement analysée dans l’URL de requête appropriée pour soumettre une requête HTTP à l’API REST de Recherche Azure.   
   
   Vous pouvez utiliser n’importe quelle syntaxe de requête Lucene simple ou complète valide pour créer la requête. Le caractère `*` équivaut à une recherche vide ou non spécifiée qui retourne tous les documents dans aucun ordre particulier.

2. Dans **Résultats**, les résultats de la requête sont présentés au format JSON brut, identiques à la charge utile retournée dans un corps de réponse HTTP lors de l’émission de requêtes par programmation.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes fournissent des exemples et des informations supplémentaires concernant la syntaxe de requête.

 + [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemples de syntaxe de requête Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Syntaxe d’expression de filtre OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 