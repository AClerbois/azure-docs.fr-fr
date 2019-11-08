---
title: Modèles d’achat
description: Apprenez-en davantage sur les modèles d’achat disponibles pour Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: be223245c634b8e38dc5e4c89df4c265c22c0b57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821259"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Choisir entre les modèles d’achat vCore et DTU

Azure SQL Database vous permet d’acheter facilement un moteur de base de données PaaS (Platform as a Service) entièrement managé adapté à vos besoins en terme de performances et de coûts. Selon le modèle de déploiement choisi pour Azure SQL Database, vous pouvez sélectionner le modèle d’achat qui vous convient :

- [Modèle d’achat vCore (basé sur une mémoire à tores magnétiques virtuelle)](sql-database-service-tiers-vcore.md) (recommandé). Ce modèle d’achat offre le choix entre un niveau de calcul approvisionné et un niveau de calcul serverless (sans serveur). Avec le niveau calcul provisionné, vous choisissez la quantité exacte de ressources de calcul qui sont toujours approvisionnées pour votre charge de travail. Avec le niveau de calcul serverless, vous spécifiez la mise à l’échelle automatique des ressources de calcul sur une plage de calcul configurable. Ce niveau de calcul vous permet également de suspendre et reprendre automatiquement l’activité de la base de données en fonction de l’activité de la charge de travail. Le prix unitaire de vCore par unité de temps du niveau de calcul approvisionné est inférieur à celui du niveau de calcul serverless.
- [Modèle d’achat DTU (basé sur une unité de transaction de base de données)](sql-database-service-tiers-dtu.md). Ce modèle d’achat fournit des packages de calcul et de stockage groupés et équilibrés pour les charges de travail courantes.

Différents modèles d’achat sont disponibles pour les différents modèles de déploiement Azure SQL Database :

- Les options de déploiement [Base de données unique](sql-database-single-databases-manage.md) et [Pool élastique](sql-database-elastic-pool.md) d’[Azure SQL Database](sql-database-technical-overview.md) proposent à la fois le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) et le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- L’option de déploiement [Instance gérée](sql-database-managed-instance.md) dans Azure SQL Database propose uniquement le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Le [niveau de service Hyperscale](sql-database-service-tier-hyperscale.md) est disponible pour les bases de données uniques qui utilisent le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

Le tableau et le graphique suivants comparent ces deux modèles d’achat :

|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure groupée des ressources de calcul, de stockage et d’E/S. Les tailles de calcul sont exprimées en unités de transaction de base de données (DTU) pour les bases de données uniques, et en unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour plus d’informations sur les DTU et eDTU, voir [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-purchase-models.md#dtu-based-purchasing-model).|Idéal pour les clients qui souhaitent des options de ressources simples préconfigurées.|
|Modèle vCore|Ce modèle vous permet de sélectionner indépendamment les ressources de calcul et de stockage. Le modèle d’achat vCore vous permet également d’utiliser [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![comparaison des modèles de tarification](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Coûts de calcul

### <a name="provisioned-compute-costs"></a>Coûts de calcul approvisionné

Dans le niveau de calcul approvisionné, le coût du calcul reflète la capacité de calcul totale approvisionnée pour l’application.

Dans le niveau de service Critique pour l’entreprise, nous allouons automatiquement au moins 3 réplicas. Pour refléter cette allocation supplémentaire de ressources de calcul, le prix du modèle d’achat vCore du niveau de service Critique pour l’entreprise est approximativement 2,7 fois supérieur à celui du niveau de service Usage général. Pour la même raison, le prix plus élevé du stockage par Go dans le niveau de service Critique pour l’entreprise reflète le nombre important d’E/S et la faible latence du stockage SSD.

Le coût du stockage de sauvegarde est le même pour les niveaux de service Critique pour l’entreprise et Usage général, car les deux niveaux utilisent un stockage standard.

### <a name="serverless-compute-costs"></a>Coûts de calcul serverless

Si vous souhaitez consulter une description de la méthode permettant de définir la capacité de calcul et de calculer les coûts pour le niveau de calcul serverless, veuillez consulter la page [Microsoft Azure SQL Database serverless](sql-database-serverless.md).

## <a name="storage-costs"></a>Coûts de stockage

Les divers types de stockage sont facturés différemment. Pour le stockage des données, vous êtes facturé pour le stockage approvisionné, en fonction de la taille maximale de la base de données ou du pool que vous sélectionnez. Le coût ne change pas, sauf si vous réduisez ou augmentez cette taille maximale. Le stockage de sauvegarde est associé aux sauvegardes automatisées de votre instance et il est alloué de manière dynamique. L’allongement de la période de rétention des sauvegardes a pour effet d’augmenter le volume de stockage de sauvegarde que votre instance utilise.

Par défaut, l’équivalent de 7 jours de sauvegardes automatisées de vos bases de données est copié vers un compte de stockage Blob standard de RA-GRS (stockage géographiquement redondant avec accès en lecture). Ce stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux qui sont copiés toutes les 5 minutes. La taille des journaux des transactions dépend de la fréquence de changement de la base de données. Un volume de stockage minimal égal à 100 pour cent de la taille de la base de données est fourni sans frais supplémentaires. Toute consommation supérieure du stockage de sauvegarde est facturée en Go par mois.

Pour plus d’informations sur les prix du stockage, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Un vCore représente une UC logique et offre la possibilité de choisir entre plusieurs générations de matériel et caractéristiques physiques de matériel (par exemple, le nombre de cœurs, la mémoire et la taille de stockage). Le modèle d’achat vCore apporte flexibilité, contrôle et transparence pour la consommation des ressources individuelles. C’est aussi un moyen facile de traduire les exigences des charges de travail locales pour le cloud. Ce modèle permet de sélectionner les ressources de calcul, de mémoire et de stockage en fonction des besoins de votre charge de travail.

Avec le modèle d’achat vCore, vous avez le choix entre les niveaux de service [Usage général](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) et [Critique pour l’entreprise](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) pour les [bases de données uniques](sql-database-single-database-scale.md), les [pools élastiques](sql-database-elastic-pool.md) et les [instances managées](sql-database-managed-instance.md). Pour les bases de données uniques, vous pouvez également choisir le [niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).

Le modèle d’achat vCore vous permet de choisir les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Dans le modèle d’achat vCore, vous payez pour :

- Les ressources de calcul (niveau de service + nombre de vCore quantité de mémoire + génération du matériel).
- Le type et la quantité de stockage des journaux et des données.
- Le stockage de sauvegarde (RA-GRS).

> [!IMPORTANT]
> Les ressources de calcul, les E/S, ainsi que le stockage des données et des journaux, sont facturés au niveau de chaque base de données ou au niveau du pool élastique. Le stockage de sauvegarde est facturé au niveau de chaque base de données. Pour plus d'informations sur les frais liés aux instances gérées, consultez [Instances gérées](sql-database-managed-instance.md).
> **Limitations concernant les régions :** Pour obtenir la liste des régions prises en charge, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance gérée dans une région qui n’est actuellement pas prise en charge, [envoyez une demande de support via le portail Microsoft Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Si votre base de données unique ou votre pool élastique consomme plus de 300 DTU, le passage au modèle d’achat vCore peut réduire les coûts. Vous pouvez passer au modèle vCore à l’aide de l’API de votre choix ou du portail Azure, sans aucun temps d’arrêt. Toutefois, ce passage n’est ni obligatoire ni automatique. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser.

Pour passer du modèle d’achat DTU au modèle d’achat vCore, sélectionnez la taille de calcul en suivant les règles élémentaires ci-dessous :

- Chaque tranche de 100 DTU au niveau Standard nécessite au moins 1 vCore au niveau de service Usage général.
- Chaque tranche de 120 DTU au niveau Premium nécessite au moins 1 vCore au niveau de service Critique pour l’entreprise.

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

L’unité de transaction de base de données (DTU) correspond à un mélange de mesures d’UC, de mémoire, de lectures et d’écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Si vous préférez la simplicité d’un ensemble préconfiguré et de mensualités fixes, il se peut que le modèle DTU soit plus adapté à vos besoins.

Avec le modèle d’achat DTU, vous avez le choix entre les niveaux de service De base, Standard et Premium tant pour les [bases de données uniques](sql-database-single-database-scale.md) que pour les [pools élastiques](sql-database-elastic-pool.md). Le modèle d’achat DTU n’est pas disponible pour les [instances gérées](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unités de transaction de base de données (DTU)

Pour les bases de données uniques ayant une taille de calcul spécifique et appartenant à un [niveau de service](sql-database-single-database-scale.md), Microsoft garantit un certain niveau de ressources (indépendamment de toute autre base de données dans le cloud Azure). Cela garantit un niveau de performances prévisible. La quantité de ressources allouée est calculée en tant que nombre d’unités de transactions de base de données ou DTU. Il s’agit d’une mesure groupée des ressources de calcul, de stockage et d’E/S.

Le ratio entre ces ressources est déterminé à l’origine par une [charge de travail d’évaluation de traitement transactionnel en ligne (OLTP)](sql-database-benchmark-overview.md), reflétant les charges de travail de OLTP réelles standard. Quand votre charge de travail dépasse la quantité de ces ressources, le débit est limité, ce qui entraîne un ralentissement des performances et des délais d’attente.

Les ressources que votre charge de travail utilise n’ont pas d’incidence sur les ressources disponibles pour d’autres bases de données SQL dans le cloud Azure. De même, les ressources qu’utilisent d’autres charges de travail n’ont pas d’incidence sur les ressources disponibles pour votre base de données SQL.

![cadre englobant](./media/sql-database-what-is-a-dtu/bounding-box.png)

Les DTU sont particulièrement utiles pour comprendre les ressources relatives allouées aux bases de données Azure SQL de tailles de calcul et de niveaux de service variables. Par exemple :

- Le fait de doubler les DTU en augmentant la taille de calcul d’une base de données revient à doubler l’ensemble des ressources disponibles pour cette base de données.
- Une base de données P11 de niveau de service Premium comprenant 1750 DTU fournit une puissance de calcul DTU 350 fois supérieure à celle d’une base de données de base comprenant 5 DTU.  

Afin d’avoir une idée plus précise de la consommation de ressources (DTU) de votre charge de travail, utilisez l’[analyse des performances des requêtes](sql-database-query-performance.md) pour :

- Identifier les principales requêtes par UC/durée/nombre d’exécutions qui peuvent être réglées pour améliorer les performances. Par exemple, une requête nécessitant beaucoup d’E/S peut tirer profit de l’utilisation de [techniques d’optimisation en mémoire](sql-database-in-memory.md) pour mieux utiliser la mémoire disponible avec un certain niveau de service et une certaine taille de calcul.
- Explorer au niveau du détail une requête afin d’afficher son texte et son historique d’utilisation des ressources.
- Accéder aux recommandations de réglage des performances qui indiquent les actions effectuées par [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unités de transaction de base de données élastique (eDTU)

Pour les bases de données SQL toujours disponibles, au lieu de fournir un ensemble dédié de ressources (DTU) qui pourraient ne pas être toujours nécessaires, vous pouvez placer ces bases dans un [pool élastique](sql-database-elastic-pool.md). Les bases de données d’un pool élastique se trouvent sur un seul serveur Azure SQL Database et partagent un pool de ressources.

Les ressources partagées dans un pool élastique sont mesurées en unités de transaction de base de données élastique (eDTU). Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et non prévisibles. Un pool élastique garantit que les ressources ne peuvent pas être entièrement consommées par une base de données dans le pool, et aussi que chaque base de données dans le pool dispose toujours de la quantité minimale de ressources nécessaires.

Un pool bénéficie d’un nombre défini d’eDTU pour un prix donné. Au sein du pool élastique, les différentes bases de données peuvent se mettre à l’échelle automatiquement dans le respect des limites définies. Une base de données soumise à une charge plus élevée consomme plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges plus légères consomment moins d’eDTU. Les bases de données qui ne subissent aucune charge ne consomment aucune eDTU. Les ressources étant approvisionnées pour le pool entier plutôt que pour chaque base de données, les pools élastiques simplifient vos tâches de gestion et fournissent un budget prévisible pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données du pool connaissent de temps d’arrêt ou soient affectées. De même, si vous n’avez plus besoin d’eDTU supplémentaires, supprimez-les d’un pool existant à tout moment. Vous pouvez également ajouter ou supprimer des bases de données dans un pool à tout moment. Pour réserver des eDTU pour d’autres bases de données, limitez le nombre d’eDTU qu’une base de données peut utiliser sous une charge importante. Si une base de données sous-utilise constamment les ressources, déplacez-la hors du pool et configurez-la en tant que base de données unique avec une quantité prévisible de ressources requises.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Déterminer le nombre de DTU requises par une charge de travail

Si vous souhaitez migrer une charge de travail de machine virtuelle SQL Server ou locale existante vers Azure SQL Database, utilisez la [calculatrice de DTU](https://dtucalculator.azurewebsites.net/) pour évaluer approximativement le nombre de DTU requises. Dans le cas d’une charge de travail Azure SQL Database existante, utilisez l’[analyse des performances des requêtes](sql-database-query-performance.md) pour évaluer la consommation de ressources (DTU) de votre base de données et obtenir une analyse plus approfondie afin d’optimiser votre charge de travail. La vue de gestion dynamique (DMV) [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vous permet de voir la consommation des ressources au cours de la dernière heure. La vue de catalogue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) affiche la consommation des ressources au cours des 14 derniers jours, mais avec une précision inférieure (moyennes de cinq minutes).

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Charges de travail tirant avantage d’un pool élastique de ressources

Les pools conviennent parfaitement pour des bases de données avec une utilisation moyenne des ressources faible et des pics d’utilisation relativement peu fréquents. SQL Database évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur SQL Database existant et recommande la configuration de pool appropriée dans le portail Azure. Pour plus d’informations, voir [Quand devez-vous envisager d’utiliser un pool élastique SQL Database ?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Dois-je mettre mon application hors connexion pour convertir un niveau de service DTU en niveau de service basé vCore ?

Non. Vous n’avez pas besoin de déconnecter l’application. Les nouveaux niveaux de service offrent une méthode de conversion en ligne simple, similaire au processus actuel de conversion des bases de données de niveau de service Standard vers le niveau de service Premium, et inversement. Vous pouvez démarrer cette conversion à l’aide du portail Azure, de PowerShell, d’Azure CLI ou des API REST. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Puis-je convertir une base de données d’un niveau de service du modèle d’achat vCore vers un niveau de service du modèle d’achat DTU ?

Oui, vous pouvez facilement convertir votre base de données pour atteindre un objectif de performance pris en charge à l’aide du portail Azure, de PowerShell, d’Azure CLI, de T-SQL ou des API REST. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le modèle d’achat vCore, voir [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Pour plus d’informations sur le modèle d’achat DTU, voir [Modèle d’achat DTU](sql-database-service-tiers-dtu.md).
