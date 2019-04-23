---
title: Gestion des utilisateurs sur Azure Data Box Edge | Microsoft Docs
description: Décrit comment utiliser le Portail Azure pour gérer les utilisateurs de votre Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 68f8ad903f967812c4a416c732b35fa1712404cd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998564"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Utiliser le Portail Azure pour gérer les utilisateurs de votre Azure Data Box Edge

Cet article explique comment gérer les utilisateurs sur votre ressource Azure Data Box Edge. Vous pouvez gérer le service Azure Data Box Edge via le Portail Azure ou via l’interface utilisateur web locale. Utilisez le Portail Azure pour ajouter, modifier ou supprimer des utilisateurs.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Ajouter un utilisateur
> * Modification d’une planification
> * Supprimer un utilisateur

## <a name="about-users"></a>À propos des utilisateurs

Les utilisateurs peuvent être en lecture seule ou disposer de toutes les autorisations. Comme indiqué par les noms, les utilisateurs en lecture seule ne peuvent consulter que les données partagées. Les utilisateurs disposant de toutes les autorisations peuvent consulter les données de partage, écrire dessus, et les modifier ou les supprimer.

 - **Utilisateur disposant de toutes les autorisations** - Utilisateur local disposant de tous les droits d’accès.
 - **Utilisateur en lecture seule** - Utilisateur local disposant d’un accès en lecture seule. Ces utilisateurs sont associés à des partages permettant des opérations en lecture seule.

Les autorisations des utilisateurs sont d’abord définies à la création de l’utilisateur lors de la création du partage. Une fois que les autorisations associées à un utilisateur sont définies, elles peuvent être modifiées à l’aide de l’explorateur de fichiers. 


## <a name="add-a-user"></a>Ajouter un utilisateur

Pour ajouter une planification, procédez comme suit dans le Portail Azure.

1. Sur le portail Azure, accédez à votre ressource Data Box Edge, puis à **Vue d’ensemble > Partages**. Sélectionnez **+ Ajouter un utilisateur** dans la barre de commandes.

    ![Sélectionner Ajouter un utilisateur](media/data-box-edge-manage-users/add-user-1.png)

2. Indiquez le nom d’utilisateur et le mot de passe de l’utilisateur que vous souhaitez ajouter. Confirmez le mot de passe, puis sélectionnez **Ajouter**.

    ![Spécifier un nom d’utilisateur et un mot de passe](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Les utilisateurs suivants sont réservés par le système et ne devraient pas être utilisés : Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Une notification s’affiche lorsque la création de l’utilisateur commence et se termine. Une fois l’utilisateur est créé, dans la barre de commandes, sélectionnez **Actualiser** pour afficher la liste actualisée des utilisateurs.


## <a name="modify-user"></a>Modification d’une planification

Vous pouvez modifier le mot de passe associé à un utilisateur une fois l’utilisateur créé. Sélectionnez dans la liste des utilisateurs. Entrez et confirmez le nouveau mot de passe. Enregistrez les modifications.
 
![Modification d’une planification](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur, effectuez les étapes suivantes dans le Portail Azure.


1. Sur le portail Azure, accédez à votre ressource Data Box Edge, puis à **Vue d’ensemble > Partages**.

    ![Sélectionner un utilisateur à supprimer](media/data-box-edge-manage-users/delete-user-1.png)

2. Sélectionnez un utilisateur dans la liste des utilisateurs, puis choisissez **Supprimer**.  

   ![Sélectionner Supprimer](media/data-box-edge-manage-users/delete-user-2.png)

3. Quand vous y êtes invité, confirmez la suppression. 

   ![Confirmation de suppression](media/data-box-edge-manage-users/delete-user-3.png)

La liste des utilisateurs est actualisée afin de tenir compte de la suppression de l’utilisateur.

![Liste des utilisateurs mise à jour](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](data-box-edge-manage-bandwidth-schedules.md).
