---
title: Gestion d’une base de données Azure Database pour PostgreSQL - Portail Azure
description: Découvrez comment gérer un serveur Azure Database pour PostgreSQL à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 02a50a94b0b07d1755abe78c567df7ff5c7eda92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907439"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gérer un serveur Azure Database pour PostgreSQL à l’aide du portail Azure

Cet article vous explique comment gérer vos serveurs Azure Database pour PostgreSQL. Les tâches de gestion incluent notamment la mise à l’échelle du calcul et du stockage, la réinitialisation de mot de passe et l’affichage des informations relatives au serveur.

## <a name="sign-in"></a>Se connecter

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Créer un serveur

Consultez le [guide de démarrage rapide](quickstart-create-server-database-portal.md) pour savoir comment créer et prendre en main un serveur Azure Database pour PostgreSQL.

## <a name="scale-compute-and-storage"></a>Mettre à l’échelle le calcul et le stockage

Une fois le serveur créé, vous pouvez mettre à l’échelle entre les niveaux Usage général et Mémoire optimisée en fonction de vos besoins. Vous pouvez également mettre à l’échelle le calcul et la mémoire en augmentant ou en diminuant le nombre de vCores. Le stockage peut être monté en puissance (mais pas descendu en puissance).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Mettre à l’échelle entre les niveaux Usage général et Mémoire optimisée

Vous pouvez passer du niveau Usage général au niveau Mémoire optimisée et inversement. Le passage au niveau De base ou son remplacement par un autre niveau de service n’est pas pris en charge après la création du serveur.

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Sélectionnez **Usage général** ou **Mémoire optimisée**, selon le niveau souhaité.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Capture d’écran du portail Azure pour choisir un niveau De base, Usage général ou À mémoire optimisée dans Azure Database pour PostgreSQL":::

   > [!NOTE]
   > Le changement de niveau entraîne un redémarrage du serveur.

3. Sélectionnez **OK** pour enregistrer les modifications.

### <a name="scale-vcores-up-or-down"></a>Scale-up ou scale-down des vCores

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Modifiez le paramètre **vCore** en déplaçant le curseur vers la valeur souhaitée.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Capture d’écran du portail Azure pour choisir l’option vCore dans Azure Database pour PostgreSQL":::

   > [!NOTE]
   > La mise à l’échelle des vCores entraîne un redémarrage du serveur.

3. Sélectionnez **OK** pour enregistrer les modifications.

### <a name="scale-storage-up"></a>Scale-up du stockage

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Modifiez le paramètre **Stockage** en déplaçant le curseur vers la valeur souhaitée.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Capture d’écran du portail Azure pour choisir le scale-in de stockage dans Azure Database pour PostgreSQL":::

   > [!NOTE]
   > Le stockage ne peut pas faire l’objet d’un scale-down.

3. Sélectionnez **OK** pour enregistrer les modifications.

## <a name="update-admin-password"></a>Mettre à jour le mot de passe administrateur

Vous pouvez modifier le mot de passe du rôle d’administrateur à l’aide du Portail Azure.

1. Dans le Portail Azure, sélectionnez votre serveur. Dans la fenêtre **Vue d’ensemble**, sélectionnez **Réinitialiser le mot de passe**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Capture d’écran du portail Azure pour réinitialiser le mot de passe dans Azure Database pour PostgreSQL":::

2. Entrez un nouveau mot de passe et confirmez-le. La zone de texte vous indique les exigences en matière de complexité du mot de passe.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Capture d’écran du portail Azure pour réinitialiser votre mot de passe et l’enregistrer dans Azure Database pour PostgreSQL":::

3. Sélectionnez **OK** pour enregistrer le nouveau mot de passe.

## <a name="delete-a-server"></a>Supprimer un serveur

Vous pouvez supprimer votre serveur si vous n’en avez plus besoin. 

1. Dans le Portail Azure, sélectionnez votre serveur. Dans la fenêtre **Vue d’ensemble**, sélectionnez **Supprimer**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Capture d’écran du portail Azure pour supprimer le serveur dans Azure Database pour PostgreSQL":::

2. Entrez le nom du serveur dans la zone d’entrée pour confirmer qu’il s’agit du serveur à supprimer.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Capture d’écran du portail Azure pour confirmer la suppression du serveur dans Azure Database pour PostgreSQL":::

   > [!NOTE]
   > La suppression d’un serveur est irréversible.

3. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [sauvegardes et les restaurations de serveur](howto-restore-server-portal.md)
- Découvrez les [options d’optimisation et de surveillance dans Azure Database pour PostgreSQL](concepts-monitoring.md)
