---
title: Déplacer des machines virtuelles IaaS Azure vers une autre région Azure à l’aide du service Azure Site Recovery | Microsoft Docs
description: Utilisez Azure Site Recovery pour déplacer des machines virtuelles IaaS Azure d’une région Azure à l’autre.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dc27e49cc67a902bb45b1d889bb61b1f4b3aab83
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788767"
---
# <a name="move-azure-vms-to-another-region"></a>Déplacer des machines virtuelles Azure vers une autre région

En plus de l’utilisation du service [Azure Site Recovery](site-recovery-overview.md) pour gérer et orchestrer la reprise d’activité après sinistre des ordinateurs locaux et des machines virtuelles Azure dans le cadre de la continuité d’activité et de la reprise d’activité, vous pouvez utiliser Site Recovery pour gérer le déplacement des machines virtuelles Azure vers une région secondaire. Pour déplacer des machines virtuelles Azure, activez la réplication pour celles-ci, puis basculez-les de la région principale vers la région secondaire de votre choix.

Ce tutoriel vous montre comment déplacer des machines virtuelles Azure vers une autre région. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un coffre Recovery Services
> * Activer la réplication pour une machine virtuelle
> * Effectuer un basculement pour déplacer la machine virtuelle

Ce didacticiel suppose que vous possédez déjà un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.





## <a name="prerequisites"></a>Prérequis

- Vérifiez que vous disposez de machines virtuelles Azure dans la région Azure à partir de laquelle vous souhaitez effectuer le déplacement.
- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](azure-to-azure-architecture.md).
- Examinez les [exigences et les limites de prise en charge](azure-to-azure-support-matrix.md).



## <a name="before-you-start"></a>Avant de commencer

Avant de configurer la réplication, effectuez les étapes suivantes.


### <a name="verify-target-resources"></a>Vérifier les ressources cibles

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée pour la récupération d’urgence. Contactez le support pour activer le quota requis.

2. Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge des machines virtuelles de tailles correspondant à vos machines virtuelles source. Site Recovery choisit la même taille ou la taille la plus proche possible pour la machine virtuelle cible.


### <a name="verify-account-permissions"></a>Vérifier les autorisations du compte

Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication pour une nouvelle machine virtuelle, vous devez avoir :

1. Des autorisations pour créer une machine virtuelle dans les ressources Azure. Le rôle prédéfini « Contributeur de machines virtuelles » a ces autorisations, incluant :
    - L’autorisation de créer une machine virtuelle dans le groupe de ressources sélectionné
    - L’autorisation de créer une machine virtuelle dans le réseau virtuel sélectionné
    - L’autorisation d’écrire dans le compte de stockage sélectionné

2. Vous devez également avoir l’autorisation de gérer les opérations Azure Site Recovery. Le rôle « Collaborateur Site Recovery » a toutes les autorisations nécessaires pour gérer les opérations Site Recovery dans un coffre Recovery Services.


### <a name="verify-vm-outbound-access"></a>Vérifier l’accès sortant d’une machine virtuelle

1. N’utilisez pas de proxy d’authentification dans le but de contrôler la connectivité réseau pour les machines virtuelles que vous voulez déplacer. 
2. Pour les besoins de ce tutoriel, nous partons du principe que les machines virtuelles que vous voulez déplacer peuvent accéder à Internet et qu’elles n’utilisent pas de proxy de pare-feu pour contrôler l’accès sortant. Si c’est le cas, vérifiez la configuration requise [ici](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Vérifier les certificats des machines virtuelles

Vérifiez que tous les certificats racines les plus récents sont présents sur les machines virtuelles à déplacer. Si les certificats racines les plus récents ne le sont pas, la machine virtuelle ne peut pas être inscrite auprès du service Site Recovery en raison de contraintes de sécurité.

- Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.
- Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.



## <a name="create-a-vault"></a>Création d'un coffre

Créez le coffre dans n’importe quelle région, à l’exception de la région source.

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez le nom convivial **ContosoVMVault**. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources **ContosoRG**.
5. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.






## <a name="select-the-source"></a>Sélectionner la source

1. Dans Coffres Recovery Services, cliquez sur **ConsotoVMVault** > **+Répliquer**.
2. Dans **Source**, sélectionnez **Azure**.
3. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
4. Sélectionnez le modèle de déploiement Resource Manager. Sélectionnez ensuite **Groupe de ressources source**.
5. Cliquez sur **OK** pour enregistrer les paramètres.


## <a name="enable-replication-for-azure-vms"></a>Activer la réplication des machines virtuelles Azure

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources.


1. Dans le portail Azure, sélectionnez **Machines virtuelles**.
2. Sélectionnez la machine virtuelle que vous souhaitez déplacer. Cliquez ensuite sur **OK**.
3. Dans **Paramètres**, cliquez sur **Récupération d’urgence**.
4. Dans **Configurer la récupération d’urgence** > **Région cible**, sélectionnez la région cible vers laquelle vous allez effectuer la réplication.
5. Pour ce didacticiel, acceptez les autres paramètres par défaut.
6. Cliquez sur **Activer la réplication**. Cette opération démarre un travail consistant à activer la réplication pour la machine virtuelle.

    ![activer la réplication](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>Exécuter un basculement

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine, puis sur **Basculement**.
2. Dans **Basculement**, sélectionnez **Dernier**. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
3. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
4. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
5. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Valider**. Cette étape termine le processus de migration.
6. Une fois la validation terminée, cliquez sur **Désactiver la réplication**.  Cette étape arrête la réplication de la machine virtuelle.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déplacé une machine virtuelle Azure vers une autre région. Vous pouvez maintenant configurer la reprise d’activité après sinistre pour la machine virtuelle que vous avez déplacée.

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence après la migration](azure-to-azure-quickstart.md)

