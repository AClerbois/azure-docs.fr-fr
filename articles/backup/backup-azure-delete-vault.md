---
title: Supprimer un coffre Recovery Services Sauvegarde Microsoft Azure
description: Cet article explique comment supprimer un coffre Recovery Services Sauvegarde Microsoft Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 3d6d374b6e516180ec488fe4de1317a3c99a7f7c
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050113"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Supprimer un coffre Recovery Services Sauvegarde Azure

Cet article explique comment supprimer un coffre Recovery Services [Sauvegarde Microsoft Azure](backup-overview.md). Il indique comment enlever les dépendances, puis supprimer un coffre.


## <a name="before-you-start"></a>Avant de commencer

Vous ne pouvez pas supprimer un coffre Recovery Services qui a des dépendances comme des serveurs protégés ou des serveurs de gestion de sauvegarde associés.

- Les coffres contenant des données de sauvegarde ne peuvent pas être supprimés (même si vous avez arrêté la protection mais conservé les données de sauvegarde).

- Si vous supprimez un coffre qui contient des dépendances, le message suivant s’affiche :

  ![Supprimez l’erreur du coffre.](./media/backup-azure-delete-vault/error.png)

- Si vous supprimez d’un portail un élément protégé localement qui contient des dépendances, un message d’avertissement s’affiche :

  ![Supprimez l’erreur du serveur protégé.](./media/backup-azure-delete-vault/error-message.jpg)

  
Pour supprimer le coffre, choisissez le scénario qui correspond à votre configuration et suivez les étapes recommandées :

Scénario | Étapes de suppression des dépendances pour supprimer un coffre |
-- | --
J’ai des fichiers et dossiers locaux protégés à l’aide de la sauvegarde de l’agent Sauvegarde Azure | Exécuter les étapes indiquées dans [Supprimer des éléments de sauvegarde de la console de gestion MARS](#delete-backup-items-from-the-mars-management-console)
J’ai des machines locales protégées par MABS (serveur de sauvegarde Microsoft Azure) ou DPM (System Center Data Protection Manager) sur Azure | Exécuter les étapes indiquées dans [Supprimer des éléments de sauvegarde de la console de gestion MABS](#delete-backup-items-from-the-mabs-management-console)
J’ai des éléments protégés dans le cloud (par exemple, une machine virtuelle laaS ou un partage Azure Files)  | Exécuter les étapes indiquées dans [Supprimer des éléments protégés dans le cloud](#delete-protected-items-in-the-cloud)
J’ai des éléments protégés à la fois localement et dans le cloud | Effectuez les étapes décrites dans toutes les sections suivantes, dans l’ordre suivant : <br> 1. [Supprimer des éléments protégés dans le cloud](#delete-protected-items-in-the-cloud)<br> 2. [Supprimer des éléments de sauvegarde de la console de gestion MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Supprimer des éléments de sauvegarde de la console de gestion MABS](#delete-backup-items-from-the-mabs-management-console)
Je n’ai pas d’éléments protégés localement ou dans le cloud ; toutefois, je reçois toujours le message d’erreur de suppression du coffre | Suivez les étapes indiquées dans [Supprimer le coffre Recovery Services à l’aide d’Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)


## <a name="delete-protected-items-in-the-cloud"></a>Supprimer des éléments protégés dans le cloud

Commencez par lire la section **[Avant de commencer](#before-you-start)** pour comprendre le processus de suppression des dépendances et du coffre.

Pour arrêter la protection et supprimer les données de sauvegarde, effectuez les étapes suivantes :

1. À partir du portail, accédez à **Coffre Recovery Services**, puis à **Éléments de sauvegarde**. Ensuite, choisissez les éléments protégés dans le cloud (par exemple, machines virtuelles Azure, Stockage Azure [service Azure Files] ou SQL Server sur les machines virtuelles Azure).

    ![Sélectionnez le type de sauvegarde.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Cliquez avec le bouton droit pour sélectionner l’élément de sauvegarde. Selon que l’élément de sauvegarde est protégé ou non, le menu présente soit le volet **Arrêter la sauvegarde**, soit le volet **Supprimer les données de sauvegarde**.

    - Si le volet **Arrêter la sauvegarde** s’affiche, sélectionnez **Supprimer les données de sauvegarde** dans le menu déroulant. Entrez le nom de l’élément de sauvegarde (ce champ respecte la casse), puis sélectionnez une raison dans le menu déroulant. Entrez vos commentaires, le cas échéant. Ensuite, sélectionnez **Arrêter la sauvegarde**.

        ![Volet Arrêter la sauvegarde.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Si le volet **Supprimer les données de sauvegarde** s’affiche, entrez le nom de l’élément de sauvegarde (ce champ respecte la casse), puis sélectionnez une raison dans le menu déroulant. Entrez vos commentaires, le cas échéant. Ensuite, sélectionnez **Supprimer**. 

         ![Volet Supprimer les données de sauvegarde.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Vérifiez l’icône **Notification** : ![Icône Notification.](./media/backup-azure-delete-vault/messages.png) Une fois le processus terminé, le service affiche le message suivant : *Arrêt de la sauvegarde et suppression des données de sauvegarde pour «*  Élément de sauvegarde  *»* . *L’opération s’est terminée correctement.*
6. Sélectionnez **Actualiser** dans le menu **Éléments de sauvegarde** pour vous assurer que l’élément de sauvegarde a été supprimé.

      ![Page Supprimer des éléments de sauvegarde.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Supprimer des éléments protégés localement

Commencez par lire la section **[Avant de commencer](#before-you-start)** pour comprendre le processus de suppression des dépendances et du coffre.

1. Dans le menu du tableau de bord du coffre, sélectionnez **Infrastructure de sauvegarde**.
2. Selon votre scénario local, choisissez l’une des options suivantes :

      - Pour MARS, sélectionnez **Serveurs protégés**, puis **Agent Azure Backup**. Ensuite, sélectionnez le serveur à supprimer. 

        ![Pour MARS, sélectionnez votre coffre pour en ouvrir le tableau de bord.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Pour MABS ou DPM, sélectionnez  **Serveurs de gestion de sauvegarde**. Ensuite, sélectionnez le serveur à supprimer. 


          ![Pour MABS, sélectionnez votre coffre pour en ouvrir le tableau de bord.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Le volet **Supprimer** s’affiche avec un message d’avertissement.

     ![Volet Supprimer.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Consultez le message d’avertissement et les instructions fournies dans la case à cocher de consentement.
    > [!NOTE]
    >- Si le serveur protégé est synchronisé avec les services Azure et que les éléments de sauvegarde existent, la case à cocher de consentement indique le nombre d’éléments de sauvegarde dépendants et le lien pour les consulter.
    >- Si le serveur protégé n’est pas synchronisé avec les services Azure et que les éléments de sauvegarde existent, la case à cocher de consentement présente uniquement le nombre d’éléments de sauvegarde.
    >- En l’absence d’éléments de sauvegarde, la case à cocher de consentement demande une suppression.

4. Cochez la case de consentement, puis sélectionnez **Supprimer**.




5. Vérifiez l’icône **Notification** de ![suppression des données de sauvegarde](./media/backup-azure-delete-vault/messages.png). Une fois l’opération terminée, le service affiche le message suivant : *Arrêt de la sauvegarde et suppression des données de sauvegarde pour « Élément de sauvegarde ».* *L’opération s’est terminée correctement.*
6. Sélectionnez **Actualiser** dans le menu **Éléments de sauvegarde** pour vous assurer que l’élément de sauvegarde est supprimé.

Une fois ce processus terminé, vous pouvez supprimer les éléments de sauvegarde de la console de gestion :
    
   - [Supprimer des éléments de sauvegarde de la console de gestion MARS](#delete-backup-items-from-the-mars-management-console)
    - [Supprimer des éléments de sauvegarde de la console de gestion MABS](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>Supprimer des éléments de sauvegarde de la console de gestion MARS

1. Ouvrez la console de gestion de MARS, accédez au volet **Actions** et sélectionnez **Planifier la sauvegarde**.
2. Dans la page **Modifier ou arrêter une sauvegarde planifiée**, sélectionnez **Ne plus utiliser cette panification de sauvegarde et supprimer toutes les sauvegardes stockées**. Ensuite, sélectionnez **Suivant**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Dans la page **Arrêter une sauvegarde planifiée**, sélectionnez **Terminer**.

    ![Arrêtez une sauvegarde planifiée.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Vous êtes invité à entrer un code PIN de sécurité, que vous devez générer manuellement. Pour cela, commencez par vous connecter au portail Azure.
5. Accédez à **Coffre Recovery Services** > **Paramètres** > **Propriétés**.
6. Sous **Code PIN de sécurité**, sélectionnez **Générer**. Copiez ce code PIN. Il n’est valide que pendant cinq minutes.
7. Dans la console de gestion, collez le code PIN, puis sélectionnez **OK**.

    ![Générez un code PIN de sécurité.](./media/backup-azure-delete-vault/security-pin.png)

8. Dans la page **Modifier la progression de la sauvegarde**, le message suivant s’affiche : *Les données supprimées sont conservées pendant 14 jours. Passé ce délai, les données de sauvegarde sont définitivement supprimées.*  

    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Une fois les éléments de sauvegarde locaux supprimés, effectuez les étapes suivantes à partir du portail.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Supprimer des éléments de sauvegarde de la console de gestion MABS

Il existe deux méthodes pour supprimer des éléments de sauvegarde de la console de gestion MABS.

#### <a name="method-1"></a>Méthode 1
Pour arrêter la protection et supprimer des données de sauvegarde, effectuez les étapes suivantes :

1.  Ouvrez la console Administrateur DPM, puis sélectionnez **Protection** dans la barre de navigation.
2.  Dans le volet d’informations, sélectionnez le membre du groupe de protection que vous souhaitez supprimer. Cliquez avec le bouton droit pour sélectionner l’option **Arrêter la protection des membres du groupe**.
3.  Dans la boîte de dialogue **Arrêt de la protection**, cochez la case **Supprimer les données protégées**, puis **Supprimer le stockage en ligne**. Ensuite, sélectionnez **Arrêter la protection**.

    ![Sélectionnez Supprimer les données protégées dans le volet Arrêter la protection.](./media/backup-azure-delete-vault/delete-storage-online.png)

    L’état du membre protégé devient *Réplica inactif disponible*.

4. Cliquez avec le bouton droit sur le groupe de protection inactif, puis sélectionnez **Supprimer la protection inactive**.

    ![Supprimez la protection inactive.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Dans la fenêtre **Supprimer la protection inactive**, cochez la case **Supprimer le stockage en ligne**, puis sélectionnez **OK**.

    ![Supprimez le stockage en ligne.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Méthode 2
Ouvrez la **console de gestion MABS**. Sous **Sélectionner la méthode de protection des données**, décochez la case **Je voudrais une protection en ligne**.

  ![Sélectionnez la méthode de protection des données.](./media/backup-azure-delete-vault/data-protection-method.png)

Une fois les éléments de sauvegarde locaux supprimés, effectuez les étapes suivantes à partir du portail.


## <a name="delete-the-recovery-services-vault"></a>Supprimer le coffre Recovery Services

1. Une fois toutes les dépendances supprimées, faites défiler l’affichage jusqu’au volet **Bases** dans le menu du coffre.
2. Vérifiez qu’aucun élément de sauvegarde, serveur de gestion de sauvegarde ni élément répliqué ne sont listés. Si des éléments apparaissent encore dans le coffre, reportez-vous à la section [Avant de commencer](#before-you-start).

3. Lorsqu’il n’y a plus d’éléments dans le coffre, sélectionnez **Supprimer** dans le tableau de bord du coffre.

    ![Sélectionnez Supprimer dans le tableau de bord du coffre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Sélectionnez **Oui** pour confirmer la suppression du coffre. Le coffre est supprimé. Le portail revient au menu de service **Nouveau**.

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Supprimer le coffre Recovery Services à l’aide d’Azure Resource Manager

Cette option de suppression du coffre Recovery Services est recommandée uniquement si toutes les dépendances sont supprimées alors que vous obtenez toujours le *message d’erreur de suppression du coffre*. Essayez l’une ou l’ensemble des astuces suivantes :

- Dans le volet **Essentials** du menu du coffre, vérifiez qu’il n’y a pas d’éléments de sauvegarde, de serveurs de gestion de sauvegarde ou d’éléments répliqués listés. S’il existe des éléments de sauvegarde, reportez-vous à la section [Avant de commencer](#before-you-start).
- Réessayez de [supprimer le coffre du portail](#delete-the-recovery-services-vault).
- Si toutes les dépendances sont supprimées et que vous recevez toujours le *message d’erreur de suppression du coffre*, utilisez l’outil ARMClient pour effectuer les étapes suivantes (après la remarque).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Accédez à [chocolatey.org](https://chocolatey.org/) pour télécharger et installer Chocolatey. Ensuite, installez ARMClient en exécutant la commande suivante :

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Connectez-vous à votre compte Azure, puis exécutez la commande suivante :

    `ARMClient.exe login [environment name]`

3. Dans le portail Azure, rassemblez l’ID d’abonnement et le nom du groupe de ressources pour le coffre à supprimer.

Pour plus d’informations sur la commande ARMClient, reportez-vous au fichier [Lisez-moi ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Utiliser le client Azure Resource Manager pour supprimer un coffre Recovery Services

1. Exécutez la commande suivante à l’aide de votre ID d’abonnement, du nom du groupe de ressources et du nom du coffre. Si vous n’avez pas de dépendances, le coffre est supprimé quand vous exécutez la commande suivante :

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Si le coffre n’est pas vide, vous recevez le message d’erreur suivant : *Impossible de supprimer le coffre car celui-ci contient des ressources.* Pour supprimer un élément ou conteneur protégé au sein d’un coffre, exécutez la commande suivante :

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Dans le portail Azure, vérifiez que le coffre est supprimé.


## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les coffres Recovery Services](backup-azure-recovery-services-vault-overview.md)<br/>
[En savoir plus sur la supervision et la gestion des coffres Recovery Services](backup-azure-manage-windows-server.md)
