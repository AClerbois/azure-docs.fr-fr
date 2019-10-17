---
title: Examiner les incidents avec Azure Sentinel | Microsoft Docs
description: Utilisez ce tutoriel pour apprendre à examiner les alertes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: fbab3eff3d7f742c2542e4264ef1b2124c5f15e5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024610"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Didacticiel : Examiner les incidents avec Azure Sentinel

> [!IMPORTANT]
> Le graphique d’examen est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail en production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ce tutoriel vous permet d’apprendre à examiner les alertes avec Azure Sentinel. Après avoir connecté vos sources de données à Azure Sentinel, vous souhaitez être averti en cas d’activité suspecte. Pour vous permettre de l’être, Azure Sentinel vous permet de créer des règles d’alerte avancées, qui génèrent des incidents que vous pouvez attribuer et examiner.

Cet article couvre les points suivants :
> [!div class="checklist"]
> * Investiguer les incidents
> * Utiliser le graphique d’examen
> * Répondre aux menaces

Un incident peut inclure plusieurs alertes. C’est une agrégation de toutes les preuves pertinentes pour une investigation spécifique. Un incident est créé en fonction des règles analytiques que vous avez créées sur la page **Analytics**. Les propriétés relatives aux alertes, telles que l’état et la gravité sont définies au niveau de l’incident. Après avoir informé Azure Sentinel des types de menaces que vous recherchez et de comment les trouver, vous pouvez surveiller les menaces détectées en étudiant des incidents.

## <a name="prerequisites"></a>Prérequis
Vous ne pourrez examiner l’incident que si vous avez utilisé les champs de mappage d’entité lorsque vous avez configuré votre règle d’analyse. Le graphique d’examen requiert que votre incident d’origine comprenne des entités.

## <a name="how-to-investigate-incidents"></a>Comment examiner les incidents

1. Sélectionnez **Incidents**. La page **Incidents** vous permet de connaître le nombre d’incidents, le nombre d’incidents ouverts, le nombre d’incidents définis sur **En cours** et le nombre d’incidents clos. Pour chaque incident, vous pouvez voir l’heure à laquelle il s’est produit et son état. Examinez la gravité de décider quels incidents gérer en premier.

    ![Afficher la gravité de l’incident](media/tutorial-investigate-cases/incident-severity.png)

1. Vous pouvez filtrer les incidents selon les besoins, par état ou par gravité par exemple.

1. Pour commencer un examen, sélectionnez un incident spécifique. Sur la droite, vous pouvez voir des informations détaillées sur l’incident, notamment sa gravité, le résumé du nombre d’entités impliquées, les événements bruts qui ont déclenché cet incident et l’ID unique de l’incident.

1. Pour afficher plus de détails sur les alertes et les entités de l’incident, sélectionnez **Afficher tous les détails** dans la page incident et passez en revue les onglets pertinents qui résument les informations sur l’incident. Dans l’onglet **Alertes**, examinez l’alerte elle-même. Vous pouvez voir toutes les informations pertinentes sur l’alerte : la requête ayant déclenché l’alerte, le nombre de résultats retournés par la requête et la capacité d’exécuter des playbooks sur les alertes. Pour aller encore plus en détail dans l’incident, sélectionnez le nombre d’**Événements**. Cela ouvre la requête ayant généré les résultats et les événements ayant déclenché l’alerte dans Log Analytics. Dans l’onglet **Entités**, vous pouvez voir toutes les entités mappées comme faisant partie de la définition d’une règle d’alerte.

    ![Afficher les détails de l’alerte](media/tutorial-investigate-cases/alert-details.png)

1. Si vous étudiez activement un incident, il est judicieux de définir l’état de l’incident sur **En cours** jusqu’à ce que vous le fermiez.

1. Les incidents peuvent être assignés à un utilisateur spécifique. Pour chaque incident, vous pouvez affecter un propriétaire, en définissant le champ **Propriétaire** du cas. Tous les incidents ne sont pas assignés lorsqu’ils sont créés. Vous pouvez également ajouter des commentaires afin que d’autres analystes soient en mesure de comprendre ce que vous avez étudié et ce que sont les préoccupations liées à l’incident.

    ![Affecter un incident à l’utilisateur](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Sélectionnez **Examiner** pour afficher la carte d’examen.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Utiliser le graphique d’examen pour approfondir les recherches

Le graphique d’examen permet aux analystes de poser les bonnes questions pour chaque examen. Le graphique d’examen vous aide à comprendre l’étendue et à identifier la cause racine d’une menace de sécurité potentielle en mettant en corrélation les données pertinentes avec toute entité impliquée. Vous pouvez explorer plus en détail toutes les entités présentées dans le graphique en les sélectionnant et en choisissant entre les différentes options de développement.  
  
Le graphique d’examen vous fournit les éléments suivants :

- **Contexte visuel des données brutes** : Le graphique visuel en direct affiche les relations entre les entités extraites automatiquement des données brutes. Cela vous permet de visualiser aisément les connexions entre les différentes sources de données.

- **Découverte de l’étendue d’examen complète** : Étendez la portée de votre examen à l’aide de requêtes d’exploration intégrées pour découvrir toute la portée d’une violation de la sécurité.

- **Étapes d’examen intégrées** : Utilisez les options d’exploration prédéfinies pour vous assurer que vous posez les bonnes questions en face à une menace.

Pour utiliser le graphique d’examen :

1. Sélectionnez un incident, puis **Examiner**. Vous accédez au graphique d’examen. Le graphique fournit une carte illustrant les entités directement connectées à l’alerte et à chaque ressource connectée.

   > [!IMPORTANT] 
   > Vous ne pourrez examiner l’incident que si vous avez utilisé les champs de mappage d’entité lorsque vous avez configuré votre règle d’analyse. Le graphique d’examen requiert que votre incident d’origine comprenne des entités.

   ![Afficher la carte](media/tutorial-investigate-cases/map1.png)

1. Sélectionnez une entité pour ouvrir le volet **Entités** et consulter les informations y afférentes.

    ![Afficher les entités dans la carte](media/tutorial-investigate-cases/map-entities.png)
  
1. Élargissez votre examen en survolant chaque entité pour afficher une liste de questions conçues par nos experts et analystes en sécurité par type d’entité afin d’approfondir votre examen. Nous appelons ces options des **requêtes d’exploration**.

    ![Découvrir plus de détails](media/tutorial-investigate-cases/exploration-cases.png)

   Par exemple, sur un ordinateur, vous pouvez demander des alertes associées. Si vous sélectionnez une requête d’exploration, les droits résultants sont rajoutés au graphique. Dans cet exemple, la sélection d’**Alertes associées** a renvoyé les alertes suivantes dans le graphique :

    ![Afficher les alertes associées](media/tutorial-investigate-cases/related-alerts.png)

1. Pour chaque requête d’exploration, vous pouvez sélectionner l’option permettant d’ouvrir les résultats bruts de l’événement et la requête utilisée dans Log Analytics, en sélectionnant **Événements\>** .

1. Pour comprendre l’incident, le graphique vous donne une chronologie parallèle.

    ![Afficher la chronologie dans la carte](media/tutorial-investigate-cases/map-timeline.png)

1. Pointez sur la chronologie pour voir quels éléments du graphique se sont produits à un moment donné.

    ![Utiliser la chronologie dans la carte pour examiner les alertes](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à commencer à examiner les incidents à l’aide d’Azure Sentinel. Passez au didacticiel vous apprenant [comment répondre aux menaces à l’aide de playbooks automatisés](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Répondre aux menaces](tutorial-respond-threats-playbook.md) pour automatiser vos réponses aux menaces.

