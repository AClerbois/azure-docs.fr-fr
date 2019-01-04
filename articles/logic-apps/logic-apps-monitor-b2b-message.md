---
title: Surveiller des messages B2B dans Log Analytics - Azure Logic Apps | Microsoft Docs
description: Surveiller des messages AS2, X12 et EDIFACT pour des comptes d’intégration et Azure Logic Apps et configurer la journalisation des diagnostics avec Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: d5425cf7c2ea39770bbc3e9368f34d57d7279842
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53534874"
---
# <a name="monitor-b2b-messages-with-azure-log-analytics-in-azure-logic-apps"></a>Surveiller des messages B2B dans Log Analytics dans Azure Logic Apps

Après avoir configuré la communication B2B entre les partenaires commerciaux dans votre compte d’intégration, les partenaires peuvent échanger des messages entre eux. Pour vérifier que cette communication fonctionne comme prévu, vous pouvez surveiller des messages AS2, X12 et EDIFACT et configurer la journalisation des diagnostics pour votre compte d’intégration avec [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Ce service surveille vos environnements cloud et local vous aide à maintenir leur disponibilité et leurs performances, et collecte des détails d’exécution et des événements pour un débogage enrichi. Vous pouvez également [utiliser ces données avec d’autres services](#extend-diagnostic-data), tels que Stockage Azure et Azure Event Hubs.

> [!NOTE]
> Cette page peut encore contenir des références à Microsoft Operations Management Suite (OMS), qui sera [retiré du marché en janvier 2019 ](../azure-monitor/platform/oms-portal-transition.md), mais remplace ces étapes avec Azure Log Analytics lorsque cela est possible. 

## <a name="prerequisites"></a>Prérequis

* Une application logique configurée avec une journalisation des diagnostics. Découvrez comment [créer une application logique](quickstart-create-first-logic-app-workflow.md) et comment [configurer la journalisation pour cette application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Une fois les exigences précédentes remplies, vous avez également besoin d’un espace de travail Log Analytics, que vous utilisez pour la surveillance et le suivi des communications B2B via Log Analytics. Si vous n’avez pas d’espace de travail Log Analytics, découvrez [comment créer un espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un compte d’intégration lié à votre application logique. Découvrez comment [créer un compte d’intégration lié à votre application logique](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Activer la journalisation des diagnostics

Vous pouvez activer la journalisation directement à partir de votre compte d’intégration ou [via le service Azure Monitor](#azure-monitor-service). Azure Monitor exerce une surveillance de base avec des données de niveau de l’infrastructure. En savoir plus sur [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Activer la journalisation à partir du compte d’intégration

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

   ![Rechercher et sélectionner votre compte d’intégration, sélectionnez « Paramètres de diagnostic »](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Trouvez et sélectionnez maintenant votre compte d’intégration. Dans les listes de filtres, sélectionnez les valeurs qui s’appliquent à votre compte d’intégration.
Lorsque vous avez terminé, choisissez **Ajouter le paramètre de diagnostic**.

   | Propriété | Valeur | Description | 
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | L’abonnement Azure associé à votre compte d’intégration | 
   | **Groupe de ressources** | <*nom-groupe-de-ressources-Azure*> | Le groupe de ressources Azure pour votre compte d’intégration | 
   | **Type de ressource** | **Comptes d’intégration** | Le type de la ressource Azure où vous souhaitez activer la journalisation | 
   | **Ressource** | <*integration-account-name*> | Le nom de votre ressource Azure où vous souhaitez activer la journalisation | 
   ||||  

   Par exemple : 

   ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Fournissez un nom pour votre nouveau paramètre de diagnostic et sélectionnez votre espace de travail Log Analytics et les données que vous souhaitez journaliser.

   1. Sélectionnez **Envoyer à Log Analytics**. 

   1. Sous **Log Analytics**, sélectionnez **Configurer**. 

   1. Sous **Espaces de travail OMS**, sélectionnez l’espace de travail Log Analytics que vous souhaitez utiliser pour la journalisation. 

      > [!NOTE]
      > Les espaces de travail OMS sont remplacés par des espaces de travail Log Analytics. 

   1. Sous **Journal**, sélectionnez la catégorie **IntegrationAccountTrackingEvents** et choisissez **Save**.

   Par exemple :  

   ![Configurer Log Analytics pour pouvoir envoyer des données de diagnostic à un journal](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. À présent, [configurez le suivi de vos messages B2B dans Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Activer la journalisation via Azure Monitor

1. Sur le [portail Azure](https://portal.azure.com), dans le menu principal Azure, sélectionnez **Surveiller**. Sous **Paramètres**, sélectionnez **Paramètres de diagnostic**. 

   ![Sélectionnez « Surveiller » > « Paramètres de diagnostic » > votre compte d’intégration](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Trouvez et sélectionnez maintenant votre compte d’intégration. Dans les listes de filtres, sélectionnez les valeurs qui s’appliquent à votre compte d’intégration.
Lorsque vous avez terminé, choisissez **Ajouter le paramètre de diagnostic**.

   | Propriété | Valeur | Description | 
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | L’abonnement Azure associé à votre compte d’intégration | 
   | **Groupe de ressources** | <*nom-groupe-de-ressources-Azure*> | Le groupe de ressources Azure pour votre compte d’intégration | 
   | **Type de ressource** | **Comptes d’intégration** | Le type de la ressource Azure où vous souhaitez activer la journalisation | 
   | **Ressource** | <*integration-account-name*> | Le nom de votre ressource Azure où vous souhaitez activer la journalisation | 
   ||||  

   Par exemple : 

   ![Configurer les diagnostics pour votre compte d’intégration](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Fournissez un nom pour votre nouveau paramètre de diagnostic et sélectionnez votre espace de travail Log Analytics et les données que vous souhaitez journaliser.

   1. Sélectionnez **Envoyer à Log Analytics**. 

   1. Sous **Log Analytics**, sélectionnez **Configurer**. 

   1. Sous **Espaces de travail OMS**, sélectionnez l’espace de travail Log Analytics que vous souhaitez utiliser pour la journalisation. 

      > [!NOTE]
      > Les espaces de travail OMS sont remplacés par des espaces de travail Log Analytics. 

   1. Sous **Journal**, sélectionnez la catégorie **IntegrationAccountTrackingEvents** et choisissez **Save**.

   Par exemple :  

   ![Configurer Log Analytics pour pouvoir envoyer des données de diagnostic à un journal](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. À présent, [configurez le suivi de vos messages B2B dans Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Utiliser les données de diagnostic avec d’autres services

Avec Azure Log Analytics, vous pouvez étendre le mode d’utilisation des données de diagnostic de votre application logique avec d’autres services Azure, par exemple : 

* [Archivage des journaux de diagnostic Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Diffuser en continu les journaux de diagnostic vers Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Vous pouvez ensuite obtenir une surveillance en temps réel en utilisant les ressources de télémétrie et d’analyse d’autres services, tels que [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) et [Power BI](../azure-monitor/platform/powerbi.md). Par exemple : 

* [Diffuser les données d’Event Hubs vers Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyser les données de diffusion avec Stream Analytics et créer un tableau de bord analytique en temps réel dans Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Selon les options que vous souhaitez configurer, veillez au préalable à [créer un compte de stockage Azure](../storage/common/storage-create-storage-account.md) ou à [créer un Event Hub Azure](../event-hubs/event-hubs-create.md). Vous pouvez ensuite sélectionner les destinations où vous souhaitez envoyer les données de diagnostic.
Les périodes de rétention s’appliquent uniquement lorsque vous choisissez d’utiliser un compte de stockage.

![Envoyer des données à un compte de stockage ou à un hub d’événements Azure](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Schémas de suivi pris en charge

Azure prend en charge les types de schémas de suivi ci-dessous, qui ont tous des schémas fixes, à l’exception du type personnalisé.

* [Schéma de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma de suivi X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schéma de suivi personnalisé](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* [Suivre les messages B2B dans Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Suivre les messages B2B dans Azure Log Analytics")
* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

