---
title: Nouveautés d’Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble du service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/06/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: af9305aafc3a77df9d7c4cffa65f6c61c53ad8a4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715393"
---
# <a name="whats-new-in-azure-migrate"></a>Nouveautés d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) vous aide à découvrir, à évaluer et à migrer localement des serveurs, applications et données vers le cloud Microsoft Azure. Cet article récapitule les nouvelles fonctionnalités d’Azure Migrate.



## <a name="update-november-2019"></a>Mise à jour (novembre 2019)



Un certain nombre de nouvelles fonctionnalités ont été ajoutées à Azure Migrate :

> [!NOTE]
> Si vous ne voyez pas encore certaines de ces fonctionnalités dans le portail Azure Migrate, patientez un peu. Elles apparaîtront au cours de la semaine suivante ou un peu plus tard.

- **Évaluation des serveurs physiques**. L’évaluation des serveurs physiques locaux est désormais prise en charge, en plus de la migration du serveur physique qui est déjà prise en charge.
- **Évaluation basée sur l’importation**. L’évaluation des machines à l’aide des métadonnées et des données de performances fournies dans un fichier CSV est désormais prise en charge.
- **Découverte des applications** : Azure Migrate prend désormais en charge la découverte au niveau de l’application des applications, des rôles et des fonctionnalités à l’aide de l’appliance Azure Migrate. Ceci est actuellement pris en charge pour les machines virtuelles VMware uniquement et est limité à la détection uniquement (l’évaluation n’est pas actuellement prise en charge). [En savoir plus](how-to-discover-applications.md)
- **Visualisation des dépendances sans agent** : Vous n’avez plus besoin d’installer explicitement des agents pour la visualisation des dépendances. La visualisation sans agent et basée sur l’agent sont désormais prises en charge.
- **Bureau virtuel** : Utilisez les outils ISV pour évaluer et migrer une infrastructure de bureau virtuel locale (VDI) vers Windows Virtual Desktop dans Azure.
- **Application web** : L’assistant Migration Azure App Service, utilisé pour l’évaluation et la migration d’applications web, est désormais intégré à Azure Migrate.

De nouveaux outils d’évaluation et de migration ont été ajoutés à Azure Migrate :

- **Rackware** : Offre de migration cloud.
- **Movere** : Offre d’évaluation.

[Découvrez plus d’informations ](migrate-services-overview.md) sur l’utilisation des outils et des offres ISV pour l’évaluation et la migration dans Azure Migrate.

## <a name="release-version-july-2019"></a>Mise en production de la version (juillet 2019)

Une version actuelle d’Azure Migrate a été mise en production en juillet 2019. 

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. 
- **Version précédente** : les clients utilisant la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), doivent désormais utiliser la version actuelle. La version précédente ne permet plus de créer des projets Azure Migrate ou d’effectuer de nouvelles découvertes. Vous pouvez toujours accéder aux projets existants. Pour ce faire, dans le portail Azure > **Tous les services**, recherchez **Azure Migrate**. Les notifications Azure Migrate contiennent un lien permettant d’accéder aux anciens projets Azure Migrate.


### <a name="azure-migrate-features"></a>Fonctionnalités d’Azure Migrate

La version actuelle d’Azure Migrate offre un certain nombre de nouvelles fonctionnalités :


- **Plateforme de migration unifiée** : Azure Migrate offre désormais un portail unique pour centraliser, gérer et suivre votre parcours de migration vers Azure, avec une expérience de flux de déploiement et de portail améliorée.
- **Outils d’évaluation et de migration** : Azure Migrate fournit des outils natifs et s’intègre avec d’autres services Azure, ainsi qu’avec des outils d’éditeurs de logiciels indépendants (ISV). [Apprenez-en davantage](migrate-services-overview.md#isv-integration) sur l’intégration d’éditeurs de logiciels indépendants.
- **Évaluation d’Azure Migrate** : l’outil Évaluation de serveur Azure Migrate Server vous permet d’évaluer des machines virtuelles VMware et Hyper-V en vue de leur migration vers Azure. Vous pouvez également évaluer la migration à l’aide d’autres services Azure et outils d’éditeurs de logiciels indépendants.
- **Migration Azure Migrate** : l’outil Migration de serveur Azure Migrate vous permet de migrer vers Azure des machines virtuelles VMware et Hyper-V locales, ainsi que des serveurs physiques, d’autres serveurs virtualisés et des machines virtuelles de cloud privé/public. Vous pouvez également migrer vers Azure à l’aide d’outils d’éditeurs de logiciels indépendants.
- **Appliance Azure Migrate** : Azure Migrate déploie une appliance légère pour la découverte et l’évaluation des machines virtuelles VMware et Hyper-V locales.
    - Les solutions Évaluation de serveur Azure Migrate et Migration de serveur Azure Migrate utilisent cette appliance pour opérer une migration sans agent.
    - L’appliance découvre en permanence les métadonnées et données de performances du serveur à des fins d’évaluation et de migration.  
- **Migration de machine virtuelle VMware** :  La solution Migration de serveur Azure Migrate permet de migrer des machines virtuelles VMware locales vers Azure à l’aide de deux méthodes.  Une migration sans agent à l’aide de l’appliance Azure Migrate, et une migration basée sur un agent, qui utilise une appliance de réplication et déploie un agent sur chaque machine virtuelle que vous souhaitez migrer. [En savoir plus](server-migrate-overview.md)
 - **Évaluation et migration de base de données** : À partir d’Azure Migrate, vous pouvez évaluer des bases de données locales pour la migration vers Azure à l’aide de l’Assistant Migration de base de données Azure. Vous pouvez migrer des bases de données à l’aide d’Azure Database Migration Service.
- **Migration d’application web** : Azure App Service vous permet d’évaluer des applications web à l’aide d’une URL de point de terminaison public. Pour la migration d’applications .NET internes, vous pouvez télécharger et exécuter l’Assistant Migration App Service. 
- **Data Box** : importez de grandes quantités de données hors connexion dans Azure à l’aide d’Azure Data Box dans Azure Migrate.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
- Essayez nos didacticiels pour évaluer les [machines virtuelles VMware](tutorial-assess-vmware.md) et les [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
