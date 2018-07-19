---
title: Voir les changements de contenu de fichier avec Azure Automation
description: Utilisez la fonctionnalité de changement de contenu de fichier de Change Tracking pour voir le contenu d’un fichier qui a changé.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0582505d66bbef3064359fa4047676c4ba60b4e9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870554"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Voir le contenu d’un fichier suivi avec Change Tracking

Le suivi de contenu de fichier vous permet de voir le contenu d’un fichier avant et après un changement suivi avec Change Tracking. Pour ce faire, il enregistre le contenu du fichier dans un compte de stockage chaque fois qu’un changement a lieu.

## <a name="requirements"></a>Configuration requise

* Un compte de stockage standard utilisant le modèle de déploiement Resource Manager est nécessaire pour stocker le contenu du fichier. Les comptes de stockage des modèles de déploiement Premium et Classic ne doivent pas être utilisés. Pour plus d’informations sur les comptes de stockage, consultez [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md)

* Le compte de stockage utilisé peut seulement avoir un compte Automation connecté.

* [Change Tracking](automation-change-tracking.md) est activé dans votre compte Automation.

## <a name="enable-file-content-tracking"></a>Activer le suivi de contenu de fichier

1. Dans le portail Azure, ouvrez votre compte Automation, puis sélectionnez **Change Tracking**.
2. Dans le menu en haut, sélectionnez **Modifier les paramètres**.
3. Sélectionnez **Contenu du fichier** et cliquez sur **Lier**. Cette action ouvre le volet **Ajouter l’emplacement du contenu pour Change Tracking**.

   ![enable](./media/change-tracking-file-contents/enable.png)

4. Sélectionnez l’abonnement et le compte de stockage à utiliser pour stocker le contenu du fichier. Si vous voulez activer le suivi de contenu de fichier pour tous les fichiers suivis existants, sélectionnez **Activé** pour **Charger le contenu de fichier pour tous les paramètres**. Vous pouvez changer ce paramètre pour chaque chemin de fichier par la suite.

   ![définir le compte de stockage](./media/change-tracking-file-contents/storage-account.png)

5. Après l’activation, le compte de stockage et les URI SAP sont affichés. Les URI SAP expirent au bout de 365 jours et peuvent être recréés en cliquant sur le bouton **Regénérer**.

   ![répertorier les clés de comptes](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Ajouter un fichier

Les étapes suivantes vous indiquent comment activer le suivi des changements pour un fichier :

1. Dans la page **Modifier les paramètres** de **Change Tracking**, sélectionnez l’onglet **Fichiers Windows** ou **Fichiers Linux**, puis cliquez sur **Ajouter**

1. Renseignez les informations du chemin de fichier et sélectionnez **True** sous **Charger le contenu de fichier pour tous les paramètres**. Ce paramètre active le suivi de contenu de fichier pour ce chemin de fichier uniquement.

   ![ajouter un fichier Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Voir le contenu d’un fichier suivi

1. Dès qu’un changement est détecté pour le fichier ou un fichier dans le chemin, il s’affiche dans le portail. Sélectionnez le changement de fichier dans la liste des changements. Le volet **Détails du changement** s’affiche.

   ![répertorier les changements](./media/change-tracking-file-contents/change-list.png)

1. Dans la page **Détails du changement**, vous voyez les informations standard du fichier avant et après, en haut à gauche, cliquez sur **Voir les changements de contenu de fichier** pour voir le contenu du fichier.

  ![Détails du changement](./media/change-tracking-file-contents/change-details.png)

1. La nouvelle page montre le contenu du fichier dans une vue côte à côte. Vous pouvez aussi sélectionner **Inline** pour avoir une vue inline des changements.

  ![voir les changements de fichier](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez le didacticiel sur Change Tracking pour en savoir plus sur l’utilisation de la solution :

> [!div class="nextstepaction"]
> [Dépanner les modifications apportées à votre environnement](automation-tutorial-troubleshoot-changes.md)

* Utilisez les [recherches de journaux dans Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.
