---
title: Planifier un déploiement d’attribution automatique d’utilisateurs pour Azure Active Directory
description: Conseils relatifs à la planification et à l’exécution de l’attribution automatique d’utilisateurs
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab1caeb59cf7fc0a6baef5ba0001e734a75fccd2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757836"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planifier un déploiement d’attribution automatique d’utilisateurs

De nombreuses organisations s’appuient sur des applications SaaS, comme ServiceNow, Zscaler et Slack, pour accroître la productivité des utilisateurs finals. Historiquement, le personnel informatique comptait sur des méthodes de provisionnement manuelles, comme le chargement de fichiers CSV ou l’utilisation de scripts personnalisés, pour gérer de façon sécurisée les identités utilisateur dans chaque application SaaS. Ces processus sont sujets aux erreurs ; ils sont non sécurisés et difficiles à gérer.

L’attribution automatique d’utilisateurs Azure Active Directory (Azure AD) simplifie ce processus en automatisant de façon sécurisée la création, la maintenance et la suppression des identités utilisateur dans les applications SaaS, selon les règles métier. Cette automatisation vous permet de mettre à l’échelle efficacement vos systèmes de gestion des identités dans les environnements cloud uniquement et hybrides, au fur et à mesure que vous étendez leur dépendance aux solutions cloud.

Consultez [Automatisation du provisionnement et déprovisionnement d’utilisateurs sur les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) pour mieux comprendre la fonctionnalité.

## <a name="learn"></a>Découvrir

L’attribution d’utilisateurs jette les bases d’une gouvernance des identités suivie, et améliore la qualité des processus métier qui s’appuient sur les données d’identité faisant autorité.

### <a name="key-benefits"></a>Principaux avantages

Les principaux avantages de l’activation de l’attribution automatique d’utilisateurs sont les suivants :

* **Accroissement de la productivité**. Vous pouvez gérer les identités utilisateur sur les applications SaaS avec une seule interface de gestion de l’attribution d’utilisateurs. Cette interface dispose d’un ensemble unique de stratégies d’attribution.

* **Gestion des risques**. Vous pouvez augmenter la sécurité en automatisant les changements, en fonction du statut des employés ou des appartenances aux groupes qui définissent les rôles et/ou les accès.

* **Respect de la gouvernance et de la conformité**. Azure AD prend en charge les journaux d’audit natifs de chaque requête d’attribution d’utilisateurs. Les requêtes sont exécutées dans les systèmes source et cible. Vous pouvez ainsi effectuer le suivi des personnes qui ont accès aux applications à partir d’un seul écran.

* **Réduction des coûts**. L’attribution automatique d’utilisateurs réduit les coûts tout en évitant l’inefficacité et les erreurs humaines qui sont inhérents à l’attribution manuelle. Elle diminue la nécessité de recourir à des journaux d’audit, des scripts et des solutions d’attribution d’utilisateurs personnalisées.

### <a name="licensing"></a>Gestion des licences

Azure AD assure une intégration en libre-service de n’importe quelle application utilisant des modèles fournis dans le menu de la galerie d’applications. Pour obtenir la liste complète des conditions de licence, consultez la page [Gestion des licences Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licences d’application

Vous avez besoin des licences appropriées pour la ou les applications que vous souhaitez provisionner automatiquement. Entretenez-vous avec les propriétaires des applications pour savoir si les utilisateurs affectés à l’application disposent des licences adaptées à leurs rôles dans l’application. Si Azure AD gère l’attribution automatique en fonction des rôles, les rôles attribués dans Azure AD doivent s’aligner sur les licences de l’application. Les licences incorrectes qui sont détenues dans l’application peuvent entraîner des erreurs lors de l’attribution ou de la mise à jour des utilisateurs.

### <a name="terms"></a>Termes

Cet article utilise les termes suivants :

* Opérations CRUD (Create, Read, Update, Delete) - Actions effectuées sur les comptes d’utilisateur : Créer, Lire, Mettre à jour, Supprimer.

* Authentification unique (SSO) - Possibilité pour un utilisateur de se connecter une seule fois et d’accéder à toutes les applications activées pour l’authentification unique. Dans le contexte de l’attribution d’utilisateurs, l’authentification unique se matérialise par le fait que les utilisateurs disposent d’un seul compte pour accéder à tous les systèmes qui utilisent l’attribution automatique d’utilisateurs.

* Système source - Référentiel d’utilisateurs à partir duquel Azure AD provisionne. Azure AD représente le système source pour la plupart des connecteurs de provisionnement préintégrés. Toutefois, il existe quelques exceptions pour les applications cloud, telles que SAP, Workday et AWS. Par exemple, consultez [Attribution d’utilisateurs à partir de Workday vers AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

* Système cible - Référentiel d’utilisateurs vers lequel Azure AD provisionne. Le système cible est généralement une application SaaS, telle que ServiceNow, Zscaler et Slack. Le système cible peut également être un système local, comme AD.

* [SCIM (System for Cross-Domain Identity Management)](http://www.simplecloud.info/) - Norme ouverte qui permet l’automatisation de l’attribution d’utilisateurs. SCIM communique les données d’identité des utilisateurs entre les fournisseurs d’identité tels que Microsoft, et les fournisseurs de services tels que Salesforce ou d’autres applications SaaS nécessitant des informations d’identité d’utilisateur.

### <a name="training-resources"></a>Ressources de formation

| Ressources| Lien et description |
| - | - |
| Webinaires à la demande| [Gérer vos applications d’entreprise avec Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Découvrez comment Azure AD peut vous aider à implémenter l’authentification unique sur vos applications SaaS d’entreprise ainsi que les bonnes pratiques pour le contrôle de l’accès. |
| Videos| [Présentation de l’attribution d’utilisateurs dans Azure Active Directory](https://youtu.be/_ZjARPpI6NI) <br> [Comment déployer l’attribution d’utilisateurs dans Azure Active Directory](https://youtu.be/pKzyts6kfrw) <br> [Intégration de Salesforce à Azure AD : Comment automatiser l’attribution d’utilisateurs](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cours en ligne| Développez vos compétences en ligne :  [Gestion des identités](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Apprenez à intégrer Azure AD à de nombreuses applications SaaS et à sécuriser l’accès utilisateur à ces applications. |
| Livres| [Modern Authentication with Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Il s’agit d’un guide détaillé faisant autorité en matière d’élaboration de solutions d’authentification Active Directory pour ces nouveaux environnements. |
| Tutoriels| Consultez la [Liste de tutoriels sur l’intégration d’applications SaaS à Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list). |
| Forum Aux Questions| [Questions fréquentes (FAQ)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) sur l’attribution automatique d’utilisateurs |

### <a name="solution-architectures"></a>Architectures de solution

Le service de provisionnement Azure AD attribue des utilisateurs aux applications SaaS et aux autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Attribution automatique d’utilisateurs pour les entreprises hybrides

Dans cet exemple, les utilisateurs et/ou les groupes sont créés dans une base de données RH connectée à un annuaire local. Le service de provisionnement Azure AD gère l’attribution automatique d’utilisateurs aux applications SaaS cibles.

 ![attribution d’utilisateurs](media/auto-user-provision-dp/hybridprovisioning.png)

**Description du workflow :**

1. Les utilisateurs/groupes sont créés dans une application ou un système RH local, tel que SAP. 

1. L’agent Azure AD Connect effectue des synchronisations planifiées d’identités (utilisateurs et groupes) depuis l’annuaire Active Directory local vers Azure AD.

1. Le service de provisionnement Azure AD démarre un [cycle initial](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) sur le système source et le système cible. 

1. Le service de provisionnement Azure AD interroge le système source à la recherche d’utilisateurs et de groupes modifiés depuis le cycle initial, et envoie (push) les modifications apportées par l’exécution de [cycles incrémentiels](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Attribution automatique d’utilisateurs pour les entreprises sur cloud uniquement

Dans cet exemple, la création d’utilisateurs a lieu dans Azure AD tandis que le service de provisionnement Azure AD gère l’attribution automatique d’utilisateurs vers les applications (SaaS) cibles :

![Image 2](media/auto-user-provision-dp/cloudprovisioning.png)

**Description du workflow :**

1. Les utilisateurs/groupes sont créés dans Azure AD.

1. Le service de provisionnement Azure AD démarre un [cycle initial](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) sur le système source et le système cible. 

1. Le service de provisionnement Azure AD interroge le système source à la recherche d’utilisateurs et de groupes mis à jour depuis le cycle initial, et effectue des [cycles incrémentiels](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Attribution automatique d’utilisateurs pour les applications RH dans le cloud 

Dans cet exemple, les utilisateurs et/ou les groupes sont créés dans une application RH cloud, telle que Workday.

![Image 2](media/auto-user-provision-dp/workdayprovisioning.png)

1. Comptes créés dans le système RH du cloud
1. Les données circulent dans une instance AD locale via le service de provisionnement Azure AD et l’agent de provisionnement.
1. Azure AD Connect synchronise les données avec Azure AD.
1. L’attribut de nom d’utilisateur et d’e-mail peut être réécrit dans l’application RH du cloud.

Pour plus d’informations sur l’architecture et le déploiement de la solution, consultez [Tutoriel : Configurer Workday pour l’attribution automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

## <a name="plan-the-deployment-project"></a>Planifier le projet de déploiement

Tenez compte des besoins de votre organisation pour déterminer la stratégie de déploiement de l’attribution d’utilisateurs dans votre environnement.

### <a name="engage-the-right-stakeholders"></a>Impliquer les parties prenantes appropriées

Les échecs de projets informatiques, lorsqu’ils se produisent, proviennent généralement d’une disparité entre les attentes et l’impact, les responsabilités et les résultats. Pour éviter ces écueils, [veillez à faire appel aux parties prenantes appropriées](https://aka.ms/deploymentplans) et à ce que les rôles de ces parties prenantes dans le projet soient également bien compris. Pour ce faire, dressez une liste de leurs contributions et de leurs responsabilités.

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Communiquez de manière proactive avec vos utilisateurs sur ce qui va changer, à quel moment les changements seront appliqués et comment ils peuvent obtenir de l’aide en cas de problème.

### <a name="plan-a-pilot"></a>Prévoir un pilote

Nous recommandons de procéder à la configuration initiale de l’attribution automatique d’utilisateurs dans un environnement de test avec une petite partie des utilisateurs avant de l’appliquer à tous les utilisateurs en production.

#### <a name="best-practices-for-a-pilot"></a>Bonnes pratiques pour un pilote  

Un pilote vous permet de tester une fonctionnalité sur un petit groupe avant de la déployer pour tout le monde. Vérifiez que, dans le cadre de vos tests, chaque cas d’usage au sein de votre organisation est soigneusement testé.

Dans votre première vague, ciblez le service informatique, la convivialité et d’autres utilisateurs appropriés qui peuvent tester et fournir des commentaires. Utilisez ces commentaires pour enrichir les communications et les instructions que vous envoyez à vos utilisateurs, et pour donner également des insights sur les types de problèmes que le personnel de votre support technique peut rencontrer.

Élargissez le déploiement à des groupes d’utilisateurs plus importants en augmentant l’étendue du ou des groupes ciblés. Pour ce faire, vous pouvez utiliser l’[appartenance de groupe dynamique](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) ou ajouter manuellement des utilisateurs aux groupes ciblés.

## <a name="plan-application-connections-and-administration"></a>Planifier l’administration et les connexions des applications

Utilisez le portail Azure AD pour voir et gérer toutes les applications qui prennent en charge le provisionnement. Consultez [Recherche de vos applications dans le portail](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal).

### <a name="determine-the-type-of-connector-to-use"></a>Déterminer le type de connecteur à utiliser

Les étapes nécessaires pour activer et configurer l’approvisionnement automatique varient selon l’application. Si l’application que vous souhaitez provisionner automatiquement est indiquée dans la [galerie d’applications SaaS Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), vous devez sélectionner le [tutoriel d’intégration propre à l’application](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) pour configurer son connecteur d’attribution d’utilisateurs préintégré.

Sinon, suivez ces étapes ci-dessous :

1. [Créez une requête](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) pour un connecteur d’attribution d’utilisateurs préintégré. Notre équipe travaille avec vous et avec le développeur de l’application pour intégrer votre application à notre plateforme, si elle prend en charge SCIM.

1. Utilisez la prise en charge de l’attribution d’utilisateurs générique [BYOA SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) (Apportez votre propre application avec SCIM) pour l’application. Il s’agit d’une condition nécessaire à Azure AD pour attribuer des utilisateurs à l’application sans connecteur de provisionnement préintégré.

1. Si l’application est en mesure d’utiliser le connecteur BYOA SCIM, reportez-vous au [Tutoriel sur l’intégration de BYOA SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) afin de configurer ce connecteur pour l’application.

Pour plus d’informations, consultez [Quelles applications et quels systèmes puis-je utiliser avec l’attribution automatique d’utilisateurs d’Azure AD ?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

### <a name="collect-information-to-authorize-application-access"></a>Collecter des informations pour autoriser l’accès aux applications

La configuration de l’attribution automatique d’utilisateurs est un processus propre à chaque application. Vous devez fournir pour chacune d’elles des [informations d’identification administrateur](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) afin de vous connecter au point de terminaison de gestion des utilisateurs du système cible.

L’image ci-dessous montre un exemple d’informations d’identification administrateur exigées :

![Écran Approvisionnement pour gérer les paramètres d’approvisionnement du compte d'utilisateur](media/auto-user-provision-dp/userprovisioning-admincredentials.png)

Alors que certaines applications demandent le nom d’utilisateur et le mot de passe de l’administrateur, d’autres peuvent exiger un jeton de porteur.

## <a name="plan-user-and-group-provisioning"></a>Planifier l’attribution d’utilisateurs et de groupes

Si vous activez l’attribution d’utilisateurs pour des applications d’entreprise, le [portail Azure](https://portal.azure.com/) contrôle ses valeurs d’attributs par le biais du mappage d’attributs.

### <a name="determine-operations-for-each-saas-app"></a>Déterminer les opérations pour chaque application SaaS

Chaque application peut avoir des attributs d’utilisateur ou de groupe uniques qui doivent être mappés aux attributs de votre instance Azure AD. L’application peut avoir uniquement un sous-ensemble d’opérations CRUD disponibles.

Pour chaque application, réunissez les informations suivantes :

* Les opérations de provisionnement CRUD à effectuer sur les objets utilisateur et/ou groupe pour les systèmes cibles. Par exemple, chaque responsable d’application SaaS peut ne pas vouloir toutes les opérations possibles.

* Les attributs disponibles dans le système source.

* Les attributs disponibles dans le système cible.

* Le mappage des attributs entre les systèmes.

### <a name="choose-which-users-and-groups-to-provision"></a>Choisir les utilisateurs et les groupes à provisionner

Avant d’implémenter l’attribution automatique d’utilisateurs, vous devez déterminer les utilisateurs et les groupes à provisionner dans votre application.

* Utilisez les [filtres d’étendue](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) pour définir les règles basées sur des attributs qui précisent quels utilisateurs sont provisionnés dans une application.

* Ensuite, utilisez les [attributions d’utilisateurs et de groupes](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) en fonction des besoins pour un filtrage supplémentaire.

### <a name="define-user-and-group-attribute-mapping"></a>Définir le mappage des attributs d’utilisateur et de groupe

Pour implémenter l’attribution automatique d’utilisateurs, vous devez définir les attributs d’utilisateur et de groupe qui sont nécessaires à l’application. Il existe un ensemble préconfiguré d’attributs et de [mappages d’attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications SaaS n’autorisent pas les attributs de groupe.

Azure AD prend en charge par mappage direct d’attribut à attribut, en fournissant des valeurs constantes ou [en écrivant des expressions pour les mappages d’attributs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Cette flexibilité vous permet de contrôler précisément ce qui sera renseigné dans l’attribut du système ciblé. Vous pouvez utiliser l’[API Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) et l’Afficheur Graph pour exporter vos mappages d’attributs et votre schéma d’attribution d’utilisateurs dans un fichier JSON, et le réimporter dans Azure AD.

Pour plus de détails, consultez [Personnalisation des mappages d’attributs de l’attribution d’utilisateurs pour les applications SaaS dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

### <a name="special-considerations-for-user-provisioning"></a>Considérations spéciales relatives à l’attribution d’utilisateurs

Tenez compte des éléments suivants pour réduire les problèmes postérieurs au déploiement :

* Assurez-vous que les attributs utilisés pour mapper les objets utilisateur/groupe entre les applications source et cible sont résilients. Ils ne doivent pas être à l’origine d’une attribution d’utilisateurs/de groupes incorrecte si les attributs changent (par exemple, lorsqu’un utilisateur est transféré dans un autre département de la société).

* Les applications peuvent avoir des restrictions et/ou des exigences particulières à respecter pour que l’attribution d’utilisateurs fonctionne correctement. Par exemple, Slack tronque les valeurs de certains attributs. Reportez-vous aux [tutoriels d’attribution automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) propres à chaque application.

* Vérifiez la cohérence du schéma entre les systèmes source et cible. Les problèmes courants incluent des attributs, tels que UPN ou mail, qui n’ont pas de correspondance. Par exemple, dans Azure AD, UPN est défini selon le format *john_smith@contoso.com* , et dans l’application, il s’agit de *jsmith@contoso.com* . Pour plus d’informations, consultez la [documentation de référence du schéma des utilisateurs et des groupes](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups).

## <a name="plan-testing-and-security"></a>Planifier les tests et la sécurité

À chaque étape de votre déploiement, assurez-vous que les tests que vous effectuez donnent les résultats attendus, et que vous auditez les cycles de provisionnement.

### <a name="plan-testing"></a>Planifier les tests

Une fois que vous avez configuré l’attribution automatique d’utilisateurs pour l’application, vous devez exécuter des cas de test pour vérifier que cette solution répond aux besoins de votre organisation.

| Scénarios| Résultats attendus |
| - | - |
| L’utilisateur est ajouté à un groupe affecté au système cible | L’objet utilisateur est provisionné dans le système cible. <br>L’utilisateur peut se connecter au système cible et effectuer les actions souhaitées. |
| L’utilisateur est supprimé d’un groupe affecté au système cible | L’objet utilisateur est déprovisionné dans le système cible.<br>L’utilisateur ne peut pas se connecter au système cible. |
| Les informations utilisateur sont mises à jour dans Azure AD par une méthode quelconque | Les attributs utilisateur mis à jour sont pris en compte dans le système cible après un cycle incrémentiel |
| L’utilisateur est hors de portée | L’objet utilisateur est désactivé ou supprimé. <br>Remarque : Ce comportement est substitué pour le [provisionnement de Workday](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions). |

### <a name="plan-security"></a>Planifier la sécurité

Il est fréquent qu’une révision de sécurité soit nécessaire dans le cadre d’un déploiement. Si vous avez besoin d’une révision de sécurité, consultez les nombreux [livres blancs](https://www.microsoft.com/download/details.aspx?id=36391) Azure AD qui fournissent une vue d’ensemble sur l’identité en tant que service.

### <a name="plan-rollback"></a>Planifier la restauration

Si l’implémentation de l’attribution automatique d’utilisateurs ne fonctionne pas comme vous le souhaitez dans l’environnement de production, les étapes de restauration ci-dessous peuvent vous aider à revenir à un état précédent connu pour être correct :

1. Consultez le [rapport de synthèse sur le provisionnement](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) et les [journaux de provisionnement](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) pour déterminer les opérations incorrectes qui se sont produites sur les utilisateurs et/ou les groupes concernés.

1. Utilisez les journaux d’audit de provisionnement pour déterminer le dernier état correct connu des utilisateurs et/ou des groupes concernés. Examinez également les systèmes sources (Azure AD ou AD).

1. Collaborez avec le propriétaire de l’application pour mettre à jour les utilisateurs et/ou groupes concernés directement dans l’application, à l’aide des dernières valeurs d’état connues pour être correctes.

## <a name="deploy-automatic-user-provisioning-service"></a>Déployer le service d’attribution automatique d’utilisateurs

Choisissez les étapes qui correspondent aux besoins de votre solution.

### <a name="prepare-for-the-initial-cycle"></a>Préparer le cycle initial

Lorsque le service de provisionnement Azure AD s’exécute pour la première fois, le cycle initial, utilisant le système source et les systèmes cibles, crée un instantané de tous les objets utilisateur pour chaque système cible.

Lors de l’activation du provisionnement automatique d’une application, le cycle initial peut demander entre 20 minutes et plusieurs heures pour s’accomplir. La durée dépend de la taille de l’annuaire Azure AD et du nombre d’utilisateurs dans l’étendue du provisionnement. Consultez [Comment améliorer les performances du provisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish).

Le service de provisionnement stocke l’état des deux systèmes après le cycle initial, ce qui améliore les performances des cycles incrémentiels suivants.

### <a name="configure-automatic-user-provisioning"></a>Configurer le provisionnement d’utilisateurs automatique

Utilisez le [portail Azure](https://portal.azure.com/) pour gérer le provisionnement et le déprovisionnement automatiques de comptes d’utilisateur pour les applications qui les prennent en charge. Suivez les étapes dans [Comment configurer le provisionnement automatique pour une application ?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

Le service d’approvisionnement des utilisateurs Azure AD peut également être configuré et géré à l’aide de [l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gérer l’attribution automatique d’utilisateurs

Maintenant que vous avez déployé, vous devez gérer la solution.

### <a name="monitor-user-provisioning-operation-health"></a>Superviser l’intégrité de l’opération d’attribution d’utilisateurs

Après l’exécution d’un [cycle initial](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) réussi, le service de provisionnement Azure AD exécute indéfiniment des mises à jour incrémentielles, à des intervalles propres à chaque application, jusqu’à ce que l’un des événements suivants se produise :

* Le service est arrêté manuellement, et un nouveau cycle initial est déclenché à l’aide du [portail Azure](https://portal.azure.com/) ou de la commande [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriée.

* Une nouveau cycle initial est déclenché par une modification dans les mappages d’attributs ou les filtres d’étendue.

* Le processus de provisionnement est mis en quarantaine en raison d’un taux d’erreur élevé, et reste en quarantaine pendant plus de quatre semaines à l’issue desquelles il sera automatiquement désactivé.

Pour passer en revue ces événements, et toutes les autres activités effectuées par le service de provisionnement, reportez-vous aux [journaux de provisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/manage-apps/context/manage-apps-context) Azure AD.

### <a name="gain-insights-from-reports"></a>Mieux exploiter les rapports

Azure AD peut fournir des [insights supplémentaires](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) sur l’exploitation de l’attribution d’utilisateurs et son intégrité opérationnelle au sein de votre organisation, par le biais des journaux d’audit et des rapports.

Les administrateurs doivent consulter le rapport de synthèse du provisionnement pour superviser l’intégrité opérationnelle de la tâche de provisionnement. Toutes les activités effectuées par le service de provisionnement sont enregistrées dans les journaux d’audit Azure AD. Consultez le [tutoriel : Création de rapports sur le provisionnement automatique de comptes d’utilisateur](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

Nous vous recommandons de vous attribuer la propriété de ces rapports et de les consommer à une cadence qui satisfait aux exigences de votre organisation. Azure AD conserve la plupart des données d’audit pendant 30 jours.

### <a name="troubleshoot"></a>Résolution des problèmes

Reportez-vous aux liens suivants pour résoudre les problèmes susceptibles de survenir au cours du provisionnement :

* [Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)

* [Synchroniser un attribut entre votre instance Active Directory local et Azure AD pour le provisionnement d’une application](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)

* [L’attribution d’utilisateurs dans une application de la galerie Azure AD prend des heures](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)

* [Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)

* [Aucun utilisateur n’est attribué à une application de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

* [Un mauvais ensemble d’utilisateurs est attribué à une application de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)

### <a name="helpful-documentation"></a>Documentation utile

* [Écriture d’expressions pour les mappages d’attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)

* [Ignorer la suppression des comptes d’utilisateurs qui sortent de l’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)

* [Agent de provisionnement Azure AD Connect : Historique de publication des versions](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)

#### <a name="resources"></a>Ressources

* [Indiquer des commentaires sur le produit](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Se tenir informé des nouveautés d’Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Forum Stack Overflow Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Étapes suivantes
* [Configurer l’attribution automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)

* [Exporter ou importer votre configuration de provisionnement à l’aide de l’API Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)

* [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
