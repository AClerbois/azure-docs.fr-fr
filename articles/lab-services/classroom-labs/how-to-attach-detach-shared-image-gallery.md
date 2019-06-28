---
title: Joindre ou détacher une galerie d’images partagées dans Azure Lab Services | Microsoft Docs
description: Découvrez comment attacher une galerie d’images partagées à un laboratoire dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413887"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Joindre ou détacher une galerie d’images partagées dans Azure Lab Services
L’administrateur du laboratoire ou des enseignants peuvent enregistrer un modèle d’image de machine virtuelle dans une [galerie d’images partagées](../../virtual-machines/windows/shared-image-galleries.md), afin qu’il puisse être réutilisé par d’autres utilisateurs. Dans un premier temps, l’administrateur de laboratoire joint associe une galerie d’images partagées existante au compte de laboratoire. Une fois la galerie d’images partagées associée, les laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagées. D’autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagées pour créer un modèle pour leurs classes. 

Cet article vous montre comment joindre ou détacher une galerie d’images partagées pour un compte de laboratoire. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Effectuer la configuration au moment de la création de comptes de laboratoire
Lorsque vous créez un compte de laboratoire, vous pouvez joindre une galerie d’images partagées au compte de laboratoire. Vous pouvez sélectionner une galerie d’images partagées existante dans la liste déroulante ou en créer une. Pour créer et joindre une galerie d’images partagées pour le compte de laboratoire, sélectionnez **Créer**, entrez un nom pour la galerie, puis saisissez **OK**. 

![Configurer la galerie d’images partagées lors de la création du compte de laboratoire](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Effectuer la configuration une fois le compte de laboratoire créé
Une fois que le compte de laboratoire est créé, vous pouvez effectuer les tâches suivantes :

- Créer et joindre une galerie d’images partagées
- Joindre une galerie d’images partagées au compte de laboratoire
- Détacher une galerie d’images partagées du compte de laboratoire

## <a name="create-and-attach-a-shared-image-gallery"></a>Créer et joindre une galerie d’images partagées
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Lab Services** dans la section **DEVOPS**. Si vous sélectionnez l’étoile (`*`) en regard de **Lab Services**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Lab Services** sous **FAVORIS**.

    ![Tous les services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Sélectionnez votre compte de laboratoire pour afficher la page **Compte Lab**. 
4. Sélectionnez l’option **Galerie d’images partagé** dans le menu de gauche, puis choisissez **+Créer** sur la barre d’outils.  

    ![Bouton Créer une galerie d’images partagées](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Dans la fenêtre **Créer une galerie image partagées**, saisissez un **nom** pour la galerie, puis saisissez **OK**. 

    ![Créer une galerie d’images partagées](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crée la galerie d’images partagées et la joint au compte de laboratoire. Tous les laboratoires créés avec ce compte de laboratoire ont accès à la galerie d’images partagées jointe. 

    ![Galerie d’images jointe](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Dans le volet inférieur, vous pouvez voir les images de la galerie d’images partagées. Dans cette nouvelle galerie, il n’y a aucune image. Lorsque vous chargez des images dans la galerie, elles apparaissent sur cette page.     

    Toutes les images de la galerie d’images partagées jointe sont activées par défaut. Vous pouvez activer ou désactiver les images sélectionnées en les sélectionnant dans la liste et en utilisant le bouton **Activer les images sélectionnées** ou **Désactiver les images sélectionnées**.

## <a name="attach-an-existing-shared-image-gallery"></a>Joindre une galerie d’images partagées existante
La procédure suivante vous montre comment joindre une galerie d’images partagées existante à un compte de laboratoire. 

1. Sur la page **Compte de laboratoire**, sélectionnez **Galerie d’images partagées** dans le menu de gauche, et choisissez **Joindre** dans la barre d’outils. 

    ![Galerie d’images partagées - Bouton Ajouter](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sur la page **Attacher une galerie d’images partagées existante**, sélectionnez votre galerie d’images partagées et cliquez sur **OK**.

    ![Sélectionner une galerie existante](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Vous voyez l’écran suivant : 

    ![Ma galerie dans la liste](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Dans cet exemple, il n’y pas encore d’images dans la galerie d’images partagées.

    Une identité Azure Lab Services est ajoutée en tant que contributeur à la galerie d’images partagées qui est jointe au laboratoire. Cela permet aux enseignants ou à l’administrateur informatique d’enregistrer les images de machines virtuelles dans la galerie d’images partagées. Tous les laboratoires créés avec ce compte de laboratoire ont accès à la galerie d’images partagées jointe. 

    Toutes les images de la galerie d’images partagées jointe sont activées par défaut. Vous pouvez activer ou désactiver les images sélectionnées en les choisissant dans la liste et en utilisant le bouton **Activer les images sélectionnées** ou **Désactiver les images sélectionnées**. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagées
Une fois qu’une galerie d’images partagées est associée, un administrateur de laboratoire ou un enseignant peut enregistrer ou charger une image dans la galerie d’images partagées, afin qu’elle puisse être réutilisée par d’autres enseignants. Pour obtenir des instructions sur le chargement d’une image dans la galerie d’images partagées, consultez la [présentation de la galerie d’images partagées](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Actuellement, l’interface utilisateur des laboratoires de salle de classe ne prend pas en charge l’enregistrement d’une image de laboratoire dans la galerie d’images partagées. 

## <a name="detach-a-shared-image-gallery"></a>Détacher une galerie d’images partagées
Une seule galerie d’images partagées peut être jointe à un laboratoire. Si vous souhaitez en joindre une autre, commencez par détacher la galerie existante. Pour détacher une galerie d’images partagées de votre laboratoire, sélectionnez l’option **Détacher** de la barre d’outils, puis confirmez l’opération de détachement. 

![Détacher une galerie d’images partagées du compte de laboratoire](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment enregistrer une image de laboratoire dans la galerie d’images partagées ou utiliser une image de cette galerie afin de créer une machine virtuelle, voir [How to use shared image gallery](how-to-use-shared-image-gallery.md) (Comment utiliser une galerie d’images partagées).

Pour plus d’informations sur les galeries d’images partagées en général, voir [Shared Image Gallery overview](../../virtual-machines/windows/shared-image-galleries.md) (Vue d’ensemble de la galerie d’images partagées).
