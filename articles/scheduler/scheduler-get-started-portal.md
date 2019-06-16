---
title: Créer des travaux planifiés avec Azure Scheduler - Portail Azure | Microsoft Docs
description: Découvrez comment créer, planifier et exécuter votre premier travail automatisé avec Azure Scheduler dans le portail Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532008"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Créer et planifier votre premier travail avec Azure Scheduler - Portail Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) remplace Azure Scheduler, qui est en cours de retrait. Pour planifier des travaux, [essayez à la place Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Ce tutoriel montre comment vous pouvez créer et planifier facilement un travail, puis surveiller et gérer ce travail. 

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

## <a name="create-job"></a>Créer un travail

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).  

1. Dans le menu principal d’Azure, choisissez **Créer une ressource**. Dans la zone de recherche, entrez « planificateur ». Dans la liste des résultats, sélectionnez **Planificateur**, puis choisissez **Créer**.

   ![Créer une ressource Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Créez maintenant une tâche qui envoie une demande GET à cette URL : `https://www.microsoft.com/` 

1. Sous **Tâche du planificateur**, entrez ces informations :

   | Propriété | Exemple de valeur | Description |
   |----------|---------------|-------------| 
   | **Nom** | getMicrosoft | Nom pour votre travail | 
   | **Collection de travaux** | <*job-collection-name*> | Créez une collection de travaux ou sélectionnez une collection existante. | 
   | **Abonnement** | <*Azure-subscription-name*> | Nom de votre abonnement Azure. | 
   |||| 

1. Sélectionnez **Paramètres d’action - Configurer**, spécifiez ces informations, puis choisissez **OK** quand vous avez terminé :

   | Propriété | Exemple de valeur | Description |
   |----------|---------------|-------------| 
   | **Action** | **HTTP** | Type d’action à exécuter | 
   | **Méthode** | **Get** | Méthode à appeler | 
   | **URL** | **https://www.microsoft.com** | URL de destination | 
   |||| 
   
   ![Définir un travail](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Sélectionnez **Planification - Configurer**, définissez la planification, puis sélectionnez **OK** quand vous avez terminé :

   Bien qu’il soit possible de créer un travail ponctuel, cet exemple configure une planification périodique.

   | Propriété | Exemple de valeur | Description |
   |----------|---------------|-------------| 
   | **Périodicité** | **Récurrent** | Un travail ponctuel ou récurrent | 
   | **Commencent le** | <*date du jour*> | Date de début du travail | 
   | **Tous les** | **1 heure** | Intervalle et fréquence de la récurrence | 
   | **Fin** | **Fin le** deux jours à compter de la date du jour | Date de fin du travail | 
   | **Décalage UTC** | **UTC +08:00** | La différence de temps entre le temps universel coordonné (UTC) et l’heure observée à votre emplacement | 
   |||| 

   ![Définir la planification](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Une fois ces opérations effectuées, sélectionnez **Créer**.

   Une fois que vous avez créé votre travail, Azure déploie votre travail, qui apparaît sur le tableau de bord Azure. 

1. Quand Azure affiche une notification indiquant que le déploiement a réussi, choisissez **Épingler au tableau de bord**. Sinon, choisissez l’icône **Notifications** (cloche) dans la barre d’outils Azure, puis choisissez **Épingler au tableau de bord**.

## <a name="monitor-and-manage-jobs"></a>Surveiller et gérer les travaux

Pour examiner, surveiller et gérer votre travail, choisissez votre travail dans le tableau de bord Azure. Sous **Paramètres**, voici les zones que vous pouvez examiner et gérer pour votre travail :

![Paramètres d’un travail](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Pour plus d’informations sur ces zones, sélectionnez une zone :

* [**Propriétés**](#properties)
* [**Paramètres d’action**](#action-settings)
* [**Planification**](#schedule)
* [**Historique**](#history)
* [**Utilisateurs**](#users)

<a name="properties"></a>

### <a name="properties"></a>properties

Pour voir les propriétés en lecture seule qui décrivent les métadonnées de gestion pour votre travail, sélectionnez **Propriétés**.

![Afficher les propriétés du travail](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Paramètres d’action

Pour modifier les paramètres avancés de votre travail, sélectionnez **Paramètres d’action**. 

![Examiner les paramètres d’action](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Type d’action | Description | 
|-------------|-------------| 
| Tous les types | Vous pouvez modifier les paramètres **Stratégie de nouvelle tentative** et **Action d’erreur**. | 
| HTTP et HTTPS | Vous pouvez changer **Méthode** pour n’importe quelle méthode autorisée. Vous pouvez également ajouter, supprimer ou modifier les en-têtes et les informations d’authentification de base. | 
| File d’attente de stockage| Vous pouvez modifier le compte de stockage, le nom de la file d’attente, le jeton SAP et le corps. | 
| Service Bus | Vous pouvez modifier l’espace de noms, le chemin de la rubrique ou de la file d’attente, les paramètres d’authentification, le type de transport, les propriétés du message et le corps du message. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Planification

Si vous configurez une planification via l’Assistant Travail, vous pouvez modifier cette planification, comme la date et l’heure de début, la planification de la récurrence, et la date et l’heure de fin pour les tâches récurrentes.
Vous pouvez aussi créer des [planifications plus complexes et des récurrences avancées](scheduler-advanced-complexity.md).

Pour modifier l’affichage ou la planification de votre travail, sélectionnez **planification** :

![Afficher la planification du travail](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Historique

Pour afficher des métriques relatives à chaque exécution pour un travail sélectionné, sélectionnez **Historique**. Ces métriques fournissent des valeurs en temps réel sur l’intégrité de votre travail, comme l’état, le nombre de nouvelles tentatives, le nombre d’occurrences, l’heure de début et l’heure de fin.

![Afficher l’historique et les métriques des travaux](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Pour afficher les détails de l’historique pour chaque exécution, comme la réponse complète pour chaque exécution, sous **Historique**, sélectionnez chaque exécution. 

![Afficher les détails de l’historique d’un travail](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Utilisateurs

Vous pouvez gérer l’accès à Azure Scheduler pour chaque utilisateur à un niveau granulaire avec le contrôle d’accès en fonction du rôle (RBAC) Azure. Pour découvrir comment configurer l’accès en fonction des rôles, consultez [Gérer l’accès avec RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Étapes suivantes

* Découvrir plus d’informations sur les [concepts, la terminologie et la hiérarchie des entités](scheduler-concepts-terms.md)
* [Créer des planifications complexes et des récurrences avancées](scheduler-advanced-complexity.md)
* Découvrir plus d’informations sur la [haute disponibilité et la fiabilité pour Scheduler](scheduler-high-availability-reliability.md)
* Découvrir plus d’informations sur les [limites, les quotas, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)
