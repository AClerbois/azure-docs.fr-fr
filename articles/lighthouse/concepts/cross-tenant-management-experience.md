---
title: Expériences de la gestion multilocataire
description: La gestion des ressources déléguées Azure offre une expérience de gestion inter-locataires.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 10/18/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 8d7b1f24d5dcf3d66ffd04704c79a284c4810365
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598448"
---
# <a name="cross-tenant-management-experiences"></a>Expériences de la gestion multilocataire

Cet article décrit les scénarios que vous, en tant que fournisseur de services, pouvez utiliser avec la [gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md) pour gérer des ressources Azure pour plusieurs clients à partir de votre propre locataire dans le [portail Azure](https://portal.azure.com).

> [!NOTE]
> La gestion des ressources déléguées Azure peut également être utilisée au sein d’une entreprise qui dispose de plusieurs locataires pour simplifier l’administration inter-locataire.

## <a name="understanding-customer-tenants"></a>Compréhension des locataires du client

Dans Azure Active Directory (Azure AD), un locataire est une représentation d’une organisation. Il s’agit d’une instance dédiée d’Azure AD qu’une organisation reçoit quand elle crée une relation avec Microsoft en s’inscrivant à Azure, à Microsoft 365 ou à d’autres services. Chaque locataire Azure AD est distinct et indépendant des autres, et a son propre ID de locataire (GUID). Pour plus d’informations, voir [Qu’est-ce qu’Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

En règle générale, pour gérer des ressources Azure pour un client, les fournisseurs de services doivent se connecter au portail Azure à l’aide d’un compte associé au locataire de ce client, ce qui requiert qu’un administrateur dans le locataire du client crée et gère des comptes d’utilisateur pour le fournisseur de services.

Avec la gestion des ressources déléguées Azure, le processus d’intégration spécifie les utilisateurs au sein du locataire du fournisseur de services qui pourront accéder aux abonnements, aux groupes de ressources et aux ressources dans le locataire du client, ainsi que les gérer. Ces utilisateurs peuvent ensuite se connecter au portail Azure en utilisant leurs propres informations d’identification. Dans le portail Azure, ils peuvent gérer les ressources appartenant à tous les clients auxquels ils ont accès. Pour ce faire, ils peuvent accéder à la page [Mes clients](../how-to/view-manage-customers.md) du portail Azure ou travailler directement dans le contexte de l’abonnement de ce client, soit sur le portail Azure ou via des API.

La gestion des ressources déléguées Azure offre davantage de flexibilité pour gérer les ressources de plusieurs clients sans devoir se connecter aux différents comptes dans les différents locataires. Par exemple, un fournisseur de services peut avoir trois clients, avec des responsabilités et des niveaux d’accès différents, comme illustré ici :

![Trois locataires du client présentant les responsabilités du fournisseur de services](../media/azure-delegated-resource-management-customer-tenants.jpg)

À l’aide de la gestion des ressources déléguées Azure, les utilisateurs autorisés peuvent se connecter au locataire du fournisseur de services pour accéder à ces ressources, comme illustré ici :

![Ressources du client gérées via un locataire du fournisseur de services](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Services et scénarios pris en charge

Actuellement, l’expérience de gestion inter-locataire prend en charge les scénarios suivants avec les ressources du client déléguées :

[Azure Automation](https://docs.microsoft.com/azure/automation/) :

- Utiliser des comptes Automation pour accéder à des ressources du client déléguées et les utiliser

[Sauvegarde Azure](https://docs.microsoft.com/azure/backup/) :

- Sauvegarder et restaurer des données client dans des locataires du client

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/) :

- Gérer des environnements Kubernetes hébergés, ainsi que déployer et gérer des applications en conteneur au sein des locataires du client

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) :

- Afficher les alertes relatives aux abonnements délégués sur le portail Azure, ou par programme via des appels d’API REST, avec la possibilité d’afficher des alertes dans tous les abonnements
- Afficher les détails du journal d’activité pour des abonnements délégués
- Log Analytics : interroger des données à partir d’espaces de travail du clients distants dans plusieurs locataires
- Créer des alertes dans les locataires du client qui déclenchent une automatisation, par exemple des runbooks Azure Automation ou des fonctions Azure, dans le locataire du fournisseur de services par le biais de webhooks

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/) :

- Les instantanés de conformité affichent les détails sur les stratégies attribuées au sein d’abonnements délégués
- Créer et modifier des définitions de stratégie au sein d’un abonnement délégué
- Affecter des définitions de stratégie définies par le client au sein de l’abonnement délégué
- Les clients voient les stratégies créées par le fournisseur de services en même temps que les stratégies qu’ils ont créées eux-mêmes
- Peut [corriger deployIfNotExists ou modifier des affectations au sein du locataire du client](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/) :

- Inclut désormais l’ID de locataire dans les résultats de requête renvoyés, ce qui vous permet d’identifier si un abonnement appartient au locataire du client ou au locataire du fournisseur de services

[Azure Security Center](https://docs.microsoft.com/azure/security-center/) :

- Visibilité inter-locataire
  - Surveiller la conformité aux stratégies de sécurité et garantir la couverture de sécurité des ressources de tous les locataires
  - Surveillance continue de la conformité réglementaire de plusieurs clients dans une seule vue
  - Surveiller, trier et hiérarchiser les recommandations de sécurité actionnables avec un calcul du degré de sécurisation
- Gestion d’état de la sécurité inter-locataire
  - Gérer les stratégies de sécurité
  - Agir sur des ressources non conformes à l’aide de recommandations de sécurité actionnables
  - Collecter et stocker des données liées à la sécurité
- Détection et traitement des menaces inter-locataires
  - Détecter des menaces pesant sur des ressources inter-locataires
  - Appliquer des contrôles de protection avancée contre les menaces tels que l’accès à la machine virtuelle juste-à-temps (JIT)
  - Renforcer la configuration du groupe de sécurité réseau avec le renforcement du réseau adaptatif
  - S’assurer que les serveurs exécutent uniquement les applications et processus qu’ils doivent exécuter avec des contrôles d’application adaptatifs
  - Surveiller les modifications apportées aux fichiers et aux entrées de Registre importants avec le monitoring d’intégrité de fichier

[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers) :

- Gérer les ressources Azure Sentinel dans les locataires du client

[Azure Service Health](https://docs.microsoft.com/azure/service-health/) :

- Surveiller l’intégrité des ressources du client avec Azure Resource Health
- Suivre l’intégrité des services Azure utilisés par les clients

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) :

- Gérer les options de récupération d’urgence pour les machines virtuelles Azure dans les locataires du client (notez que vous ne pouvez pas utiliser de comptes d’identification pour copier des extensions de machine virtuelle)

[Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/) :

- Utiliser des extensions de machine virtuelle pour accomplir des tâches d’automatisation et de configuration après déploiement sur des machines virtuelles Azure dans les locataires du client
- Utiliser des diagnostics de démarrage pour résoudre des problèmes de machines virtuelles Azure dans les locataires du client
- Accéder aux machines virtuelles avec une console série dans les locataires du client
- Notez que vous ne pouvez pas utiliser Azure Active Directory pour une ouverture de session à distance sur une machine virtuelle, ou intégrer une machine virtuelle avec Key Vault pour les mots de passe, les secrets ou les clés de chiffrement du disque.

[Réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/) :

- Déployer et gérer des réseaux virtuels et des cartes d’interface réseau virtuelles (cartes réseau virtuelles) au sein des locataires du client

Demandes de support :

- Ouvrir des demandes de support pour des ressources déléguées à partir du panneau **Aide + Support** dans le portail Azure (en sélectionnant le plan de support disponible pour l’étendue déléguée)

## <a name="current-limitations"></a>Limitations actuelles
Dans tous les scénarios, gardez à l’esprit les limitations actuelles suivantes :

- Les demandes traitées par Azure Resource Manager peuvent être effectuées à l’aide de la gestion des ressources déléguées Azure. Les URI d’opération pour ces demandes commencent par `https://management.azure.com`. Toutefois, les demandes qui sont gérées par une instance d’un type de ressource (par exemple, accès aux secrets du coffre de clés ou accès aux données de stockage) ne sont pas prises en charge avec la gestion des ressources déléguées Azure. Les URI d’opération pour ces demandes commencent généralement par une adresse propre à votre instance, telle que `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/`. Ces dernières sont également des opérations sur les données plutôt que des opérations de gestion. 
- Les attributions de rôles doivent utiliser des [rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) de contrôle d’accès en fonction du rôle (RBAC). Tous les rôles intégrés sont actuellement pris en charge avec la gestion des ressources déléguées Azure, à l’exception des rôles Propriétaire et Administrateur de l’accès utilisateur, ainsi que des rôles intégrés avec l’autorisation [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions). Les rôles personnalisés et les [Rôles Administrateur classique de l’abonnement](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) ne sont pas non plus pris en charge.
- Actuellement, vous ne pouvez pas intégrer un abonnement (ou un groupe de ressources au sein d’un abonnement) pour la gestion des ressources déléguées Azure si l’abonnement utilise Azure Databricks. De même, si un abonnement a été inscrit pour une intégration avec le fournisseur de ressources **Microsoft.ManagedServices**, vous ne pouvez pas actuellement créer d’espace de travail Databricks pour cet abonnement.
- Bien que vous puissiez intégrer des abonnements et des groupes de ressources pour la gestion des ressources déléguées Azure qui ont des verrous de ressources, ces verrous n’empêchent pas les actions d’être effectuées par les utilisateurs dans le locataire gestionnaire. Les [affectations de refus](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments), qui protègent les ressources managées par le système, telles que celles créées par les applications managées Azure ou Azure Blueprints (affectations de refus émises par le système), empêchent les utilisateurs du locataire gestionnaire d’agir sur ces ressources. Toutefois, à ce moment-là, les utilisateurs du locataire client ne peuvent pas créer leurs propres affectations de refus (affectations de refus émises par l’utilisateur).

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>Utilisation d’API et d’outils de gestion avec la gestion inter-locataire

Pour les services et les scénarios pris en charge répertoriés ci-dessus, vous pouvez effectuer des tâches de gestion directement sur le portail ou à l’aide d’API et d’outils de gestion (tels que Azure CLI et Azure PowerShell). Toutes les API existantes peuvent être utilisées lors de l’utilisation de ressources déléguées (pour les services pris en charge).

Il existe également des API spécifiques pour l’exécution de tâches de gestion des ressources déléguées Azure. Pour plus d’informations, voir la section **Référence**.


## <a name="next-steps"></a>Étapes suivantes

- Intégrez vos clients à la gestion des ressources déléguées Azure [en utilisant des modèles Azure Resource Manager](../how-to/onboard-customer.md) ou [en publiant une offre de services managés privés ou publics sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
- [Affichez et gérez les clients](../how-to/view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
