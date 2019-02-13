---
title: Résolution des problèmes liés au démarrage et à l’arrêt des machines virtuelles avec Azure Automation
description: Cet article fournit des informations sur la résolution des problèmes liés au démarrage et à l’arrêt des machines virtuelles dans Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ef2a782a19dd319de346f14d6189759d0a26686c
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665770"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Résoudre les problèmes liés à la solution Start/Stop VMs during off-hours

## <a name="all-vms-fail-to-startstop"></a>Scénario : Le démarrage/l’arrêt de toutes les machines virtuelles échouent

### <a name="issue"></a>Problème

Vous avez configuré la solution Start/Stop VM, mais elle ne démarre pas ou n’arrête pas toutes les machines virtuelles configurées.

### <a name="cause"></a>Cause :

Cette erreur peut être due à l’une des raisons suivantes :

1. Une planification n’est pas configurée correctement
2. Le compte d’identification n’est peut-être pas configuré correctement
3. Un runbook a peut-être rencontré des erreurs
4. Les machines virtuelles ont peut-être été exclues

### <a name="resolution"></a>Résolution :

Examinez la liste suivante pour trouver des solutions possibles à votre problème ou des endroits où en chercher :

* Vérifiez que vous avez correctement configuré une planification pour la solution Start/Stop VMs. Pour savoir comment configurer une planification, consultez l’article [Planifications](../automation-schedules.md).

* Vérifiez les flux de tâches pour les runbooks afin de rechercher les éventuelles erreurs. Dans le portail, accédez à votre compte Automation, puis sélectionnez **Tâches** sous **Automatisation des processus**. Dans la page **Tâches**, recherchez des tâches dans l’un des runbooks suivants :

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Vérifiez que votre [Compte d’identification](../manage-runas-account.md) dispose des autorisations appropriées pour les machines virtuelles que vous tentez de démarrer ou d’arrêter. Pour savoir comment vérifier les autorisations sur une ressource, consultez [Démarrage rapide : Afficher les rôles attribués à un utilisateur à l’aide du portail Azure](../../role-based-access-control/check-access.md). Vous devez fournir l’ID d’application pour le service principal utilisé par le compte d’identification. Pour récupérer cette valeur, accédez à votre compte Automation dans le portail Azure, sélectionnez **Comptes d’identification** sous **Paramètres de compte**, puis cliquez sur le compte d’identification approprié.

* Les machines virtuelles peuvent ne pas être démarrées ou arrêtées si elles sont explicitement exclues. Les machines virtuelles exclues sont définies dans la variable **External_ExcludeVMNames** du compte Automation dans lequel la solution est déployée. L’exemple suivant montre comment vous pouvez interroger cette valeur avec PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scénario : Le démarrage/l’arrêt de certaines de mes machines virtuelles échouent

### <a name="issue"></a>Problème

Vous avez configuré la solution Start/Stop VMs, mais elle ne démarre pas ou n’arrête pas certaines des machines virtuelles configurées.

### <a name="cause"></a>Cause :

Cette erreur peut être due à l’une des raisons suivantes :

1. Si vous utilisez le scénario de séquence, une balise est peut-être manquante ou incorrecte
2. La machine virtuelle est peut-être exclue
3. Le compte d’identification ne dispose peut-être pas des autorisations suffisantes sur la machine virtuelle
4. Quelque chose dans la machine virtuelle a peut-être empêché son démarrage ou son arrêt

### <a name="resolution"></a>Résolution :

Examinez la liste suivante pour trouver des solutions possibles à votre problème ou des endroits où en chercher :

* Quand vous utilisez le [scénario de séquence](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) de la solution Start/Stop VMs during off-hours, vous devez vérifier que chaque machine virtuelle que vous voulez démarrer ou arrêter dispose de la balise appropriée. Vérifiez que les machines virtuelles que vous voulez démarrer ont la balise `sequencestart` et que celles que vous voulez arrêter ont la balise `sequencestop`. Les deux balises exigent une valeur entière positive. Vous pouvez utiliser une requête semblable à l’exemple suivant pour rechercher toutes les machines virtuelles avec les balises et leurs valeurs.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Les machines virtuelles peuvent ne pas être démarrées ou arrêtées si elles sont explicitement exclues. Les machines virtuelles exclues sont définies dans la variable **External_ExcludeVMNames** du compte Automation dans lequel la solution est déployée. L’exemple suivant montre comment vous pouvez interroger cette valeur avec PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Pour démarrer et arrêter des machines virtuelles, le compte d’identification du compte Automation doit disposer des autorisations appropriées sur la machine virtuelle. Pour savoir comment vérifier les autorisations sur une ressource, consultez [Démarrage rapide : Afficher les rôles attribués à un utilisateur à l’aide du portail Azure](../../role-based-access-control/check-access.md). Vous devez fournir l’ID d’application pour le service principal utilisé par le compte d’identification. Pour récupérer cette valeur, accédez à votre compte Automation dans le portail Azure, sélectionnez **Comptes d’identification** sous **Paramètres de compte**, puis cliquez sur le compte d’identification approprié.

* Si la machine virtuelle rencontre un problème de démarrage ou de désallocation, ce comportement peut provenir d’un problème sur la machine virtuelle elle-même. L’application d’une mise à jour pendant une tentative d’arrêt ou le blocage d’un service sont, entre autres, des exemples de problèmes potentiels. Accédez à la ressource de votre machine virtuelle et vérifiez les **journaux d’activité** pour voir s’ils indiquent des erreurs. Vous pouvez également essayer de vous connecter à la machine virtuelle pour voir s’il existe des erreurs dans les journaux des événements.

## <a name="custom-runbook"></a>Scénario : Mon runbook personnalisé ne parvient pas à démarrer ou à arrêter mes machines virtuelles

### <a name="issue"></a>Problème

Vous avez créé un runbook personnalisé ou vous en avez téléchargé un à partir de PowerShell Gallery, et il ne fonctionne pas correctement.

### <a name="cause"></a>Cause :

Les causes de l’échec peuvent être diverses. Accédez à votre compte Automation dans le portail Azure, puis sélectionnez **Tâches** sous **Automatisation des processus**. Dans la page **Tâches**, recherchez les tâches de votre runbook pour afficher les éventuels échecs des tâches.

### <a name="resolution"></a>Résolution :

Il est recommandé d’utiliser la [solution Start/Stop VMs during off-hours](../automation-solution-vm-management.md) pour démarrer et arrêter des machines virtuelles dans Azure Automation. Cette solution a été créée par Microsoft. Les runbooks personnalisés ne sont pas pris en charge par Microsoft. Vous pouvez trouver une solution pour votre runbook personnalisé en consultant l’article relatif à la [résolution des problèmes liés aux runbooks](runbooks.md). Cet article fournit des conseils généraux et de résolution des problèmes liés aux runbooks de tous types.

## <a name="dont-start-stop-in-sequence"></a>Scénario : Les machines virtuelles ne démarrent pas ou ne s’arrêtent pas dans l’ordre approprié

### <a name="issue"></a>Problème

Les machines virtuelles que vous avez configurées dans la solution ne démarrent pas ou ne s’arrêtent pas dans l’ordre approprié.

### <a name="cause"></a>Cause :

Cela est dû à un balisage incorrect sur les machines virtuelles.

### <a name="resolution"></a>Résolution :

Effectuez les étapes suivantes pour vérifier que la solution est correctement configurée.

1. Vérifiez que toutes les machines virtuelles à démarrer ou à arrêter ont une balise `sequencestart` ou `sequencestop`, suivant votre situation. La valeur de ces balises doit être un entier positif. Les machines virtuelles sont traitées dans l’ordre croissant en fonction de cette valeur.
2. Vérifiez que les groupes de ressources des machines virtuelles à démarrer ou à arrêter sont dans les variables `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, suivant votre situation.
3. Testez vos changements en exécutant le runbook `SequencedStartStop_Parent` avec le paramètre WHATIF défini sur True pour prévisualiser vos changements.

Pour obtenir des instructions plus détaillées et des instructions supplémentaires sur l’utilisation de la solution pour démarrer et arrêter des machines virtuelles dans l’ordre, consultez [Démarrer/arrêter les machines virtuelles dans un certain ordre](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scénario : Échec de la tâche Start/Stop VM (Démarrage/arrêt de machines virtuelles) avec l’état 403 forbidden (Interdit) 

### <a name="issue"></a>Problème

Vous trouvez des tâches qui ont échoué avec une erreur `403 forbidden` pour les runbooks de la solution Start/Stop VMs during off-hours.

### <a name="cause"></a>Cause :

Ce problème peut être dû à un compte d’identification incorrectement configuré ou ayant expiré. Il peut également être dû à des autorisations inappropriées pour les ressources des machines virtuelles accordées par le compte d’identification des comptes Automation.

### <a name="resolution"></a>Résolution :

Pour vérifier que votre compte d’identification est correctement configuré, accédez à votre compte Automation dans le portail Azure, puis sélectionnez **Comptes d’identification** sous **Paramètres du compte**. L’état de vos comptes d’identification s’affiche alors. Si un compte d’identification est incorrectement configuré ou a expiré, son état l’indique.

Si votre compte d’identification est [mal configuré](../manage-runas-account.md#misconfiguration), vous devez le supprimer et le recréer.

Si le certificat a expiré pour votre compte d’identification, suivez les étapes listées dans [Renouvellement de certificat auto-signé](../manage-runas-account.md#cert-renewal) pour renouveler le certificat.

Le problème peut être causé par des autorisations manquantes. Pour savoir comment vérifier les autorisations sur une ressource, consultez [Démarrage rapide : Afficher les rôles attribués à un utilisateur à l’aide du portail Azure](../../role-based-access-control/check-access.md). Vous devez fournir l’ID d’application pour le service principal utilisé par le compte d’identification. Pour récupérer cette valeur, accédez à votre compte Automation dans le portail Azure, sélectionnez **Comptes d’identification** sous **Paramètres de compte**, puis cliquez sur le compte d’identification approprié.

## <a name="other"></a>Scénario : Mon problème n’est pas listé ci-dessus

### <a name="issue"></a>Problème

Lors de l’utilisation de la solution Start/Stop VMs during off-hours, vous rencontrez un problème ou vous obtenez un résultat inattendu non listés sur cette page.

### <a name="cause"></a>Cause :

Souvent, les erreurs peuvent être dues à l’utilisation d’une ancienne version obsolète de la solution.

### <a name="resolution"></a>Résolution :

Pour résoudre de nombreuses erreurs, il est recommandé de supprimer et de mettre à jour la solution. Pour savoir comment mettre à jour la solution, consultez [Mettre à jour la solution Start/Stop VMs during off-hours](../automation-solution-vm-management.md#update-the-solution).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
