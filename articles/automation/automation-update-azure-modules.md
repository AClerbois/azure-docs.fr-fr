---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fe263346d8794b3dc85b6420d8b9b02efa5f9684
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met à jour régulièrement les modules Azure. Vous disposez donc dans le compte Automation d’une méthode permettant de mettre à jour les modules dans le compte lorsque de nouvelles versions sont disponibles dans le portail.  

Étant donné que les modules sont régulièrement mis à jour par le groupe de produits, les modifications peuvent se produire avec les cmdlets inclus. Cela peut avoir un impact négatif sur vos runbooks suivant le type de modification, comme renommer un paramètre ou désapprouver entièrement un cmdlet. Pour éviter toute incidence sur vos runbooks ainsi que les processus qu’ils automatisent, il est conseillé de tester et valider les mises à jour avant de les appliquer. Si vous ne possédez pas de compte Automation dédié destiné à cet usage, pensez à en créer un. Vous pourrez tester de nombreux scénarios et différentes permutations lors du développement de vos runbooks, en plus des modifications répétitives comme la mise à jour des modules PowerShell. Dès que les résultats sont validés et une fois les modifications requises appliquées, passez à la coordination de la migration de tout runbook nécessitant une modification, puis réalisez la mise à jour comme décrit dans la production.

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. La page Modules de votre compte Automation comprend une option **Mettre à jour les modules Azure**. Elle est toujours activée.<br><br> ![Option Mettre à jour les modules Azure dans la page Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Cliquez sur **Mettre à jour les modules Azure**, un message de confirmation s’affiche pour vous demander si vous souhaitez continuer.<br><br> ![Notification de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Cliquez sur **Oui** et le processus de mise à jour du module commence. Le processus de mise à jour prend environ 15 à 20 minutes pour les modules suivants :

  * Microsoft Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Si les modules sont déjà à jour, le processus se termine en quelques secondes. Vous êtes averti quand le processus de mise à jour est terminé.<br><br> ![Mise à jour d’état de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.    

Si vous utilisez des cmdlets à partir de ces modules Azure PowerShell dans des runbooks, vous devez effectuer cette mise à jour chaque mois pour être certain d’avoir les derniers modules.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).

* Envisagez d’intégrer un contrôle de code source à l’aide de [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou de [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md). Vous pourrez gérer et contrôler votre runbook Automation et votre portefeuille de configuration de manière centralisée.  