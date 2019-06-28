---
title: Exemples et scénarios courants - Azure Logic Apps | Microsoft Docs
description: Exemples, scénarios, tutoriels et procédures pas à pas relatifs à Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/2018
ms.openlocfilehash: 89e0294db3178cedd3b14aada0b505787b17c75e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303687"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Scénarios, exemples, didacticiels et procédures pas à pas courants relatifs à Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous permet d’orchestrer et d’intégrer différents services en fournissant [plus de 100 connecteurs prêts à l’emploi](../connectors/apis-list.md), depuis des connecteurs SQL Server ou SAP locaux jusqu’à Microsoft Cognitive Services. Le service Logic Apps est un service « sans serveur ». Ainsi, vous n’avez pas à vous soucier de la mise à l’échelle ni des instances. Il vous suffit de définir le workflow avec un déclencheur et les actions exécutées par ce workflow. La plateforme sous-jacente gère la mise à l’échelle, la disponibilité et les performances. Le service Logic Apps se révèle d’une grande utilité pour les cas d’usage et les scénarios impliquant la coordination de plusieurs actions dans différents systèmes.

Voici quelques exemples et scénarios courants qui vous aideront à en savoir plus sur les nombreux modèles et fonctionnalités pris en charge par [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="popular-starting-points-for-logic-app-workflows"></a>Points de départ courants pour les workflows d’application logique

Chaque application logique commence avec un seul et unique [*déclencheur*](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui démarre votre workflow d’application logique et transmet des données. Certains connecteurs fournissent des déclencheurs, qui présentent les types suivants :

* *Déclencheurs d'interrogation* : vérifient régulièrement l'existence de nouvelles données dans un point de terminaison de service. Lorsque de nouvelles données existent, le déclencheur crée et exécute une nouvelle instance de workflow en utilisant ces données en guise d’entrée.

* *Déclencheurs d'émission* : écoutent les données au niveau d'un point de terminaison de service et attendent qu'un événement spécifique se produise. Lorsque cet événement se produit, le déclencheur s’active aussitôt en créant et exécutant une instance de workflow qui utilise toutes les données disponibles en tant qu’entrée.

Voici quelques exemples de déclencheurs courants :

* Interrogation : 

  * Le [déclencheur **Planification - Périodicité**](../connectors/connectors-native-recurrence.md) vous permet de définir la date et l’heure de début et la périodicité du déclenchement de votre application logique. 
  Par exemple, vous pouvez sélectionner les jours de la semaine et les heures de la journée auxquels votre application logique doit se déclencher.

  * Le déclencheur « Lors de la réception d’un message électronique » permet à votre application logique de vérifier l’existence de nouveaux messages électroniques à partir de n’importe quel fournisseur de messagerie pris en charge par Logic Apps, par exemple, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/), etc.

  * Le [déclencheur **HTTP**](../connectors/connectors-native-http.md) permet à votre application logique de vérifier un point de terminaison de service spécifié en communiquant par le biais de HTTP.
  
* Émission :

  * Le [déclencheur **Requête/réponse - Requête**](../connectors/connectors-native-reqres.md) permet à votre application logique de recevoir des requêtes HTTP et de répondre d’une certaine manière aux événements en temps réel.

  * Le [déclencheur **HTTP Webhook**](../connectors/connectors-native-webhook.md) s’abonne au point de terminaison d’un service en inscrivant une *URL de rappel* auprès de ce service. 
  De cette façon, le service peut tout simplement informer le déclencheur lorsque l’événement spécifié se produit, ce qui évite au déclencheur d’avoir à interroger le service.

Après avoir reçu une notification concernant de nouvelles données ou un événement, le déclencheur s’active, crée une instance de workflow d’application logique et exécute les actions dans le workflow. Vous pouvez accéder à toutes les données du déclencheur dans l’ensemble du workflow. Par exemple, le déclencheur « On a new tweet » (En cas de nouveau tweet) transmet le contenu du tweet dans l’exécution de l’application logique. 

## <a name="respond-to-triggers-and-extend-actions"></a>Répondre aux déclencheurs et étendre des actions

Pour les systèmes et services pour lesquels aucun connecteur n’a été publié, vous pouvez également étendre les applications logiques.

* [Créer des actions ou des déclencheurs personnalisés](../logic-apps/logic-apps-create-api-app.md)
* [Configurer des actions de longue durée pour les exécutions de flux de travail](../logic-apps/logic-apps-create-api-app.md)
* [Répondre aux événements et actions externes à l’aide de webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Appeler, déclencher ou imbriquer des flux de travail avec des réponses synchrones aux requêtes HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutoriel : explique comment créer un tableau de bord social reposant sur l'intelligence artificielle en quelques minutes avec Logic Apps et Power BI](https://aka.ms/logicappsdemo)
* [Vidéo : explique comment répondre aux webhooks SMS Twilio et envoyer une réponse textuelle](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fonctionnalités de flux de contrôle, de gestion des erreurs et de journalisation

Les applications logiques incluent de puissantes fonctionnalités de flux de contrôle avancé, notamment des conditions, des commutateurs, des boucles et des étendues. Pour garantir la résilience de vos solutions, vous pouvez également implémenter la gestion des erreurs et des exceptions dans vos flux de travail. Pour la notification et les journaux de diagnostic relatifs à l’état d’exécution des flux de travail, Azure Logic Apps fournit également la supervision et des alertes.

* Exécuter des actions différentes en fonction [d’instructions conditionnelles](../logic-apps/logic-apps-control-flow-conditional-statement.md) et [d’instructions switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Répéter les étapes ou traiter les éléments dans des tableaux et collections avec des boucles](../logic-apps/logic-apps-control-flow-loops.md)
* [Regrouper des actions avec des étendues](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Gestion des erreurs de création et des exceptions dans un flux de travail](../logic-apps/logic-apps-exception-handling.md)
* [Cas d’usage : montre comment un prestataire de soins de santé utilise la gestion des exceptions d'application logique pour les flux de travail HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Activer la surveillance, la journalisation et les alertes pour les applications logiques existantes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Activer la surveillance et la journalisation des diagnostics lors de la création d’applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Déployer et gérer des applications logiques

Vous pouvez développer et déployer des applications logiques entièrement avec Visual Studio, Azure DevOps ou tout autre outil de génération automatisée et contrôle de code source. Pour prendre en charge le déploiement de flux de travail et de connexions dépendantes d’un modèle de ressource, les applications logiques utilisent des modèles de déploiement de ressources Azure. Les outils Visual Studio génèrent automatiquement ces modèles, que vous pouvez archiver dans le contrôle de code source à des fins de contrôle de version.

* [Créer et déployer des applications logiques avec Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Activer la surveillance, la journalisation et les alertes pour les applications logiques existantes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Créer un modèle de déploiement automatisé](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Types de contenu, conversions et transformations d’une exécution

Vous pouvez accéder à plusieurs types de contenu, les convertir et les transformer à l’aide des nombreuses fonctions du [langage de définition de flux de travail](https://aka.ms/logicappsdocs) d’Azure Logic Apps. Par exemple, vous pouvez effectuer des conversions entre une chaîne, JSON et XML avec les expressions de flux de travail `@json()` et `@xml()`. Le moteur Logic Apps préserve les types de contenu pour prendre en charge le transfert de contenu entre les services sans perte.

* [Fonctionnement des expressions de flux de travail dans les applications logiques](../logic-apps/logic-apps-author-definitions.md)
* [Gérer les types de contenu non-JSON](../logic-apps/logic-apps-content-type.md), comme `application/xml`, `application/octet-stream` et`multipart/formdata`
* [Schéma du langage de définition du flux de travail pour Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Autres intégrations et fonctionnalités

Les applications logiques offrent également une intégration avec de nombreux services comme Azure Functions, la Gestion des API Azure, Azure App Services et les points de terminaison HTTP personnalisés, par exemple, REST et SOAP.

* [Créer un tableau de bord social en temps réel avec Azure Serverless](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Appeler Azure Functions à partir d’applications logiques](../logic-apps/logic-apps-azure-functions.md)
* [Tutoriel : Déclencher des applications logiques avec Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutoriel : Surveiller les modifications d'une machine virtuelle avec Azure Event Grid et Azure Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutoriel : Créer une fonction qui s'intègre à Azure Logic Apps et à Microsoft Cognitive Services pour analyser le sentiment lié à la publication Twitter](../azure-functions/functions-twitter-email.md)
* [Tutoriel : Notifications et surveillance à distance IoT à l'aide d'Azure Logic Apps, avec connexion de votre hub IoT et de votre boîte aux lettres](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog : Appeler des points de terminaison SOAP à partir d'applications logiques](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Scénarios de bout en bout

* [Livre blanc : Intégration de la gestion de bout en bout des dossiers aux services Azure comme Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Témoignages client

Découvrez comment Azure Logic Apps, ainsi que d’autres services Azure et produits Microsoft, ont aidé [ces sociétés](https://aka.ms/logic-apps-customer-stories) à améliorer leur flexibilité et à se concentrer sur leurs activités principales en simplifiant, organisant, automatisant et orchestrant des processus complexes.

## <a name="next-steps"></a>Étapes suivantes

* [Créer à partir des définitions d’application logique avec JSON](../logic-apps/logic-apps-author-definitions.md)
* [Gérer les erreurs et les exceptions dans Azure Logic Apps](../logic-apps/logic-apps-exception-handling.md)
* [Submit your comments, questions, feedback, or suggestions for improving Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps) (Envoyer vos questions, commentaires ou suggestions pour améliorer Azure Logic Apps)
