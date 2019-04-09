---
title: Vue d’ensemble d’Advanced Data Security pour Azure SQL Database | Microsoft Docs
description: Cette rubrique décrit Advanced Data Security pour Azure SQL Database et explique son fonctionnement ainsi que ses différences par rapport à une base de données unique ou en pool dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: f1e86e4556931c00d317a618eeaf35ac988a2879
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266237"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Utiliser Advanced Data Security pour SQL Database avec des réseaux virtuels et une compatibilité de presque 100 %

L’instance managée est une nouvelle option de déploiement d’Azure SQL Database, presque 100 % compatible avec le dernier moteur de base de données local SQL Server (Édition Entreprise), qui fournit une implémentation de [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) native traitant les problèmes de sécurité courants, ainsi qu’un [modèle d’entreprise](https://azure.microsoft.com/pricing/details/sql-database/) favorable aux clients du serveur SQL Server local. Le modèle de déploiement d’instance managée permet aux clients de SQL Server existants d’effectuer une migration « lift-and-shift » de leurs applications locales vers le cloud en changeant le moins possible les applications et bases de données. En même temps, l’option de déploiement d’instance managée conserve toutes les fonctionnalités PaaS (correctifs et mises à jour de versions automatiques, [sauvegardes automatisées](sql-database-automated-backups.md), [haute disponibilité](sql-database-high-availability.md)), ce qui réduit considérablement le temps de gestion et le coût total de possession (TCO).

> [!IMPORTANT]
> Pour obtenir la liste des régions où l’option de déploiement d’instance managée est actuellement disponible, consultez les [régions prises en charge](sql-database-managed-instance-resource-limits.md#supported-regions).

Le diagramme suivant présente les principales fonctionnalités des instances managées :

![fonctionnalités clés](./media/sql-database-managed-instance/key-features.png)

Le modèle de déploiement d’instance managée est conçu pour les clients qui veulent migrer un grand nombre d’applications locales ou provenant d’environnements IaaS, générés automatiquement ou fournis par un éditeur de logiciels indépendant, vers un environnement cloud PaaS complètement managé, avec le moins d’efforts de migration possible. Avec le service [Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) entièrement automatisé dans Azure, les clients peuvent effectuer une migration « lift-and-shift » de leur serveur SQL Server local vers une instance managée compatible avec le SQL Server local et qui offre une isolation totale des instances des clients avec une prise en charge native des réseaux virtuels.  Avec Software Assurance, vous pouvez échanger leurs licences existantes contre des tarifs réduits sur une instance managée à l’aide d’[Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Une instance managée est la meilleure destination de migration dans le cloud pour les instances SQL Server qui nécessitent une haute sécurité et une surface de programmabilité riche.

L’option de déploiement d’instance managée a pour but d’assurer une compatibilité de la surface d’exposition proche de 100 % avec la dernière version locale de SQL Server par le biais d’un plan de mise en production par étapes.

Pour choisir une des options de déploiement Azure SQL Database (base de données unique, base de données en pool, instance managée et SQL Server hébergé dans une machine virtuelle), consultez [Comment choisir la version appropriée de SQL Server dans Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Fonctionnalités principales

L’instance managée combine les meilleures fonctionnalités d’Azure SQL Database et du moteur de base de données SQL Server.

> [!IMPORTANT]
> Une instance managée s’exécute avec toutes les fonctionnalités de la version la plus récente de SQL Server, notamment les opérations en ligne, les corrections de plan automatiques et d’autres améliorations des performances d’entreprise. Une comparaison des fonctionnalités disponibles est expliquée dans [Comparaison des fonctionnalités : Azure SQL Database et SQL Server](sql-database-features.md).

| **Avantages de PaaS** | **Continuité de l’activité** |
| --- | --- |
|Aucun achat ni gestion de matériel <br>Aucun temps de gestion à dédier à l’infrastructure sous-jacente <br>Provisionnement et mise à l’échelle du service rapides <br>Application automatisée de correctifs et de mises à niveau de version <br>Intégration à d’autres services de données PaaS |Contrat SLA à 99,99 % de durée de fonctionnement  <br>[Haute disponibilité](sql-database-high-availability.md) intégrée <br>Données protégées par des [sauvegardes automatisées](sql-database-automated-backups.md) <br>Période de rétention de sauvegarde configurable par le client <br>[Sauvegardes](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) lancées par l’utilisateur <br>Fonctionnalité de [limite de restauration dans le temps d’une base de données](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sécurité et conformité** | **gestion**|
|Environnement isolé ([intégration de réseau virtuel](sql-database-managed-instance-connectivity-architecture.md), service de locataire unique, calcul et stockage dédiés) <br>[Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>Prise en charge de l’[authentification unique Azure AD](sql-database-aad-authentication.md) <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Principaux (connexions) de serveur Azure AD</a> (**préversion publique**) <br>Conformité aux mêmes normes qu’une base de données Azure SQL <br>[Audit SQL](sql-database-managed-instance-auditing.md) <br>[threat detection](sql-database-managed-instance-threat-detection.md) |API Azure Resource Manager pour automatiser le provisionnement et la mise à l’échelle des services <br>Fonctionnalités du portail Azure pour le provisionnement et la mise à l’échelle manuels des services <br>Service de migration des données

> [!IMPORTANT]
> Base de données SQL Azure (toutes les options de déploiement), a été certifié par rapport à un nombre de normes de conformité. Pour plus d’informations, consultez le [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/) où vous trouverez la liste actualisée des certifications de conformité de base de données SQL.

Les fonctionnalités clés des instances managées figurent dans le tableau suivant :

|Fonctionnalité | Description|
|---|---|
| Version/Build de SQL Server | Moteur de base de données SQL Server (dernière version stable) |
| Sauvegardes automatisées gérées | Oui |
| Analyse et métriques des instances et bases de données intégrées | Oui |
| Mise à jour corrective automatique des logiciels | Oui |
| Les dernières fonctionnalités du moteur de base de données | Oui |
| Nombre de fichiers de données (ROWS) par base de données | Multiple |
| Nombre de fichiers journaux (LOG) par base de données | 1 |
| Réseau virtuel - Déploiement Azure Resource Manager | Oui |
| Réseau virtuel - Modèle de déploiement classique | Non  |
| Prise en charge du portail | Oui|
| Integration Services (SSIS) intégré | Non : SSIS fait partie de la [plateforme PaaS Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) intégré | Non : SSAS est une [plateforme PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distincte |
| Reporting Services (SSRS) intégré | Non : utilisez Power BI ou l’infrastructure IaaS SSRS |
|||

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) pour les instances managées vous assure flexibilité, contrôle et transparence. Il permet de traduire de manière simple les exigences des charges de travail locales dans le cloud. Ce modèle vous permet de changer la capacité de calcul, la mémoire et le stockage en fonction des besoins de vos charges de travail. Le modèle vCore permet également de réaliser jusqu’à 30 % d’économies avec [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

Dans le modèle vCore, vous pouvez choisir entre différentes générations de matériel.

- Les processeurs logiques **Gen4** sont basés sur des processeurs Intel E5-2673 v3 (Haswell) de 2,4 GHz, un disque SSD attaché, des cœurs physiques, 7 Go de RAM par cœur, et des tailles de capacité de calcul comprises entre 8 et 24 vCores.
- Les processeurs logiques **Gen5** sont basés sur des processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz, un disque SSD NVMe rapide, un cœur logique multithread, et des tailles de capacité de calcul comprises entre 8 et 80 cœurs.

Vous trouverez des informations sur les différences entre les générations de matériel dans les [limites de ressources d’instance managée](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Niveaux de service de l’instance managée

L’instance managée est disponible en deux niveaux de service :

- **Usage général** : conçu pour des applications avec des exigences de performances et de latence d’E/S standard.
- **Critique pour l’entreprise** : conçu pour les applications avec des exigences de latence d’E/S faible et un impact minimal des opérations de maintenance sous-jacentes sur la charge de travail.

Les deux niveaux de service garantissent une disponibilité de 99,99 % et vous permettent de sélectionner la taille de stockage et la capacité de calcul indépendamment. Pour plus d’informations sur l’architecture à haute disponibilité d’Azure SQL Database, consultez [Haute disponibilité et Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Niveau de service Usage général

La liste suivante décrit les principales caractéristiques du niveau de service Usage général :

- Concevoir pour la majorité des applications métier avec des exigences de performances standard
- Stockage Blob Azure à hautes performances (8 To)
- [Haute disponibilité](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) intégrée basée sur un Stockage Blob Azure fiable et [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Pour plus d’informations, consultez [storage layer in general purpose tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) et [Storage performance best practices and considerations for managed instances (general purpose)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Plus d’informations sur les différences entre les niveaux de service sont disponibles dans les [limites de ressources d’instance managée](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Niveau de service critique pour l’entreprise

Le niveau de service Critique pour l’entreprise est conçu pour les applications dont les exigences en termes d’E/S sont élevées. Il offre la meilleure résilience face aux défaillances en utilisant plusieurs réplicas isolés.

La liste suivante décrit les principales caractéristiques du niveau de service Critique pour l’entreprise :

- Conçu pour les applications d’entreprise avec les exigences les plus hautes en matière de performances et de disponibilité
- Fourni avec un stockage SSD local extrêmement rapide (jusqu’à 1 To sur Gen4, jusqu’à 4 To sur Gen5)
- [Haute disponibilité](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) intégrée basée sur les [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) et [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Un [réplica de base de données en lecture seule](sql-database-read-scale-out.md) intégré supplémentaire qui peut être utilisé pour les rapports et d’autres charges de travail en lecture seule
- [OLTP en mémoire](sql-database-in-memory.md), qui peut être utilisé pour les charges de travail avec des exigences de hautes performances  

Vous trouverez plus d’informations sur les différences entre les niveaux de service dans les [limites de ressources d’instance managée](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Sécurité et conformité avancées

L’option de déploiement d’instance managée combine les fonctionnalités de sécurité avancées fournies par le cloud Azure et le moteur de base de données SQL Server.

### <a name="managed-instance-security-isolation"></a>Isolation de la sécurité de l’instance managée

Une instance managée offre une meilleure isolation de la sécurité vis-à-vis des autres locataires dans le cloud Azure. L’isolation de la sécurité inclut :

- [Implémentation d’un réseau virtuel natif](sql-database-managed-instance-connectivity-architecture.md) et sa connexion à votre environnement local à l’aide d’Azure Express Route ou d’une passerelle VPN.
- Exposition du point de terminaison SQL uniquement par le biais d’une adresse IP privée, ce qui permet de sécuriser la connexion à partir de réseaux Azure privés ou hybrides.
- Locataire unique avec infrastructure sous-jacente dédiée (calcul, stockage).

Le diagramme suivant présente différentes options de connectivité pour vos applications :

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Pour plus d’informations sur l’intégration de réseau virtuel et l’application de stratégie de mise en réseau au niveau du sous-réseau, consultez [Architecture de réseau virtuel pour les instances managées](sql-database-managed-instance-connectivity-architecture.md) et [Connecter votre application à une instance managée](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Placez plusieurs instances gérées dans le même sous-réseau, partout où vos exigences de sécurité l’autorisent, car cela vous apportera des avantages supplémentaires. La colocation d’instances dans le même sous-réseau simplifie considérablement la maintenance de l’infrastructure réseau et réduit le temps de provisionnement des instances, car une durée de provisionnement longue est associée au coût de déploiement de la première instance managée dans un sous-réseau.

### <a name="azure-sql-database-security-features"></a>Fonctionnalités de sécurité d’Azure SQL Database

Azure SQL Database fournit un ensemble de fonctionnalités de sécurité avancées qui peuvent être utilisées pour protéger vos données.

- [L’audit d’instance managée](sql-database-managed-instance-auditing.md) suit les événements de base de données et les écrit dans un fichier journal d’audit placé dans votre compte de stockage Azure. L’audit permet de respecter une conformité réglementaire, de comprendre l’activité de la base de données et de découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.
- Chiffrement des données en mouvement : une instance managée sécurise vos données par le biais d’un chiffrement des données en mouvement à l’aide du protocole TLS. En plus du protocole TLS, l’option de déploiement d’instance managée offre une protection des données sensibles en vol, au repos et pendant le traitement des requêtes avec [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted est une nouveauté qui offre une protection inégalée des données contre les failles de sécurité impliquant le vol de données critiques. Par exemple, avec Always Encrypted, les numéros de carte de crédit sont toujours chiffrés dans la base de données, même pendant le traitement des requêtes, ce qui permet le déchiffrement au point d’utilisation par les applications ou le personnel autorisés qui doivent traiter ces données.
- La [détection des menaces](sql-database-managed-instance-threat-detection.md) complète l’[audit](sql-database-managed-instance-auditing.md) en fournissant une couche supplémentaire d’informations de sécurité intégrée au service qui détecte les tentatives d’accès ou d’exploitation de base de données inhabituelles et potentiellement dangereuses. Vous êtes alerté en cas d’activités suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes de détection des menaces peuvent être consultées dans [Azure Security Center](https://azure.microsoft.com/services/security-center/). Elles fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.  
- Le [masquage dynamique des données](/sql/relational-databases/security/dynamic-data-masking) limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilèges. Le masquage des données dynamique contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche Application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.
- La [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) vous permet de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). La sécurité au niveau des lignes (RLS) simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux lignes de données. Par exemple, en s’assurant que les employés ne peuvent accéder qu’aux lignes de données utiles à leur service, ou en limitant l’accès aux données aux seules données pertinentes.
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) : également appelé chiffrement des données au repos, chiffre les fichiers de données d’instance managée. TDE effectue le chiffrement et le déchiffrement d’E/S en temps réel des données et des fichiers journaux. Le chiffrement utilise une clé de chiffrement de base de données stockée dans l’enregistrement de démarrage de base de données à des fins de disponibilité lors de la récupération. Vous pouvez protéger toutes vos bases de données dans une instance managée avec Transparent Data Encryption. Il s’agit de la technologie de chiffrement au repos éprouvée de SQL Server, qui est requise par de nombreuses normes de conformité comme protection contre le vol d’un support de stockage.

La migration d’une base de données chiffrée vers une instance managée est prise en charge par le biais d’Azure Database Migration Service (DMS) ou d’une restauration native. Si vous projetez de migrer une base de données chiffrée à l’aide de la restauration native, la migration du chiffrement transparent des données certificat existant à partir de SQL Server en local ou de SQL Server sur une machine virtuelle sur une instance gérée est une étape obligatoire. Pour plus d’informations sur les options de migration, consultez [Migration d’une instance SQL Server vers une instance managée](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Intégration d'Azure Active Directory

L’option de déploiement d’instance managée prend en charge les connexions traditionnelles au moteur de base de données SQL Server et les connexions intégrées à Azure Active Directory (AAD). Les principaux (connexions) de serveur Azure AD (**préversion publique**) correspondent à une version cloud Azure des connexions aux bases de données locales que vous utilisez dans votre environnement local. Les principaux (connexions) de serveur Azure AD vous permettent de spécifier des utilisateurs et des groupes de votre locataire Azure Active Directory sous forme de principaux limités à une instance et capables d'effectuer toutes les opérations au niveau de l'instance, y compris les requêtes entre plusieurs bases de données au sein de la même instance managée.

Une nouvelle syntaxe a été ajoutée pour créer des principaux (connexions) de serveur Azure AD (**préversion publique**), **À PARTIR D'UN FOURNISSEUR EXTERNE**. Pour plus d’informations sur la syntaxe, consultez <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> et lisez l’article [Provisionner un administrateur Azure Active Directory pour votre instance managée](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Intégration d’Azure Active Directory et authentification multifacteur

L’option de déploiement d’instance managée vous permet de gérer de manière centralisée les identités d’utilisateur de base de données et d’autres services Microsoft avec l’[Intégration d’Azure Active Directory](sql-database-aad-authentication.md). Cette fonctionnalité simplifie la gestion des autorisations et améliore la sécurité. Azure Active Directory prend en charge l’[authentification multifacteur](sql-database-ssms-mfa-authentication-configure.md) (MFA) pour augmenter la sécurité des données et des applications, ainsi qu’un processus d’authentification unique.

### <a name="authentication"></a>Authentication

L’authentification d’instance managée fait référence à la façon dont les utilisateurs prouvent leur identité quand ils se connectent à la base de données. Une base de données SQL prend en charge deux types d’authentification :  

- **Authentification SQL** :

  Cette méthode d’authentification utilise un nom d’utilisateur et un mot de passe.
- **Authentification Azure Active Directory** :

  Cette méthode d’authentification utilise des identités gérées par Azure Active Directory, et est prise en charge pour les domaines managés et intégrés. Utilisez l’authentification Active Directory (sécurité intégrée) [dans la mesure du possible](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Authorization

Le terme autorisation fait référence aux actions qu’un utilisateur peut exécuter au sein d’Azure SQL Database. Celles-ci sont contrôlées par les appartenances aux rôles et les autorisations au niveau objet de la base de données de votre compte d’utilisateur. Une instance managée a les mêmes fonctionnalités d’autorisation que SQL Server 2017.

## <a name="database-migration"></a>Migration de base de données

L’option de déploiement d’instance managée cible des scénarios d’utilisateur avec une migration massive de bases de données locales ou des implémentations de base de données IaaS. L’instance managée prend en charge plusieurs options de migration de base de données :

### <a name="back-up-and-restore"></a>Sauvegarder et restaurer des données  

L’approche de la migration s’appuie sur les sauvegardes SQL dans Stockage Blob Azure. Les sauvegardes stockées dans un objet blob de stockage Azure peuvent être directement restaurées dans une instance managée à l’aide de la [commande T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Pour obtenir un guide de démarrage rapide montrant comment restaurer le fichier de sauvegarde de base de données Wide World Importers - Standard, consultez [Restaurer un fichier de sauvegarde dans une instance managée](sql-database-managed-instance-get-started-restore.md). Ce guide vous montre que vous devez charger un fichier de sauvegarde dans Stockage Blob Azure et le sécuriser à l’aide d’une clé de signature d’accès partagé (SAP).
- Pour plus d’informations sur la restauration à partir d’une URL, consultez [Restauration native à partir d’une URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Les sauvegardes d’une instance managée peuvent uniquement être restaurées sur une autre instance managée. Elles ne peuvent pas être restaurées sur une instance SQL Server locale ni dans une base de données unique ou un pool élastique.

### <a name="data-migration-service"></a>Service de migration des données

Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime. Ce service simplifie les tâches nécessaires pour déplacer les bases de données de tiers et les bases de données SQL Server vers Azure SQL Database (bases de données uniques, bases de données en pool élastique et bases de données dans une instance managée) et SQL Server dans une machine virtuelle Azure. Consultez [Comment migrer votre base de données locale vers une instance managée à l’aide de DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Fonctionnalités SQL prises en charge

L’option de déploiement d’instance managée vise à assurer une compatibilité de la surface d’exposition proche de 100 % avec l’instance SQL Server locale par étapes jusqu’à la disponibilité générale du service. Pour obtenir la liste des fonctionnalités et des différences, consultez [Comparaison des fonctionnalités SQL Database](sql-database-features.md). Pour obtenir la liste des différences de T-SQL entre les instances managées et SQL Server, consultez [Différences de T-SQL entre une instance managée et SQL Server](sql-database-managed-instance-transact-sql-information.md).

L’option de déploiement d’instance managée prend en charge la compatibilité descendante vers les bases de données SQL 2008. La migration directe à partir de serveurs de base de données SQL 2005 est prise en charge, le niveau de compatibilité des bases de données SQL 2005 qui ont migré est mis à jour vers SQL 2008.
  
Le diagramme suivant présente la compatibilité de la surface d’exposition dans une instance managée :  

![migration](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Principales différences entre une instance SQL Server locale et une instance managée

L’option de déploiement d’instance managée est toujours à jour dans le cloud, ce qui signifie que certaines fonctionnalités de l’instance locale de SQL Server peuvent quant à elles être obsolètes, mises hors service ou remplacées par d’autres. Dans certains cas, les outils ont besoin de reconnaître qu’une fonctionnalité particulière agit de façon légèrement différente ou que le service n’est pas exécuté dans un environnement que vous ne contrôlez pas entièrement :

- La haute disponibilité est intégrée et préconfigurée avec une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Les sauvegardes sont automatisées et la restauration dans le temps limitée. Le client peut lancer des sauvegardes `copy-only` qui n’interfèrent pas avec la chaîne de sauvegarde automatique.
- Comme l’instance managée ne permet pas de spécifier des chemins physiques complets, tous les scénarios correspondants doivent être pris en charge d’une autre façon : RESTORE DB ne prend pas en charge WITH MOVE, CREATE DB n’autorise pas les chemins d’accès physiques, BULK INSERT fonctionne uniquement avec les objets BLOB Azure, etc.
- L’instance managée prend en charge l’[authentification Azure AD](sql-database-aad-authentication.md) comme une alternative cloud à l’authentification Windows.
- L’instance managée gère automatiquement le groupe de fichiers et les fichiers XTP des bases de données contenant des objets OLTP en mémoire
- L’instance managée prend en charge SSIS (SQL Server Integration Services) et peut héberger le catalogue SSIS (SSISDB) qui stocke les packages SSIS, mais ceux-ci sont exécutés sur une instance Azure-SSIS IR (Integration Runtime) managée dans ADF (Azure Data Factory). Consultez [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Pour comparer les fonctionnalités SSIS de SQL Database, consultez [Comparer des bases de données uniques/pools élastiques Azure SQL Database et une instance managée](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Fonctionnalités d’administration des instances managées

L’option de déploiement d’instance managée permet à l’administrateur système de passer moins de temps sur les tâches d’administration, car le service SQL Database les effectue pour vous ou les simplifie considérablement. Par exemple, l’[installation et la mise à jour corrective d’un système d’exploitation ou système de gestion de base de données relationnelle](sql-database-high-availability.md), le [redimensionnement et la configuration d’une instance dynamique](sql-database-single-database-scale.md), les [sauvegardes](sql-database-automated-backups.md), la [réplication de base de données](replication-with-sql-database-managed-instance.md) (notamment des bases de données système), la [configuration de la haute disponibilité](sql-database-high-availability.md) et la configuration des flux de données de [supervision des performances](../azure-monitor/insights/azure-sql.md) et de l’intégrité.

> [!IMPORTANT]
> Pour obtenir la liste des fonctionnalités prises en charge, partiellement prises en charge et non prises en charge, consultez [Fonctionnalités de SQL Database](sql-database-features.md). Pour obtenir la liste des différences de T-SQL entre les instances managées et SQL Server, consultez [Différences de T-SQL entre une instance managée et SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Comment identifier par programmation une instance managée

Le tableau suivant montre plusieurs propriétés, accessibles par le biais de Transact SQL, que vous pouvez utiliser pour détecter que votre application fonctionne avec des instances managées et récupérer des propriétés importantes.

|Propriété|Valeur|Commentaire|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Cette valeur identifie de façon unique une instance managée.|
|`@@SERVERNAME`. `SERVERPROPERTY ('ServerName')`|Nom DNS d’instance complet au format suivant :`<instanceName>`.`<dnsPrefix>`.database.windows.net, où `<instanceName>` est le nom fourni par le client, tandis que `<dnsPrefix>` est une partie générée automatiquement du nom garantissant l’unicité des noms DNS globaux (par exemple, « wcus17662feb9ce98 »)|Exemple : my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configuration de réseau virtuel d’une instance managée](sql-database-managed-instance-connectivity-architecture.md).
- Pour obtenir un guide de démarrage rapide qui crée une instance managée et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une instance managée](sql-database-managed-instance-get-started.md).
- Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migration d’une instance managée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pour une supervision avancée des performances de base de données d’instance managée avec des informations de dépannage intégrées, consultez [Superviser Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)
- Pour des informations sur les prix, consultez [Tarifs des instances managées SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
