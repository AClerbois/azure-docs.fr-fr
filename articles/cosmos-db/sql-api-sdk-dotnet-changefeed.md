---
title: 'Azure Cosmos DB : API, SDK et ressources du processeur de flux de modification .NET'
description: Découvrez l’API et le kit SDK du processeur de flux de modification, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du kit SDK du processeur de flux de modification .NET.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 56ec4d867abd5f2767c64b0800eeb017c0fb9923
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793006"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Kit de développement logiciel (SDK) du processeur de flux de modification .NET Téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentation de l’API**|[Documentation de référence de l’API de la bibliothèque du processeur de flux de modification](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Prise en main**|[Prise en main du kit SDK du processeur de flux de modification .NET](change-feed.md)|
|**Infrastructure actuellement prise en charge**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notes de publication

### <a name="v2-builds"></a>Builds V2

### <a name="a-name227227"></a><a name="2.2.7"/>2.2.7
* Équilibrage de la stratégie pour le scénario lors de l’obtention de tous les baux de charge améliorée prend plus de temps que l’intervalle d’expiration de bail, par exemple, en raison de problèmes de réseau :
  * Dans ce scénario d’équilibrage algorithme utilisé pour considérer faussement baux comme ayant expiré, à l’origine de baux dérobant des propriétaires actives. Cela risque de déclencher inutiles rééquilibrant un grand nombre de baux.
  * Ce problème est résolu dans cette version, en évitant de nouvelle tentative en cas de conflit alors que l’acquisition de bail expiré, le propriétaire n’a pas changé et posponing l’acquisition de bail pour l’itération d’équilibrage de charge suivant expiré.

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Gestion améliorée des exceptions de l’Observateur.
* Informations plus détaillées sur les erreurs de l’Observateur :
  * Lorsqu’un Observateur est fermé en raison d'une exception levée par ProcessChangesAsync, CloseAsync reçoit alors le paramètre du motif qui est défini sur ChangeFeedObserverCloseReason.ObserverError.
  * Ajout de traces pour identifier les erreurs dans le code utilisateur d’un Observateur.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Ajout de la prise en charge de la gestion des collections fractionnées qui utilisent un débit de base de données partagé.
  * Cette version corrige un problème qui peut se produire dans les collections fractionnées utilisant un débit de base de données partagé lorsque le fractionnement entraîne un rééquilibrage des partitions avec la création d’une seule plage de clés de partition enfants au lieu de deux. Dans ce cas, le processeur de flux de modification peut se bloquer lors de la suppression du bail pour l’ancienne plage de clés de partition et ne pas créer de nouveaux baux. Ce problème a été résolu dans cette version.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Ajout de la nouvelle propriété ChangeFeedProcessorOptions.StartContinuation pour prendre en charge le démarrage du flux de modification à partir d’un jeton de continuation de requête. Elle est utilisée uniquement quand la collection de baux est vide ou quand aucune propriété ContinuationToken n’est définie pour un bail. Pour les baux de la collection dont la propriété ContinuationToken est définie, ContinuationToken est utilisée et ChangeFeedProcessorOptions.StartContinuation est ignorée.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Ajout de la prise en charge de l’utilisation d’un magasin personnalisé afin de rendre persistants les jetons de continuation par partition.
  * Par exemple, vous pouvez utiliser un magasin de baux personnalisé pour persister une collection de baux Azure Cosmos DB partitionnée de n’importe quelle manière personnalisée.
  * Les magasins de baux personnalisés peuvent utiliser le nouveau point d’extensibilité ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) et la nouvelle interface publique ILeaseStoreManager.
  * Refactorisation de l’interface ILeaseManager en plusieurs interfaces de rôle.
* Changement cassant mineur : suppression du point d’extensibilité ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager). Utilisez ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) à la place.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Cette version corrige un problème qui se produit durant le traitement d’un fractionnement dans la collection supervisée et durant l’utilisation d’une collection de baux partitionnée. Lors du traitement d’un bail pour une partition fractionnée, le bail correspondant à cette partition ne peut pas être supprimé. Ce problème a été résolu dans cette version.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Calcul de l'estimateur fixe pour les comptes multimaîtres et le nouveau format de jeton de session.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Ajout de la prise en charge des collections de baux partitionnées. La clé de partition doit être définie comme /id.
* Changement cassant mineur : les méthodes de l’interface IChangeFeedDocumentClient et la classe ChangeFeedDocumentClient ont été modifiées pour inclure les paramètres RequestOptions et CancellationToken. IChangeFeedDocumentClient est un point d’extensibilité avancée qui vous permet d'offrir une implémentation personnalisée du client du document à utiliser le processeur de flux de modification, par exemple, décorer DocumentClient et intercepter tous les appels correspondants pour renforcer le suivi, la gestion des erreurs, etc. Avec cette mise à jour, le code qui implémente IChangeFeedDocumentClient devra être modifié pour inclure les nouveaux paramètres dans l’implémentation.
* Améliorations mineures des diagnostics.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Ajout de la nouvelle API, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Elle peut être utilisée pour estimer un travail pour chaque partition.
* Prend en charge le kit de développement logiciel (SDK) Microsoft.Azure.DocumentDB 2.0. Nécessite Microsoft.Azure.DocumentDB 2.0 ou version ultérieure.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Ajout de la propriété publique ChangeFeedEventHost.HostName pour assurer la compatibilité avec la version 1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Condition de concurrence qui se produit pendant un fractionnement de partition. La condition de concurrence peut conduire à l’acquisition d’un bail et à sa perte immédiate pendant le fractionnement de partition, entraînant une contention. Le problème de condition de concurrence est résolu avec cette version.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* Kit SDK GA

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Les problèmes suivants ont été résolus :
  * En cas de fractionnement de la partition, un double traitement des documents modifiés avant le fractionnement peut se produire.
  * L’API GetEstimatedRemainingWork a retourné 0 quand aucun bail n’était présent dans la collection de baux.

* Les exceptions suivantes sont rendues publiques. Les extensions qui implémentent IPartitionProcessor peuvent lever ces exceptions.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Évolutions mineures de l’API :
  * Suppression de ChangeFeedProcessorOptions.IsAutoCheckpointEnabled qui a été marqué comme étant obsolète.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Améliorations sur le plan de la stabilité :
  * Meilleure gestion de l’initialisation du magasin de baux. Quand le magasin de baux est vide, une seule instance de processeur peut l’initialiser. Pendant ce temps, les autres attendent.
  * Renouvellement/libération de bail plus stable/efficace. Le renouvellement et la libération d’un bail au niveau d’une partition sont indépendants du renouvellement des autres. Dans v1, il s’agissait d’une opération séquentielle qui portait sur toutes les partitions.
* Nouvelle API v2 :
  * Modèle de générateur pour la construction flexible du processeur : la classe ChangeFeedProcessorBuilder.
    * Peut prendre n’importe quelle combinaison de paramètres.
    * Peut prendre une instance DocumentClient pour la surveillance et/ou la collection de baux (non disponible dans v1).
  * IChangeFeedObserver.ProcessChangesAsync accepte désormais CancellationToken.
  * IRemainingWorkEstimator : l’estimateur de travail restant peut être utilisé indépendamment du processeur.
  * Nouveaux points d’extensibilité :
    * IPartitionLoadBalancingStrategy : pour un équilibrage de charge de partitions personnalisé entre des instances du processeur.
    * ILease, ILeaseManager : pour la gestion de baux personnalisée.
    * IPartitionProcessor : pour les modifications de traitement personnalisées sur une partition.
* Journalisation : utilise la bibliothèque [LibLog](https://github.com/damianh/LibLog).
* 100 % compatible avec l’API v1.
* Nouvelle base de code.
* Compatible avec les versions 1.21.1 et ultérieures du [SDK SQL .NET](sql-api-sdk-dotnet.md).

### <a name="v1-builds"></a>Builds V1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Journalisation supplémentaire ajoutée.
* Réparation d’une fuite DocumentClient lors de l’appel du travail en attente plusieurs fois.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correctifs inclus dans l’estimation de travail en attente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Améliorations de la stabilité.
  * Correctif pour gérer le problème des tâches annulées qui peut-être entraîner des observateurs arrêtés sur certaines partitions.
* Prise en charge des points de contrôle manuels.
* Compatible avec les versions 1.21 et version ultérieure du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ajout de la prise en charge de .NET Standard 2.0. Le package prend désormais en charge les monikers d’infrastructure `netstandard2.0` et `net451`.
* Compatible avec les versions 1.17.0 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).
* Compatible avec les versions 1.5.1 et supérieures du [Kit de développement logiciel (SDK) principal SQL .NET](sql-api-sdk-dotnet-core.md).

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Résout un problème relatif au calcul de l’estimation du travail restant lorsque le flux de modification était vide ou qu’aucun travail n’était en attente.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Ajout d’une méthode pour obtenir une estimation du travail restant à traiter dans le flux de modification.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit SDK GA
* Compatible avec les versions 1.14.1 et inférieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).


## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de mise en production | Date de suppression |
| --- | --- | --- |
| [2.2.7](#2.2.7) |14 mai 2019 |--- |
| [2.2.6](#2.2.6) |29 janvier 2019 |--- |
| [2.2.5](#2.2.5) |13 décembre 2018 |--- |
| [2.2.4](#2.2.4) |29 novembre 2018 |--- |
| [2.2.3](#2.2.3) |19 novembre 2018 |--- |
| [2.2.2](#2.2.2) |31 octobre 2018 |--- |
| [2.2.1](#2.2.1) |24 octobre 2018 |--- |
| [1.3.3](#1.3.3) |8 mai 2018 |--- |
| [1.3.2](#1.3.2) |18 avril 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 octobre 2017 |--- |
| [1.1.1](#1.1.1) |29 août 2017 |--- |
| [1.1.0](#1.1.0) |13 août 2017 |--- |
| [1.0.0](#1.0.0) |7 juillet 2017 |--- |


## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

