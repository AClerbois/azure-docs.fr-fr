---
title: Ajuster la capacité des charges de travail de requête et d’index
titleSuffix: Azure Cognitive Search
description: Ajustez les ressources informatiques des partitions et des réplicas dans Recherche cognitive Azure, où chaque ressource est facturée en unités de recherche facturables.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 476af7dd40cd1f31d03f3bd80affac0ce10ef900
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927202"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Ajuster la capacité dans la Recherche cognitive Azure

Avant de [provisionner un service de recherche](search-create-service-portal.md) et de choisir un niveau tarifaire particulier, prenez quelques minutes pour bien comprendre le rôle des réplicas et des partitions dans un service, et la manière dont vous pouvez ajuster la capacité d’un service en fonction des variations de la demande de ressources.

La capacité est une fonction du [niveau que vous choisissez](search-sku-tier.md) (les niveaux déterminent les caractéristiques matérielles), et de la combinaison de réplicas et de partitions nécessaires pour les charges de travail projetées. Selon le niveau et la taille de l’ajustement, le processus d’ajout ou de réduction de capacité peut prendre de 15 minutes à plusieurs heures. 

Si vous modifiez l’allocation des réplicas et des partitions, nous vous recommandons d’utiliser le portail Azure. Le portail applique des limites aux combinaisons autorisées inférieures aux limites maximales d’un niveau. Toutefois, si vous souhaitez suivre une approche de provisionnement basée sur un script ou du code, [Azure PowerShell](search-manage-powershell.md) et l’[API REST Gestion](/rest/api/searchmanagement/services) constituent des solutions alternatives.

## <a name="terminology-replicas-and-partitions"></a>Terminologie : réplicas et partitions

|||
|-|-|
|*Partitions* | Fournissent un stockage des index et des E/S pour les opérations de lecture-écriture (par exemple, lors de la reconstruction ou de l’actualisation d’un index). Chaque partition se partage l’index total. Si vous allouez trois partitions, l’index est divisé en trois. |
|*Réplicas* | Instances du service de recherche, principalement utilisées pour équilibrer la charge des opérations de requête. Chaque réplica est une copie d’un index. Si vous allouez trois réplicas, vous avez trois copies d’un index disponibles pour traiter les demandes de requête.|

## <a name="when-to-add-nodes"></a>Quand ajouter des nœuds

Au départ, un service se voit allouer un niveau minimal de ressources consistant en une partition et un réplica. 

Un seul service doit avoir suffisamment de ressources pour gérer toutes les charges de travail (indexation et requêtes). Aucune charge de travail ne s’exécute en arrière-plan. Vous pouvez planifier l’indexation à des moments où les demandes de requête sont naturellement moins fréquentes, mais à part cela, le service n’établit pas de priorité entre les tâches. De plus, un certain degré de redondance lisse les performances des requêtes lorsque les services ou les nœuds sont mis à jour en interne.

En règle générale, les applications de recherche tendent à avoir besoin de plus de réplicas que de partitions, en particulier lorsque les opérations de service favorisent les charges de travail de requête. La section [Haute disponibilité](#HA) explique pourquoi.

> [!NOTE]
> L’ajout de réplicas ou de partitions augmente le coût d’exécution du service et peut introduire de légères variations dans la façon dont les résultats sont classés. N’oubliez pas d’utiliser la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour bien comprendre l’impact de l’ajout de nœuds supplémentaires sur votre facturation. Le [graphique ci-dessous](#chart) peut vous aider à déterminer le nombre d’unités de recherche requises pour une configuration spécifique. Pour plus d’informations sur l’impact des réplicas supplémentaires sur le traitement des requêtes, consultez [Classement des résultats](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Comment allouer des réplicas et partitions

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis sélectionnez le service de recherche.

1. Dans **Paramètres**, ouvrez la page **Mise à l’échelle** pour modifier les réplicas et partitions. 

   La capture d’écran suivante montre un service standard approvisionné avec un réplica et une partition. La formule en bas indique combien d’unités de recherche sont utilisées (1). Si le prix unitaire était de 100 (prix fictif), le coût mensuel de l’exécution de ce service serait de 100 en moyenne.

   ![Page de mise à l’échelle avec les valeurs actuelles](media/search-capacity-planning/1-initial-values.png "Page de mise à l’échelle avec les valeurs actuelles")

1. Utilisez le curseur pour augmenter ou diminuer le nombre de partitions. La formule en bas indique combien d’unités de recherche utilisées.

   Cet exemple double la capacité, avec deux réplicas et partitions. Notez le nombre d’unités de recherche. Il est désormais de quatre, car la formule de facturation multiplie le nombre de réplicas par le nombre de partitions (2 x 2). Le doublement de la capacité fait plus que doubler le coût de l’exécution du service. Si le coût d’une unité de recherche était de 100, la nouvelle facture mensuelle serait désormais de 400.

   Pour le coût unitaire de chaque niveau, visitez la [page Tarification](https://azure.microsoft.com/pricing/details/search/).

   ![Ajouter des réplicas et des partitions](media/search-capacity-planning/2-add-2-each.png "Ajouter des réplicas et des partitions")

1. Cliquez sur **Enregistrer** pour confirmer les modifications.

   ![Confirmer les modifications de la mise à l'échelle et de la facturation](media/search-capacity-planning/3-save-confirm.png "Confirmer les modifications de la mise à l'échelle et de la facturation")

   La prise d’effet des changements de capacité peut nécessiter quelques heures. Une fois que le processus a démarré, vous ne pouvez pas l’annuler, et il n’y a pas de supervision en temps réel des ajustements de réplicas et de partitions. Toutefois, le message suivant reste visible pendant que les changements prennent effet.

   ![Message de statut dans le portail](media/search-capacity-planning/4-updating.png "Message de statut dans le portail")

> [!NOTE]
> Une fois qu’un service a été provisionné, il ne peut pas être mis à niveau vers un niveau supérieur. Vous devez créer un service de recherche au nouveau niveau et recharger vos index. Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service Recherche cognitive Azure dans le portail](search-create-service-portal.md) .
>
> De plus, les partitions et les réplicas sont gérés de manière exclusive et en interne par le service. Il n’existe pas de concept d’affinité du processeur ni d’affectation d’une charge de travail à un nœud spécifique.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinaisons de partitions et de réplicas

Un service basique peut avoir exactement une partition et jusqu’à trois réplicas, pour une limite maximale de trois unités de recherche. Les seules ressources ajustables sont les réplicas. Vous devez disposer d’au moins 2 réplicas pour la haute disponibilité sur des requêtes.

Tous les services de recherche standard et à stockage optimisé peuvent supposer les combinaisons suivantes de réplicas et de partitions, soumises à la limite de 36 unités de stockage. 

|   | **1 partition** | **2 partitions** | **3 partitions** | **4 partitions** | **6 partitions** | **12 partitions** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 unité de recherche |2 unités de recherche |3 unités de recherche |4 unités de recherche |6 unités de recherche |12 unités de recherche |
| **2 réplicas** |2 unités de recherche |4 unités de recherche |6 unités de recherche |8 unités de recherche |12 unités de recherche |24 unités de recherche |
| **3 réplicas** |3 unités de recherche |6 unités de recherche |9 unités de recherche |12 unités de recherche |18 unités de recherche |36 unités de recherche |
| **4 réplicas** |4 unités de recherche |8 unités de recherche |12 unités de recherche |16 unités de recherche |24 unités de recherche |N/A |
| **5 réplicas** |5 unités de recherche |10 unités de recherche |15 unités de recherche |20 unités de recherche |30 unités de recherche |N/A |
| **6 réplicas** |6 unités de recherche |12 unités de recherche |18 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |
| **12 réplicas** |12 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |N/A |N/A |

Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Le nombre de réplicas et de partitions est divisible par 12 de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Recherche cognitive Azure divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties équitablement sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un index, chaque partition contiendra quatre partitions de l'index. Le partitionnement d’un index réalisé par la Recherche cognitive Azure est un détail d'implémentation susceptible d’être modifié dans des futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.
>

<a id="HA"></a>

## <a name="high-availability"></a>Haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Les charges de travail de requêtes s’exécutent principalement sur des réplicas. Si vous nécessitez une haute disponibilité ou un débit plus important, vous aurez probablement besoin de réplicas supplémentaires.

Recommandations générales pour la haute disponibilité :

* Deux réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)

* Trois réplicas minimum pour la haute disponibilité des charges de travail en lecture/écriture (les requêtes et l’indexation en tant que documents individuels sont ajoutées, mises à jour ou supprimées)

Les contrats de niveau de service (SLA) pour la Recherche cognitive Azure sont ciblés au moment des opérations de requête et des mises à jour d’index qui se composent d’ajout, de mise à jour ou de suppression de documents.

Le niveau De base est plafonné à une partition et trois réplicas. Si vous souhaitez pouvoir répondre immédiatement aux fluctuations de la demande sur le plan de l’indexation et du débit des requêtes, songez à passer à l’un des niveaux Standard.  Si vous trouvez que vos besoins de stockage augmentent beaucoup plus rapidement que votre débit de requête, envisagez l’un des niveaux de stockage optimisé.

## <a name="disaster-recovery"></a>Récupération d'urgence

Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. L’approche la plus courante consiste à intégrer la redondance au niveau du service en configurant un deuxième service de recherche dans une autre région. Comme avec la disponibilité pendant une reconstruction d’index, la redirection ou la logique de basculement doit provenir de votre code.

## <a name="estimate-replicas"></a>Estimer les réplicas

Sur un service de production, vous devez allouer trois réplicas pour les besoins de contrat SLA. Si vous rencontrez des problèmes de lenteur, vous pouvez ajouter des réplicas afin que des copies supplémentaires de l’index soient mises en ligne pour prendre en charge des charges de travail avec davantage de requêtes et équilibrer la charge des requêtes sur plusieurs réplicas.

Nous ne fournissons pas d’instructions sur le nombre de réplicas nécessaires pour prendre en charge les charges de requêtes. Les performances des requêtes dépendent de la complexité des requêtes et des charges de travail concurrentes. Bien que l’ajout de réplicas entraîne clairement une amélioration de l’évolutivité et des performances, le résultat final n’est pas strictement linéaire : si vous ajoutez trois réplicas, le débit n’est pas forcément multiplié par trois.

Pour obtenir des conseils sur l’estimation du nombre de requêtes par seconde (RPS) pour votre solution, consultez [Mettre à l’échelle pour les performances](search-performance-optimization.md)et [Superviser les requêtes](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Estimer les partitions

Le [niveau que vous choisissez](search-sku-tier.md) détermine la taille et la vitesse des partitions, et chaque niveau est optimisé par rapport à un ensemble de caractéristiques adaptées à différents scénarios. Si vous choisissez un niveau tarifaire supérieur, vous aurez sans doute besoin de moins de partitions que si vous optez pour le niveau S1. L’une des questions auxquelles vous devrez répondre au moyen de tests autonomes est de savoir si une partition plus grande et plus coûteuse offre de meilleures performances que deux partitions moins chères sur un service provisionné à un niveau inférieur.

Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture/écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. La complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir un niveau tarifaire pour Recherche cognitive Azure](search-sku-tier.md)