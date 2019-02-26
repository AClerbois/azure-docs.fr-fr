---
title: Configurer un compte de laboratoire avec Azure Lab Services | Microsoft Docs
description: Dans ce tutoriel, vous configurez un compte de laboratoire avec Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 764b5ebb73340d377250d39e20b17ed0daf18bbe
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447682"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Didacticiel : Configurer un compte lab avec Azure Lab Services
Dans Azure Lab Services, un compte de laboratoire sert de compte central, dans lequel sont gérés tous les laboratoires de votre entreprise. Dans votre compte lab, accordez l’autorisation à d’autres personnes pour créer des laboratoires et définissez des stratégies qui s’appliquent à tous les laboratoires du compte de laboratoire. Dans ce tutoriel, découvrez comment créer un compte de laboratoire en tant qu’administrateur de laboratoire. 

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte de laboratoire
> * Ajouter un utilisateur au rôle Créateur de laboratoire
> * Spécifier les images de la Place de marché disponibles pour les propriétaires de laboratoire

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Comptes Lab** dans la section **DEVOPS**. Si vous sélectionnez l’étoile (`*`) en regard de **Comptes Lab**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Comptes Lab** sous **FAVORIS**.

    ![Tous les services -> Comptes Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Dans la page **Comptes Lab**, sélectionnez **Ajouter** dans la barre d’outils. 

    ![Sélectionnez Ajouter dans la page Comptes Lab.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Dans la page **Comptes Lab**, effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 
    5. Dans le champ **Autoriser le créateur du lab à choisir l’emplacement du lab**, indiquez si vous souhaitez que les créateurs de laboratoire soient en mesure de sélectionner un emplacement pour le laboratoire. Par défaut, l’option est désactivée. Lorsqu’elle est désactivée, les créateurs de laboratoire ne peuvent pas spécifier un emplacement pour le laboratoire qu’ils créent. Les laboratoires sont créés dans l’emplacement géographique le plus proche du compte de laboratoire. Lorsqu’elle est activée, un créateur de laboratoire peut sélectionner un emplacement au moment de créer le laboratoire. 
    6. Sélectionnez **Créer**. 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Sélectionnez l’**icône représentant une cloche** dans la barre d’outils (**Notifications**), confirmez la réussite du déploiement, puis sélectionnez **Accéder à la ressource**. 

    Sinon, sélectionnez **Actualiser** dans la page **Comptes Lab**, puis sélectionnez le compte lab que vous avez créé. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe, vous pouvez ignorer cette étape. Pour utiliser un autre compte d’utilisateur pour créer un laboratoire de classe, procédez comme suit : 

Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle **Créateur de laboratoire** :

1. Dans la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+ Ajouter une attribution de rôle** dans la barre d’outils. 

    ![Contrôle d’accès -> bouton Ajouter une attribution de rôle](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Créateur lab** pour **Rôle**, sélectionnez l’utilisateur à ajouter au rôle Créateur lab, puis sélectionnez **Enregistrer**. 

    ![Ajouter un créateur lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Spécifier les images de la Place de marché accessibles aux créateurs lab
En tant que propriétaire d’un compte de laboratoire, vous pouvez spécifier les images de la place de marché que les créateurs de laboratoire peuvent utiliser pour créer des laboratoires dans le compte de laboratoire. 

1. Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Enabled only (Activées uniquement)**/**Disabled only (Désactivées uniquement)** dans la liste déroulante en haut. 
    
    ![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
        
    - Crée une seule machine virtuelle.
    - Utilise Azure Resource Manager pour approvisionner des machines virtuelles
    - Ne nécessite pas l’achat d’un plan de licences supplémentaire
2. Pour **désactiver** une image de la Place de marché qui a été activée, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Disable image (Désactiver l’image)**. 

        ![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 

        ![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De même, pour **activer** une image de la Place de marché, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Enable image (Désactiver l’image)**. 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un compte de laboratoire. Pour en savoir plus sur la création d’un laboratoire pour une classe en tant que profession, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Set up a classroom lab](tutorial-setup-classroom-lab.md) (Configurer un laboratoire de classe)

