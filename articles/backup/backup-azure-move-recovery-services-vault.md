---
title: Déplacer un coffre Recovery Services entre des abonnements Azure ou vers un autre groupe de ressources
description: Instructions pour déplacer un coffre Recovery Services entre des abonnements Azure et des groupes de ressources.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: 72bfbc34f57e7725ae9556e893825900474317cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64876844"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Déplacer un coffre Recovery Services entre des abonnements Azure et des groupes de ressources

Cet article explique comment déplacer un coffre Recovery Services configuré pour Sauvegarde Azure entre des abonnements Azure ou vers un autre groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail Azure ou PowerShell pour déplacer un coffre Recovery Services.

## <a name="supported-region"></a>Région prise en charge

Le déplacement de ressources pour un coffre Recovery Services est pris en charge dans les régions suivantes : Australie Est, Australie Sud-Est, Canada Centre, Canada Est, Asie Sud-Est, Asie Est, USA Centre, USA Centre Nord, USA Est, USA Est2, USA Centre-Sud, USA Centre-Ouest, USA Centre-Ouest2, USA Ouest, Inde Centre, Inde Sud, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Europe Nord, Europe Ouest, Afrique du Sud Nord, Afrique du Sud Ouest, Royaume-Uni Sud et Royaume-Uni Ouest.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Conditions préalables au déplacement d'un coffre Recovery Services

- Lors du déplacement du coffre entre des groupes de ressources, les groupes de ressources source et cible sont verrouillés pour empêcher les opérations d'écriture et de suppression. Pour plus d’informations, consultez cet [article](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Seul l'abonnement Administrateur dispose des autorisations pour déplacer un coffre.
- Pour déplacer un coffre entre des abonnements, l'abonnement cible doit se trouver dans le même locataire que l'abonnement source et son état doit être défini sur Activé.
- Vous devez être autorisé à effectuer des opérations d’écriture sur le groupe de ressources cible.
- Le déplacement du coffre change seulement le groupe de ressources. Le coffre Recovery Services se trouve au même emplacement et il n'est pas modifiable.
- Vous ne pouvez déplacer qu'un seul coffre Recovery Services à la fois par région.
- Si une machine virtuelle n'est pas déplacée avec le coffre Recovery Services entre des abonnements ou vers un nouveau groupe de ressources, les points de récupération actuels de la machine virtuelle restent intacts dans le coffre jusqu'à leur expiration.
- Que la machine virtuelle soit déplacée ou non avec le coffre, vous pouvez toujours la restaurer à partir de l’historique des sauvegardes conservé dans le coffre.
- Azure Disk Encryption exige que le coffre de clés et les machines virtuelles se trouvent dans la même région et le même abonnement Azure.
- Pour déplacer une machine virtuelle avec des disques managés, consultez cet [article](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplacez les ressources au sein d’un abonnement ou vers un nouvel abonnement. Pour plus d’informations, consultez cet [article](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
- Les stratégies de sauvegarde définies pour le coffre sont conservées après le déplacement du coffre entre des abonnements ou vers un groupe de ressources.
- Le déplacement de coffres avec Fichiers Azure, Azure File Sync ou SQL dans des machines virtuelles IaaS entre des abonnements et des groupes de ressources n'est pas pris en charge.
- Si vous déplacez un coffre contenant des données de sauvegarde de machine virtuelle entre des abonnements, vous devez déplacer vos machines virtuelles dans le même abonnement et utiliser le même groupe de ressources cible pour continuer les sauvegardes.<br>

> [!NOTE]
>
> Les coffres Recovery Services configurés pour une utilisation avec **Azure Site Recovery** ne peuvent pas encore être déplacés. Si vous avez configuré des machines virtuelles (IaaS Azure, Hyper-V, VMware) ou des machines physiques pour la reprise d’activité avec **Azure Site Recovery**, l’opération de déplacement est bloquée. La fonctionnalité de déplacement de ressource pour le service Site Recovery n’est pas encore disponible.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Utiliser le portail Azure pour déplacer un coffre Recovery Services vers un autre groupe de ressources

Pour déplacer un coffre Recovery Services et ses ressources associées vers un autre groupe de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Ouvrez la liste des **coffres Recovery Services** et sélectionnez le coffre à déplacer. Quand le tableau de bord du coffre s’ouvre, il s’affiche comme illustré dans l’image suivante.

   ![Ouvrir le coffre Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Si vous ne voyez pas les informations **Essentials** pour votre coffre, cliquez sur l’icône de liste déroulante. Vous devez maintenant voir les informations Essentials pour votre coffre.

   ![Onglet Informations Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Dans le menu Vue d’ensemble du coffre, cliquez sur **Modifier** à côté du **Groupe de ressources** pour ouvrir le panneau **Déplacer des ressources**.

   ![Changer le groupe de ressources](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Dans le panneau **Déplacer des ressources**, pour le coffre sélectionné, il est recommandé de déplacer les ressources associées facultatives en cochant la case comme illustré dans l’image suivante.

   ![Déplacer un abonnement](./media/backup-azure-move-recovery-services/move-resource.png)

5. Pour ajouter le groupe de ressources cible, dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou cliquez sur l’option **Créer un groupe**.

   ![Créer une ressource](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Après avoir ajouté le groupe de ressources, confirmez l’option **Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource**, puis cliquez sur **OK** pour effectuer le déplacement du coffre.

   ![Message de confirmation](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Utiliser le portail Azure pour déplacer un coffre Recovery Services vers un autre abonnement

Vous pouvez déplacer un coffre Recovery Services et ses ressources associées vers un autre abonnement.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Ouvrez la liste des coffres Recovery Services et sélectionnez le coffre à déplacer. Quand le tableau de bord du coffre s’ouvre, il apparaît comme illustré dans l’image suivante.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Si vous ne voyez pas les informations **Essentials** pour votre coffre, cliquez sur l’icône de liste déroulante. Vous devez maintenant voir les informations Essentials pour votre coffre.

    ![Onglet Informations Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Dans le menu Vue d’ensemble du coffre, cliquez sur **Modifier** à côté de **Abonnement**  pour ouvrir le panneau **Déplacer des ressources**.

   ![Changer l’abonnement](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Sélectionnez les ressources à déplacer : nous vous recommandons d’utiliser ici l’option **Sélectionner tout** pour sélectionner toutes les ressources facultatives listées.

   ![Déplacer une ressource](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement cible où vous voulez déplacer le coffre.
6. Pour ajouter le groupe de ressources cible, dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou cliquez sur l’option **Créer un groupe**.

   ![Ajouter un abonnement](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Cliquez sur l’option **Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource;** , puis cliquez sur **OK**.

> [!NOTE]
> La sauvegarde entre abonnements (le coffre Recovery Services et les machines virtuelles protégées se trouvent dans des abonnements différents) n’est pas un scénario pris en charge. En outre, pendant l’opération de déplacement du coffre, vous ne pouvez pas changer l’option de redondance du stockage de stockage localement redondant (LRS) en stockage globalement redondant (GRS) et inversement.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Utiliser PowerShell pour déplacer un coffre Recovery Services

Pour déplacer un coffre Recovery Services vers un autre groupe de ressources, utilisez l’applet de commande `Move-AzureRMResource`. `Move-AzureRMResource` nécessite le nom de la ressource et le type de ressource. Vous pouvez les obtenir avec l’applet de commande `Get-AzureRmRecoveryServicesVault`.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre `-DestinationSubscriptionId`.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Après l’exécution des applets de commande ci-dessus, vous devez confirmer que vous voulez déplacer les ressources spécifiées. Tapez **O** pour le confirmer. Après une validation réussie, la ressource est déplacée.

## <a name="use-cli-to-move-recovery-services-vault"></a>Utiliser l'interface CLI pour déplacer un coffre Recovery Services

Pour déplacer un coffre Recovery Services vers un autre groupe de ressources, utilisez l’applet de commande suivante :

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

## <a name="post-migration"></a>Après la migration

1. Vous devez définir/vérifier les contrôles d’accès pour les groupes de ressources.  
2. La fonctionnalité de création de rapports et de surveillance de Sauvegarde doit être reconfigurée pour le coffre une fois le déplacement terminé. La configuration antérieure est perdue lors de l’opération de déplacement.



## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements.

Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
