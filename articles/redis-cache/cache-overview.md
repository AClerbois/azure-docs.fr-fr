---
title: Présentation du Cache Redis Azure | Microsoft Docs
description: Découvrez en quoi consiste le Cache Redis Azure et ses usages courants.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 585dcd120c42562b1520d4454f9d04e445553101
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096190"
---
# <a name="what-is-azure-redis-cache"></a>Présentation du Cache Redis Azure

Le Cache Redis Azure se base sur le [cache Redis](https://redis.io/)open source connu. Il est généralement utilisé en tant que cache pour améliorer les performances et l’évolutivité des systèmes qui reposent grandement sur des banques de données back-end. Les performances sont améliorées en copiant temporairement les données fréquemment sollicitées dans un stockage rapide situé près de l’application. Avec le [cache Redis](https://redis.io/), ce stockage rapide est localisé en mémoire avec le Cache Redis plutôt que d’être chargé à partir d’un disque par une base de données.

Le Cache Redis Azure peut également être utilisé en tant que banque de structure de données en mémoire, que base de données non relationnelle distribuée et que répartiteur de messages. Les performances des applications sont améliorées en tirant parti des performances à faible latence et à débit élevé du moteur Redis.

Le Cache Redis Azure vous donne accès à un cache Redis dédié sécurisé, géré par Microsoft, hébergé dans Azure et accessible pour toutes les applications à l’intérieur et à l’extérieur d’Azure.

## <a name="why-use-azure-redis-cache"></a>Pourquoi utiliser le Cache Redis Azure ?

Il existe de nombreux modèles courants où le Cache Redis est utilisé pour prendre en charge l’architecture applicative ou pour améliorer les performances des applications, notamment les modèles suivants :

| Modèle      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Étant donné qu’une base de données peut être volumineuse, il est déconseillé de charger une base de données entière dans un cache. Le modèle [Cache-Aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) est couramment utilisé pour charger des éléments de données dans le cache uniquement en cas de besoin. Quand le système modifie les données back-end, il peut à ce moment-là également mettre à jour le cache, qui est distribué avec d’autres clients. En outre, le système peut définir un délai d’expiration pour les éléments de données ou utiliser une stratégie d’éviction pour que les mises à jour soient rechargées dans le cache.|
| [Mise en cache du contenu](cache-aspnet-output-cache-provider.md) | La plupart des pages web sont générées à partir de modèles présentant des en-têtes, des pieds de page, des barres d’outils, des menus, etc. Elles ne changent pas souvent et ne doivent pas être générées dynamiquement. L’utilisation d’un cache en mémoire, comme le Cache Redis Azure, permettra à vos serveurs web d’accéder plus rapidement à ce contenu statique qu’avec des banques de données back-end. Ce modèle réduit le temps de traitement et la charge serveur qui seraient requis pour générer le contenu dynamiquement. Cela augmente la réactivité des serveurs web et peut vous permettre de réduire le nombre de serveurs nécessaires pour gérer les charges. Le Cache Redis Azure propose le fournisseur de caches de sortie Redis pour faciliter la prise en charge de ce modèle avec ASP.NET.|
| [Mise en cache des sessions utilisateur](cache-aspnet-session-state-provider.md) | Ce modèle est souvent utilisé avec les paniers d’achat et d’autres informations de type historique utilisateur qu’une application web peut associer avec les cookies des utilisateurs. Le stockage de trop nombreuses informations dans un cookie peut avoir un impact négatif sur les performances à mesure que la taille du cookie augmente, ce dernier devant être transmis et validé à chaque requête. Une solution classique consiste à utiliser le cookie en tant que clé pour interroger les données dans une base de données back-end. L’utilisation d’un cache en mémoire tel que le Cache Redis Azure pour associer des informations à un utilisateur est beaucoup plus rapide que l’interaction avec une base de données relationnelle complète. |
| Mise en file d’attente des travaux et des messages | Quand des applications reçoivent des requêtes, l’exécution des opérations associées à la requête prend souvent plus de temps. Il est courant de différer les opérations plus longues en les ajoutant à une file d’attente qui est traitée ultérieurement, potentiellement par un autre serveur. Cette méthode est appelée « mise en file d’attente des tâches ». Il existe de nombreux composants logiciels conçus pour prendre en charge les files d’attente de tâches. Le Cache Redis remplit également parfaitement ce rôle en tant que file d’attente distribuée.|
| Transactions distribuées | Les applications doivent souvent être en mesure d’exécuter une série de commandes sur une banque de données back-end comme une seule opération (atomique). Toutes les commandes doivent réussir, ou elles devront toutes être restaurées à leur état initial. Le Cache Redis prend en charge l’exécution d’un lot de commandes comme une seule opération sous la forme de [transactions](https://redis.io/topics/transactions). |

## <a name="azure-redis-cache-offerings"></a>Offres Cache Redis Azure

Les niveaux suivants sont proposés pour le Cache Redis Azure :

| Niveau | Description |
|---|---|
De base | Cache incluant un seul nœud. Ce niveau prend en charge plusieurs tailles de mémoire (de 250 Mo à 53 Go). Il est idéal pour le développement et les tests, ainsi que pour les charges de travail non critiques. Aucun contrat de niveau de service (SLA) ne couvre le niveau De base. |
| standard | Cache répliqué dans une configuration primaire/secondaire à deux nœuds, géré par Microsoft et assorti d’un contrat SLA garantissant une haute disponibilité (99,9 %). |
| Premium | Le niveau Premium est conçu pour les entreprises. Les caches de niveau Premium prennent en charge un plus grand nombre de fonctionnalités et assurent un débit plus élevé avec une latence plus faible. Les caches du niveau Premium sont déployés sur du matériel plus puissant offrant de meilleures performances par rapport au niveau De base ou Standard. Par conséquent, le débit d’un cache de même taille est plus élevé avec le niveau Premium qu’avec le niveau Standard. |

> [!TIP]
> Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

Vous pouvez mettre à l’échelle votre cache vers un niveau supérieur après l’avoir créé. En revanche, il n’est pas possible de le mettre à l’échelle vers un niveau inférieur. Pour connaître la procédure de mise à l’échelle, consultez [Mise à l’échelle du Cache Redis Azure](cache-how-to-scale.md) et [Automatisation d’une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Comparaison des fonctionnalités

La page [Tarification Cache Redis](https://azure.microsoft.com/pricing/details/cache/) fournit une comparaison détaillée des différents niveaux. Le tableau suivant décrit quelques-unes des fonctionnalités prises en charge par niveau :

| Description de la fonctionnalité | Premium | Standard | De base |
| ------------------- | :-----: | :------: | :---: |
| [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistance des données Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Sécurité via les règles de pare-feu](cache-configure.md#firewall) |✔|✔|✔|
| [Amélioration de la sécurité et de l’isolation avec VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Planification de mises à jour](cache-administration.md#schedule-updates) |✔|-|-|
| [Géoréplication](cache-how-to-geo-replication.md) |✔|-|-|
| [Redémarrage](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide ASP.NET](cache-web-app-howto.md) Créez une application web ASP.NET simple qui utilise un cache Redis Azure.
* [Démarrage rapide .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Créez une application .NET simple qui utilise un cache Redis Azure.
* [Démarrage rapide .NET Core](cache-dotnet-core-quickstart.md) Créez une application .NET Core simple qui utilise un cache Redis Azure.
* [Démarrage rapide Node.js](cache-nodejs-get-started.md) Créez une application Node.js simple qui utilise un cache Redis Azure.
* [Démarrage rapide Java](cache-java-get-started.md) Créez une application Java simple qui utilise un cache Redis Cache Azure.
* [Démarrage rapide Python](cache-python-get-started.md) Créez une application Python simple qui utilise un cache Redis Cache Azure.
