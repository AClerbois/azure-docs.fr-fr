---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour d’une propriété d’appareil ou d’un paramètre, ou bien l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 903b4f1ea1484aec827c4b2d54c8dd8a9dd69d8f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509526"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc sur les propriétés, les paramètres et les commandes des appareils. Cet article vous initie à l’utilisation de travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail. Il vous montre comment augmenter la vitesse du ventilateur pour plusieurs distributeurs réfrigérés.

1. Accédez à Travaux à partir du volet de navigation.

1. Sélectionnez **+ Nouveau** pour créer un travail.

    ![Créer un travail](./media/howto-run-a-job/createnewjob.png)

1. Entrez un nom et une description pour identifier le travail que vous créez.

1. Sélectionnez l’ensemble d’appareils auquel vous voulez appliquer votre travail. Les appareils appartenant à cet ensemble d’appareils apparaissent alors sur le côté droit. Si vous sélectionnez un ensemble d’appareils incomplet, aucun appareil n’apparaît et vous voyez un message indiquant que votre ensemble d’appareils est incomplet.

1. Ensuite, choisissez le type de travail à définir (un paramètre, une propriété ou une commande). Sélectionnez **+** en regard du type de travail sélectionné et ajoutez vos opérations.

    ![Configurer le travail](./media/howto-run-a-job/configurejob.png)

1. Sur le côté droit, choisissez les appareils sur lesquels vous voulez exécuter le travail. Si vous cochez la case du haut, tous les appareils de l’ensemble d’appareils sont sélectionnés. Si vous cochez la case en regard de **Nom**, tous les appareils présents sur la page actuelle sont sélectionnés.

1. Après avoir sélectionné vos appareils, choisissez **Exécuter** ou **Enregistrer**. Le travail apparaît maintenant dans la page **Travaux** principale. Dans cette vue, vous pouvez voir le travail en cours d’exécution et l’historique des travaux déjà exécutés. Le travail en cours d’exécution apparaît toujours en haut de la liste. Votre travail enregistré peut être ouvert à nouveau à tout moment pour être modifié ou exécuté.

    ![Afficher le travail](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Vous pouvez voir l’historique des derniers travaux exécutés sur une période maximale de 30 jours.

1. Pour obtenir une vue d’ensemble de votre travail, sélectionnez le travail à afficher dans la liste. Cette vue d’ensemble contient les détails du travail, ainsi que les noms et les états des appareils. Dans cette vue d’ensemble, vous pouvez également sélectionner **Détails du travail de téléchargement** pour télécharger un fichier .csv des détails de votre travail, notamment les appareils et les valeurs de leur état. Ces informations peuvent être utiles pour le dépannage.

    ![Afficher l’état de l’appareil](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrêter un travail en cours d’exécution

Pour arrêter un travail en cours d’exécution, sélectionnez-la et choisissez **Arrêter** sur le panneau. L’état du travail change pour indiquer qu’il est arrêté.

   ![Arrêter une tâche](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Exécuter une tâche arrêtée

Pour exécuter un travail qui est actuellement arrêté, sélectionnez-le. Choisissez **Exécuter** sur le panneau. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution.

   ![Tâche reprise](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copier un travail

Pour copier un travail existant que vous avez créé, sélectionnez-le dans la page principale des travaux et sélectionnez **Copier**. Une nouvelle copie de la configuration du travail s’ouvre pour vous permettre de la modifier. Vous pouvez enregistrer ou d’exécuter le nouveau travail. Si des modifications ont été apportées à votre ensemble d’appareils sélectionné, elles sont reflétées dans ce travail copié que vous pouvez modifier.

   ![Copier un travail](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Afficher l’état d’un travail

Une fois qu’un travail a été créé, la colonne **État** est mise à jour avec le dernier message d’état du travail. Le tableau suivant liste les valeurs d’état possibles :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | Ce travail n’a pas encore commencé à s’exécuter sur les appareils.         |
| Exécution              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Ce travail a été arrêté manuellement par un utilisateur.           |

Le message d’état est suivi d’une vue d’ensemble des appareils au sein du travail. Le tableau suivant liste les valeurs d’état possibles des appareils :

| Message d’état       | Signification de l’état                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Nombre d’appareils sur lesquels le travail s’est exécuté correctement.       |
| Échec               | Nombre d’appareils sur lesquels l’exécution du travail a échoué.       |

### <a name="view-the-device-status"></a>Afficher l’état des appareils

Pour voir l’état du travail et tous les appareils affectés, sélectionnez le travail. Pour télécharger un fichier .csv des détails de votre travail, avec la liste des appareils et les valeurs de leur état, sélectionnez **Détails du travail de téléchargement**. En regard du nom de chaque appareil, vous voyez un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | Le travail n’a pas pu être exécuté sur cet appareil. Le message d’erreur montre plus d’informations.  |
| Pending              | Le travail n’a pas encore été exécuté sur cet appareil.                                   |

> [!NOTE]
> Si un appareil a été supprimé, vous ne pouvez pas le sélectionner et il apparaît comme étant supprimé avec mention de son ID.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer des travaux dans votre application Azure IoT Central, voici les étapes suivantes :

- [Utiliser des ensembles d’appareils](howto-use-device-sets.md)
- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-device-template.md)
