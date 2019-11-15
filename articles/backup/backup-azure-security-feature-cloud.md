---
title: Fonctionnalités de sécurité pour protéger les charges de travail cloud utilisant Sauvegarde Azure
description: Découvrez comment utiliser les fonctionnalités de sécurité dans Sauvegarde Azure pour renforcer la sécurité des sauvegardes.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: f0e4540f3f5ab3fdbb5953cbf100c5fdc2b2542a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622006"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Fonctionnalités de sécurité pour protéger les charges de travail cloud utilisant Sauvegarde Azure

Les préoccupations en matière de risques de sécurité, comme les logiciels malveillants, le ransomware et les intrusions, sont de plus en plus nombreuses. Ces problèmes de sécurité peuvent coûter cher, à la fois en termes d’argent et de données. Pour vous protéger contre ces attaques, Sauvegarde Azure fournit désormais des fonctionnalités de sécurité visant à protéger les données de sauvegarde après la suppression. Une fonctionnalité de ce type est la suppression réversible. Avec la suppression réversible, même si un acteur malveillant supprime la sauvegarde d’une machine virtuelle (ou que les données de sauvegarde sont accidentellement supprimées), les données de sauvegarde sont conservées pendant 14 jours supplémentaires, ce qui permet la récupération de cet élément de sauvegarde sans perte de données. Cette conservation des données de sauvegarde pendant 14 jours supplémentaires dans l’état « suppression réversible » n’engendre pas de frais pour le client.

> [!NOTE]
> La suppression réversible protège uniquement les données de sauvegarde supprimées. Si une machine virtuelle est supprimée sans sauvegarde, la fonctionnalité de suppression réversible ne conserve pas les données. Toutes les ressources doivent être protégées avec Sauvegarde Azure pour garantir une résilience complète.
>

## <a name="soft-delete"></a>Suppression réversible

### <a name="supported-regions"></a>Régions prises en charge

La suppression réversible est actuellement prise en charge dans USA Centre-Ouest, Asie Est, Canada Centre, Canada Est, France Centre, France Sud, Corée Centre, Corée Sud, Royaume-Uni Sud, Royaume-Uni Ouest, Australie Est, Australie Sud-Est, Europe Nord, USA Ouest, USA Ouest 2, USA Centre, Asie Sud-Est, USA Centre Nord, USA Centre Sud, Japon Est, Japon Ouest, Inde Sud, Inde Centre, Inde Ouest, USA Est 2, Suisse Nord, Suisse Ouest et toutes les régions des pays.

### <a name="soft-delete-for-vms"></a>Suppression réversible pour les machines virtuelles

1. Pour supprimer les données de sauvegarde d’une machine virtuelle, vous devez arrêter la sauvegarde. Dans le portail Azure, accédez à votre coffre Recovery Services, cliquez avec le bouton droit sur l’élément de sauvegarde, puis choisissez **Arrêter la sauvegarde**.

   ![Capture d’écran des éléments de sauvegarde du portail Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Dans la fenêtre suivante, vous pouvez choisir de supprimer ou de conserver les données de sauvegarde. Si vous choisissez **Supprimer les données de sauvegarde**, puis **Arrêter la sauvegarde**, la sauvegarde de la machine virtuelle n’est pas supprimée définitivement. Au lieu de cela, les données de sauvegarde sont conservées pendant 14 jours dans l’état de suppression réversible. Si vous choisissez **Supprimer les données de sauvegarde**, une alerte de suppression par e-mail est envoyée à l’ID d’e-mail configuré afin d’informer l’utilisateur qu’il reste 14 jours de conservation prolongée pour les données de sauvegarde. De plus, une alerte par e-mail est envoyée le douzième jour afin de signaler qu’il reste deux jours supplémentaires pour récupérer les données supprimées. La suppression est différée jusqu’au quinzième jour, moment auquel la suppression définitive a lieu et où une dernière alerte par e-mail est envoyée pour signaler la suppression définitive des données.

   ![Capture d’écran du portail Azure, écran Arrêter la sauvegarde](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Pendant ces 14 jours, dans le coffre Recovery Services, la machine virtuelle supprimée de manière réversible s’affiche avec une icône rouge de suppression réversible.

   ![Capture d’écran du portail Azure, machine virtuelle dans l’état de suppression réversible](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Si des éléments de sauvegarde supprimés de manière réversible sont présents dans le coffre, celui-ci ne peut pas être supprimé à ce moment-là. Essayez de supprimer le coffre une fois que les éléments de sauvegarde ont été supprimés définitivement et qu’il ne contient plus d’élément dans l’état de suppression réversible.

4. Pour restaurer la machine virtuelle supprimée de manière réversible, vous devez d’abord annuler sa suppression. Pour ce faire, choisissez la machine virtuelle supprimée de manière réversible, puis cliquez sur l’option **Annuler la suppression**.

   ![Capture d’écran du portail Azure, annulation de la suppression de la machine virtuelle](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Une fenêtre s’affiche pour vous avertir que si vous choisissez l’annulation de la suppression, tous les points de restauration de la machine virtuelle sont restaurés et disponibles pour effectuer une opération de restauration. La machine virtuelle est conservée dans l’état « Arrêter la protection avec conservation des données » avec suspension des sauvegardes et conservation des données de sauvegarde à l’infini sans que la stratégie de sauvegarde soit effective.

   ![Capture d’écran du portail Azure, confirmation de l’annulation de la suppression de la machine virtuelle](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   À ce stade, vous pouvez également restaurer la machine virtuelle en sélectionnant **Restaurer la machine virtuelle** à partir du point de restauration choisi.  

   ![Capture d’écran du portail Azure, option Restaurer la machine virtuelle](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Le récupérateur de mémoire s’exécute et nettoie les points de récupération expirés une fois que l’utilisateur a effectué l’opération **Reprendre la sauvegarde**.

5. Une fois terminé le processus d’annulation de la suppression, l’état est redéfini sur « Arrêter la sauvegarde avec conservation des données », puis vous pouvez choisir **Reprendre la sauvegarde**. L’opération **Reprendre la sauvegarde** réactive l’élément de sauvegarde, associé à une stratégie de sauvegarde sélectionnée par l’utilisateur qui définit les planifications de sauvegarde et de conservation.

   ![Capture d’écran du portail Azure, option Reprendre la sauvegarde](./media/backup-azure-security-feature-cloud/resume-backup.png)

Cet organigramme montre les différentes étapes et états d’un élément de sauvegarde :

![Cycle de vie d’un élément de sauvegarde supprimé de manière réversible](./media/backup-azure-security-feature-cloud/lifecycle.png)

Pour plus d’informations, consultez la section [Questions fréquentes (FAQ)](backup-azure-security-feature-cloud.md#frequently-asked-questions) ci-après.

## <a name="disabling-soft-delete"></a>Désactivation de la suppression réversible

La suppression réversible est activée par défaut sur les coffres nouvellement créés. Si la fonctionnalité de sécurité de la suppression réversible est désactivée, les données de sauvegarde ne sont pas protégées contre les suppressions accidentelles ou malveillantes. Sans la fonctionnalité de suppression réversible, toutes les suppressions d’éléments protégés entraînent une suppression immédiate, sans possibilité de restauration. Étant donné que les données de sauvegarde dans l’état « suppression réversible » n’entraînent aucun frais pour le client, la désactivation de cette fonctionnalité n’est pas recommandée. La seule circonstance dans laquelle vous devez envisager la désactivation de la suppression réversible est si vous vous préparez à déplacer vos éléments protégés vers un nouveau coffre et que vous ne pouvez pas attendre les 14 jours requis avant d’effectuer la suppression et la reprotection (dans un environnement de test, par exemple).

### <a name="prerequisites-for-disabling-soft-delete"></a>Conditions préalables à la désactivation de la suppression réversible

- L’activation ou la désactivation de la suppression réversible pour les coffres (sans éléments protégés) ne peut être effectuée que via le Portail Azure. Cela s’applique aux :
  - Coffres nouvellement créés qui ne contiennent pas d’éléments protégés
  - Coffres existants dont les éléments protégés ont été supprimés et ont expiré (au-delà de la période de rétention fixe de 14 jours)
- Si la fonctionnalité de suppression réversible est désactivée pour le coffre, vous pouvez la réactiver, mais vous ne pouvez pas inverser ce choix et la désactiver si le coffre contient des éléments protégés.
- Vous ne pouvez pas désactiver la suppression réversible pour les coffres contenant des éléments protégés ou des éléments en état de suppression réversible. Si vous devez désactiver la fonctionnalité, procédez comme suit :
  - Arrêtez la protection des données supprimées pour tous les éléments protégés.
  - Attendez que le délai de rétention de la sécurité de 14 jours expire.
  - Désactivez la suppression réversible.

Pour désactiver la suppression réversible, assurez-vous que les conditions préalables sont remplies, puis procédez comme suit :

1. Dans le Portail Azure, accédez à votre coffre, puis accédez à **Paramètres** -> **Propriétés**.
2. Dans le volet Propriétés, sélectionnez **Paramètres de sécurité** -> **Mettre à jour**.
3. Dans le volet Paramètres de sécurité, sous Suppression réversible, sélectionnez **Désactiver**.

![Désactiver la suppression réversible](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>Autres fonctionnalités de sécurité

### <a name="storage-side-encryption"></a>Chiffrement côté stockage

Le Stockage Azure chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du chiffrement AES 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows. La Sauvegarde Azure chiffre automatiquement les données avant de les stocker. Le Stockage Azure déchiffre les données avant de les récupérer.  

Dans Azure, les données en transit entre le stockage Azure et le coffre sont protégées par HTTPS. Ces données restent sur le réseau principal Azure.

Pour plus d’informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

### <a name="vm-encryption"></a>Chiffrement des machines virtuelles

Vous pouvez sauvegarder et restaurer des machines virtuelles Azure Windows ou Linux avec des disques chiffrés à l’aide de du service Sauvegarde Azure. Pour obtenir les instructions correspondantes, consultez [Sauvegarder et restaurer des machines virtuelles chiffrées avec Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Protection des points de récupération de Sauvegarde Azure

Les comptes de stockage utilisés par les coffres Recovery Services sont isolés et ne sont pas accessibles aux utilisateurs à des fins malveillantes. L’accès est autorisé uniquement par le biais d’opérations de gestion de Sauvegarde Azure, telles que la restauration. Ces opérations de gestion sont contrôlées par le biais du contrôle d’accès en fonction du rôle (RBAC).

Pour plus d’informations, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération de Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="soft-delete"></a>Suppression réversible

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Dois-je activer la fonctionnalité de suppression réversible sur chaque coffre ?

Non, elle est créée et activée par défaut pour tous les coffres Recovery Services.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Puis-je configurer le nombre de jours pendant lesquels mes données sont conservées dans un état de suppression réversible une fois l’opération de suppression terminée ?

Non, la durée de conservation supplémentaire après l’opération de suppression est fixée à 14 jours.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Dois-je payer le coût de cette conservation supplémentaire de 14 jours ?

Non, cette conservation supplémentaire de 14 jours est gratuite dans le cadre de la fonctionnalité de suppression réversible.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Puis-je effectuer une opération de restauration quand mes données sont dans l’état de suppression réversible ?

Non, vous devez annuler la suppression de la ressource supprimée de manière réversible pour effectuer la restauration. L’opération d’annulation de suppression replace la ressource dans l’état **Arrêter la protection avec conservation des données**, qui vous permet d’effectuer une restauration à n’importe quel point dans le temps. Le récupérateur de mémoire reste en pause dans cet état.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Mes instantanés suivent-ils le même cycle de vie que mes points de récupération dans le coffre ?

Oui.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Comment puis-je redéclencher des sauvegardes planifiées pour une ressource supprimée de manière réversible ?

Une annulation de la suppression suivie d’une opération de reprise reprotège la ressource. L’opération de reprise associe une stratégie de sauvegarde pour déclencher les sauvegardes planifiées avec la période de conservation sélectionnée. En outre, le récupérateur de mémoire s’exécute dès que l’opération de reprise est terminée. Si vous souhaitez effectuer une restauration à partir d’un point de récupération qui a dépassé sa date d’expiration, il est recommandé de le faire avant de déclencher l’opération de reprise.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Puis-je supprimer mon coffre s’il contient des éléments supprimés de manière réversible ?

Le coffre Recovery Services ne peut pas être supprimé s’il contient des éléments de sauvegarde dans l’état de suppression réversible. Les éléments supprimés de manière réversible sont définitivement supprimés au bout de 14 jours de l’opération de suppression. Vous pouvez supprimer le coffre qu’une fois que tous les éléments supprimés de manière réversible ont été vidés.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Puis-je supprimer les données antérieures à la période de suppression réversible de 14 jours après la suppression ?

Non. Vous ne pouvez pas forcer la suppression des éléments supprimés de manière réversible, ils sont automatiquement supprimés à l’issue de 14 jours. Cette fonctionnalité de sécurité est activée pour protéger les données sauvegardées contre des suppressions accidentelles ou malintentionnées.  Vous devez attendre 14 jours avant d’effectuer toute autre action sur la machine virtuelle.  Les éléments supprimés de façon réversible ne sont pas facturés.  Si vous avez besoin de reprotéger les machines virtuelles marquées pour une suppression réversible dans un délai de 14 jours dans un nouveau coffre, contactez le support technique Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Est-il possible d’effectuer des opérations de suppression réversible dans PowerShell ou CLI ?

Non, la prise en charge pour PowerShell ou CLI n’est pas disponible.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>La suppression réversible est-elle prise en charge pour les autres charges de travail cloud, comme SQL Server sur des machines virtuelles Azure et SAP HANA sur des machines virtuelles Azure ?

Non. La suppression réversible est uniquement prise en charge pour les machines virtuelles Azure.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Contrôles de sécurité pour la Sauvegarde Azure](backup-security-controls.md).
