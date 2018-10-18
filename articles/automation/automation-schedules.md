---
title: Planifications dans Azure Automation
description: Les planifications Automation permettent de planifier les Runbooks dans Azure pour qu'ils démarrent automatiquement. Décrit comment créer et gérer une planification afin de pouvoir démarrer automatiquement un Runbook à un instant donné ou selon une planification périodique.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3d8492d2a8982c9c85bfc91867f7eb6c2da04e58
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294762"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planification d'un Runbook dans Azure Automation

Pour planifier le démarrage d'un Runbook dans Azure Automation à une heure spécifiée, liez-le à une ou plusieurs planifications. Une planification peut être configurée pour une exécution des runbooks ponctuelle ou régulière à des heures et jours précis dans le portail Azure. Vous pouvez également planifier une exécution hebdomadaire ou mensuelle, ou des jours spécifiques de la semaine ou du mois, ou encore un jour particulier du mois. Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés.

> [!NOTE]
> Les planifications ne prennent pas en charge les configurations Azure Automation DSC pour le moment.

## <a name="windows-powershell-cmdlets"></a>Applets de commande Windows PowerShell

Les applets de commande du tableau suivant permettent de créer et de gérer les planifications avec Windows PowerShell dans Azure Automation. Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview).

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Récupère une planification. |
| [Nouvelle AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crée une planification. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Supprime une planification. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Définit les propriétés d'une planification existante. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Récupère les Runbooks planifiés. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associe un Runbook à une planification. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocie un Runbook d'une planification. |

## <a name="creating-a-schedule"></a>Création d'une planification

Vous pouvez utiliser le portail Azure ou Windows PowerShell pour créer une planification de runbooks.

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.  Pour éviter d’affecter vos runbooks et les processus qu’ils automatisent, vous devez tout d’abord tester les runbooks ayant des planifications associées avec un compte Automation dédié au test.  Cela permet de vérifier que vos runbooks planifiés continuent de fonctionner correctement et, dans le cas contraire, vous pouvez résoudre les problèmes et appliquer les modifications nécessaires avant de migrer la version mise à jour du runbook en production.
> Votre compte Automation n’obtient pas automatiquement les nouvelles versions des modules, sauf si vous les avez mis à jour manuellement en sélectionnant l’option [Mettre à jour les modules Azure](automation-update-azure-modules.md) du panneau **Modules**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Pour créer une planification à l’aide du portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche.
1. Cliquez sur **Ajouter une planification** en haut de la page.
1. Dans le volet **Nouvelle planification**, tapez un **Nom** et éventuellement une **Description** pour la nouvelle planification.
1. Indiquez si la planification doit s’exécuter une seule fois ou selon un calendrier récurrent en sélectionnant **Une fois** ou **Récurrent**. Si vous sélectionnez **Une fois**, indiquez une **Heure de début**, puis cliquez sur **Créer**. Si vous sélectionnez **Récurrent**, spécifiez une **Heure de début** et pour **Tous les**, sélectionnez la fréquence à laquelle vous voulez répéter le runbook : **heure**, **jour**, **semaine** ou **mois**.
    1. Si vous sélectionnez **semaine**, vous accédez à une liste des jours de la semaine parmi lesquels choisir. Sélectionnez autant de jours que vous le voulez. La première exécution de votre planification a lieu le premier jour sélectionné, après l’heure de début.
    2. Si vous sélectionnez **mois**, vous disposez de différentes options. Pour l’option **Occurrences mensuelles**, sélectionnez **Jours du mois** ou **Jours de la semaine**. Si vous choisissez **Jours du mois**, un calendrier s’affiche vous permettant de choisir autant de jours que vous le voulez. Si par exemple vous choisissez comme date le 31, et que le mois en cours ne dispose pas de 31e jour, la planification ne s’exécute pas. Si vous souhaitez que la planification s’exécute le dernier jour, choisissez **Oui** sous **Run on last day of month** (Exécution le dernier jour du mois). Si vous choisissez **Jours de la semaine**, l’option **Tous les** est proposée. Choisissez **Premier**, **Deuxième**, **Troisième**, **Quatrième** ou **Dernier**. Enfin, choisissez un jour pour la répétition.
1. Une fois que vous avez terminé, cliquez sur **Créer**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Pour créer une planification avec Windows PowerShell

Vous utilisez l’applet de commande [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) pour créer des planifications. Spécifiez l’heure de début de la planification et indiquez sa fréquence d’exécution.

Les exemples de commandes suivants montrent comment créer une planification le 15 et le 30 de chaque mois à l’aide d’une cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Liaison d'une planification à un Runbook

Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés. Si un Runbook possède des paramètres, vous pouvez leur fournir des valeurs. Vous devez fournir des valeurs pour tous les paramètres obligatoires et vous pouvez fournir des valeurs pour tous les paramètres facultatifs. Ces valeurs sont utilisées à chaque démarrage du Runbook par cette planification. Vous pouvez attacher le même Runbook à une autre planification et spécifier différentes valeurs de paramètre.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pour lier une planification à un Runbook avec le portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Runbooks** dans la section **Automatisation de processus** à gauche.
2. Cliquez sur le nom du Runbook à planifier.
3. Si le Runbook n'est pas actuellement lié à une planification, vous avez la possibilité de créer une planification ou de le lier à une planification existante.
4. Si le Runbook possède des paramètres, vous pouvez sélectionner l’option **Modifier les paramètres d’exécution (par défaut : Azure)** pour accéder au volet **Paramètres**, dans lequel vous pouvez saisir les informations correspondantes.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Pour lier une planification à un Runbook avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) pour lier une planification. Vous pouvez spécifier les valeurs des paramètres du Runbook avec le paramètre Parameters. Pour plus d'informations sur la spécification des valeurs des paramètres, consultez [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) .
Les exemples de commandes suivants montrent comment lier une planification à un Runbook à l’aide d’une applet de commande Azure Resource Manager avec des paramètres.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Planification plus fréquente des runbooks

L’intervalle le plus fréquent pour lequel une planification dans Azure Automation peut être configurée est d’une heure. Si vous avez besoin pour exécuter les planifications plus fréquemment, il existe deux options :

* Créer un [webhook](automation-webhooks.md) de runbook et utiliser [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) pour appeler le webhook. Azure Scheduler fournit une granularité plus fine lors de la définition d’une planification.

* Créer quatre planifications éloignées de 15 minutes et exécutées une fois par heure. Ce scénario permet au runbook de s’exécuter toutes les 15 minutes avec différentes planifications.

## <a name="disabling-a-schedule"></a>Désactivation d'une planification

Lorsque vous désactivez une planification, les Runbooks qui y sont liés ne s'exécutent plus sur cette planification. Vous pouvez désactiver manuellement une planification ou affecter un délai d’expiration aux planifications selon une certaine fréquence lors de leur création. Lorsque le délai d'expiration est atteint, la planification est désactivée.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Pour désactiver une planification à partir du portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche.
1. Cliquez sur le nom d’une planification pour ouvrir le volet Détails.
1. Remplacez **Activé** par **Non**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Pour désactiver une planification avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) pour modifier les propriétés d’une planification existante. Pour désactiver la planification, spécifiez la valeur **false** pour le paramètre **IsEnabled**.

Les exemples de commandes suivants montrent comment désactiver une planification pour un Runbook à l’aide d’une applet de commande Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour vous familiariser avec les Runbooks dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md)
