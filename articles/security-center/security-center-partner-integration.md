---
title: Intégrer des solutions de sécurité dans Azure Security Center | Microsoft Docs
description: Découvrez comment Azure Security Center s’intègre avec les partenaires pour améliorer la sécurité globale de vos ressources Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064272"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Intégrer des solutions de sécurité dans Azure Security Center
Ce document vous aide à gérer les solutions de sécurité déjà connectées à Azure Security Center et à en ajouter de nouvelles.

> [!NOTE]
> Un sous-ensemble de solutions de sécurité sera supprimé le 31 juillet 2019. Pour plus d’informations et connaître les alternatives, consultez [Retrait des fonctionnalités de Security Center (juillet 2019).](security-center-features-retirement-july2019.md#menu_solutions)

## <a name="integrated-azure-security-solutions"></a>Solutions de sécurité Azure intégrées
Security Center simplifie l’activation des solutions de sécurité intégrées dans Azure. Voici les avantages :

- **Déploiement simplifié** : Security Center permet un provisionnement simplifié des solutions de partenaire intégrées. Pour des solutions comme les logiciels anti-programme malveillant et l’évaluation des vulnérabilités, Security Center peut approvisionner l’agent nécessaire sur vos machines virtuelles. En outre, pour les appliances de pare-feu, Security Center peut se charger d’une grande partie de la configuration réseau nécessaire.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Security Center. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Gestion et surveillance unifiées de l’intégrité** : Les clients peuvent utiliser des événements d’intégrité intégrés pour surveiller facilement les solutions des partenaires. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.

Actuellement, les solutions de sécurité intégrées incluent l’évaluation des vulnérabilités par [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) et [Rapid7](https://www.rapid7.com/products/insightvm/), et le pare-feu d’applications web Application Gateway de Microsoft.

> [!NOTE]
> Security Center n’installe pas Microsoft Monitoring Agent sur les appliances virtuelles de partenaire, car la plupart des fournisseurs de sécurité n’autorisent pas l’exécution d’agents externes sur leur appliance.
>
>

## <a name="how-security-solutions-are-integrated"></a>Comment sont intégrées les solutions de sécurité
Les solutions de sécurité Azure déployées à partir de Security Center sont automatiquement connectées. Vous pouvez également connecter d’autres sources de données de sécurité, notamment des ordinateurs s’exécutant en local ou dans d’autres clouds.

![Intégration de solutions de partenaire](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gérer des solutions de sécurité Azure intégrées et d’autres sources de données

1. Connectez-vous au [Portail Azure](https://azure.microsoft.com/features/azure-portal/).

2. Dans le **menu Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

3. Dans le menu de Security Center, sélectionnez **Solutions de sécurité**.

   ![Vue d’ensemble de Security Center](./media/security-center-partner-integration/overview.png)

Sous **Solutions de sécurité**, vous pouvez consulter des informations sur le fonctionnement des solutions de sécurité Azure intégrées et effectuer des tâches de gestion de base. Vous pouvez également connecter d’autres types de sources de données de sécurité, telles que des journaux d’activité de pare-feu et des alertes Azure AD Identity Protection au format CEF.

### <a name="connected-solutions"></a>Solutions connectées

La section **Solutions connectées** inclut des solutions de sécurité qui sont actuellement connectées à Security Center et des informations sur l’état de fonctionnement de chaque solution.  

![Solutions connectées](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

L’état d’une solution de partenaire peut être :

* Sain (vert) : aucun problème d’intégrité.
* Défectueux (rouge) : problème d’intégrité nécessitant une action immédiate.
* Problèmes d’intégrité (orange) : état d’intégrité non signalé par la solution.
* Non signalé (gris) : état non encore signalé par la solution (ce qui peut être le cas si celle-ci a été récemment connectée et est en cours de déploiement), ou aucune donnée d’intégrité n’est disponible.

> [!NOTE]
> Si les données sur l’état d’intégrité ne sont pas disponibles, Security Center affiche la date et l’heure du dernier événement reçu pour indiquer si la solution signale ou non des données. Si aucune donnée d’intégrité n’est disponible et qu’aucune alerte n’a été reçue au cours des 14 derniers jours, Security Center indique que la solution est défectueuse ou qu’elle ne signale pas de données.
>
>

1. Sélectionnez **AFFICHAGE** pour obtenir des informations et des options supplémentaires, notamment :

   - **Console de solution**. Ouvre l’expérience de gestion pour cette solution.
   - **Associer la machine virtuelle**. Ouvre le panneau Associer les applications. Il vous permet de connecter des ressources à la solution de partenaire.
   - **Supprimer la solution**.
   - **Configurer**.

   ![Détail de la solution partenaire](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Solutions découvertes

Security Center découvre automatiquement les solutions exécutées dans Azure mais qui ne sont pas connectées à Security Center. Elles sont ensuite affichées dans la section **Solutions découvertes**. Cela inclut les solutions Azure, telles que [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), ainsi que les solutions partenaires.

> [!NOTE]
> Le niveau Standard de Security Center est requis au niveau de l’abonnement pour la fonctionnalité de découverte des solutions. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security.
>
>

Sélectionne **CONNECTER** sous une solution pour l’intégrer à Security Center et être notifié sur les alertes de sécurité.

![Solutions découvertes](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center découvre aussi les solutions déployées dans l’abonnement, capables d’envoyer des journaux d’activité CEF (Common Event Format). Découvrez comment [Connecter une solution de sécurité](quick-security-solutions.md) qui utilise des journaux d’activité CEF dans Security Center.

### <a name="add-data-sources"></a>Ajouter des sources de données

La section **Ajouter des sources de données** comprend d’autres sources de données disponibles qui peuvent être connectées. Pour obtenir des instructions sur l’ajout de données à partir d’une de ces sources, cliquez sur **AJOUTER**.

![Sources de données](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportation de données vers un serveur SIEM

Les événements traités produits par Azure Security Center sont publiés dans le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure, l’un des types de journaux disponibles avec Azure Monitor. Azure Monitor offre un pipeline centralisé pour router les données de monitoring dans un outil SIEM. Ces données sont acheminées vers un hub d’événements, d’où elles peuvent ensuite être extraites dans un outil partenaire.

Pour ce faire, est utilisé le [seul pipeline de monitoring Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) permettant d’accéder aux données de monitoring à partir de votre environnement Azure. Cela vous permet de configurer facilement des systèmes SIEM et des outils de monitoring pour consommer les données.

Les sections suivantes expliquent comment configurer les données à diffuser vers un hub d’événements. Les étapes partent du principe qu’Azure Security Center est déjà configuré dans votre abonnement Azure.

Vue d’ensemble globale

![Vue d’ensemble globale](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quelles sont les données de sécurité Azure exposées à SIEM ?

Dans cette version, nous exposons les [alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md). Dans les versions à venir, nous enrichirons le jeu de données avec des recommandations de sécurité.

### <a name="how-to-setup-the-pipeline"></a>Comment configurer le pipeline

#### <a name="create-an-event-hub"></a>Création d’un concentrateur d’événements

Avant de commencer, vous devez [créer un espace de noms Event Hubs](../event-hubs/event-hubs-create.md). Cet espace de noms et cet hub d’événements sont la destination de toutes vos données de monitoring.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Diffuser en continu le journal des activités Azure sur les Event Hubs

Consultez l’article [Acheminer le journal des activités Azure vers Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installer un connecteur SIEM partenaire 

Le routage de vos données de monitoring vers un hub d’événement avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM et des outils de monitoring partenaires.

Consultez le lien suivant pour afficher la liste des [systèmes SIEM pris en charge](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="example-for-querying-data"></a>Exemple d’interrogation de données 

Voici quelques exemples de requêtes Splunk que vous pouvez utiliser pour extraire des données d’alerte :

| **Description de la requête** | **Requête** |
|----|----|
| Toutes les alertes| index=main Microsoft.Security/locations/alerts|
| Résumer le nombre d’opérations par leur nom| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Obtenir les informations des alertes : heure, nom, état, ID et abonnement | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Questions fréquentes : Azure Security Center](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation de Security Center.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
