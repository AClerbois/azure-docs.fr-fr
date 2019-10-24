---
title: Sélection du type de déploiement adapté à Azure Database pour MySQL
description: Cet article décrit les facteurs à prendre en compte avant de déployer Azure Database pour MySQL comme infrastructure en tant que service (IaaS) ou plateforme en tant que service (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 035a559f27d11a89dba1983f1bcaf406ef6a0d05
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331945"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Choisir l’option MySQL Server appropriée dans Azure

Avec Azure, vos charges de travail de serveur MySQL peuvent s’exécuter dans une infrastructure en tant que service (IaaS) de machine virtuelle hébergée ou dans une plateforme en tant que service (PaaS) hébergée. PaaS a plusieurs options de déploiement, et il existe des niveaux de service au sein de chaque option de déploiement. Quand vous choisissez entre IaaS et PaaS, vous devez déterminer si vous voulez gérer votre base de données, appliquer des correctifs et effectuer des sauvegardes, ou plutôt déléguer ces opérations à Azure.

Pour prendre votre décision, envisagez les deux options suivantes :

- **Azure Database pour MySQL**. Cette option est un moteur de base de données MySQL entièrement managé, basé sur la version stable de MySQL Community Edition. Cette base de données en tant que service (DBaaS) relationnelle, hébergée sur la plateforme cloud Azure, appartient à la catégorie de secteur PaaS.

  Avec une instance managée de MySQL sur Azure, vous pouvez utiliser les fonctionnalités intégrées qui nécessitent normalement une configuration étendue quand le serveur MySQL est utilisé en local ou sur une machine virtuelle Azure.

  Quand vous utilisez MySQL en tant que service, vous payez à l’utilisation avec des options de scale-up ou de scale-out pour bénéficier de plus de contrôle sans interruption de service. De plus, contrairement au serveur MySQL autonome, Azure Database pour MySQL présente des fonctionnalités supplémentaires telles que la haute disponibilité, l’intelligence et la gestion intégrées.

- **MySQL sur des machines virtuelles Azure**. Cette option s’inscrit dans la catégorie de secteur IaaS. Avec ce service, vous pouvez exécuter le serveur MySQL sur une machine virtuelle entièrement managée dans le cadre de la plateforme cloud Azure. Toutes les versions et éditions récentes de MySQL peuvent être installées sur une machine virtuelle IaaS.

  La différence la plus significative par rapport à Azure Database pour MySQL est que MySQL sur des machines virtuelles Azure offre le contrôle du moteur de base de données. Toutefois, ce contrôle est fourni au détriment de la responsabilité de gestion des machines virtuelles et de nombreuses tâches d’administration de base de données. Ces tâches incluent la maintenance et la mise à jour corrective des serveurs de base de données, la récupération des bases de données et une conception à haute disponibilité.

Le tableau suivant liste les principales différences entre ces options :

|            | Azure Database pour MySQL | MySQL sur des machines virtuelles Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contrat de niveau de service (SLA)                | Offre un contrat SLA garantissant une disponibilité de 99,99 %| Disponibilité jusqu’à 99,95 % avec deux instances ou plus dans le même groupe à haute disponibilité.<br/><br/>Disponibilité de 99,9 % avec une machine virtuelle à instance unique utilisant le stockage Premium.<br/><br/>Disponibilité de 99,99 % à l’aide de zones de disponibilité avec plusieurs instances dans plusieurs groupes à haute disponibilité.<br/><br/>Consultez le [SLA pour machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Mise à jour corrective du système d’exploitation        | Automatique  | Géré par les clients |
| Mise à jour corrective de MySQL     | Automatique  | Géré par les clients |
| Haute disponibilité | Le modèle de haute disponibilité (HA) repose sur des mécanismes de basculement intégrés au cas où une interruption se produirait au niveau du nœud. Dans ce cas, le service crée automatiquement une instance et attache le stockage à cette nouvelle instance. | Les clients conçoivent, implémentent, testent et maintiennent la haute disponibilité. Les fonctionnalités peuvent inclure le clustering de basculement AlwaysOn, la réplication de groupe AlwaysOn, la copie des journaux de transactions ou la réplication transactionnelle.|
| Redondance de zone | Actuellement non pris en charge | Les machines virtuelles Azure peuvent être configurées pour s’exécuter dans différentes zones de disponibilité. Pour une solution locale, les clients doivent créer et gérer leur propre centre de données secondaire.|
| Scénarios hybrides | La [réplication des données entrantes](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) vous permet de synchroniser les données d’un serveur MySQL externe dans le service Azure Database pour MySQL. Le serveur externe peut être hébergé localement, dans des machines virtuelles, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud.<br/><br/> La fonctionnalité de [réplica en lecture](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) vous permet de répliquer des données d’un serveur maître Azure Database pour MySQL sur jusqu’à cinq serveurs réplicas en lecture seule. Les réplicas se trouvent dans la même région Azure ou dans plusieurs régions. Les réplicas en lecture seule sont mis à jour de manière asynchrone à l’aide de la technologie de réplication binlog.<br/><br/>La réplication en lecture entre plusieurs régions est disponible en préversion publique.| Géré par les clients
| Sauvegarde et restauration | Crée automatiquement des [sauvegardes de serveur](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) et les conserve dans un stockage configuré par l’utilisateur qui est redondant localement ou géoredondant. Le service accepte les sauvegardes complètes, différentielles et de fichier journal. | Géré par les clients |
| Supervision des opérations de base de données | Offre aux clients la possibilité de [définir des alertes](https://docs.microsoft.com/azure/mysql/concepts-monitoring) sur l’opération de base de données et d’agir dès qu’un seuil est atteint. | Géré par les clients |
| Protection avancée contre les menaces | Fournit une [protection avancée contre les menaces](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Cette protection détecte les activités anormales qui indiquent des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de bases de données. | Les clients doivent établir cette protection pour se prémunir eux-mêmes.
| Récupération d'urgence | Stocke les sauvegardes automatisées dans un [stockage géoredondant ou redondant localement](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal) configuré par l’utilisateur. Les sauvegardes peuvent également restaurer un serveur à un point dans le temps. La période de conservation est comprise entre 7 et 35 jours. La restauration est effectuée à l’aide du Portail Azure. | Entièrement managée par les clients. Les responsabilités incluent entre autres la planification, le test, l’archivage, le stockage et la conservation. Une autre option consiste à utiliser un coffre Azure Recovery Services pour sauvegarder les machines virtuelles Azure et les bases de données sur les machines virtuelles. Cette option est en préversion. |
| Recommandations en matière de performances | Fournit aux clients des [recommandations sur les performances](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) basées sur les fichiers journaux d’utilisation générés par le système. Ces recommandations aident à optimiser les charges de travail. | Géré par les clients |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivations métier pour choisir PaaS ou IaaS

Plusieurs facteurs peuvent influencer votre décision quant au choix de PaaS ou IaaS pour héberger vos bases de données MySQL.

### <a name="cost"></a>Coût

Le manque de capitaux est souvent le facteur principal qui détermine la meilleure solution d’hébergement de vos bases de données. C’est vrai que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires. Cette section décrit les principes de base de facturation et de licence dans Azure qui s’appliquent à Azure Database pour MySQL et à MySQL sur des machines virtuelles Azure.

#### <a name="billing"></a>Facturation

Azure Database pour MySQL est disponible en tant que service dans plusieurs niveaux avec différents prix pour les ressources. Toutes les ressources sont facturées à un tarif horaire fixe. Pour obtenir les dernières informations sur les niveaux de service, les tailles de calcul et les quantités de stockage actuellement pris en charge, consultez [Modèle d’achat vCore](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Vous pouvez ajuster les niveaux de service et les tailles de calcul de manière dynamique pour répondre aux besoins de débit variés de votre application. Vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) standard.

Avec Azure Database pour MySQL, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données. Ces actions automatisées réduisent vos coûts d’administration. De plus, Azure Database pour MySQL offre des fonctionnalités de [sauvegarde intégrée](https://docs.microsoft.com/azure/mysql/concepts-backup). Ces fonctionnalités vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données. Au contraire, avec MySQL sur des machines virtuelles Azure, vous pouvez choisir et exécuter n’importe quelle version de MySQL. Quelle que soit la version de MySQL que vous utilisez, vous payez pour la machine virtuelle provisionnée et les coûts liés au type de licence MySQL spécifique utilisé.

Azure Database pour MySQL offre une haute disponibilité intégrée pour tout type d’interruption de niveau nœud tout en conservant le niveau SLA garanti de 99,99 % pour le service. Toutefois, pour la haute disponibilité de base de données sur les machines virtuelles, les clients doivent utiliser les options de haute disponibilité proposées sur une base de données MySQL, telles que la [réplication MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html). L’utilisation d’une option de haute disponibilité prise en charge ne fournit pas de SLA supplémentaire. Toutefois, pour des coûts supplémentaires et une plus grande charge administrative, elle vous permet d’atteindre une disponibilité de base de données supérieure à 99,99 %.

Pour plus d’informations sur les tarifs, consultez les articles suivants :
* [Tarifs Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Tarifs des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise autant à simplifier l’administration qu’à réduire son coût. Avec IaaS et PaaS, Microsoft :

- Administre l’infrastructure sous-jacente.
- Réplique automatiquement toutes les données pour assurer leur récupération d’urgence.
- Configure et met à niveau le logiciel de base de données.
- Gère l’équilibrage de charge.
- Effectue un basculement transparent en cas de défaillance d’un serveur.

La liste suivante décrit les considérations administratives pour chaque option :

* Avec Azure Database pour MySQL, vous pouvez continuer à administrer votre base de données. Mais vous n’avez plus besoin de gérer le moteur de base de données, le système d’exploitation ni le matériel. Voici quelques exemples d’éléments que vous pouvez continuer à administrer :

  - Bases de données
  - Connexion
  - Réglage des index
  - Paramétrage des requêtes
  - Audit
  - Sécurité

  Par ailleurs, la configuration de la haute disponibilité vers un autre centre de données requiert une configuration ou une administration minimale ou nulle.

* Avec MySQL sur des machines virtuelles Azure, vous disposez d’un contrôle total sur le système d’exploitation et la configuration des instances de serveur MySQL. Avec une machine virtuelle, vous décidez quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données. Vous décidez également quand installer des logiciels supplémentaires tels qu’une application antivirus. Certaines fonctionnalités automatisées simplifient considérablement la gestion des correctifs, la sauvegarde et la haute disponibilité. Vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Pour plus d’informations, consultez [Tailles des machines virtuelles et des services cloud pour Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Il est temps de migrer vers Azure

* Azure Database pour MySQL est la solution idéale pour des applications cloud quand la productivité des développeurs et la rapidité de mise sur le marché de nouvelles solutions sont essentielles. Avec des fonctionnalités de programmation similaires à DBA, le service est adapté pour les architectes et les développeurs cloud, car il tempère la nécessité de gérer le système d’exploitation et la base de données sous-jacents.

* Si vous voulez éviter la perte de temps et les coûts occasionnés par l’acquisition de nouveaux matériels sur site, MySQL sur des machines virtuelles Azure est idéal pour les applications qui exigent une base de données MySQL ou un accès aux fonctionnalités MySQL sur Windows ou Linux. Cette solution permet également d’effectuer une migration d’applications et de bases de données locales vers Azure en l’état, au cas où Azure Database pour MySQL ne conviendrait pas.

  Comme il n’est pas nécessaire de changer la présentation, l’application ni les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. À la place, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requise par la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Tarifs Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Commencer par [créer votre premier serveur](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
