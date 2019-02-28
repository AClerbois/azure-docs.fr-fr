---
title: 'Sauvegarde Azure : Restaurer des machines virtuelles à l’aide du portail Azure'
description: Restaurer une machine virtuelle Azure à partir d’un point de récupération à l’aide du portail Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: restauration de sauvegarde ; restauration ; point de récupération ;
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: geg
ms.openlocfilehash: b919adbaf665055ee19df9b9167984cc29388032
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428736"
---
# <a name="restore-azure-vms"></a>Restaurer des machines virtuelles Azure

Cet article décrit comment restaurer les données de machines virtuelles Azure à partir de points de récupération stockés dans des coffres Recovery Services [Sauvegarde Azure](backup-overview.md).

### <a name="restore-options"></a>Options de restauration

Sauvegarde Azure permet de restaurer une machine virtuelle de plusieurs façons.

**Option de restauration** | **Détails**
--- | ---
**Créer une machine virtuelle** | Permet d’avoir rapidement une machine virtuelle de base opérationnelle à partir d’un point de restauration.<br/><br/> Vous pouvez nommer la machine virtuelle, sélectionner le groupe de ressources et le réseau virtuel (VNet) où elle sera placée et spécifier un type de stockage.
**Restaurer un disque** | Restaure un disque de machine virtuelle que vous pouvez ensuite utiliser pour créer une machine virtuelle.<br/><br/> Sauvegarde Azure fournit un modèle pour vous aider à personnaliser et à créer une machine virtuelle. <br/><br/> Cette option copie des disques durs virtuels dans le compte de stockage que vous spécifiez. Le travail de restauration génère un modèle que vous pouvez télécharger et utiliser pour spécifier des paramètres de machine virtuelle personnalisés et créer une machine virtuelle.<br/><br/> - Le compte de stockage doit se trouver au même emplacement que le coffre. Si vous n’avez pas de compte de stockage, créez-en un.<br/><br/> Le type de réplication du compte de stockage est indiqué. Le stockage redondant interzone (ZRS) n’est pas pris en charge.<br/><br/> Vous pouvez également attacher le disque à une machine virtuelle existante ou créer une machine virtuelle à l’aide de PowerShell.<br/><br/> Cette option est utile si vous souhaitez personnaliser la machine virtuelle, ajouter des paramètres de configuration qui n’existaient pas au moment de la sauvegarde ou encore ajouter des paramètres qui doivent être configurés à l’aide du modèle ou de PowerShell.
**Remplacer l’existant** | Vous pouvez restaurer un disque et l’utiliser pour remplacer un disque sur la machine virtuelle existante.<br/><br/> La machine virtuelle actuelle doit exister. Si elle a été supprimée, vous ne pouvez pas utiliser cette option.<br/><br/> Sauvegarde Azure prend un instantané de la machine virtuelle existante avant de remplacer le disque. L’instantané est stocké dans l’emplacement intermédiaire que vous spécifiez. Les disques existants connectés à la machine virtuelle sont ensuite remplacés à l’aide du point de restauration sélectionné. L’instantané pris est copié dans le coffre et conservé conformément à votre stratégie de conservation spécifiée.<br/><br/> L’option Remplacer l’existant est prise en charge pour les machines virtuelles managées non chiffrées. Elle ne l’est pas pour les disques non managés, les [machines virtuelles généralisées](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) ou les machines virtuelles [créées à l’aide d’images personnalisées](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Si le point de restauration a plus ou moins de disques que la machine virtuelle actuelle, le nombre de disques du point de restauration reflète alors uniquement la configuration de la machine virtuelle.



> [!NOTE]
> Vous pouvez également récupérer des fichiers et des dossiers spécifiques sur une machine virtuelle Azure. [Plus d’informations](backup-azure-restore-files-from-vm.md)
>
> Si vous exécutez la [dernière version](backup-instant-restore-capability.md) de Sauvegarde Azure pour les machines virtuelles Azure (appelée Restauration instantanée), les instantanés sont conservés sept jours au plus. Vous pouvez restaurer une machine virtuelle à partir de ces instantanés avant l’envoi des données de sauvegarde au coffre. Si vous souhaitez restaurer une machine virtuelle à partir d’une sauvegarde effectuée au cours des sept derniers jours, il est plus rapide de la restaurer à partir de l’instantané qu’à partir du coffre.


## <a name="select-a-restore-point"></a>Sélectionner un point de restauration 

1. Dans le coffre associé à la machine virtuelle à restaurer, cliquez sur **Éléments de sauvegarde** > **Machine virtuelle Azure**.
2. Cliquez sur une machine virtuelle. Par défaut, les points de récupération des 30 derniers jours sont affichés sur le tableau de bord de la machine virtuelle. Vous pouvez afficher les points de récupération de plus de 30 jours ou appliquer un filtre pour rechercher des points de récupération en fonction de dates, plages de dates et différents types de cohérence d’instantané.
3. Pour restaurer la machine virtuelle, cliquez sur **Restaurer la machine virtuelle**.
    ![Point de restauration](./media/backup-azure-arm-restore-vms/restore-point.png)
4. Sélectionnez un point de restauration à utiliser pour la récupération.



## <a name="choose-a-vm-restore-configuration"></a>Choisir une configuration de restauration de machine virtuelle

1. Dans **Configuration de la restauration**, sélectionnez une option de restauration :
    - **Créer un nouveau**. Utilisez cette option si vous souhaitez créer une machine virtuelle. Vous pouvez créer une machine virtuelle avec des paramètres simples, ou restaurer un disque et créer une machine virtuelle personnalisée.
    - **Remplacer l’existant** : Utilisez cette option si vous souhaitez remplacer les disques sur une machine virtuelle existante.
        ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/restore-configuration.png)
2. Spécifiez les paramètres de l’option de restauration sélectionnée.

## <a name="create-new-create-a-vm"></a>Créer-Créer une machine virtuelle

Cette [option de restauration](#restore-options) vous permet de créer rapidement une machine virtuelle avec des paramètres de base à partir d’un point de restauration. 

1. Dans **Configuration de la restauration** > **Créer** > **Type de restauration**, sélectionnez **Créer une machine virtuelle**.
2. Dans **Nom de la machine virtuelle**, spécifiez une machine virtuelle qui n’existe pas dans l’abonnement.
3. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant pour la nouvelle machine virtuelle ou créez-en un avec un nom global unique. Si vous attribuez un nom qui existe déjà, Azure attribue au groupe le même nom que celui de la machine virtuelle.
4. Dans **Réseau virtuel**, sélectionnez le réseau virtuel dans lequel la machine virtuelle sera placée. Tous les réseaux virtuels associés à l’abonnement sont affichés. Sélectionnez le sous-réseau. Le premier sous-réseau est sélectionné par défaut.
5. Dans **Emplacement de stockage**, spécifiez le type de stockage pour la machine virtuelle.

    ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Dans **Configuration de la restauration**, sélectionnez **OK**. Dans **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.


## <a name="create-new-restore-disks"></a>Créer-Restaurer des disques

Cette [option de restauration](#restore-options) vous permet de créer un disque à partir d’un point de restauration. Une fois le disque créé, vous pouvez effectuer l’une des opérations suivantes :

- Utiliser le modèle généré durant l’opération de restauration pour personnaliser les paramètres et déclencher le déploiement de la machine virtuelle. Vous modifiez les paramètres de modèle par défaut, puis vous envoyez le modèle pour le déploiement de machine virtuelle.
- [Attacher des disques restaurés](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal#option-2-attach-an-existing-disk) à une machine virtuelle existante.
- [Créer une machine virtuelle](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks à partir des disques restaurés à l’aide de PowerShell.


1. Dans **Configuration de la restauration** > **Créer** > **Type de restauration**, sélectionnez **Restaurer des disques**.
2. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant pour les disques restaurés ou créez-en un avec un nom global unique.
3. Dans **Compte de stockage**, spécifiez le compte dans lequel copier les disques durs virtuels. Vérifiez que le compte se trouve dans la même région que le coffre.

    ![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Dans **Configuration de la restauration**, sélectionnez **OK**. Dans **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.


### <a name="use-templates-to-customize-a-restored-vm"></a>Utiliser des modèles pour personnaliser une machine virtuelle restaurée

Une fois le disque restauré, utilisez le modèle généré dans le cadre de l’opération de restauration pour personnaliser et créer une machine virtuelle :

1. Ouvrez **Restaurer > Détails du travail** pour le travail approprié.

2. Dans **Détails du travail de restauration**, sélectionnez **Déployer le modèle** pour lancer le déploiement du modèle.

    ![Détail du travail de restauration](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Pour personnaliser le paramètre de la machine virtuelle fourni dans le modèle, cliquez sur **Modifier le modèle**. Si vous souhaitez ajouter d’autres personnalisations, cliquez sur **Modifier les paramètres**.
    - [Découvrez-en plus](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) sur le déploiement de ressources à partir d’un modèle personnalisé.
    - [Découvrez-en plus](../azure-resource-manager/resource-group-authoring-templates.md) sur la création de modèles.

  ![Charger un déploiement de modèle](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Entrez les valeurs personnalisées pour la machine virtuelle, acceptez les **conditions générales**, puis cliquez sur **Acheter**.

  ![Envoyer un déploiement de modèle](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Remplacer les disques existants

Cette [option de restauration](#restore-options) vous permet de remplacer un disque de machine virtuelle existant par le point de restauration sélectionné. [Passez en revue](#restore-options) toutes les options de restauration.

1. Dans **Configuration de la restauration**, cliquez sur **Remplacer l’existant**.
2. Dans **Type de restauration**, sélectionnez **Remplacer le(s) disque(s)**. Il s’agit du point de restauration qui sera utilisé pour remplacer les disques existants de la machine virtuelle.
3. Dans **Emplacement intermédiaire**, spécifiez l’emplacement auquel les instantanés des disques managés actuels doivent être enregistrés.

   ![Assistant Configuration de restauration avec l’option Replace existing (Remplacer l’existant)](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)
   
  
## <a name="restore-vms-with-special-network-configurations"></a>Restaurer des machines virtuelles avec des configurations réseau spéciales

Il existe un certain nombre de scénarios courants dans lesquels vous pouvez avoir besoin de restaurer des machines virtuelles.

**Scénario** | **Assistance**
--- | ---
**Restaurer des machines virtuelles avec Hybrid Use Benefit** | Si une machine virtuelle Windows utilise une [licence HUB (Hybrid Use Benefit)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaurez les disques et créez une machine virtuelle à l’aide du modèle fourni (**Type de licence** ayant pour valeur **Windows_Server**) ou de PowerShell.  Vous pouvez également appliquer ce paramètre après avoir créé la machine virtuelle.
**Restaurer des machines virtuelles en cas de défaillance du centre de données Azure** | Si le coffre utilise GRS et que le centre de données principal de la machine virtuelle tombe en panne, Sauvegarde Azure prend en charge la restauration des machines virtuelles sauvegardées sur le centre de données appairé. Sélectionnez un compte de stockage dans le centre de données appairé et restaurez-le comme d’habitude. Sauvegarde Azure utilise le service de calcul dans l’emplacement appairé pour créer la machine virtuelle restaurée. [Découvrez-en plus](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sur la résilience des centres de données.
**Restaurer une machine virtuelle contrôleur de domaine unique dans un seul domaine** | Restaurez la machine virtuelle comme n’importe quelle autre machine virtuelle. Notez les points suivants :<br/><br/> Du point de vue d’Active Directory, la machine virtuelle Azure est semblable à toute autre machine virtuelle.<br/><br/> Le mode DSRM (Directory Restore Mode) étant également disponible, tous les scénarios de récupération Active Directory sont viables. [Découvrez-en plus](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) sur les considérations relatives à la sauvegarde et à la restauration de contrôleurs de domaine virtualisés. 
**Restaurer plusieurs machines virtuelles contrôleurs de domaine dans un seul domaine** | Si d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. S’il s’agit du dernier contrôleur de domaine dans le domaine ou si une récupération dans un réseau isolé est effectuée, utilisez une [récupération de forêt](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurer plusieurs domaines dans une forêt** | Nous recommandons une [récupération de forêt](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restauration complète** | La principale différence entre les machines virtuelles Azure et les hyperviseurs locaux est l’absence d’une console de machine virtuelle dans Azure. Une console est nécessaire pour certains scénarios tels que la récupération à l’aide d’une sauvegarde de type Récupération complète (BMR, Bare Metal Recovery). Toutefois, la restauration d’une machine virtuelle à partir du coffre constitue un remplacement total pour la récupération complète.
**Restaurer des machines virtuelles avec des configurations réseau spéciales** | Les machines virtuelles qui utilisent un équilibrage de charge interne ou externe, plusieurs cartes réseau ou plusieurs adresses IP réservées sont des exemples de configurations réseau spéciales. Vous pouvez restaurer ces machines virtuelles à l’aide de l’[option de restauration de disque](#create-new-restore-disks). Cette option effectue une copie des disques durs virtuels dans le compte de stockage spécifié. Vous pouvez ensuite créer une machine virtuelle avec un équilibreur de charge interne (https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)) ou externe (https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/), [plusieurs cartes réseau](../virtual-machines/windows/multiple-nics.md) ou [plusieurs adresses IP réservées](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), conformément à votre configuration. 


## <a name="track-the-restore-operation"></a>Suivi de l’opération de restauration
Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée un travail à des fins de suivi. Sauvegarde Azure affiche des notifications sur le travail dans le portail. Si elles ne sont pas visibles, cliquez sur le symbole **Notifications** pour les afficher.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-notification1.png)
 
 Faites le suivi de la restauration comme ceci :

1. Pour afficher les opérations du travail, cliquez sur le lien hypertexte de notifications. Vous pouvez également cliquer sur **Travaux de sauvegarde** dans le coffre, puis sur la machine virtuelle appropriée.

    ![Liste des machines virtuelles d’un coffre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Pour suivre la progression de la restauration, cliquez sur n’importe quel travail de restauration avec l’état **En cours**. Vous obtenez une barre de progression qui affiche des informations sur l’avancement de la restauration :

    - **Estimation de la durée de la restauration** : indique initialement le temps nécessaire pour terminer l’opération de restauration. Au fur et à mesure que l’opération progresse, le temps nécessaire se réduit pour atteindre zéro une fois l’opération de restauration terminée.
    - **Pourcentage de la restauration** : affiche le pourcentage de l’opération de restauration terminée.
    - **Nombre d’octets transférés** : si vous restaurez en créant une machine virtuelle, indique les octets qui ont été transférés par rapport au nombre total d’octets à transférer.

## <a name="post-restore-steps"></a>Étapes post-restauration

Tenez compte de ce qui suit après la restauration d’une machine virtuelle :

- Les extensions présentes durant la configuration de sauvegarde sont installées, mais pas activées. Si vous rencontrez un problème, réinstallez les extensions.
- Si la machine virtuelle sauvegardée a une adresse IP statique, la machine virtuelle restaurée a une adresse IP dynamique pour éviter tout conflit. Vous pouvez [ajouter une adresse IP statique à la machine virtuelle restaurée](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Une machine virtuelle restaurée n’a pas de groupe à haute disponibilité. Si vous utilisez l’option de restauration de disque, vous pouvez [spécifier un groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) quand vous créez une machine virtuelle à partir du disque à l’aide du modèle fourni ou de PowerShell.
* Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe est bloqué après la restauration pour des raisons de sécurité. Utilisez l’extension VMAccess sur la machine virtuelle restaurée pour [réinitialiser le mot de passe](../virtual-machines/linux/reset-password.md). Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour ne pas avoir à réinitialiser le mot de passe après la restauration. 


## <a name="backing-up-restored-vms"></a>Sauvegarde de machines virtuelles restaurées

- Si vous avez restauré une machine virtuelle sur le même groupe de ressources avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration.
- Si vous avez restauré la machine virtuelle sur un groupe de ressources différent ou que vous avez spécifié un nom différent pour la machine virtuelle restaurée, vous devez configurer la sauvegarde pour la machine virtuelle restaurée.



## <a name="next-steps"></a>Étapes suivantes


- Si vous rencontrez des problèmes durant le processus de restauration, [passez en revue](backup-azure-vms-troubleshoot.md#restore) les erreurs et les problèmes courants.
- Une fois la machine virtuelle restaurée, découvrez-en plus sur la [gestion des machines virtuelles](backup-azure-manage-vms.md)


