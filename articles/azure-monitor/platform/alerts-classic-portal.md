---
title: Créer, afficher et gérer des alertes de métrique classiques à l’aide d’Azure Monitor
description: Découvrez comment utiliser le portail Azure, l’interface CLI ou PowerShell pour créer, afficher et gérer des règles d’alerte de métrique classique.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 6b74d83de0495e3436c9bef623a827e8a1496767
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343285"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Créer, afficher et gérer des alertes de métrique classiques à l’aide d’Azure Monitor

Dans Azure Monitor, les alertes de métrique classiques vous avertissent lorsque l’une de vos métriques dépasse un certain seuil. Les alertes de métriques classiques constituent une ancienne fonctionnalité qui permet d’obtenir des alertes uniquement sur les métriques sans dimensions. Il existe une fonctionnalité plus récente et plus avancée : alertes de métrique. Vous pouvez en savoir plus sur la nouvelle fonctionnalité d’alertes de métrique dans la [vue d’ensemble des alertes de métrique](../../azure-monitor/platform/alerts-metric-overview.md). Dans cet article, nous allons décrire comment créer, afficher et gérer des règles d’alerte de métrique classique via le portail Azure, Azure CLI et PowerShell.

## <a name="with-azure-portal"></a>Avec le Portail Azure

1. Dans le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller, puis sélectionnez-la.

2. Sélectionnez **Alertes (classiques)** dans la section **SURVEILLANCE**. Le texte et l’icône peuvent varier légèrement pour les différentes ressources. Si vous ne trouvez pas **Alertes (classiques)** à cet endroit, vous les trouverez peut-être dans **Alertes** ou **Règles d’alerte**.

    ![Surveillance](media/alerts-classic-portal/AlertRulesButton.png)

3. Sélectionnez la commande **Ajouter une alerte de métrique (classique)** puis renseignez les champs.

    ![Ajouter une alerte](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nommez** votre règle d’alerte. Choisissez ensuite une **Description** qui apparait également dans les adresses électroniques de notification.

5. Sélectionnez la **métrique** que vous souhaitez surveiller. Choisissez ensuite une **Condition** et une valeur de **seuil** pour la métrique. Choisissez également la **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche. Par exemple, si vous utilisez la période « Au cours des 5 dernières minutes » et que votre alerte recherche une utilisation de l’UC supérieure à 80 %, l’alerte se déclenche quand l’utilisation de l’UC dépasse 80 % depuis cinq minutes. Après le premier déclenchement, elle se déclenche à nouveau lorsque l’UC reste au-dessous de 80 % pendant cinq minutes. La mesure de la métrique de l’UC est effectuée chaque minute.

6. Sélectionnez **Propriétaires de messagerie...** si vous souhaitez que les administrateurs et les coadministrateurs reçoivent des notifications par e-mail lorsque l’alerte se déclenche.

7. Si vous souhaitez envoyer des notifications à d’autres adresses e-mail lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)**. Séparez les e-mails par des points-virgules, selon le format suivant : *email@contoso.com;email2@contoso.com*

8. Insérez un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.

9. Si vous utilisez Azure Automation, vous pouvez sélectionner un Runbook à exécuter lorsque l’alerte se déclenche.

10. Sélectionnez **OK** pour créer l’alerte.

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

Une fois que vous avez créé une alerte, vous pouvez la sélectionner et exécuter l’une des tâches suivantes :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent.
* La modifier ou la supprimer.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte.

## <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Les sections précédentes décrivaient comment créer, afficher et gérer des règles d’alerte de métrique à l’aide du portail Azure. Cette section décrit comment faire de même à l’aide de l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) multiplateforme. Le plus rapide pour commencer à utiliser Azure CLI est de s’appuyer sur [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Obtenir toutes les règles d’alerte de métrique classique dans un groupe de ressources

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Consulter les détails d’une règle d’alerte métrique classique particulière

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Créer une règle d’alerte de métrique classique

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Supprimer une règle d’alerte de métrique classique

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Avec PowerShell

Cette section montre comment utiliser les commandes PowerShell pour créer, afficher et gérer des alertes de métrique classiques. Les exemples de cet article illustrent comment utiliser les cmdlets Azure Monitor pour les alertes de métrique classiques.

1. Si vous ne l’avez déjà fait, configurez PowerShell pour s’exécuter sur votre ordinateur. Pour plus d’informations, consultez l’article [Guide pratique pour installer et configurer PowerShell](/powershell/azure/overview). Vous pouvez également consulter la liste complète des applets de commande PowerShell Azure Monitor dans la rubrique [Applets de commande Azure Monitor (Insights)](https://docs.microsoft.com/powershell/module/azurerm.insights).

2. Tout d’abord, connectez-vous à votre abonnement Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```

3. Un écran de connexion apparaît. Dès que vous vous connectez à votre compte, les paramètres ID de locataire et ID d’abonnement par défaut s’affichent. Toutes les applets de commande Azure fonctionnent dans le cadre de votre abonnement par défaut. Pour afficher la liste des abonnements auxquels vous avez accès, utilisez la commande suivante :

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. Pour remplacer votre contexte de travail par un autre abonnement, utilisez la commande suivante :

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. Vous pouvez récupérer toutes les règles d’alerte de métrique classique dans un groupe de ressources :

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. Vous pouvez afficher les détails d’une règle d’alerte métrique classique :

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Vous pouvez récupérer toutes les règles d'alerte définies pour une ressource cible : Par exemple, toutes les règles d'alerte définies sur une machine virtuelle.

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Il n’est plus possible d’utiliser PowerShell pour créer des règles d’alerte classiques. Pour créer une règle d’alerte, vous devez utiliser la nouvelle commande [Add-AzureRmMetricAlertRule](https://docs.microsoft.com/powershell/module/azurerm.insights/add-azurermmetricalertrule?view=azurermps-6.13.0).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une alerte de métrique classique avec un modèle Resource Manager](../../azure-monitor/platform/alerts-enable-template.md)
- [Recevoir une alerte de métrique classique pour notifier un système non-Azure à l’aide d’un webhook](../../azure-monitor/platform/alerts-webhooks.md)
