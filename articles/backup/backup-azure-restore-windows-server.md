---
title: Restaurer des données dans Azure sur un serveur Windows ou un ordinateur Windows
description: Découvrez comment restaurer des données stockées dans Azure sur un serveur Windows ou un ordinateur Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: d58b51f06c21c787e4aa720c803ab6533544d55c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110815"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Restaurer les fichiers dans Windows à l’aide du modèle de déploiement Resource Manager

Cet article explique comment restaurer des données depuis un coffre de sauvegarde. Pour restaurer des données, vous pouvez utiliser l’Assistant Récupérer des données dans l’agent Microsoft Azure Recovery Services (MARS). Vous pouvez :

* Restaurer des données sur l’ordinateur à partir duquel les sauvegardes ont été effectuées.
* Restaurez les données sur un autre ordinateur.

Utilisez la fonctionnalité de restauration instantanée afin de monter une capture instantanée de point de récupération inscriptible comme volume de récupération. Vous pouvez ensuite explorer le volume de récupération, copier les fichiers sur un ordinateur local et sélectionner les fichiers à restaurer.

> [!NOTE]
> La [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) est nécessaire si vous souhaitez utiliser la restauration instantanée pour restaurer des données. De plus, les données de sauvegarde doivent être protégées dans des coffres pour les pays listés dans l’article relatif au support. Consultez la [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) pour obtenir la liste la plus récente des pays qui prennent en charge la restauration instantanée.
>

Utilisez la restauration instantanée avec les coffres Recovery Services dans le portail Azure. Si vous avez stocké des données dans des coffres de sauvegarde, ces derniers ont été convertis en coffres Recovery Services. Si vous souhaitez utiliser la restauration instantanée, téléchargez la mise à jour MARS et suivez les procédures mentionnant cette opération.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Utiliser la restauration instantanée pour récupérer des données sur le même ordinateur

Si vous avez supprimé accidentellement un fichier et que vous voulez le restaurer sur le même ordinateur (à partir duquel la sauvegarde est effectuée), les étapes suivantes vous aident à récupérer les données.

1. Ouvrez le composant logiciel enfichable **Sauvegarde Microsoft Azure**. Si vous ne savez pas où il a été installé, recherchez **Sauvegarde Microsoft Azure** sur l’ordinateur ou le serveur.

    L’application de bureau devrait apparaître dans les résultats de recherche.

2. Sélectionnez **Récupérer des données** pour lancer l’Assistant.

    ![Capture de Sauvegarde, avec « Récupérer des données » mis en surbrillance](./media/backup-azure-restore-windows-server/recover.png)

3. Dans la page **Prise en main**, pour restaurer les données sur le même serveur ou ordinateur, sélectionnez **Ce serveur (`<server name>`)** > **Suivant**.

    ![Capture d’écran de la page Mise en route de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Sur le **sélectionner le Mode de récupération** page, choisissez **fichiers et dossiers individuels** > **suivant**.

    ![Capture d’écran de la page Mode de récupération de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > L’option de restauration de fichiers et dossiers individuels requiert .NET Framework version 4.5.2 ou ultérieure. Si vous ne voyez pas l’option **Individual Files and Folders** (Fichiers et dossiers individuels), vous devez mettre à niveau .NET Framework vers la version 4.5.2 ou une version ultérieure, puis réessayer.

   > [!TIP]
   > L’option **Individual Files and Folders** (Fichiers et dossiers individuels) autorise un accès rapide aux données de point de récupération. Elle convient pour la récupération de fichiers individuels, avec des tailles ne totalisant pas plus de 80 Go, et offre des vitesses de transfert ou de copie pouvant atteindre 6 Mbits/s lors de la récupération. L’option **Volume** récupère toutes les données sauvegardées dans un volume spécifié. Cette option offre des vitesses de transfert supérieures (jusqu’à 60 Mbits/s), ce qui est idéal pour la récupération de données de grande taille ou de volumes entiers.

5. Dans la page **Sélectionner le volume et la date**, sélectionnez le volume qui contient les fichiers et dossiers à restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Si plusieurs points de récupération sont disponibles pour une même date, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

    ![Capture d’écran de la page Volume et date de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Après avoir choisi le point de récupération à restaurer, sélectionnez **Monter**.

    La Sauvegarde Azure monte le point de récupération local et l’utilise comme volume de récupération.

7. Dans la page **Rechercher et récupérer des fichiers**, cliquez sur **Parcourir** pour ouvrir l’Explorateur Windows et rechercher les fichiers et dossiers que vous souhaitez.

    ![Capture d’écran de la page Récupérer des fichiers de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Dans l’Explorateur Windows, copiez les fichiers et les dossiers que vous souhaitez restaurer et collez-les à l’emplacement local de votre choix sur l’ordinateur ou le serveur. Vous pouvez ouvrir ou diffuser en continu les fichiers directement à partir du volume de récupération et vérifier que les versions récupérées sont les bonnes.

    ![Capture d’écran de l’Explorateur Windows avec Copier en surbrillance](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Lorsque vous avez terminé, dans la page **Rechercher et récupérer des fichiers**, sélectionnez **Démonter**. Puis sélectionnez **Oui** pour confirmer que vous souhaitez démonter le volume.

    ![Capture d’écran de la page Récupérer des fichiers de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Si vous ne sélectionnez pas **Démonter**, le volume de récupération reste monté pendant 6 heures à compter du montage. Toutefois, la durée du montage est étendue jusqu’à un maximum de 24 heures en cas de copie de fichier en cours. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Utiliser la restauration instantanée pour restaurer des données sur un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer les données d’Azure Backup sur un autre ordinateur. Les étapes suivantes illustrent le flux de travail.


Ces étapes incluent la terminologie suivante :

* *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.
* *Ordinateur cible* : ordinateur sur lequel les données sont récupérées.
* *Exemple d’archivage* : coffre Recovery Services dans lequel l’ordinateur source et l’ordinateur cible sont enregistrés. <br/>

> [!NOTE]
> Il est impossible de restaurer les sauvegardes sur un ordinateur cible qui fonctionne avec une version antérieure du système d’exploitation. Par exemple, une sauvegarde effectuée à partir d’un ordinateur Windows 7 ne peut pas être restaurée sur un ordinateur sous Windows 8 (ou une version ultérieure). Il n’est pas possible de restaurer sur un ordinateur Windows 7 une sauvegarde effectuée à partir d’un ordinateur Windows 8.
>
>

1. Ouvrez le composant logiciel enfichable **Sauvegarde Microsoft Azure** sur l’ordinateur cible.

2. Vérifiez que l’ordinateur cible et l’ordinateur source sont inscrits auprès du même coffre Recovery Services.

3. Sélectionnez **Récupérer des données** pour ouvrir **l’Assistant Récupération de données**.

    ![Capture de Sauvegarde, avec « Récupérer des données » mis en surbrillance](./media/backup-azure-restore-windows-server/recover.png)

4. Dans la page **Prise en main**, sélectionnez **Autre serveur**.

    ![Capture d’écran de la page Mise en route de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Fournissez le fichier d’informations d’identification correspondant à l’exemple de coffre, puis cliquez sur **Suivant**.

    Si le fichier d’informations d’identification du coffre n’est pas valide (ou a expiré), téléchargez un nouveau fichier d’informations d’identification de coffre à partir de l’exemple de coffre dans le Portail Azure. Une fois que vous avez fourni des informations d’identification de coffre valides, le nom du coffre de sauvegarde correspondant s’affiche.


6. Dans la page **Sélectionner un serveur de sauvegarde**, sélectionnez l’ordinateur source dans la liste des ordinateurs affichés et fournissez la phrase secrète. Sélectionnez ensuite **Suivant**.

    ![Capture d’écran de la page Sélectionner un serveur de sauvegarde de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Dans la page **Sélectionnez le mode de récupération**, sélectionnez **Fichiers et dossiers individuels** > **Suivant**.

    ![Capture d’écran de la page Mode de récupération de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Dans la page **Sélectionner le volume et la date**, sélectionnez le volume qui contient les fichiers et dossiers à restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Si plusieurs points de récupération sont disponibles pour une même date, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

    ![Capture d’écran de la page Volume et date de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Sélectionnez **Monter** pour monter localement le point de récupération comme volume de récupération sur votre ordinateur cible.

10. Dans la page **Rechercher et récupérer des fichiers**, cliquez sur **Parcourir** pour ouvrir l’Explorateur Windows et rechercher les fichiers et dossiers que vous souhaitez.

    ![Capture d’écran de la page Récupérer des fichiers de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Dans l’Explorateur Windows, copiez les fichiers et les dossiers du volume de récupération et collez-les à l’emplacement de votre ordinateur cible. Vous pouvez ouvrir ou diffuser en continu les fichiers directement à partir du volume de récupération et vérifier que les versions récupérées sont les bonnes.

    ![Capture d’écran de l’Explorateur Windows avec Copier en surbrillance](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Lorsque vous avez terminé, dans la page **Rechercher et récupérer des fichiers**, sélectionnez **Démonter**. Puis sélectionnez **Oui** pour confirmer que vous souhaitez démonter le volume.

    ![Capture d’écran de la page Récupérer des fichiers de l’Assistant Récupérer des données](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Si vous ne sélectionnez pas **Démonter**, le volume de récupération reste monté pendant 6 heures à compter du montage. Toutefois, la durée du montage est étendue jusqu’à un maximum de 24 heures en cas de copie de fichier en cours. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez restauré vos fichiers et vos dossiers, vous pouvez [gérer vos sauvegardes](backup-azure-manage-windows-server.md).
