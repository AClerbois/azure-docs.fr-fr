---
title: Comment restaurer un serveur dans Azure Database pour MySQL
description: Cet article décrit comment restaurer un serveur Azure Database pour MySQL à l’aide du portail.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5d6118a47e10763373c9376ca08d328cf22ab3c8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Comment sauvegarder et restaurer un serveur dans Azure Database pour MySQL à l’aide du Portail Azure

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Les serveurs Azure Database pour MySQL sont sauvegardés régulièrement pour activer les fonctionnalités de restauration. À l’aide de cette fonctionnalité, vous pouvez restaurer le serveur et toutes ses bases de données à un point dans le temps antérieur, sur un nouveau serveur.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :
- Un [serveur et une base de données Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Définir la configuration de sauvegarde

Vous choisissez entre la configuration de votre serveur pour des sauvegardes redondantes localement ou géographiquement redondantes lors de la création du serveur, dans la fenêtre **Niveau tarifaire**.

> [!NOTE]
> Après la création d’un serveur, son type de redondance (géographique ou locale) ne peut pas être modifié.
>

Lors de la création d’un serveur via le portail Azure, la fenêtre **Niveau tarifaire** vous permet de sélectionner **Localement redondant** ou **Géographiquement redondant** pour les sauvegardes de votre serveur. Cette fenêtre vous permet également de sélectionner la **période de rétention de la sauvegarde**, c’est-à-dire la durée (en nombre de jours) pendant laquelle vous souhaitez conserver les sauvegardes de serveur.

   ![Niveau tarifaire - Choisir la redondance de sauvegarde](./media/howto-restore-server-portal/pricing-tier.png)

Pour plus d’informations sur la définition de ces valeurs lors de la création, consultez [Démarrage rapide du serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

La période de rétention de sauvegarde peut être modifiée sur un serveur en suivant les étapes ci-après :
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez votre serveur Azure Database pour MySQL. Cette action ouvre la page **Vue d’ensemble**.
3. Sélectionnez **Niveau tarifaire** dans le menu, sous **PARAMÈTRES**. Le curseur vous permet de modifier la **période de rétention de sauvegarde** selon vos préférences entre 7 et 35 jours.
Dans la capture d’écran ci-dessous, elle a été augmentée à 34 jours.
![Période de rétention de sauvegarde augmentée](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Cliquez sur **OK** pour confirmer la modification.

La période de rétention de sauvegarde détermine jusqu’à quelle date une restauration à un point dans le temps peut être récupérée, dans la mesure où elle est basée sur les sauvegardes disponibles. La restauration à un point dans le temps est décrite plus loin dans la section suivante. 

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps
Azure Database pour MySQL vous permet de restaurer le serveur à un moment donné et dans une nouvelle copie du serveur. Vous pouvez utiliser ce nouveau serveur pour récupérer vos données ou faire en sorte que vos applications client pointent vers ce nouveau serveur.

Par exemple, si une table a été accidentellement supprimée à midi aujourd'hui, vous pourrez restaurer à l’heure juste avant midi et retrouver la table et les données manquantes à partir de cette nouvelle copie du serveur. La restauration à un point dans le temps est effectuée au niveau du serveur, pas au niveau de la base de données.

Les étapes suivantes restaurent l’exemple de serveur à un point dans le temps :
1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL. 

2. Dans la barre d’outils de la page **Vue d’ensemble** du serveur, sélectionnez **Restaurer**.

   ![Azure Database pour MySQL - Vue d’ensemble - Bouton Restaurer](./media/howto-restore-server-portal/2-server.png)

3. Remplissez le formulaire Restaurer avec les informations requises :

   ![Azure Database pour MySQL - Informations de restauration ](./media/howto-restore-server-portal/3-restore.png)
  - **Point de restauration** : sélectionnez le point dans le temps vers lequel vous souhaitez restaurer.
  - **Serveur cible** : fournissez un nom pour le nouveau serveur.
  - **Emplacement :** vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source.
  - **Niveau tarifaire** : vous ne pouvez pas modifier ces paramètres lorsque vous effectuez une restauration à un point dans le temps. Elle est identique à celle du serveur source. 

4. Cliquez sur **OK** pour restaurer le serveur à un point dans le temps. 

5. Une fois la restauration terminée, recherchez le nouveau serveur créé pour vérifier que les données ont été restaurées correctement.

>[!Note]
>Notez que le serveur créé par la restauration à un point dans le temps a les mêmes nom de connexion administrateur de serveur et mot de passe qui étaient valides pour le serveur existant au point dans le temps choisi. Vous pouvez modifier le mot de passe sur la page **Vue d’ensemble** du nouveau serveur.

## <a name="geo-restore"></a>Restauration géographique
Si vous avez configuré votre serveur pour les sauvegardes redondantes géographiquement, un serveur peut être créé à partir de la sauvegarde de ce serveur existant. Ce nouveau serveur peut être créé dans toutes les régions dans lesquelles Azure Database pour MySQL est disponible.  

1. Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail. Sélectionnez **Bases de données** > **Azure Database pour MySQL**.

   ![L’option « Azure Database pour MySQL »](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Dans la liste déroulante **Sélectionner la source** du formulaire, choisissez **Sauvegarde**. Cette action charge une liste des serveurs pour lesquels les sauvegardes géoredondantes sont activées. Sélectionnez l’une de ces sauvegardes comme source pour votre nouveau serveur.
   ![Sélectionner la source : sauvegarde et liste des sauvegardes géoredondantes](./media/howto-restore-server-portal/2-georestore.png)

3. Renseignez le reste du formulaire avec vos préférences. Vous pouvez sélectionner n’importe quel **Emplacement**. Une fois l’emplacement sélectionné, vous pouvez sélectionner **Niveau tarifaire**. Par défaut, les paramètres du serveur existant à partir duquel vous effectuez la restauration sont affichés. Vous pouvez cliquer sur **OK** sans apporter de modifications pour hériter de ces paramètres. Sinon, vous pouvez modifier la **génération de calcul** (si disponible dans la région choisie), le nombre de **vCores**, la **période de rétention de sauvegarde** et **l’option de redondance de sauvegarde**. La modification du **niveau tarifaire** (De base, Usage général ou À mémoire optimisée) ou de la taille du**stockage** pendant la restauration n’est pas prise en charge.

>[!Note]
>Le serveur créé par la restauration géographique a les mêmes nom de connexion administrateur de serveur et mot de passe qui étaient valides pour le serveur existant au moment où la restauration a été initiée. Le mot de passe peut être modifié sur la page **Vue d’ensemble** du nouveau serveur.


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [sauvegardes](concepts-backup.md) du service.
- En savoir plus sur les options de [continuité d’activité](concepts-business-continuity.md).
