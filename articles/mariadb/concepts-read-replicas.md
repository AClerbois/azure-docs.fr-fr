---
title: Réplicas en lecture dans Azure Database for MariaDB
description: Cet article décrit les réplicas en lecture pour Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 8d4a7a1b176a0c232c4461c7a8cfc2b1e3faddd6
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638380"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas en lecture dans Azure Database for MariaDB

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un serveur Azure Database for MariaDB sur un serveur en lecture seule. Vous pouvez effectuer la réplication à partir du serveur maître vers cinq réplicas au maximum. Les réplicas sont mis à jour de manière asynchrone à l’aide de la technologie de réplication selon la position du fichier journal binaire (binlog) du moteur MariaDB avec ID de transaction global (GTID). Pour découvrir plus en détail la réplication binlog, consultez la [vue d’ensemble de la réplication binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Vous pouvez créer un réplica en lecture dans la même région que votre serveur maître ou dans n’importe quelle autre région Azure de votre choix. Les réplicas en lecture (même région et entre régions) sont actuellement en préversion publique.

Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire aux serveurs Azure Database for MariaDB classiques. Pour chaque réplica en lecture, vous êtes facturé en fonction de la capacité de calcul provisionnée dans les vCores et du stockage provisionné en Go/mois.

Pour en savoir plus sur la réplication GTID, veuillez consulter la [documentation sur la réplication MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Quand utiliser un réplica en lecture

La fonctionnalité de réplica en lecture contribue à améliorer les performances et la scalabilité des charges de travail nécessitant une lecture intensive. Les charges de travail de lecture peuvent être isolées sur les réplicas, tandis que les charges de travail d’écriture peuvent être dirigées vers le serveur maître.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Dans la mesure où les réplicas sont en lecture seule, ils ne réduisent pas directement les charges relatives à la capacité d’écriture sur le serveur maître. Cette fonctionnalité ne cible pas les charges de travail nécessitant une écriture intensive.

La fonctionnalité de réplica en lecture utilise la réplication asynchrone. La fonctionnalité n’est pas destinée aux scénarios de réplication synchrone. Il y aura un délai mesurable entre le maître et le réplica. Les données du réplica finissent par devenir cohérentes avec les données du serveur maître. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai.

Les réplicas en lecture peuvent améliorer votre plan de récupération d’urgence. En cas de sinistre régional et si votre serveur maître n’est pas disponible, vous pouvez diriger votre charge de travail vers un réplica dans une autre région. Pour ce faire, permettez d’abord au réplica d’accepter des écritures en utilisant la fonction Arrêter la réplication. Vous pouvez ensuite rediriger l’application en mettant à jour la chaîne de connexion. Découvrez plus d’informations dans la section relative à [l’arrêt de la réplication](#stop-replication).

## <a name="create-a-replica"></a>Créer un réplica

Si un serveur maître ne dispose d’aucun serveur réplica, le serveur maître redémarre tout d’abord afin de se préparer pour la réplication.

Quand vous démarrez le flux de travail de création de réplica, un serveur Azure Database for MariaDB vide est créé. Le nouveau serveur est rempli avec les données qui se trouvaient sur le serveur maître. Le temps de création dépend de la quantité de données présentes sur le serveur maître et du temps écoulé depuis la dernière sauvegarde complète hebdomadaire. Le temps nécessaire peut aller de quelques minutes à plusieurs heures.

> [!NOTE]
> Si aucune alerte de stockage n’est configurée sur vos serveurs, nous vous recommandons de le faire. L’alerte vous informe quand un serveur approche de sa limite de stockage, ce qui affecte la réplication.

Découvrez comment [créer un réplica en lecture dans le portail Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Se connecter à un réplica

Quand vous créez un réplica, il n’hérite pas des règles de pare-feu ni du point de terminaison de service VNet (réseau virtuel) du serveur maître. Ces règles doivent être configurés indépendamment pour le réplica.

Le réplica hérite du compte Administrateur du serveur maître. Tous les comptes d’utilisateur sur le serveur maître sont répliqués sur les réplicas en lecture. Vous pouvez uniquement vous connecter à un réplica en lecture à l’aide des comptes d’utilisateur disponibles sur le serveur maître.

Vous pouvez vous connecter au réplica à l’aide de son nom d’hôte et d’un compte d’utilisateur valide, comme vous le faites sur un serveur Azure Database for MariaDB classique. Sur un serveur nommé **myreplica**, à l’aide du nom d’utilisateur administrateur **myadmin**, vous pouvez vous connecter au réplica via l’interface de ligne de commande mysql :

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

À l’invite, entrez le mot de passe du compte d’utilisateur.

## <a name="monitor-replication"></a>Superviser la réplication

Azure Database for MariaDB fournit la métrique **Décalage de la réplication en secondes** dans Azure Monitor. Cette métrique est disponible pour les réplicas uniquement.

Cette métrique est calculée à l’aide de la métrique `seconds_behind_master` disponible dans la commande `SHOW SLAVE STATUS` de MariaDB.

Définissez une alerte qui vous informe quand le décalage de la réplication atteint une valeur inacceptable pour votre charge de travail.

## <a name="stop-replication"></a>Arrêter la réplication

Vous pouvez arrêter la réplication entre un serveur maître et un réplica. Une fois la réplication arrêtée entre un serveur maître et un réplica en lecture, celui-ci devient un serveur autonome. Les données du serveur autonome sont les données disponibles sur le réplica au lancement de la commande d’arrêt de la réplication. Le serveur autonome ne se met pas au même niveau que le serveur maître.

Lorsque vous décidez d’arrêter la réplication pour un réplica, celui-ci perd tous les liens avec son serveur maître précédent et d’autres réplicas. Il n’existe aucun basculement automatique entre un serveur maître et son réplica.

> [!IMPORTANT]
> Le serveur autonome ne peut pas être retransformé en réplica.
> Avant d’arrêter la réplication sur un réplica en lecture, vérifiez que celui-ci contient toutes les données nécessaires.

Découvrez comment [arrêter la réplication sur un réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considérations et limitations

### <a name="pricing-tiers"></a>Niveaux de tarification

Les réplicas en lecture ne sont actuellement disponibles que dans les niveaux tarifaires Usage général et Mémoire optimisée.

### <a name="master-server-restart"></a>Redémarrage du serveur maître

Lorsque vous créez un réplica pour un serveur maître qui ne dispose d’aucun réplica existant, le serveur maître commence par redémarrer pour se préparer pour la réplication. Veuillez prendre cela en compte et effectuer ces opérations pendant une période creuse.

### <a name="new-replicas"></a>Nouveaux réplicas

Un réplica en lecture est créé en tant que serveur Azure Database for MariaDB. Un serveur existant ne peut pas être transformé en réplica. Vous ne pouvez pas créer un réplica d’un autre réplica en lecture.

### <a name="replica-configuration"></a>Configuration du réplica

Un réplica est créé à partir de la même configuration que celle du serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage, période de rétention de sauvegarde et version du moteur MariaDB. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de mettre à jour une configuration de serveur maître avec de nouvelles valeurs, mettez à jour la configuration du réplica avec des valeurs égales ou supérieures. Ainsi, vous avez la garantie que le réplica peut suivre les changements apportés au maître.

### <a name="stopped-replicas"></a>Réplicas arrêtés

Si vous arrêtez la réplication entre un serveur maître et un réplica en lecture, le réplica arrêté devient un serveur autonome qui accepte aussi bien les lectures que les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

### <a name="deleted-master-and-standalone-servers"></a>Serveurs maîtres et autonomes supprimés

Quand un serveur maître est supprimé, la réplication est arrêtée sur tous les réplicas en lecture. Ces réplicas deviennent des serveurs autonomes. Le serveur maître lui-même est supprimé.

### <a name="user-accounts"></a>Comptes d'utilisateurs

Les utilisateurs sur le serveur maître sont répliqués sur les réplicas en lecture. Vous ne pouvez vous connecter à un réplica en lecture qu’avec des comptes d’utilisateur disponibles sur le serveur maître.

### <a name="server-parameters"></a>Paramètres de serveur

Pour empêcher les données de se désynchroniser et pour éviter leur perte ou leur endommagement potentiels, certains paramètres de serveur sont verrouillés et ne peuvent pas être mis à jour lors de l’utilisation des réplicas en lecture.

Les paramètres de serveur suivants sont verrouillés sur les serveurs maîtres et réplicas :
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Le paramètre [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) est verrouillé sur les serveurs réplicas.

### <a name="other"></a>Autres

- La création d’un réplica d’un réplica n’est pas prise en charge.
- Les tables en mémoire peuvent entraîner la désynchronisation des réplicas. Il s’agit d’une limitation de la technologie de réplication MariaDB.
- Vérifiez que les tables du serveur maître possèdent des clés primaires. L’absence de clés primaires peut entraîner une latence de réplication entre le maître et les répliques.

## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment [créer et gérer des réplicas en lecture clusters avec le portail Azure](howto-read-replicas-portal.md)
- Découvrir comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure](howto-read-replicas-cli.md)