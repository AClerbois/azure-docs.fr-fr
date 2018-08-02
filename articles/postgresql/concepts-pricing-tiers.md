---
title: Niveaux tarifaires pour Azure Database pour PostgreSQL
description: Cet article présente les niveaux tarifaires pour Azure Database pour PostgreSQL.
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/21/2018
ms.openlocfilehash: 7494f139f824d3794fced3a0eb4f8d676f3961f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173705"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Niveaux tarifaires d’Azure Database pour PostgreSQL

Vous pouvez créer un serveur Azure Database pour PostgreSQL dans un des trois différents niveaux tarifaires : De base, Usage général et À mémoire optimisée. Les niveaux tarifaires diffèrent par la quantité de calcul dans vCores qui peut être configurée, la mémoire par vCore et la technologie de stockage utilisée pour stocker les données. Toutes les ressources sont approvisionnées au niveau du serveur PostgreSQL. Un serveur peut avoir une ou plusieurs bases de données.

|    | **De base** | **Usage général** | **Mémoire optimisée** |
|:---|:----------|:--------------------|:---------------------|
| Génération de calcul | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Mémoire par vCore | 2 Go | 5 GO | 10 Go |
| Taille de stockage | 5 Go à 1 To | 5 Go à 4 To | 5 Go à 4 To |
| Type de stockage | Stockage Azure Standard | Stockage Premium Azure | Stockage Premium Azure |
| Période de rétention de sauvegarde de bases de données | 7 à 35 jours | 7 à 35 jours | 7 à 35 jours |

Pour choisir un niveau tarifaire, utilisez le tableau suivant comme point de départ.

| Niveau tarifaire | Charges de travail cibles |
|:-------------|:-----------------|
| De base | Charges de travail qui nécessitent des performances légères en termes de calcul et d’E/S. Il s’agit, par exemple, de serveurs utilisés pour le développement ou le test, ou pour des applications à petite échelle rarement utilisées. |
| Usage général | La plupart des charges de travail professionnelles qui nécessitent une capacité de calcul et de mémoire équilibrée avec un débit d’E/S extensible. Il s’agit, par exemple, de serveurs destinés à l’hébergement d’applications web et mobiles, ainsi que d’autres applications d’entreprise.|
| Mémoire optimisée | Charges de travail de base de données haute performance qui nécessitent des performances en mémoire suffisantes pour un traitement plus rapide des transactions et une simultanéité plus élevée. Il s’agit, par exemple, de serveurs destinés au traitement de données en temps réel et à des applications transactionnelles ou analytiques haute performance.|

Après avoir créé un serveur, le nombre de vCores, la génération du matériel et le niveau tarifaire peuvent être augmentés ou diminués (excepté à vers et à partir de la version De base) en quelques secondes. Vous pouvez également augmenter ou diminuer de manière indépendante la quantité de stockage et la période de rétention des sauvegardes sans interruption de l’application. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Pour plus d’informations, consultez la section [Ressources de mise à l’échelle](#scale-resources).


## <a name="compute-generations-and-vcores"></a>Générations de calcul et vCores

Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. Actuellement, vous pouvez choisir entre deux générations de calcul, Gen 4 et Gen 5. Les processeurs logiques Gen 4 sont basés sur des processeurs Intel E5-2673 v3 (Haswell) 2.4 GHz. Les processeurs logiques Gen 5 sont basés sur des processeurs Intel E5-2673 v4 (Broadwell) 2.3 GHz. Les processeurs Gen 4 et Gen 5 sont disponibles dans les régions suivantes (« X » indique la disponibilité). 

| **Région Azure** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Centre des États-Unis | X |  |
| Est des États-Unis | X | X |
| Est des États-Unis 2 | X | X |
| Centre-Nord des États-Unis | X | X |
| États-Unis - partie centrale méridionale | X | X |
| États-Unis de l’Ouest | X | X |
| Ouest des États-Unis 2 |  | X |
| Centre du Canada | X | X |
| Est du Canada | X | X |
| Sud du Brésil | X | X |
| Europe du Nord | X | X |
| Europe de l'Ouest |  | X |
| France-Centre |  | X |
| Ouest du Royaume-Uni |  | X |
| Sud du Royaume-Uni |  | X |
| Est de l'Asie | X | X |
| Asie du Sud-Est | X | X |
| Est de l’Australie |  | X |
| Centre de l’Australie |  | X |
| Centre de l’Australie 2 |  | X |
| Sud-est de l’Australie |  | X |
| Inde centrale | X | X |
| Inde occidentale | X | X |
| Inde du Sud |  | X |
| Est du Japon | X | X |
| Ouest du Japon | X | X |
| Centre de la Corée |  | X |
| Corée du Sud |  | X |

## <a name="storage"></a>Stockage

Le stockage que vous approvisionnez est la quantité de stockage disponible pour votre serveur Azure Database pour PostgreSQL. Le stockage est utilisé pour les fichiers de base de données, les fichiers temporaires, les journaux de transaction, et les journaux du serveur PostgreSQL. La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur votre serveur.

|    | **De base** | **Usage général** | **Mémoire optimisée** |
|:---|:----------|:--------------------|:---------------------|
| Type de stockage | Stockage Azure Standard | Stockage Premium Azure | Stockage Premium Azure |
| Taille de stockage | 5 Go à 1 To | 5 Go à 4 To | 5 Go à 4 To |
| Taille d’incrément de stockage | 1 Go | 1 Go | 1 Go |
| E/S par seconde | Variable |3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 6000 E/S par seconde | 3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 6000 E/S par seconde |

Vous pouvez ajouter une capacité de stockage supplémentaire pendant et après la création du serveur. Le niveau De base n’offre pas de garantie d’E/S par seconde. Dans les niveaux tarifaires Usage général et À mémoire optimisée, les IOPS augmentent avec la taille de stockage approvisionnée selon un ratio de 3:1.

Vous pouvez surveiller votre consommation d’E/S dans le Portail Azure ou à l’aide des commandes Azure CLI. Les métriques pertinentes à surveiller sont [la limite de stockage, le pourcentage de stockage, le stockage utilisé et le pourcentage d’E/S](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Atteindre la limite de stockage

Le serveur est marqué en lecture seule lorsque la quantité de stockage disponible est inférieure à 5 Go ou 5 % du stockage provisionné, selon la valeur qui est inférieure. Par exemple, si vous avez provisionné 100 Go de stockage, et que l’utilisation réelle dépasse 95 Go, le serveur est marqué en lecture seule. Ou, si vous avez provisionné 5 Go de stockage, le serveur est marqué en lecture seule lorsque le stockage disponible est inférieur à 250 Mo.  

Lorsque le serveur est marqué en lecture seule, toutes les sessions existantes sont déconnectées et les transactions non validées sont restaurées. Toutes les opérations d’écriture suivantes et les validations de transactions ultérieures échouent. Toutes les requêtes de lecture ultérieures fonctionnent sans interruption.  

Vous pouvez augmenter la quantité de stockage provisionné sur votre serveur ou démarrer une nouvelle session en mode lecture-écriture et supprimer des données pour récupérer du stockage disponible. L’exécution de `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` définit la session actuelle en mode lecture-écriture. Pour éviter une altération des données, n’effectuez pas d’opérations d’écriture lorsque le serveur est toujours en lecture seule.

Nous vous recommandons de configurer une alerte pour vous avertir quand votre serveur de stockage est proche du seuil afin d’éviter la mise en lecture seule. Pour plus d’informations, consultez la documentation sur la manière de [configurer une alerte](howto-alert-on-metric.md).

## <a name="backup"></a>Sauvegarde

Le service effectue automatiquement des sauvegardes de votre serveur. La période de rétention minimale pour les sauvegardes est de sept jours. Vous pouvez définir une période de rétention allant jusqu’à 35 jours. La rétention peut être ajustée à tout moment pendant la durée de vie du serveur. Vous avez le choix entre les sauvegardes géoredondantes ou localement redondantes. Les sauvegardes géoredondantes sont également stockées dans la [région associée géographiquement](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) de la région dans laquelle votre serveur a été créé. Cette redondance fournit un niveau de protection en cas de sinistre. Vous obtenez également la possibilité de restaurer votre serveur vers n’importe quelle autre région Azure dans laquelle le service est disponible avec des sauvegardes géoredondantes. Il n’est pas possible de changer entre les deux options de stockage de sauvegarde après la création du serveur.

## <a name="scale-resources"></a>Mettre les ressources à l’échelle

Après avoir créé votre serveur, vous pouvez modifier de manière indépendante les vCores, la génération du matériel, le niveau tarifaire (excepté à partir de la version De base), la quantité de stockage et la période de rétention de sauvegarde. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Le nombre de vCores peut être augmenté ou diminué. La période de rétention de sauvegarde peut être augmentée ou diminuée et va de 7 à 35 jours. La taille de stockage ne peut être qu’augmentée. La mise à l’échelle des ressources peut être effectuée par le biais du portail ou d’Azure CLI. Pour obtenir un exemple de mise à l’échelle à l’aide d’Azure CLI, consultez [Surveiller et mettre à l’échelle un serveur Azure Database pour PostgreSQL à l’aide d’Azure CLI](scripts/sample-scale-server-up-or-down.md).

Lorsque vous modifiez le nombre de vCores, la génération du matériel ou le niveau tarifaire, une copie du serveur d’origine est créée avec la nouvelle allocation du calcul. Une fois que le nouveau serveur est opérationnel, les connexions sont basculées vers le nouveau serveur. Pendant le moment durant lequel le système bascule vers le nouveau serveur, aucune nouvelle connexion ne peut être établie, et toutes les transactions non validées sont restaurées. Cette fenêtre varie, mais dans la plupart des cas elle dure moins d’une minute.

La mise à l’échelle du stockage et la modification de la période de rétention de sauvegarde sont des opérations en ligne. Aucune interruption de service n’a lieu et votre application n’est pas affectée. Comme les E/S par seconde augmentent avec la taille du stockage approvisionné, vous pouvez augmenter le nombre de E/S par seconde disponibles pour votre serveur en mettant à l’échelle l’espace de stockage.

## <a name="pricing"></a>Tarifs

Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/PostgreSQL/). Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) affiche le coût mensuel dans l’onglet **Niveau tarifaire** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir un prix estimé. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour PostgreSQL**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Créer un serveur PostgreSQL dans le portail](tutorial-design-database-using-azure-portal.md).
- Découvrez comment [Surveiller et mettre à l’échelle un serveur Azure Database pour PostgreSQL à l’aide d’Azure CLI](scripts/sample-scale-server-up-or-down.md).
- En savoir plus sur les [limitations de service](concepts-limits.md).
