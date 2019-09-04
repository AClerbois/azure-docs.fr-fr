---
title: Créer et gérer des réplicas en lecture dans Azure Database pour MySQL
description: Cet article décrit comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide du portail.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 74f27f70c4a0752975a53b3889681d3910b1dd05
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906434"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Guide pratique pour créer et gérer des réplicas en lecture dans Azure Database pour MySQL avec le portail Azure

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans le service Azure Database pour MySQL à l’aide du Portail Microsoft Azure.

> [!IMPORTANT]
> Vous pouvez créer un réplica en lecture dans la même région que votre serveur principal, ou dans n’importe quelle autre région Azure de votre choix. La réplication entre plusieurs régions est actuellement disponible en préversion publique.

## <a name="prerequisites"></a>Prérequis

- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) qui sera utilisé comme serveur maître.

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur maître se trouve dans l’un de ces niveaux tarifaires.

## <a name="create-a-read-replica"></a>Créer un réplica en lecture

Un serveur réplica en lecture peut être créé en effectuant les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez le serveur Azure Database pour MySQL que vous souhaitez utiliser comme serveur maître. Cette action ouvre la page **Vue d’ensemble**.

3. Sélectionnez **Réplication** dans le menu, sous **PARAMÈTRES**.

4. Sélectionnez **Ajouter un réplica**.

   ![Azure Database pour MySQL - Réplication](./media/howto-read-replica-portal/add-replica.png)

5. Entrez un nom pour le serveur de réplica.

    ![Azure Database pour MySQL - Nom du réplica](./media/howto-read-replica-portal/replica-name.png)

6. Sélectionnez l’emplacement du serveur de réplica. L’emplacement par défaut est le même que celui du serveur maître.

    ![Azure Database pour MySQL - Emplacement du réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

7. Sélectionnez **OK** pour confirmer la création du réplica.

> [!NOTE]
> Les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître. La configuration du serveur réplica peut être modifiée après la création de ce dernier. Il est recommandé que la configuration du serveur réplica soit maintenue à des valeurs égales ou supérieures à celles du serveur maître pour garantir que le serveur réplica est à la hauteur du serveur maître.

Une fois le serveur réplica créé, il est affiché dans le panneau **Réplication**.

   ![Azure Database pour MySQL - Répertorier les réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica

> [!IMPORTANT]
> L’arrêt de la réplication vers un serveur est irréversible. Une fois la réplication entre un serveur maître et un serveur réplica arrêtée, elle ne peut pas être annulée. Le serveur réplica devient un serveur autonome et prend désormais en charge la lecture et les écritures. Ce serveur ne peut pas être à nouveau transformé en réplica.

Pour arrêter la réplication entre un serveur maître et un serveur réplica à partir du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL maître. 

2. Sélectionnez **Réplication** dans le menu, sous **PARAMÈTRES**.

3. Sélectionnez le serveur réplica pour lequel vous souhaitez arrêter la réplication.

   ![Azure Database pour MySQL - Sélection du serveur pour lequel arrêter la réplication](./media/howto-read-replica-portal/stop-replication-select.png)

4. Sélectionnez **Arrêter la réplication**.

   ![Azure Database pour MySQL - Arrêter la réplication](./media/howto-read-replica-portal/stop-replication.png)

5. Validez que vous voulez arrêter la réplication en cliquant sur **OK**.

   ![Azure Database pour MySQL - Confirmation de l’arrêt de la réplication](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

Pour supprimer un serveur réplica en lecture du portail Azure, utilisez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL maître.

2. Sélectionnez **Réplication** dans le menu, sous **PARAMÈTRES**.

3. Sélectionnez le serveur réplica que vous voulez supprimer.

   ![Azure Database pour MySQL - Sélection du serveur réplica à supprimer](./media/howto-read-replica-portal/delete-replica-select.png)

4. Sélectionnez **Supprimer le réplica**.

   ![Azure Database pour MySQL : Supprimer le réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Tapez le nom du réplica, puis cliquez sur **Supprimer** pour confirmer la suppression de ce dernier.  

   ![Azure Database pour MySQL - Confirmation de la suppression du réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Supprimer un serveur maître

> [!IMPORTANT]
> La suppression d’un serveur maître arrête la réplication vers tous les serveurs réplicas et supprime le serveur maître lui-même. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur maître du portail Azure, utilisez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL maître.

2. Dans la **Vue d’ensemble**, sélectionnez **Supprimer**.

   ![Azure Database pour MySQL - Supprimer le serveur maître](./media/howto-read-replica-portal/delete-master-overview.png)

3. Tapez le nom du serveur maître, puis cliquez sur **Supprimer** pour confirmer la suppression de ce dernier.  

   ![Azure Database pour MySQL - Supprimer le serveur maître](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Superviser la réplication

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez le serveur réplica Azure Database pour MySQL à superviser.

2. Sous la section **Surveillance** de la barre latérale, sélectionnez **Métriques** :

3. Sélectionnez **Décalage de la réplication en secondes** dans la liste déroulante des métriques disponibles.

   ![Sélectionner le décalage de la réplication](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Sélectionnez l’intervalle de temps que vous voulez afficher. L’image ci-dessous sélectionne un intervalle de temps de 30 minutes.

   ![Sélectionner un intervalle de temps](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Affichez le décalage de la réplication pour l’intervalle de temps sélectionné. L’image ci-dessous affiche les 30 dernières minutes.

   ![Sélectionner un intervalle de temps](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir plus en détail les [réplicas en lecture](concepts-read-replicas.md)