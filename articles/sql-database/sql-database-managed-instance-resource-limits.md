---
title: Limites de ressources – Managed Instance
description: Cet article fournit une vue d’ensemble des limites de ressources Azure SQL Database pour les instances managées.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 08e378c4b5f6d8e17ac67a80401edf69611c0a13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823338"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Vue d’ensemble des limites de ressources Azure SQL Database Managed Instance

Cet article fournit une vue d’ensemble des caractéristiques techniques et des limites des ressources pour l’instance gérée Azure SQL Database, ainsi que des informations sur la façon de demander une augmentation de ces limites.

> [!NOTE]
> Pour connaître les différences entre les fonctionnalités prises en charge et les instructions T-SQL, consultez [Différences de fonctionnalités](sql-database-features.md) et [Prise en charge des instructions T-SQL](sql-database-managed-instance-transact-sql-information.md). Pour connaître les différences générales entre les niveaux de service dans une base de données unique et dans une instance gérée, consultez [Comparaison des niveaux de service](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Caractéristiques de la génération du matériel

L’instance gérée a des caractéristiques et des limites de ressources qui dépendent de l’infrastructure et de l’architecture sous-jacentes. L’instance gérée d’Azure SQL Database peut être déployée sur deux générations du matériel : Gen4 et Gen5. Les générations du matériel ont différentes caractéristiques, qui sont décrites dans le tableau suivant :

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Matériel | Processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz, disque SSD attaché, vCore = 1 PP (cœur physique) | Processeurs Intel E5-2673 v4 (Broadwell) 2,3 GHz, disque SSD fast NVMe, vCore = 1 LP (hyperthread) |
| Nombre de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Mémoire maximale (ratio mémoire/cœur) | 7 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | 5,1 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |
| Mémoire OLTP maximum en mémoire | Limite de l’instance : 1 à 1,5 Go par vCore| Limite de l’instance : 0,8 à 1,65 Go par vCore |
| Stockage réservé d’instance max. |  Usage général : 8 To<br/>Critique pour l’entreprise : 1 To | Usage général : 8 To<br/> Critique pour l’entreprise 1 To, 2 To ou 4 To, en fonction du nombre de cœurs |

> [!IMPORTANT]
> - Le matériel Gen4 est graduellement abandonné. Il est recommandé de déployer de nouvelles instances gérées sur du matériel Gen5.
> - Pour le moment, le matériel Gen4 n’est encore disponible que dans les régions suivantes : Europe Nord, Europe Ouest, USA Est, USA Centre Sud, USA Centre Nord, USA Ouest 2, USA Centre, Canada Centre, Inde Sud, Asie Sud-Est et Corée Centre.

### <a name="in-memory-oltp-available-space"></a>Espace disponible OLTP en mémoire 

La quantité d’espace OLTP en mémoire au niveau de service [Critique pour l’entreprise](sql-database-service-tier-business-critical.md) dépend du nombre de vCores et de la génération du matériel. Le tableau suivant répertorie les limites de mémoire utilisables pour des objets OLTP en mémoire.

| Espace OLTP en mémoire  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 Go | |   
| 8 vCores  | 6,28 Go | 8 Go |
| 16 vCores | 15,77 Go | 20 Go |
| 24 vCores | 25,25 Go | 36 Go |
| 32 vCores | 37,94 Go | |
| 40 vCores | 52,23 Go | |
| 64 vCores | 99,9 Go    | |
| 80 vCores | 131,68 Go| |

## <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

L’instance gérée a deux niveaux de service : [Usage général](sql-database-service-tier-general-purpose.md) et [Critique pour l’entreprise](sql-database-service-tier-business-critical.md). Ces niveaux offrent des [fonctionnalités différentes](sql-database-service-tiers-general-purpose-business-critical.md), comme décrit dans le tableau ci-dessous.

> [!Important]
> Le niveau de service Critique pour l’entreprise fournit une copie intégrée supplémentaire de l’instance (réplica secondaire) qui peut être utilisée pour les charges de travail en lecture seule. Si vous pouvez séparer les requêtes de lecture-écriture des requêtes en lecture seule/analytique/de création de rapports, vous obtenez deux fois plus de vCores et de mémoire pour le même prix. Le réplica secondaire peut présenter un décalage de quelques secondes par rapport à l’instance principale. Il est donc conçu pour décharger les charges de travail de création de rapports/analytiques qui n’ont pas besoin de l’état actuel des données. Dans le tableau ci-dessous, les **requêtes en lecture seule** sont les requêtes exécutées sur le réplica secondaire.

| **Fonctionnalité** | **Usage général** | **Critique pour l’entreprise** |
| --- | --- | --- |
| Nombre de vCores\* | Gen4 : 8, 16, 24<br/>Gen5 : 4, 8, 16, 24, 32, 40, 64, 80 | Gen4 : 8, 16, 24 <br/> Gen5 : 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Le même nombre de vCores est dédié pour les requêtes en lecture seule. |
| Mémoire maximale | Gen4 : 56-168 Go (7 Go/vCore)<br/>Gen5 : 20,4-408 Go (5,1 Go/vCore)<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | Gen4 : 56-168 Go (7 Go/vCore)<br/>Gen5 : 20,4 Go-408 Go (5,1 Go/vCore) pour les requêtes en lecture-écriture<br/>+ 20,4 Go-408 Go (5,1 Go/vCore) supplémentaires pour les requêtes en lecture seule.<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |
| Taille de stockage maximale d’instance (réservée) | - 2 To pour les 4 vCores (Gen5 uniquement)<br/>- 8 To pour les autres tailles | Gen4 : 1 To <br/> Gen5 : <br/>- 1 To pour 4, 8, 16 vCores<br/>- 2 To pour 24 vCores<br/>- 4 To pour 32, 40, 64, 80 vCores |
| Taille de base de données maximale | Jusqu’à la taille d’instance actuellement disponible (maximum 2-8 To en fonction du nombre de vCores). | Jusqu’à la taille d’instance actuellement disponible (maximum 1-4 To en fonction du nombre de vCores). |
| Taille maximale de tempDB | Limitée à 24 Go/vCore (96-1920 Go) et à la taille de stockage d’instance actuellement disponible.<br/>Ajoutez plus de vCores pour obtenir davantage d’espace TempDB.<br/> La taille du fichier journal est limitée à 120 Go.| Jusqu’à la taille de stockage d’instance actuellement disponible. |
| Nombre maximal de bases de données par instance | 100, sauf si la limite de taille de stockage d’instance a été atteinte. | 100, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Nombre maximal de fichiers de base de données par instance | Jusqu’à 280, sauf si la limite de taille de stockage d’instance ou d’[espace d’allocation de stockage sur disque Premium Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) a été atteinte. | 32 767 fichiers par base de données, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Taille maximale du fichier de données | Limitée à la taille de stockage d’instance actuellement disponible (maximum 2-8 To) et à l’[espace d’allocation de stockage sur disque Premium Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Limitée à la taille de stockage d’instances actuellement disponible (jusqu’à 1-4 To). |
| Taille maximale du fichier journal | Limitée à 2 To et à la taille de stockage d’instance actuellement disponible. | Limitée à 2 To et à la taille de stockage d’instance actuellement disponible. |
| IOPS de données/journal (approximatives) | Jusqu’à 30-40 K IOPS par instance*, 500-7500 par fichier<br/>\*[Augmentez la taille de fichier pour obtenir davantage d’IOPS](#file-io-characteristics-in-general-purpose-tier)| 5,5 K-110 K (1375 IOPS/vCore)<br/>Ajoutez plus de vCores pour obtenir de meilleures performances d’E/S. |
| Limite du débit d’écriture du journal (par instance) | 3 Mo/s par vCore<br/>22 Mo/s max. | 4 Mo/s par vCore<br/>48 Mo/s max. |
| Débit de données (approximatif) | 100 - 250 Mo/s par fichier<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](#file-io-characteristics-in-general-purpose-tier) | Non limité. |
| Latence d’E/S de stockage (approximative) | 5 - 10 ms | 1 - 2 ms |
| OLTP en mémoire | Non pris en charge | Disponible, [la taille dépend du nombre de vCores](#in-memory-oltp-available-space) |
| Nombre maximal de sessions | 30000 | 30000 |
| [Réplicas en lecture seule](sql-database-read-scale-out.md) | 0 | 1 (inclus dans le prix) |

> [!NOTE]
> - **La taille de stockage d’instance actuellement disponible** est la différence entre la taille d’instance réservée et l’espace de stockage utilisé.
> - Les tailles des données et des fichiers journaux dans les bases de données utilisateur et système sont comprises dans la taille de stockage d’instance qui est comparée à la limite de taille de stockage maximale. Utilisez la vue système <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> pour déterminer l’espace total utilisé par les bases de données. Les journaux d’activité d’erreurs ne sont ni conservés ni compris dans la taille. Les sauvegardes ne sont pas comprises dans la taille de stockage.
> - Le débit et les IOPS sur le niveau Usage général dépendent également de la [taille de page](#file-io-characteristics-in-general-purpose-tier) qui n’est pas explicitement limitée par l’instance gérée.
> - Vous pouvez créer un autre réplica lisible dans une région Azure différente à l’aide de groupes de basculement automatique.
> - Le nombre maximal d’IOPS d’instance dépend de la disposition des fichiers et de la distribution de la charge de travail. Par exemple, si vous créez sept fichiers de 1 Go avec un maximum de 5 000 IOPS et sept fichiers de petite taille (moins de 128 Go) avec 500 IOPS chacun, vous pouvez vous obtenir 38 500 IOPS par instance (7x5000+7x500) si votre charge de travail peut utiliser tous les fichiers. Notez qu’un certain nombre d’IOPS sont également utilisés pour les sauvegardes automatiques.

> [!NOTE]
> Vous trouverez plus d’informations sur les [limites des ressources dans les pools d’instances managées dans cet article](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caractéristiques d’E/S de fichier avec le niveau Usage général

Avec le niveau de service Usage général, chaque fichier de base de données reçoit des IOPS et un débit dédiés qui dépendent de la taille du fichier. Les fichiers plus volumineux reçoivent plus d’IOPS et de débit. Les caractéristiques d’E/S des fichiers de base de données sont indiquées dans le tableau suivant :

| Taille du fichier           | 0 - 128 Gio | 128 - 256 Gio | 256 - 512 Gio | 0,5 - 1 Tio    | 1 - 2 Tio    | 2 - 4 Tio | 4 - 8 Tio |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS par fichier       | 500   | 1100 | 2 300              | 5 000              | 7500              | 7500              | 12 500   |
| Débit par fichier | 100 Mio/s | 125 Mio/s | 150 Mio/s | 200 Mio/s | 250 Mio/s | 250 Mio/s | 480 Mio/s | 

Si vous constatez une latence élevée des E/S sur un fichier de base de données, ou si vous constatez que les IOPS ou le débit atteignent la limite, vous pouvez améliorer les performances en [augmentant la taille de fichier](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Il existe également des limites au niveau de l’instance, telles que le débit max d’écriture dans le journal (22 Mo/s) ; ainsi, vous ne pourrez peut-être pas atteindre le débit de fichier dans le fichier journal car vous atteignez la limite du débit d’instance.

## <a name="supported-regions"></a>Régions prises en charge

Les instances gérées peuvent être créée uniquement dans des [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance gérée dans une région qui n’est actuellement pas prise en charge, vous pouvez [envoyer une demande de support par le biais du Portail Microsoft Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

L’instance gérée prend actuellement en charge le déploiement uniquement sur les types d’abonnements suivants :

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fournisseur de services cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnements avec crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitations des ressources régionales

Les types d’abonnements pris en charge peuvent contenir un nombre limité de ressources par région. Une instance managée a deux limites par défaut par région Azure (qui peuvent être augmentées à la demande en créant une [demande spéciale de support dans le portail Azure](#obtaining-a-larger-quota-for-sql-managed-instance)) en fonction du type d’abonnement :

- **Limite de sous-réseaux** : nombre maximal de sous-réseaux dans lesquels des instances gérées sont déployées dans une seule et même région.
- **Limite d’unités de vCore** : nombre maximal d’unités de vCores qui peuvent être déployées parmi toutes les instances dans une seule région. Un vCore GP utilise une unité de vCore et un vCore BC prend quatre unités de vCore. Le nombre total d’instances n’est pas limité du moment qu’il se trouve dans la limite du nombre d’unités de vCores.

> [!Note]
> Ces limites sont des paramètres par défaut : il ne s’agit pas de limitations techniques. Ces limites peuvent être augmentées en créant une [demande de support spéciale dans le Portail Microsoft Azure](#obtaining-a-larger-quota-for-sql-managed-instance) si vous avez besoin de plus d’instances gérées dans la région actuelle. Vous pouvez aussi créer des instances gérées dans une autre région Azure sans envoyer de demandes de support.

Le tableau suivant montre les **limites régionales par défaut** pour les types d’abonnement pris en charge (les limites par défaut peuvent être étendues à l’aide de la demande de support décrite ci-dessous) :

|Type d’abonnement| Nombre maximal de sous-réseaux d’instances gérées | Nombre maximal d’unités de vCore* |
| :---| :--- | :--- |
|Paiement à l’utilisation|3|320|
|CSP |8 (15 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|Pay-As-You-Go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional et plateformes MSDN|2|32|

\* Lors de la planification de déploiements, prenez en considération le fait que le niveau de service critique pour l’entreprise (BC) requiert quatre (4) fois plus de capacité vCore que le niveau de service usage général (GP). Par exemple :  1 GP vCore = 1 unité vCore et 1 BC vCore = 4 unités vCore. Pour simplifier votre analyse de la consommation par rapport aux limites par défaut, récapitulez les unités vCore de tous les sous-réseaux de la région où les instances gérées sont déployées et comparez les résultats avec les limites d’unités d’instance pour votre type d’abonnement. La limite **Nombre maximal d’unités de vCore** s’applique à chaque abonnement dans une région. Il n’y a pas de limite par sous-réseau individuel sauf que la somme de tous les vCores déployés sur plusieurs sous-réseaux doit être inférieure ou égale à **nombre maximum d’unités vCore**.

\*\* Des limites de sous-réseau et de vCore plus importantes s’appliquent dans les régions suivantes : Australie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, USA Ouest 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtention d’un quota plus élevé pour votre instance managée SQL

Si vous avez besoin de davantage d’instances gérées dans vos régions actuelles, vous pouvez envoyer une demande de support pour étendre le quota à l’aide du Portail Microsoft Azure.
Pour lancer le processus d’obtention d’un quota plus élevé :

1. Ouvrez **Aide + Support**, puis cliquez sur **Nouvelle demande de support**.

   ![Aide et support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Sous l’onglet De base de la nouvelle demande de support :
   - Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .
   - Pour **Abonnement**, sélectionnez votre abonnement.
   - Pour **Type de quota**, sélectionnez **SQL Database Managed Instance**.
   - Pour **Plan de support**, sélectionnez votre plan de support.

     ![Type de problème : Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Cliquez sur **Suivant**.
4. Sous l’onglet **Problème** de la nouvelle demande de support :
   - Pour **Gravité**, sélectionnez le niveau de gravité du problème.
   - Pour **Détails**, fournissez des informations supplémentaires concernant votre problème, notamment les messages d’erreur.
   - Pour **Chargement de fichier**, joignez un fichier contenant plus d’informations (jusqu’à 4 Mo).

     ![Détails du problème](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Une demande valide doit inclure les éléments suivants :
     > - Région dans laquelle la limite d’abonnement doit être augmentée.
     > - Nombre nécessaire de vCores, par niveau de service dans les sous-réseaux existants après l’augmentation du quota (si l’un des sous-réseaux existants doit être développé).
     > - Nombre nécessaire de nouveaux sous-réseaux et nombre total de vCores par niveau de service dans les nouveaux sous-réseaux (si vous devez déployer des instances gérées dans les nouveaux sous-réseaux).

5. Cliquez sur **Suivant**.
6. Sous l’onglet Informations de contact pour la nouvelle demande de support, entrez la méthode de communication préférée (e-mail ou téléphone) et les détails du contact.
7. Cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’instance managée, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour des informations sur les prix, consultez [Tarifs des instances managées SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
