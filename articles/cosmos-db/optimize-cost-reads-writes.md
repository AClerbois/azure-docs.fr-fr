---
title: Optimisation des coûts de lecture et d’écriture dans Azure Cosmos DB
description: Cet article explique comment réduire les coûts d’Azure Cosmos DB lors de l’exécution d’opérations de lecture et d’écriture sur les données.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 13ce5ee8b0e2a5d9cc84ea1a408ebba152b46050
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967406"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimisation des coûts de lecture et d’écriture dans Azure Cosmos DB

Cet article décrit le calcul des coûts de lecture et d’écriture de données à partir d’Azure Cosmos DB. Les opérations de lecture incluent les opérations Get sur les éléments, tandis que les opérations d’écriture incluent l’insertion, le remplacement, la suppression et l’upsert d’éléments.  

## <a name="cost-of-reads-and-writes"></a>Coût des lectures et des écritures

Azure Cosmos DB garantit des performances prévisibles en termes de débit et de latence grâce à un modèle de débit configuré. Le débit configuré est représenté en termes d’[unités de requête](request-units.md) par seconde, ou RU/s. Une unité de requête (RU) désigne une abstraction logique sur des ressources de calcul telles que l’UC, la mémoire, les E/S, etc., qui sont nécessaires pour effectuer une requête. Le débit configuré (RU) est réservé et dédié à votre conteneur ou à votre base de données pour fournir une latence et un débit prévisibles. Le débit configuré permet à Azure Cosmos DB de fournir des performances prévisibles et cohérentes, une faible latence garantie et une haute disponibilité à n’importe quelle échelle. Les unités de requête représentent la devise normalisée qui simplifie le raisonnement lié au nombre de ressources requises pour une application. 

Vous n’êtes pas obligé de faire attention à ce qui différencie les unités de requête des lectures et des écritures. Le modèle de devise unifiée des unités de requête permet d’utiliser indifféremment la même capacité de débit pour les lectures et les écritures. Le tableau suivant indique le coût des lectures et des écritures en termes de RU/s pour les éléments dont la taille est comprise entre 1 Ko et 100 Ko.

|**Taille de l’élément**  |**Coût d’une lecture** |**Coût d’une écriture**|
|---------|---------|---------|
|1 Ko |1 unité de requête |5 unités de requête |
|100 Ko |10 unités de requête |50 unités de requête |

La lecture d’un élément de 1 Ko coûte une RU. L’écriture d’un élément de 1 Ko coûte cinq RU. Les coûts de lecture et d’écriture sont applicables avec le [niveau de cohérence](consistency-levels.md) de la session par défaut.  Les considérations relatives aux RU incluent la taille de l’élément, le nombre de propriétés, la cohérence des données, les propriétés indexées, l’indexation et les modèles de requête.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Coût normalisé pour 1 million de lectures et d’écritures

La configuration de 1 000 RU/s se traduit par un total de 3,6 millions RU/h, à un coût de 0,08 dollar/heure (aux États-Unis et en Europe). Pour un élément de 1 Ko, vous pouvez effectuer 3,6 millions de lectures ou 0,72 million d’écritures (cette valeur est calculée comme suit : `3.6 million RU / 5`) par heure avec cette configuration de débit. Avec une normalisation à un million de lectures et d’écritures, le coût s’élève à 0,022 dollar pour 1 million de lectures (cette valeur est calculée comme suit : 0,08 dollar/3,6 millions) et 0,111 dollar pour 1 million d’écritures (cette valeur est calculée comme suit : 0,08 dollar/0,72 million).

## <a name="number-of-regions-and-the-request-units-cost"></a>Nombre de régions et coût des unités de requête

Le coût des écritures est constant, quel que soit le nombre de régions associées au compte Azure Cosmos. En d’autres termes, une écriture de 1 Ko coûte cinq RU, quel que soit le nombre de régions associées au compte. Une quantité non négligeable de ressources passe dans la réplication, l’acceptation et le traitement du trafic de réplication sur chaque région. Pour plus d’informations sur l’optimisation des coûts de plusieurs régions, consultez l’article [Optimizing the cost of multi-region Cosmos accounts](optimize-cost-regions.md) (Optimisation du coût des comptes Cosmos sur plusieurs régions).

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimiser le coût des lectures et des écritures

Lorsque vous effectuez des opérations d’écriture, vous devez configurer une capacité suffisante pour prendre en charge le nombre d’écritures requises par seconde. Vous pouvez augmenter le débit configuré à l’aide du Kit de développement logiciel (SDK), du portail et de la CLI avant d’effectuer les opérations d’écriture, puis réduire le débit une fois que les écritures sont terminées. Le débit relatif à la période d’écriture correspond au débit minimal nécessaire pour les données spécifiées, auquel s’ajoute le débit nécessaire pour la charge de travail d’insertion, en supposant qu’aucune autre charge de travail n’est en cours d’exécution. 

Si vous exécutez d’autres charges de travail simultanément, par exemple, une requête/une lecture/une mise à jour/une suppression, vous devez ajouter les unités de requête supplémentaires requises pour ces opérations. Si les opérations d’écriture ont un débit limité, vous pouvez personnaliser la stratégie de nouvelle tentative/backoff à l’aide de Kits de développement logiciel (SDK) Azure Cosmos DB. Par exemple, vous pouvez augmenter la charge jusqu’à ce qu’un petit nombre de requêtes présente un débit limité. En cas de limite du débit, l’application cliente doit interrompre les requêtes avec limitation du débit pour l’intervalle de nouvelle tentative spécifié. Chaque nouvelle tentative d’écriture doit être précédée d’un intervalle de temps minimal. La prise en charge de la stratégie de nouvelles tentatives est incluse dans les Kits de développement logiciel (SDK) SQL .NET, Java, Node.js et Python, ainsi que dans toutes les versions prises en charge des Kits de développement logiciel (SDK) .NET Core. 

Vous pouvez aussi insérer des données en bloc dans Azure Cosmos DB ou copier des données à partir de n’importe quel magasin de données source pris en charge vers Azure Cosmos DB à l’aide d’[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory s’intègre en natif dans l’API en bloc Azure Cosmos DB pour offrir les meilleures performances d’écriture de données.

## <a name="next-steps"></a>Étapes suivantes

Pour continuer à développer vos connaissances sur l’optimisation des coûts dans Azure Cosmos DB, consultez les articles suivants :

* En savoir plus sur l’[optimisation pour le développement et le test](optimize-dev-test.md)
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des requêtes](optimize-cost-queries.md)
* En savoir plus sur l’[optimisation du coût des comptes Azure Cosmos sur plusieurs régions](optimize-cost-regions.md)
