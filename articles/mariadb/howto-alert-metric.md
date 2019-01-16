---
title: Configurer des alertes de métriques pour Azure Database for MariaDB dans le portail Azure
description: Cet article décrit comment configurer et consulter des alertes Métrique pour Azure Database for MariaDB à partir du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: 9cc08920c5b8814a3e4b649f4518dce78c7cb38e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160524"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Utiliser le portail Azure pour configurer des alertes sur des métriques pour Azure Database for MariaDB

Cet article explique comment configurer des alertes Azure Database pour MariaDB à l’aide du portail Azure. Vous pouvez recevoir une alerte basée sur des métriques de surveillance pour vos services Azure.

L’alerte se déclenche quand la valeur d’une métrique spécifiée dépasse le seuil que vous avez défini. L’alerte se déclenche une première fois lorsque la condition est vérifiée, puis une deuxième fois quand elle cesse de l’être.

Vous pouvez configurer une alerte pour effectuer les actions suivantes lors de son déclenchement :
* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs
* Envoyer un e-mail à d’autres adresses que vous spécifiez
* Appeler un webhook

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide des ressources suivantes :
* [Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interface de ligne de commande (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric"></a>Créer une règle d'alerte d'une métrique
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez le serveur Azure Database pour MariaDB à surveiller.

2. Sous la section **Surveillance** de la barre latérale, sélectionnez **Alertes**, comme illustré :

   ![Sélectionner des règles d’alerte](./media/howto-alert-metric/2-alert-rules.png)

3. Sélectionnez **Ajouter une alerte Métrique** (icône +).

4. La page **Créer une règle** s’ouvre, comme illustré ci-dessous. Entrez les informations obligatoires :

   ![Formulaire Ajouter une alerte Métrique](./media/howto-alert-metric/4-add-rule-form.png)

5. Dans la section **Condition**, sélectionnez **Ajouter une condition**.

6. Sélectionnez une métrique dans la liste des signaux d'alerte. Dans cet exemple, sélectionnez « Storage percent ».
   
   ![Sélectionner la métrique](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Configurez la logique d’alerte, notamment les éléments **Condition** (par exemple, « Supérieur à »), **Seuil** (par exemple, 85 %), **Agrégation de temps**, **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche (par exemple, « Au cours des 30 dernières minutes » et **Frequency**.
   
   Sélectionnez **Terminé** lorsque vous avez terminé.

   ![Sélectionner la métrique](./media/howto-alert-metric/7-set-threshold-time.png)

8. Dans la section **Groupes d’actions**, sélectionnez **Créer un nouveau** pour créer un nouveau groupe afin de recevoir des notifications sur l’alerte.

9. Renseignez le formulaire « Ajouter un groupe d'actions » avec un nom, un nom court, un abonnement et un groupe de ressources.

10. Configurez un type d'action **E-mail/SMS/Push/Voix**.
    
   Choisissez « Envoyer un e-mail au rôle Azure Resource Manager » pour sélectionner les propriétaires de l'abonnement, les contributeurs et les lecteurs qui recevront les notifications.
   
   Vous pouvez aussi fournir un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.

   Sélectionnez **OK** lorsque vous avez terminé.

   ![Groupe d’actions](./media/howto-alert-metric/10-action-group-type.png)

11. Spécifiez un nom de règle d’alerte, une description et une gravité.

   ![Groupe d’actions](./media/howto-alert-metric/11-name-description-severity.png) 

12. Sélectionnez **Créer une règle d’alerte** pour créer l’alerte.

   Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="manage-your-alerts"></a>Gérez vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et exécuter les actions suivantes :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent concernant cette alerte.
* **Modifiez** ou **Supprimez** la règle d’alerte.
* **Désactivez** ou **Activez** l’alerte, selon que vous voulez arrêter temporairement ou reprendre la réception de notifications.


## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus en détail la [configuration des webhooks dans les alertes](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Consultez une [vue d’ensemble de la collecte des métriques](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
