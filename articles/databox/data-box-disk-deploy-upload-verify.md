---
title: Tutoriel pour vérifier le chargement de données à partir d’un disque Azure Data Box vers un compte de stockage | Microsoft Docs
description: Utilisez ce tutoriel pour savoir comment vérifier les données chargées à partir de votre disque Azure Data Box vers un compte de stockage Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013994"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Didacticiel : Vérifier le chargement de données à partir d’un disque Azure Data Box

Ce tutoriel est le dernier de la série : Déployer Azure Data Box Disk. Dans ce tutoriel, vous allez découvrir :

> [!div class="checklist"]
> * Vérifier le chargement des données dans Azure
> * Effacer les données du disque Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi le [tutoriel : Renvoyer un disque Azure Data Box](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Une fois que les disques ont été récupérés par le transporteur, l’état de la commande dans le portail affiche **Picked up (Récupérée)** . Un ID de suivi s’affiche également.

![Disques récupérés](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Lorsque Microsoft reçoit et analyse le disque, l’état de la commande est défini sur **Received (Reçue)** . 

![Disques reçus](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Les données sont copiées automatiquement une fois que les disques sont connectés à un serveur dans le centre de données Azure. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)** .

![Copie de données terminée](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Si la copie de termine avec des erreurs, voir [résoudre des erreurs de chargement](data-box-disk-troubleshoot-upload.md).

Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

  - Pour les objets blob de blocs et de pages : `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Pour les fichiers Azure : `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande.

  - Si la copie vers des disques managés dans Azure a réussi, vous pouvez accéder aux **détails de la commande** dans le portail Azure et noter le nom du groupe de ressources spécifié pour les disques managés.

      ![Voir les détails de la commande](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Accédez au groupe de ressources noté précédemment pour trouver vos disques managés.

      ![Groupe de ressources pour les disques managés](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Si vous avez copié un VHDX ou un VHD dynamique ou de différenciation, le VHDX/VHD est chargé dans le compte de stockage de préproduction comme objet blob de blocs. Accédez à votre compte de stockage de préproduction (**Compte de stockage > Objets blob**), puis sélectionnez le conteneur approprié (StandardSSD, StandardHDD ou PremiumSSD). Les disques VHDX/VHD doivent s’afficher en tant qu’objets blob de blocs dans votre compte de stockage de préproduction.

Pour vérifier que les données ont bien été chargées dans Azure, procédez comme suit :

1. Accédez au compte de stockage associé à votre commande de disque.
2. Accédez à **Service BLOB > Parcourir les objets blob**. La liste des conteneurs apparaît. En fonction des sous-dossiers que vous avez créés dans les dossiers *BlockBlob* et *PageBlob*, des conteneurs de même nom sont créés dans votre compte de stockage.
    Si les noms des dossiers ne sont pas conformes aux conventions d’affectation de noms Azure, les données ne seront pas chargées dans Azure.

4. Pour vérifier que l’ensemble du jeu de données a été chargé, utilisez l’Explorateur Stockage Microsoft Azure. Associez le compte de stockage correspondant à la commande de location de disque, puis consultez la liste des conteneurs d’objets blob. Sélectionnez un conteneur, cliquez sur **...Plus**, puis cliquez sur **Statistiques des dossiers**. Le volet **Activités** affiche les statistiques de ce dossier, y compris le nombre d’objets blob et la taille totale des objets blob. La taille totale des objets blob, exprimée en octets, doit correspondre à la taille du jeu de données.

    ![Statistiques des dossiers dans l’explorateur de stockage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Effacer les données du disque Data Box

Une fois que la copie est terminée et que vous avez vérifié que les données se trouvent bien dans le compte de stockage Azure, les disques sont effacés de manière sécurisée conformément à la norme NIST.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Vérifier le chargement des données dans Azure
> * Effacer les données du disque Data Box


Passez au guide pratique suivant pour savoir comment gérer des disques Data Box via le portail Azure.

> [!div class="nextstepaction"]
> [Utiliser le portail Azure pour administrer le disque Azure Data Box](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Une fois les données chargées sur Azure, vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

    - **Pour les objets blob de blocs et de pages** : https://<nom_compte_stockage>.blob.core.windows.net/<containername>/files/a.txt

    - **Pour Azure Files** : https://<nom_compte_stockage>.file.core.windows.net/<sharename>/files/a.txt

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande.

::: zone-end


