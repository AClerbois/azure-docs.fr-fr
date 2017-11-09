---
title: "Mesures Azure Monitor : mesures prises en charge par type de ressource | Microsoft Docs"
description: Liste des mesures disponibles pour chaque type de ressource avec Azure Monitor.
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: ancav
ms.openlocfilehash: 05830547a5b8a24a59571edf6dd44d101b660189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Mesures prises en charge avec Azure Monitor
Azure Monitor offre plusieurs moyens d’interagir avec les mesures, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les mesures actuellement offertes par le pipeline de mesure d’Azure Monitor.

> [!NOTE]
> D’autres mesures peuvent être disponibles dans le portail ou via les API héritées. Cette liste ne comprend que les mesures disponibles en utilisant le pipeline de mesures Azure Monitor consolidé. Pour rechercher et accéder aux métriques avec des dimensions, veuillez utiliser [2017-05-01-preview api-version](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Nombre|Moyenne|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|Aucune dimension|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|Aucune dimension|
|TotalConnectionRequests|Nombre total de demandes de connexion|Nombre|Moyenne|Nombre total de demandes de connexion. Il s’agit des arrivées.|Aucune dimension|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Moyenne|Taux de connexions terminées réussies.|Aucune dimension|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Nombre|Moyenne|Total des échecs de tentatives de connexion.|Aucune dimension|
|CurrentUserSessions|Sessions utilisateur actuelles|Nombre|Moyenne|Nombre actuel de sessions utilisateur établies.|Aucune dimension|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de requêtes.|Aucune dimension|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|Aucune dimension|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Nombre|Moyenne|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|Aucune dimension|
|CurrentConnections|Connexion : connexions actuelles|Nombre|Moyenne|Nombre actuel de connexions client établies.|Aucune dimension|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Nombre|Moyenne|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|Aucune dimension|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|Aucune dimension|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|Aucune dimension|
|MemoryUsage|Mémoire : utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire xVelocity (VertiPaq) dépassant la limite de mémoire du moteur xVelocity.|Aucune dimension|
|MemoryLimitHard|Mémoire : limite de mémoire physique|Octets|Moyenne|Limite de mémoire physique, du fichier de configuration.|Aucune dimension|
|MemoryLimitHigh|Mémoire : limite de mémoire élevée|Octets|Moyenne|Limite de mémoire élevée, du fichier de configuration.|Aucune dimension|
|MemoryLimitLow|Mémoire : limite de mémoire basse|Octets|Moyenne|Limite de mémoire basse, du fichier de configuration.|Aucune dimension|
|MemoryLimitVertiPaq|Mémoire : limite de mémoire VertiPaq|Octets|Moyenne|Limite en mémoire, du fichier de configuration.|Aucune dimension|
|Quota|Mémoire : quota|Octets|Moyenne|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|Aucune dimension|
|QuotaBlocked|Mémoire : quota bloqué|Nombre|Moyenne|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|Aucune dimension|
|VertiPaqNonpaged|Mémoire : réserve non paginée VertiPaq|Octets|Moyenne|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|Aucune dimension|
|VertiPaqPaged|Mémoire : réserve paginée VertiPaq|Octets|Moyenne|Octets de mémoire paginée utilisée pour les données en mémoire.|Aucune dimension|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Moyenne|Taux de lignes lues à partir de toutes les bases de données relationnelles.|Aucune dimension|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Moyenne|Taux de lignes converties lors du traitement.|Aucune dimension|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Moyenne|Taux de lignes écrites lors du traitement.|Aucune dimension|
|CommandPoolBusyThreads|Threads : threads occupés du pool commandes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de commandes.|Aucune dimension|
|CommandPoolIdleThreads|Threads : threads inactifs du pool commande|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads de commandes.|Aucune dimension|
|LongParsingBusyThreads|Threads : threads d’analyse longue occupés|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse longue.|Aucune dimension|
|LongParsingIdleThreads|Threads : threads d’analyse longue inactifs|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse longue.|Aucune dimension|
|LongParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse longue|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|Aucune dimension|
|ProcessingPoolBusyIOJobThreads|Threads : traitement des threads de travail d’E/S occupés du pool|Nombre|Moyenne|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|Aucune dimension|
|ProcessingPoolBusyNonIOThreads|Threads : traitement des threads de travail autres qu’E/S occupés du pool|Nombre|Moyenne|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|Aucune dimension|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file d’attente des travaux d’E/S du pool de traitement|Nombre|Moyenne|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|Aucune dimension|
|ProcessingPoolIdleIOJobThreads|Threads : traitement des threads de travail d’E/S ignorés du pool|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|Aucune dimension|
|ProcessingPoolIdleNonIOThreads|Threads : traitement des threads de travail autres qu’E/S inactifs du pool|Nombre|Moyenne|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|Aucune dimension|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|Aucune dimension|
|QueryPoolJobQueueLength|Threads : longueur de file d’attente de travaux du pool de requêtes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|Aucune dimension|
|ShortParsingBusyThreads|Threads : threads d’analyse courte occupés|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse courte.|Aucune dimension|
|ShortParsingIdleThreads|Threads : threads d’analyse courte inactifs|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse courte.|Aucune dimension|
|ShortParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse courte|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|Aucune dimension|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|Aucune dimension|
|mashup_engine_qpu_metric|QPU du moteur M|Nombre|Moyenne|Utilisation des QPU par les processus de moteur mashup|Aucune dimension|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Moyenne|Utilisation de la mémoire par les processus de moteur mashup|Aucune dimension|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Nombre total de demandes de la passerelle|Nombre|Total|Nombre de demandes de la passerelle|Emplacement, nom d’hôte|
|SuccessfulRequests|Demandes de la passerelle ayant abouti|Nombre|Total|Nombre de demandes de la passerelle ayant abouti|Emplacement, nom d’hôte|
|UnauthorizedRequests|Demandes de la passerelle non autorisées|Nombre|Total|Nombre de demandes de la passerelle non autorisées|Emplacement, nom d’hôte|
|FailedRequests|Demandes de la passerelle ayant échoué|Nombre|Total|Nombre de défaillances des demandes de la passerelle|Emplacement, nom d’hôte|
|OtherRequests|Autres demandes de la passerelle|Nombre|Total|Nombre d’autres demandes de la passerelle|Emplacement, nom d’hôte|
|Duration|Durée globale des demandes de passerelle|Millisecondes|Moyenne|Durée globale des demandes de passerelle en millisecondes|Emplacement, nom d’hôte|
|Capacity|Capacité (préversion)|Pourcentage|Maximale|Métrique d’utilisation pour le service ApiManagement|Lieu|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Nombre total de travaux|Nombre|Total|Nombre total de travaux|Aucune dimension|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Nombre de cœurs dédiés|Nombre|Total|Nombre total de cœurs dédiés dans le compte Batch|Aucune dimension|
|TotalNodeCount|Nombre de nœuds dédiés|Nombre|Total|Nombre total de nœuds dédiés dans le compte Batch|Aucune dimension|
|LowPriorityCoreCount|Nombre de cœurs à priorité basse|Nombre|Total|Nombre total de cœurs à priorité basse dans le compte Batch|Aucune dimension|
|TotalLowPriorityNodeCount|Nombre de nœuds à priorité basse|Nombre|Total|Nombre total de nœuds à priorité basse dans le compte Batch|Aucune dimension|
|CreatingNodeCount|Nombre de nœuds créés|Nombre|Total|Nombre de nœuds en cours de création|Aucune dimension|
|StartingNodeCount|Nombre de nœuds de départ|Nombre|Total|Nœuds de nœuds de départ|Aucune dimension|
|WaitingForStartTaskNodeCount|Nombre de nœuds en attente de démarrage de tâche|Nombre|Total|Nombre de nœuds en attente de la fin d’une tâche de démarrage|Aucune dimension|
|StartTaskFailedNodeCount|Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Nombre|Total|Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Aucune dimension|
|IdleNodeCount|Nombre de nœuds inactifs|Nombre|Total|Le nombre de nœuds inactifs|Aucune dimension|
|OfflineNodeCount|Nombre de nœuds hors ligne|Nombre|Total|Le nombre de nœuds hors ligne|Aucune dimension|
|RebootingNodeCount|Nombre de nœuds en cours de redémarrage|Nombre|Total|Le nombre de nœuds en cours de redémarrage|Aucune dimension|
|ReimagingNodeCount|Nombre de nœuds en cours de réimageage|Nombre|Total|Le nombre de nœuds en cours de réimageage|Aucune dimension|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Nombre|Total|Le nombre de nœuds en cours d’exécution|Aucune dimension|
|LeavingPoolNodeCount|Nombre de nœuds quittant le pool|Nombre|Total|Le nombre de nœuds quittant le pool|Aucune dimension|
|UnusableNodeCount|Nombre de nœuds inutilisables|Nombre|Total|Le nombre de nœuds inutilisables|Aucune dimension|
|PreemptedNodeCount|Nombre de nœuds reportés|Nombre|Total|Nombre de nœuds reportés|Aucune dimension|
|TaskStartEvent|Événements de lancement de tâche|Nombre|Total|Nombre total de tâches ayant démarré|Aucune dimension|
|TaskCompleteEvent|Événements de fin de tâche|Nombre|Total|Le nombre total de tâches terminées|Aucune dimension|
|TaskFailEvent|Événements d’échec de tâches|Nombre|Total|Le nombre total de tâches terminées dans un état d’échec|Aucune dimension|
|PoolCreateEvent|Événements de création de pool|Nombre|Total|Nombre total de pools créés|Aucune dimension|
|PoolResizeStartEvent|Événements de démarrage de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool ayant démarré|Aucune dimension|
|PoolResizeCompleteEvent|Événements de fin de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool terminés|Aucune dimension|
|PoolDeleteStartEvent|Événements de démarrage de suppression de pool|Nombre|Total|Nombre total de suppressions de pool ayant démarré|Aucune dimension|
|PoolDeleteCompleteEvent|Événements de suppression de pool terminés|Nombre|Total|Nombre total de suppressions de pool terminées|Aucune dimension|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Clients connectés|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed|Total des opérations|Nombre|Total||Aucune dimension|
|cachehits|Présences dans le cache|Nombre|Total||Aucune dimension|
|cachemisses|Absences dans le cache|Nombre|Total||Aucune dimension|
|getcommands|Gets|Nombre|Total||Aucune dimension|
|setcommands|Sets|Nombre|Total||Aucune dimension|
|evictedkeys|Clés exclues|Nombre|Total||Aucune dimension|
|totalkeys|Nombre total de clés|Nombre|Maximale||Aucune dimension|
|expiredkeys|Clés expirées|Nombre|Total||Aucune dimension|
|usedmemory|Mémoire utilisée|Octets|Maximale||Aucune dimension|
|usedmemoryRss|Taille de la mémoire résidente utilisée|Octets|Maximale||Aucune dimension|
|serverLoad|Charge du serveur|Pourcentage|Maximale||Aucune dimension|
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead|Lecture du cache|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime|UC|Pourcentage|Maximale||Aucune dimension|
|connectedclients0|Clients connectés (Shard 0)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed0|Total des opérations (Shard 0)|Nombre|Total||Aucune dimension|
|cachehits0|Présences dans le cache (Shard 0)|Nombre|Total||Aucune dimension|
|cachemisses0|Absences dans le cache (Shard 0)|Nombre|Total||Aucune dimension|
|getcommands0|Gets (Shard 0)|Nombre|Total||Aucune dimension|
|setcommands0|Sets (Shard 0)|Nombre|Total||Aucune dimension|
|evictedkeys0|Clés exclues (Shard 0)|Nombre|Total||Aucune dimension|
|totalkeys0|Nombre total de clés (Shard 0)|Nombre|Maximale||Aucune dimension|
|expiredkeys0|Clés expirées (Shard 0)|Nombre|Total||Aucune dimension|
|usedmemory0|Mémoire utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|usedmemoryRss0|Mémoire résidente utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|serverLoad0|Charge du serveur (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite0|Cache d’écriture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead0|Cache de lecture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime0|UC (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|connectedclients1|Clients connectés (Shard 1)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed1|Total des opérations (Shard 1)|Nombre|Total||Aucune dimension|
|cachehits1|Présences dans le cache (Shard 1)|Nombre|Total||Aucune dimension|
|cachemisses1|Absences dans le cache (Shard 1)|Nombre|Total||Aucune dimension|
|getcommands1|Gets (Shard 1)|Nombre|Total||Aucune dimension|
|setcommands1|Sets (Shard 1)|Nombre|Total||Aucune dimension|
|evictedkeys1|Clés exclues (Shard 1)|Nombre|Total||Aucune dimension|
|totalkeys1|Nombre total de clés (Shard 1)|Nombre|Maximale||Aucune dimension|
|expiredkeys1|Clés expirées (Shard 1)|Nombre|Total||Aucune dimension|
|usedmemory1|Mémoire utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|usedmemoryRss1|Mémoire résidente utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|serverLoad1|Charge du serveur (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite1|Cache d’écriture (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead1|Lecture du cache (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime1|UC (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|connectedclients2|Clients connectés (Shard 2)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed2|Total des opérations (Shard 2)|Nombre|Total||Aucune dimension|
|cachehits2|Présences dans le cache (Shard 2)|Nombre|Total||Aucune dimension|
|cachemisses2|Absences dans le cache (Shard 2)|Nombre|Total||Aucune dimension|
|getcommands2|Gets (Shard 2)|Nombre|Total||Aucune dimension|
|setcommands2|Sets (Shard 2)|Nombre|Total||Aucune dimension|
|evictedkeys2|Clés exclues (Shard 2)|Nombre|Total||Aucune dimension|
|totalkeys2|Nombre total de clés (Shard 2)|Nombre|Maximale||Aucune dimension|
|expiredkeys2|Clés expirées (Shard 2)|Nombre|Total||Aucune dimension|
|usedmemory2|Mémoire utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|usedmemoryRss2|Mémoire résidente utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|serverLoad2|Charge du serveur (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite2|Cache d’écriture (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead2|Lecture du cache (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime2|UC (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|connectedclients3|Clients connectés (Shard 3)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed3|Total des opérations (Shard 3)|Nombre|Total||Aucune dimension|
|cachehits3|Présences dans le cache (Shard 3)|Nombre|Total||Aucune dimension|
|cachemisses3|Absences dans le cache (Shard 3)|Nombre|Total||Aucune dimension|
|getcommands3|Gets (Shard 3)|Nombre|Total||Aucune dimension|
|setcommands3|Sets (Shard 3)|Nombre|Total||Aucune dimension|
|evictedkeys3|Clés exclues (Shard 3)|Nombre|Total||Aucune dimension|
|totalkeys3|Nombre total de clés (Shard 3)|Nombre|Maximale||Aucune dimension|
|expiredkeys3|Clés expirées (Shard 3)|Nombre|Total||Aucune dimension|
|usedmemory3|Mémoire utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|usedmemoryRss3|Mémoire résidente utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|serverLoad3|Charge du serveur (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite3|Cache d’écriture (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead3|Lecture du cache (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime3|UC (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|connectedclients4|Clients connectés (Shard 4)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed4|Total des opérations (Shard 4)|Nombre|Total||Aucune dimension|
|cachehits4|Présences dans le cache (Shard 4)|Nombre|Total||Aucune dimension|
|cachemisses4|Absences dans le cache (Shard 4)|Nombre|Total||Aucune dimension|
|getcommands4|Gets (Shard 4)|Nombre|Total||Aucune dimension|
|setcommands4|Sets (Shard 4)|Nombre|Total||Aucune dimension|
|evictedkeys4|Clés exclues (Shard 4)|Nombre|Total||Aucune dimension|
|totalkeys4|Nombre total de clés (Shard 4)|Nombre|Maximale||Aucune dimension|
|expiredkeys4|Clés expirées (Shard 4)|Nombre|Total||Aucune dimension|
|usedmemory4|Mémoire utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|usedmemoryRss4|Mémoire résidente utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|serverLoad4|Charge du serveur (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite4|Cache d’écriture (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead4|Lecture du cache (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime4|UC (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|connectedclients5|Clients connectés (Shard 5)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed5|Total des opérations (Shard 5)|Nombre|Total||Aucune dimension|
|cachehits5|Présences dans le cache (Shard 5)|Nombre|Total||Aucune dimension|
|cachemisses5|Absences dans le cache (Shard 5)|Nombre|Total||Aucune dimension|
|getcommands5|Gets (Shard 5)|Nombre|Total||Aucune dimension|
|setcommands5|Sets (Shard 5)|Nombre|Total||Aucune dimension|
|evictedkeys5|Clés exclues (Shard 5)|Nombre|Total||Aucune dimension|
|totalkeys5|Nombre total de clés (Shard 5)|Nombre|Maximale||Aucune dimension|
|expiredkeys5|Clés expirées (Shard 5)|Nombre|Total||Aucune dimension|
|usedmemory5|Mémoire utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|usedmemoryRss5|Mémoire résidente utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|serverLoad5|Charge du serveur (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite5|Cache d’écriture (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead5|Lecture du cache (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime5|UC (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|connectedclients6|Clients connectés (Shard 6)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed6|Total des opérations (Shard 6)|Nombre|Total||Aucune dimension|
|cachehits6|Présences dans le cache (Shard 6)|Nombre|Total||Aucune dimension|
|cachemisses6|Absences dans le cache (Shard 6)|Nombre|Total||Aucune dimension|
|getcommands6|Gets (Shard 6)|Nombre|Total||Aucune dimension|
|setcommands6|Sets (Shard 6)|Nombre|Total||Aucune dimension|
|evictedkeys6|Clés exclues (Shard 6)|Nombre|Total||Aucune dimension|
|totalkeys6|Nombre total de clés (Shard 6)|Nombre|Maximale||Aucune dimension|
|expiredkeys6|Clés expirées (Shard 6)|Nombre|Total||Aucune dimension|
|usedmemory6|Mémoire utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|usedmemoryRss6|Mémoire résidente utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|serverLoad6|Charge du serveur (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite6|Cache d’écriture (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead6|Lecture du cache (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime6|UC (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|connectedclients7|Clients connectés (Shard 7)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed7|Total des opérations (Shard 7)|Nombre|Total||Aucune dimension|
|cachehits7|Présences dans le cache (Shard 7)|Nombre|Total||Aucune dimension|
|cachemisses7|Absences dans le cache (Shard 7)|Nombre|Total||Aucune dimension|
|getcommands7|Gets (Shard 7)|Nombre|Total||Aucune dimension|
|setcommands7|Sets (Shard 7)|Nombre|Total||Aucune dimension|
|evictedkeys7|Clés exclues (Shard 7)|Nombre|Total||Aucune dimension|
|totalkeys7|Nombre total de clés (Shard 7)|Nombre|Maximale||Aucune dimension|
|expiredkeys7|Clés expirées (Shard 7)|Nombre|Total||Aucune dimension|
|usedmemory7|Mémoire utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|usedmemoryRss7|Mémoire résidente utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|serverLoad7|Charge du serveur (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite7|Cache d’écriture (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead7|Lecture du cache (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime7|UC (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|connectedclients8|Clients connectés (Shard 8)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed8|Total des opérations (Shard 8)|Nombre|Total||Aucune dimension|
|cachehits8|Présences dans le cache (Shard 8)|Nombre|Total||Aucune dimension|
|cachemisses8|Absences dans le cache (Shard 8)|Nombre|Total||Aucune dimension|
|getcommands8|Gets (Shard 8)|Nombre|Total||Aucune dimension|
|setcommands8|Sets (Shard 8)|Nombre|Total||Aucune dimension|
|evictedkeys8|Clés exclues (Shard 8)|Nombre|Total||Aucune dimension|
|totalkeys8|Nombre total de clés (Shard 8)|Nombre|Maximale||Aucune dimension|
|expiredkeys8|Clés expirées (Shard 8)|Nombre|Total||Aucune dimension|
|usedmemory8|Mémoire utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|usedmemoryRss8|Mémoire résidente utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|serverLoad8|Charge du serveur (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite8|Cache d’écriture (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead8|Lecture du cache (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime8|UC (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|connectedclients9|Clients connectés (Shard 9)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed9|Total des opérations (Shard 9)|Nombre|Total||Aucune dimension|
|cachehits9|Présences dans le cache (Shard 9)|Nombre|Total||Aucune dimension|
|cachemisses9|Absences dans le cache (Shard 9)|Nombre|Total||Aucune dimension|
|getcommands9|Gets (Shard 9)|Nombre|Total||Aucune dimension|
|setcommands9|Sets (Shard 9)|Nombre|Total||Aucune dimension|
|evictedkeys9|Clés exclues (Shard 9)|Nombre|Total||Aucune dimension|
|totalkeys9|Nombre total de clés (Shard 9)|Nombre|Maximale||Aucune dimension|
|expiredkeys9|Clés expirées (Shard 9)|Nombre|Total||Aucune dimension|
|usedmemory9|Mémoire utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|usedmemoryRss9|Mémoire résidente utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|serverLoad9|Charge du serveur (Shard 9)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite9|Cache d’écriture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead9|Cache de lecture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime9|UC (Shard 9)|Pourcentage|Maximale||Aucune dimension|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|Aucune dimension|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|Aucune dimension|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|Aucune dimension|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Moyenne|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|Aucune dimension|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Moyenne|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde.|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture sur disque par seconde.|Aucune dimension|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|Nombre total d’appels|Nombre|Total|Nombre total d’appels.|Aucune dimension|
|SuccessfulCalls|Appels réussis|Nombre|Total|Nombre d’appels réussis.|Aucune dimension|
|TotalErrors|Nombre total d’erreurs|Nombre|Total|Nombre total d’appels avec réponse d’erreur (code de réponse HTTP : 4xx ou 5xx).|Aucune dimension|
|BlockedCalls|Appels bloqués|Nombre|Total|Nombre d’appels ayant dépassé la limite de débit ou de quota.|Aucune dimension|
|ServerErrors|Erreurs de serveur|Nombre|Total|Nombre d’appels avec erreur interne du service (code de réponse HTTP : 5xx).|Aucune dimension|
|ClientErrors|Erreurs de client|Nombre|Total|Nombre d’appels avec erreur côté client (code de réponse HTTP : 4xx).|Aucune dimension|
|DataIn|Données entrantes|Octets|Total|Taille des données entrantes en octets.|Aucune dimension|
|DataOut|Données sortantes|Octets|Total|Taille des données sortantes en octets.|Aucune dimension|
|Latency|Latency|Millisecondes|Moyenne|Latence en millisecondes.|Aucune dimension|
|CharactersTranslated|Caractères traduits|Nombre|Total|Nombre total de caractères dans la requête de texte entrante.|Aucune dimension|
|SpeechSessionDuration|Durée de la session vocale|Secondes|Total|Durée totale de la session vocale en secondes.|Aucune dimension|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|DCIApiCalls|Appels de l’API Insights client|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationSuccessfulLines|Mappage des lignes de réussite d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationFailedLines|Mappage des lignes en échec d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationTotalLines|Mappage du total des lignes d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationRuntimeInSeconds|Mappage d’exécution d’opération d’importation en secondes|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportSucceeded|Exportation réussie de profil de sortie|Nombre|Total||Aucune dimension|
|DCIOutboundProfileExportFailed|Échec de l’exportation de profil de sortie|Nombre|Total||Aucune dimension|
|DCIOutboundProfileExportDuration|Durée d’exportation de profil de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportSucceeded|Exportation réussie de l’indicateur KPI sortant|Nombre|Total||Aucune dimension|
|DCIOutboundKpiExportFailed|Échec de l’exportation de l’indicateur KPI sortant|Nombre|Total||Aucune dimension|
|DCIOutboundKpiExportDuration|Durée de l’exportation de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportStarted|Exportation démarrée de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiRecordCount|Nombre d’enregistrements KPI sortants|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportCount|Nombre d’exportations de profils de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportFailed|Échec de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportSucceeded|Réussite de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportFailed|Échec de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportSucceeded|Réussite de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportDurationInSeconds|Durée d’exportation de profil initial de sortie en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiFailed|Échec du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiTimeOut|Délai d’expiration du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiCompleted|Achèvement du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|ImportASAValuesFailed|Nombre d’importations de valeurs ASA en échec|Nombre|Total||Aucune dimension|
|ImportASAValuesSucceeded|Nombre d’importations de valeurs ASA réussies|Nombre|Total||Aucune dimension|
|DCIProfilesCount|Nombre d’instances de profil|Nombre|Dernier||Aucune dimension|
|DCIInteractionsPerMonthCount|Nombre d’interactions par mois|Nombre|Dernier||Aucune dimension|
|DCIKpisCount|Nombre de KPI|Nombre|Dernier||Aucune dimension|
|DCISegmentsCount|Nombre de segments|Nombre|Dernier||Aucune dimension|
|DCIPredictiveMatchPoliciesCount|Nombre de correspondances prédictives|Nombre|Dernier||Aucune dimension|
|DCIPredictionsCount|Nombre de prédictions|Nombre|Dernier||Aucune dimension|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Travaux réussis|Nombre|Total|Nombre de travaux réussis|Aucune dimension|
|JobEndedFailure|Travaux en échec|Nombre|Total|Nombre de travaux en échec|Aucune dimension|
|JobEndedCancelled|Travaux annulés|Nombre|Total|Nombre de travaux annulés|Aucune dimension|
|JobAUEndedSuccess|Durée AU de réussite|Secondes|Total|Durée AU totale des travaux réussis.|Aucune dimension|
|JobAUEndedFailure|Durée AU d’échec|Secondes|Total|Durée AU totale des travaux en échec|Aucune dimension|
|JobAUEndedCancelled|Durée AU d’annulation|Secondes|Total|Durée AU totale des travaux annulés|Aucune dimension|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Stockage total|Octets|Maximale|Volume total de données stockées dans le compte.|Aucune dimension|
|DataWritten|Données écrites|Octets|Total|Volume total de données écrites dans le compte.|Aucune dimension|
|DataRead|Données lues|Octets|Total|Volume total de données lues à partir du compte.|Aucune dimension|
|WriteRequests|Demandes d’écriture|Nombre|Total|Nombre de demandes d’écriture de données sur le compte.|Aucune dimension|
|ReadRequests|Demandes de lecture|Nombre|Total|Nombre de demandes de lecture de données sur le compte.|Aucune dimension|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|compute_limit|Limite d’unités de calcul|Nombre|Moyenne|Limite d’unités de calcul|Aucune dimension|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|active_connections|Nombre total de connexions actif|Nombre|Moyenne|Nombre total de connexions actif|Aucune dimension|
|connections_failed|Total de connexions ayant échoué|Nombre|Moyenne|Total de connexions ayant échoué|Aucune dimension|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|compute_limit|Limite d’unités de calcul|Nombre|Moyenne|Limite d’unités de calcul|Aucune dimension|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|active_connections|Nombre total de connexions actif|Nombre|Moyenne|Nombre total de connexions actif|Aucune dimension|
|connections_failed|Total de connexions ayant échoué|Nombre|Moyenne|Total de connexions ayant échoué|Aucune dimension|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Nombre|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Nombre|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d.commands.egress.complete.success|Commandes terminées|Nombre|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Nombre|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d.commands.egress.reject.success|Commandes rejetées|Nombre|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|devices.totalDevices|Nombre total d’appareils|Nombre|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|devices.connectedDevices.allProtocol|Appareils connectés|Nombre|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c.telemetry.egress.success|Messages de télémétrie remis|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison (total)|Aucune dimension|
|d2c.telemetry.egress.dropped|Messages supprimés|Nombre|Total|Nombre de messages ignorés, car le point de terminaison de livraison était indisponible|Aucune dimension|
|d2c.telemetry.egress.orphaned|Messages orphelins|Nombre|Total|Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris|Aucune dimension|
|d2c.telemetry.egress.invalid|Messages non valides|Nombre|Total|Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison|Aucune dimension|
|d2c.telemetry.egress.fallback|Messages correspondant à une condition de secours|Nombre|Total|Nombre de messages écrits au point de terminaison de secours|Aucune dimension|
|d2c.endpoints.egress.eventHubs|Messages remis aux points de terminaison Event Hub|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison Event Hub|Aucune dimension|
|d2c.endpoints.latency.eventHubs|Latence des messages des points de terminaison Event Hub|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusQueues|Messages remis aux points de terminaison de file d’attente Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de file d’attente Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusQueues|Latence des messages des points de terminaison de files d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusTopics|Messages remis aux points de terminaison de rubrique Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de rubrique Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusTopics|Latence des messages des points de terminaison de rubriques Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.builtIn.events|Messages remis au point de terminaison intégré (messages/événements)|Nombre|Total|Nombre de fois où des messages ont été écrits au point de terminaison intégré (messages/événements)|Aucune dimension|
|d2c.endpoints.latency.builtIn.events|Latence de message pour le point de terminaison intégré (messages/événements)|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes |Aucune dimension|
|d2c.endpoints.egress.storage|Messages remis aux points de terminaison de stockage|Nombre|Total|Nombre de fois où des messages ont correctement été écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.latency.storage|Latence des messages pour les points de terminaison de stockage|Millisecondes|Moyenne|Latence moyenne entre l’entrée des messages dans IoT Hub et l’entrée des message dans un point de terminaison de stockage, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.storage.bytes|Données écrites dans le stockage|Octets|Total|Volume de données, en octets, écrites sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.egress.storage.blobs|Objets blob écrits dans le stockage|Nombre|Total|Nombre d’objets blob écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Nombre|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Nombre|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d.methods.success|Appels de méthode directe réussis|Nombre|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d.methods.failure|Appels de méthode directe en échec|Nombre|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Nombre|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Nombre|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries.success|Requêtes de représentations réussies|Nombre|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries.failure|Requêtes de représentations en échec|Nombre|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Nombre|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Nombre|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Nombre|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Nombre|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Nombre|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Nombre|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs.cancelJob.success|Annulations de travaux réussies|Nombre|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs.cancelJob.failure|Annulations de travaux en échec|Nombre|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs.queryJobs.success|Requêtes de travaux réussies|Nombre|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Nombre|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs.completed|Travaux terminés|Nombre|Total|Total des travaux terminés.|Aucune dimension|
|jobs.failed|Travaux en échec|Nombre|Total|Total des travaux en échec.|Aucune dimension|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Nombre|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Nombre|Moyenne|Nombre total de messages utilisés aujourd'hui|Aucune dimension|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentatives d’enregistrement|Nombre|Total|Nombre d’inscriptions d’appareils tentées|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Appareils attribués|Nombre|Total|Nombre d’appareils affectés à un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentatives d’attestation|Nombre|Total|Nombre d’attestations d’appareils tentées|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Total de requêtes|Nombre|Nombre|Nombre de requêtes effectuées|DatabaseAccount, CollectionName, DatabaseName, Region, StatusCode|
|MongoRequests|Requêtes Mongo|Nombre|Nombre|Nombre de requêtes Mongo effectuées|DatabaseAccount, CollectionName, DatabaseName, Region, ErrorCode, CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|INREQS|Demandes d’envoi entrantes|Nombre|Total|Nombre total des demandes d’envoi entrantes pour un espace de noms|Aucune dimension|
|SUCCREQ|Requêtes ayant réussi|Nombre|Total|Nombre total de demandes ayant réussi pour un espace de noms|Aucune dimension|
|FAILREQ|Demandes ayant échoué|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|SVRBSY|Erreurs de serveur occupé|Nombre|Total|Nombre total d’erreurs de serveur occupé pour un espace de noms|Aucune dimension|
|INTERR|Erreurs internes du serveur|Nombre|Total|Nombre total d’erreurs de serveur internes pour un espace de noms|Aucune dimension|
|MISCERR|Autres erreurs|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|INMSGS|Messages entrants (déconseillé)|Nombre|Total|Nombre total de messages entrants pour un espace de noms. Cette mesure est déconseillée. Utilisez plutôt la mesure Messages entrants|Aucune dimension|
|EHINMSGS|Messages entrants|Nombre|Total|Nombre total de messages entrants pour un espace de noms|Aucune dimension|
|OUTMSGS|Messages sortants (déconseillé)|Nombre|Total|Nombre total de messages sortants pour un espace de noms. Cette mesure est déconseillée. Utilisez plutôt la mesure Messages sortants|Aucune dimension|
|EHOUTMSGS|Messages sortants|Nombre|Total|Nombre total de messages sortants pour un espace de noms|Aucune dimension|
|EHINMBS|Octets entrants (déconseillé)|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms. Cette mesure est déconseillée. Utilisez plutôt la mesure Octets entrants|Aucune dimension|
|EHINBYTES|Octets entrants|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms|Aucune dimension|
|EHOUTMBS|Octets sortants (déconseillé)|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms. Cette mesure est déconseillée. Utilisez plutôt la mesure Octets sortants|Aucune dimension|
|EHOUTBYTES|Octets sortants|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms|Aucune dimension|
|EHABL|Archiver les messages de file d’attente|Nombre|Total|Messages d’archive Event Hub dans le backlog pour un espace de noms|Aucune dimension|
|EHAMSGS|Archiver les messages|Nombre|Total|Messages archivés Event Hub dans un espace de noms|Aucune dimension|
|EHAMBS|Débit message archive|Octets|Total|Débit de messages archivés Event Hub dans un espace de noms|Aucune dimension|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valeur de métrique observée|Nombre|Moyenne|Valeur calculée par mise à l’échelle automatique lors de l’exécution|MetricTriggerSource|
|MetricThreshold|Seuil de métrique|Nombre|Moyenne|Seuil de mise à l’échelle automatique configurée lors de l’exécution de la mise à l’échelle automatique.|MetricTriggerRule|
|ObservedCapacity|Capacité observée|Nombre|Moyenne|Capacité envoyée à la mise à l’échelle automatique lors de l’exécution.|Aucune dimension|
|ScaleActionsInitiated|Actions de mise à l’échelle initiées|Nombre|Total|Direction de l’opération de mise à l’échelle.|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Nombre|Total|Nombre d’exécutions de flux de travail démarrées.|Aucune dimension|
|RunsCompleted|Exécutions terminées|Nombre|Total|Nombre d’exécutions de flux de travail terminées.|Aucune dimension|
|RunsSucceeded|Exécutions réussies|Nombre|Total|Nombre d’exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunsFailed|Exécutions ayant échoué|Nombre|Total|Nombre d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|RunsCancelled|Exécutions annulées|Nombre|Total|Nombre d’exécutions de flux de travail annulées.|Aucune dimension|
|RunLatency|Latence d’exécution|Secondes|Moyenne|Latence des exécutions de flux de travail terminées.|Aucune dimension|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Moyenne|Latence des exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunThrottledEvents|Exécuter événements limités|Nombre|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|Aucune dimension|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|ActionsStarted|Actions démarrées |Nombre|Total|Nombre d’actions de flux de travail démarrées.|Aucune dimension|
|ActionsCompleted|Actions terminées |Nombre|Total|Nombre d’actions de flux de travail terminées.|Aucune dimension|
|ActionsSucceeded|Actions ayant réussi |Nombre|Total|Nombre d’actions de flux de travail ayant réussi.|Aucune dimension|
|ActionsFailed|Actions ayant échoué|Nombre|Total|Nombre d’actions de flux de travail ayant échoué.|Aucune dimension|
|ActionsSkipped|Actions ignorées |Nombre|Total|Nombre d’actions de flux de travail ignorées.|Aucune dimension|
|ActionLatency|Latence de l’action |Secondes|Moyenne|Latence des actions de flux de travail terminés.|Aucune dimension|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail ayant réussi.|Aucune dimension|
|ActionThrottledEvents|Événements d’action limitée|Nombre|Total|Nombre d’événements limités d’action de flux de travail.|Aucune dimension|
|TriggersStarted|Déclenchements lancés |Nombre|Total|Nombre de déclencheurs de flux de travail démarrés.|Aucune dimension|
|TriggersCompleted|Déclenchements terminés |Nombre|Total|Nombre de déclencheurs de flux de travail terminés.|Aucune dimension|
|TriggersSucceeded|Déclenchements ayant réussi |Nombre|Total|Nombre de déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggersFailed|Déclenchements ayant échoué |Nombre|Total|Nombre de déclencheurs de flux de travail ayant échoué.|Aucune dimension|
|TriggersSkipped|Déclenchements ignorés|Nombre|Total|Nombre de déclencheurs de flux de travail ignorés.|Aucune dimension|
|TriggersFired|Déclenchements activés |Nombre|Total|Nombre de déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclenchements de flux de travail terminés.|Aucune dimension|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Moyenne|Latence des déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggerThrottledEvents|Événements limités par déclencheur|Nombre|Total|Nombre d’événements de flux de travail limités par déclencheur.|Aucune dimension|
|BillableActionExecutions|Exécutions d’actions facturables|Nombre|Total|Nombre d’exécutions d’actions de flux de travail facturées.|Aucune dimension|
|BillableTriggerExecutions|Exécutions de déclencheurs facturables|Nombre|Total|Nombre d’exécutions de déclencheurs de flux de travail facturées.|Aucune dimension|
|TotalBillableExecutions|Nombre total d’exécutions facturables|Nombre|Total|Nombre d’exécutions de flux de travail facturées.|Aucune dimension|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|Disponibilité VIP|Nombre|Moyenne|Disponibilité des points de terminaison VIP, en fonction des résultats du sondage|VipAddress, VipPort|
|DipAvailability|Disponibilité DIP|Nombre|Moyenne|Disponibilité des points de terminaison DIP, en fonction des résultats du sondage|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Nombre d’octets|Nombre|Total|Nombre total d’octets transmis dans une période de temps|VipAddress, VipPort, Direction|
|PacketCount|Nombre de paquets|Nombre|Total|Nombre total de paquets transmis dans une période de temps|VipAddress, VipPort, Direction|
|SYNCount|Nombre de SYN|Nombre|Total|Nombre total de paquets SYN transmis dans une période de temps|VipAddress, VipPort, Direction|
|SnatConnectionCount|Nombre de connexions SNAT|Nombre|Total|Nombre total de connexions SNAT créées dans une période de temps|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|Paquets entrants DDoS|CountPerSecond|Moyenne|Paquets entrants DDoS|Aucune dimension|
|PacketsDroppedDDoS|Paquets entrants ignorés DDoS|CountPerSecond|Moyenne|Paquets entrants ignorés DDoS|Aucune dimension|
|PacketsForwardedDDoS|Paquets entrants transférés DDoS|CountPerSecond|Moyenne|Paquets entrants transférés DDoS|Aucune dimension|
|TCPPacketsInDDoS|Paquets TCP entrants DDoS|CountPerSecond|Moyenne|Paquets TCP entrants DDoS|Aucune dimension|
|TCPPacketsDroppedDDoS|Paquets TCP entrants TCP ignorés DDoS|CountPerSecond|Moyenne|Paquets TCP entrants TCP ignorés DDoS|Aucune dimension|
|TCPPacketsForwardedDDoS|Paquets TCP entrants transférés DDoS|CountPerSecond|Moyenne|Paquets TCP entrants transférés DDoS|Aucune dimension|
|UDPPacketsInDDoS|Paquets UDP entrants DDoS|CountPerSecond|Moyenne|Paquets UDP entrants DDoS|Aucune dimension|
|UDPPacketsDroppedDDoS|Paquets UDP entrants ignorés DDoS|CountPerSecond|Moyenne|Paquets UDP entrants ignorés DDoS|Aucune dimension|
|UDPPacketsForwardedDDoS|Paquets UDP entrants transférés DDoS|CountPerSecond|Moyenne|Paquets UDP entrants transférés DDoS|Aucune dimension|
|BytesInDDoS|Octets entrants DDoS|BytesPerSecond|Moyenne|Octets entrants DDoS|Aucune dimension|
|BytesDroppedDDoS|Octets entrants supprimés DDoS|BytesPerSecond|Moyenne|Octets entrants supprimés DDoS|Aucune dimension|
|BytesForwardedDDoS|Octets entrants transférés DDoS|BytesPerSecond|Moyenne|Octets entrants transférés DDoS|Aucune dimension|
|TCPBytesInDDoS|Octets TCP entrants DDoS|BytesPerSecond|Moyenne|Octets TCP entrants DDoS|Aucune dimension|
|TCPBytesDroppedDDoS|Octets TCP entrants supprimés DDoS|BytesPerSecond|Moyenne|Octets TCP entrants supprimés DDoS|Aucune dimension|
|TCPBytesForwardedDDoS|Octets TCP entrants transférés DDoS|BytesPerSecond|Moyenne|Octets TCP entrants transférés DDoS|Aucune dimension|
|UDPBytesInDDoS|Octets UDP entrants DDoS|BytesPerSecond|Moyenne|Octets UDP entrants DDoS|Aucune dimension|
|UDPBytesDroppedDDoS|Octets UDP entrants supprimés DDoS|BytesPerSecond|Moyenne|Octets UDP entrants supprimés DDoS|Aucune dimension|
|UDPBytesForwardedDDoS|Octets UDP entrants transférés DDoS|BytesPerSecond|Moyenne|Octets UDP entrants transférés DDoS|Aucune dimension|
|IfUnderDDoSAttack|Sous attaque DDoS ou non|Nombre|Moyenne|Sous attaque DDoS ou non|Aucune dimension|
|DDoSTriggerTCPPackets|Paquets TCP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Moyenne|Paquets TCP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|DDoSTriggerUDPPackets|Paquets UDP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Moyenne|Paquets UDP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|VipAvailability|Disponibilité|Nombre|Moyenne|Disponibilité moyenne de l’adresse IP dans une période de temps|Port|
|ByteCount|Nombre d’octets|Nombre|Total|Nombre total d’octets transmis dans une période de temps|Port, Direction|
|PacketCount|Nombre de paquets|Nombre|Total|Nombre total de paquets transmis dans une période de temps|Port, Direction|
|SynCount|Nombre de SYN|Nombre|Total|Nombre total de paquets SYN transmis dans une période de temps|Port, Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Throughput|Throughput|BytesPerSecond|Moyenne|Nombre d’octets par seconde servis par Application Gateway|Aucune dimension|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Bande passante de tunnel|BytesPerSecond|Moyenne|Bande passante moyenne d’un tunnel en octets par seconde|ConnectionName, RemoteIP|
|TunnelPeakBandwidth|Bande passante maximale de tunnel|BytesPerSecond|Moyenne|Bande passante maximale d’un tunnel en octets par seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Octets de sortie de tunnel|Octets|Moyenne|Octets sortants d’un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|
|TunnelIngressBytes|Octets d’entrée de tunnel|Octets|Moyenne|Octets entrants d’un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|
|TunnelEgressPackets|Paquets de sortie de tunnel|Nombre|Moyenne|Nombre de paquets sortants d’un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|
|TunnelIngressPackets|Paquets en entrée de tunnel|Nombre|Moyenne|Nombre de paquets entrants d’un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Rejet de paquets TS de sortie de tunnel pour incompatibilité|Nombre|Moyenne|Rejet de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Rejet de paquets TS d’entrée de tunnel pour incompatibilité|Nombre|Moyenne|Rejet de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel dans un intervalle de cinq minutes|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesIn|BytesIn|Nombre|Total|Octets entrant dans Azure|Aucune dimension|
|BytesOut|BytesOut|Nombre|Total|Octets sortant d’Azure|Aucune dimension|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Moyenne|Bits entrant dans Azure par seconde|Aucune dimension|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Moyenne|Bits sortant d’Azure par seconde|Aucune dimension|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Requêtes par point de terminaison renvoyé|Nombre|Total|Nombre de fois où un point de terminaison Traffic Manager a été renvoyé dans le laps de temps donné|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|État du point de terminaison par point de terminaison|Nombre|Maximale|1 si l’état de sondage d’un point de terminaison est « activé », 0 dans le cas contraire.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|registration.all|Opérations d’inscription|Nombre|Total|Total des opérations d’inscription réussies (requêtes de mises à jour de créations et suppressions). |Aucune dimension|
|registration.create|Opérations de création d’inscription|Nombre|Total|Total des créations d’inscription réussies.|Aucune dimension|
|registration.update|Opérations de mise à jour d’inscription|Nombre|Total|Total des mises à jour d’inscription réussies.|Aucune dimension|
|registration.get|Opérations de lecture d’inscription|Nombre|Total|Total des requêtes d’inscription réussies.|Aucune dimension|
|registration.delete|Opérations de suppression d’inscription|Nombre|Total|Total des suppressions d’inscription réussies.|Aucune dimension|
|incoming|Messages entrants|Nombre|Total|Total des appels d’API d’envoi réussis. |Aucune dimension|
|incoming.scheduled|Notifications Push planifiées envoyées|Nombre|Total|Notifications Push planifiées annulées|Aucune dimension|
|incoming.scheduled.cancel|Notifications Push planifiées annulées|Nombre|Total|Notifications Push planifiées annulées|Aucune dimension|
|scheduled.pending|Notifications planifiées en attente|Nombre|Total|Notifications planifiées en attente|Aucune dimension|
|installation.all|Opérations de gestion de l’installation|Nombre|Total|Opérations de gestion de l’installation|Aucune dimension|
|installation.get|Opérations d’obtention d’installation|Nombre|Total|Opérations d’obtention d’installation|Aucune dimension|
|installation.upsert|Opérations de création ou de mise à jour d’installation|Nombre|Total|Opérations de création ou de mise à jour d’installation|Aucune dimension|
|installation.patch|Opérations d’installation de correctif|Nombre|Total|Opérations d’installation de correctif|Aucune dimension|
|installation.delete|Opérations de suppression d’installation|Nombre|Total|Opérations de suppression d’installation|Aucune dimension|
|outgoing.allpns.success|Notifications réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.allpns.invalidpayload|Erreurs de charge utile|Nombre|Total|Nombre d’opérations Push en échec, car le PNS a retourné une erreur de charge utile incorrecte.|Aucune dimension|
|outgoing.allpns.pnserror|Erreurs système de notification externe|Nombre|Total|Nombre d’opérations Push en échec en raison d’un problème de communication avec le PNS (à l’exclusion des problèmes d’authentification).|Aucune dimension|
|outgoing.allpns.channelerror|Erreurs de canal|Nombre|Total|Nombre d’opérations Push en échec, car le canal n’était pas valide ni associé à l’application appropriée, limité ou arrivé à expiration.|Aucune dimension|
|outgoing.allpns.badorexpiredchannel|Erreurs de canal incorrect ou arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car le canal/jeton/registrationId indiqué dans l’inscription est arrivé à expiration ou non valide.|Aucune dimension|
|outgoing.wns.success|Notifications WNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.wns.invalidcredentials|Erreurs d’autorisation WNS (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci étaient bloquées. (Windows Live ne reconnaît pas les informations d’identification).|Aucune dimension|
|outgoing.wns.badchannel|Erreur de canal WNS incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI indiqué dans l’inscription n’a pas été reconnu (état WNS : 404 introuvable).|Aucune dimension|
|outgoing.wns.expiredchannel|Erreur de canal WNS arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI est arrivé à expiration (état WNS : 410 Supprimé).|Aucune dimension|
|outgoing.wns.throttled|Notifications WNS limitées|Nombre|Total|Nombre d’opérations Push en échec, car WNS limite cette application (état WNS : 406 Non accepté).|Aucune dimension|
|outgoing.wns.tokenproviderunreachable|Erreurs d’autorisation WNS (inaccessible)|Nombre|Total|Windows Live n’est pas accessible.|Aucune dimension|
|outgoing.wns.invalidtoken|Erreurs d’autorisation WNS (jeton non valide)|Nombre|Total|Le jeton fourni à WNS n’est pas valide (état WNS : 401 Non autorisé).|Aucune dimension|
|outgoing.wns.wrongtoken|Erreurs d’autorisation WNS (jeton incorrect)|Nombre|Total|Le jeton fourni à WNS est valide, mais il concerne une autre application (état WNS : 403 Interdit). Cela peut se produire si l’élément ChannelURI indiqué dans l’inscription est associé à une autre application. Vérifiez que l’application cliente est associée à l’application dont les informations d’identification figurent dans le hub de notification.|Aucune dimension|
|outgoing.wns.invalidnotificationformat|Format de notification WNS non valide|Nombre|Total|Le format de la notification n’est pas valide (état WNS : 400). Notez que WNS ne rejette pas toutes les charges utiles non valides.|Aucune dimension|
|outgoing.wns.invalidnotificationsize|Erreur de taille de notification WNS non valide|Nombre|Total|La charge utile de notification est trop grande (état WNS : 413).|Aucune dimension|
|outgoing.wns.channelthrottled|Canal WNS limité|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (en-tête de réponse WNS : X-WNS-NotificationStatus:channelThrottled).|Aucune dimension|
|outgoing.wns.channeldisconnected|Canal WNS déconnecté|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI contenu dans l’inscription est limité (en-tête de réponse WNS : X-WNS-DeviceConnectionStatus : déconnecté).|Aucune dimension|
|outgoing.wns.dropped|Notifications WNS supprimées|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (X-WNS-NotificationStatus : supprimé, mais pas X-WNS-DeviceConnectionStatus : déconnecté).|Aucune dimension|
|outgoing.wns.pnserror|Erreurs WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec WNS.|Aucune dimension|
|outgoing.wns.authenticationerror|Erreurs d’authentification WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec Windows Live, d’informations d’identification non valides ou d’un jeton incorrect.|Aucune dimension|
|outgoing.apns.success|Notifications APNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.apns.invalidcredentials|Erreurs d’autorisation APNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.apns.badchannel|Erreur de canal APNS incorrect|Nombre|Total|Nombre d’opérations Push en échec, car le jeton n’est pas valide (code d’état APNS : 8).|Aucune dimension|
|outgoing.apns.expiredchannel|Erreur de canal APNS arrivé à expiration|Nombre|Total|Nombre de jetons qui ont été invalidés par le canal de commentaires APNS.|Aucune dimension|
|outgoing.apns.invalidnotificationsize|Erreur de taille de notification APNS non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (code d’état APNS : 7).|Aucune dimension|
|outgoing.apns.pnserror|Erreurs APNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec APNS.|Aucune dimension|
|outgoing.gcm.success|Notifications GCM réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.gcm.invalidcredentials|Erreurs d’autorisation GCM (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.gcm.badchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’a pas été reconnu (résultat GCM : inscription non valide).|Aucune dimension|
|outgoing.gcm.expiredchannel|Erreur de canal GCM arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription est arrivé à expiration (résultat GCM : NotRegistered).|Aucune dimension|
|outgoing.gcm.throttled|Notifications GCM limitées|Nombre|Total|Nombre d’opérations Push en échec, car GCM a limité cette application (code d’état GCM : 501-599 ou résultat : indisponible).|Aucune dimension|
|outgoing.gcm.invalidnotificationformat|Format de notification GCM non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile n’était pas mise en forme correctement (résultat GCM : InvalidDataKey ou InvalidTtl).|Aucune dimension|
|outgoing.gcm.invalidnotificationsize|Erreur de taille de notification GCM non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (résultat GCM : MessageTooBig).|Aucune dimension|
|outgoing.gcm.wrongchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’est pas associé à l’application actuelle (résultat GCM : InvalidPackageName).|Aucune dimension|
|outgoing.gcm.pnserror|Erreurs GCM|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec GCM.|Aucune dimension|
|outgoing.gcm.authenticationerror|Erreurs d’authentification GCM|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies, celles-ci sont bloquées ou l’élément SenderId n’est pas configuré correctement dans l’application (résultat GCM : MismatchedSenderId).|Aucune dimension|
|outgoing.mpns.success|Notifications MPNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.mpns.invalidcredentials|Informations d’identification MPNS non valides|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.mpns.badchannel|Erreur de canal incorrect MPNS|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription n’a pas été reconnu (état MPNS : 404 introuvable).|Aucune dimension|
|outgoing.mpns.throttled|Notifications MPNS limitées|Nombre|Total|Nombre d’opérations Push en échec, car MPNS limite cette application (WNS MPNS : 406 Non accepté).|Aucune dimension|
|outgoing.mpns.invalidnotificationformat|Format de notification MPNS non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile de la notification était trop importante.|Aucune dimension|
|outgoing.mpns.channeldisconnected|Canal MPNS déconnecté|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription était déconnecté (état MPNS : 412 introuvable).|Aucune dimension|
|outgoing.mpns.dropped|Notifications MPNS supprimées|Nombre|Total|Nombre d’opérations Push qui ont été supprimées par MPNS (en-tête de réponse MPNS : X-NotificationStatus : QueueFull ou Suppressed).|Aucune dimension|
|outgoing.mpns.pnserror|Erreurs MPNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec MPNS.|Aucune dimension|
|outgoing.mpns.authenticationerror|Erreurs d’authentification MPNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|notificationhub.pushes|Toutes les notifications sortantes|Nombre|Total|Toutes les notifications sortantes du hub de notification|Aucune dimension|
|incoming.all.requests|Toutes les demandes entrantes|Nombre|Total|Nombre total des demandes entrantes pour un hub de notification|Aucune dimension|
|incoming.all.failedrequests|Toutes les requêtes entrantes ayant échoué|Nombre|Total|Nombre total des demandes entrantes ayant échoué pour un hub de notification|Aucune dimension|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche du service de recherche|Aucune dimension|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|Aucune dimension|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche limitées|Pourcentage|Moyenne|Pourcentage de requêtes de recherche limitées par le service de recherche|Aucune dimension|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CPUXNS|Utilisation du processeur par espace de noms|Pourcentage|Maximale|Mesure d’utilisation du processeur de l’espace de noms Service Bus Premium|Aucune dimension|
|WSXNS|Utilisation de la taille mémoire par espace de noms|Pourcentage|Maximale|Mesure d’utilisation de la mémoire de l’espace de noms Service Bus Premium|Aucune dimension|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|storage|Taille de base de données totale|Octets|Maximale|Taille de base de données totale|Aucune dimension|
|connection_successful|Connexions réussies|Nombre|Total|Connexions réussies|Aucune dimension|
|connection_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|Aucune dimension|
|blocked_by_firewall|Bloqué par le pare-feu|Nombre|Total|Bloqué par le pare-feu|Aucune dimension|
|deadlock|Blocages|Nombre|Total|Blocages|Aucune dimension|
|storage_percent|Pourcentage de la taille de la base de données|Pourcentage|Maximale|Pourcentage de la taille de la base de données|Aucune dimension|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|dtu_limit|Limite DTU|Nombre|Moyenne|Limite DTU|Aucune dimension|
|dtu_used|DTU utilisé|Nombre|Moyenne|DTU utilisé|Aucune dimension|
|dwu_limit|Limite DWU|Nombre|Maximale|Limite DWU|Aucune dimension|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Maximale|Pourcentage DWU|Aucune dimension|
|dwu_used|DWU utilisé|Nombre|Maximale|DWU utilisé|Aucune dimension|
|dw_cpu_percent|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|dw_logical_node_id|
|dw_physical_data_read_percent|Pourcentage E/S des données au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage E/S des données au niveau du nœud DW|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|database_cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|DatabaseResourceId|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|database_physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|DatabaseResourceId|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|database_log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|DatabaseResourceId|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|DatabaseResourceId|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|database_workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|DatabaseResourceId|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|database_sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Nombre|Moyenne|Limite eDTU|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|eDTU_used|eDTU utilisé|Nombre|Moyenne|eDTU utilisé|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|database_storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|DatabaseResourceId|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|ElasticPoolResourceId|
|database_storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacité utilisée|Octets|Moyenne|Capacité de compte utilisée|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacité d’objet blob|Octets|Moyenne|Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.|/BlobType|
|BlobCount|Nombre d’objets blob|Nombre|Moyenne|Nombre d’objets blob dans le service BLOB du compte de stockage.|/BlobType|
|ContainerCount|Nombre de conteneurs d’objets blob|Nombre|Moyenne|Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacité de la table|Octets|Moyenne|Quantité de stockage utilisée par le service de Table du compte de stockage, en octets.|Aucune dimension|
|TableCount|Nombre de tables|Nombre|Moyenne|Nombre de tables dans le service de Table du compte de stockage.|Aucune dimension|
|TableEntityCount|Nombre d’entités de table|Nombre|Moyenne|Nombre d’entités de table dans le service de Table du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacité de la file d’attente|Octets|Moyenne|Quantité de stockage utilisée par le service de File d’attente du compte de stockage, en octets.|Aucune dimension|
|QueueCount|Nombre de files d’attente|Nombre|Moyenne|Nombre de files d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|QueueMessageCount|Nombre de messages dans la file d’attente|Nombre|Moyenne|Nombre approximatif de messages en file d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacité de fichiers|Octets|Moyenne|Quantité de stockage utilisée par le service de Fichier du compte de stockage, en octets.|Aucune dimension|
|FileCount|Nombre de fichiers|Nombre|Moyenne|Nombre de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|FileShareCount|Nombre de partages de fichiers|Nombre|Moyenne|Nombre de partage de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|Utilisation de % d’unités de diffusion|Pourcentage|Maximale|Utilisation de % d’unités de diffusion|Aucune dimension|
|InputEvents|Événements d’entrée|Nombre|Total|Événements d’entrée|Aucune dimension|
|InputEventBytes|Octets des événements d’entrée|Octets|Total|Octets des événements d’entrée|Aucune dimension|
|LateInputEvents|Événements d’entrée tardifs|Nombre|Total|Événements d’entrée tardifs|Aucune dimension|
|OutputEvents|Événements de sortie|Nombre|Total|Événements de sortie|Aucune dimension|
|ConversionErrors|Erreurs de conversion de données|Nombre|Total|Erreurs de conversion de données|Aucune dimension|
|Errors|Erreurs d’exécution|Nombre|Total|Erreurs d’exécution|Aucune dimension|
|DroppedOrAdjustedEvents|Événements en désordre|Nombre|Total|Événements en désordre|Aucune dimension|
|AMLCalloutRequests|Requêtes de fonction|Nombre|Total|Requêtes de fonction|Aucune dimension|
|AMLCalloutFailedRequests|Requêtes de fonction ayant échoué|Nombre|Total|Requêtes de fonction ayant échoué|Aucune dimension|
|AMLCalloutInputEvents|Événements de fonction|Nombre|Total|Événements de fonction|Aucune dimension|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Total|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Total|Longueur de la file d’attente HTTP|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (à l’exclusion de Functions)

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Requests|Requests|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Moyenne|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Moyenne|Nombre d’exécutions de fonctions|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Moyenne|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Moyenne|Nombre d’exécutions de fonctions|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Requests|Requests|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Moyenne|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Moyenne|Nombre d’exécutions de fonctions|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Requests|Requests|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Total|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Total|Longueur de la file d’attente HTTP|Instance|
|ActiveRequests|Requêtes actives|Nombre|Total|Requêtes actives|Instance|
|TotalFrontEnds|Nombre total de serveurs frontaux|Nombre|Moyenne|Nombre total de serveurs frontaux|Instance|
|SmallAppServicePlanInstances|Workers de plan App Service de petite taille|Nombre|Moyenne|Workers de plan App Service de petite taille|Instance|
|MediumAppServicePlanInstances|Workers de plan App Service de taille moyenne|Nombre|Moyenne|Workers de plan App Service de taille moyenne|Instance|
|LargeAppServicePlanInstances|Workers de plan App Service de grande taille|Nombre|Moyenne|Workers de plan App Service de grande taille|Instance|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Nombre total de workers|Nombre|Moyenne|Nombre total de workers|Instance|
|WorkersAvailable|Workers disponibles|Nombre|Moyenne|Workers disponibles|Instance|
|WorkersUsed|Workers utilisés|Nombre|Moyenne|Workers utilisés|Instance|

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les mesures dans Azure Monitor](monitoring-overview-metrics.md)
* [Créer des alertes sur les mesures](insights-receive-alert-notifications.md)
* [Exporter des mesures vers le stockage, un hub d’événements ou Log Analytics](monitoring-overview-of-diagnostic-logs.md)
