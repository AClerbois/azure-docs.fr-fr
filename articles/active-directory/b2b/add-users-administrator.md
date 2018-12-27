---
title: Ajouter des utilisateurs B2B Collaboration dans le Portail Azure - Azure Active Directory | Microsoft Docs
description: Montre comment un administrateur peut ajouter des utilisateurs invités à son répertoire à partir d’une organisation partenaire en utilisant Azure Active Directory (Azure AD) B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: 3da427974c4a270a7a743bb81d2376cf61b77390
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436906"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure

En tant qu’administrateur général ou en tant qu’utilisateur affecté à l’un des rôles d’annuaire administrateur limité, vous pouvez utiliser le Portail Azure pour inviter des utilisateurs B2B Collaboration. Libre à vous de convier des utilisateurs invités à rejoindre le répertoire, un groupe ou une application. Une fois l’utilisateur invité par le biais de l’une de ces méthodes, son compte est ajouté à Azure Active Directory (Azure AD) et son type devient *invité*. L’utilisateur invité doit ensuite échanger son invitation pour accéder aux ressources.

Après avoir ajouté un utilisateur invité dans l’annuaire, vous pouvez envoyer à l’utilisateur invité un lien direct vers une application partagée, ou l’utilisateur invité peut cliquer sur l’URL d’échange dans l’e-mail d’invitation. Pour plus d’informations sur le processus d’utilisation, consultez [Utilisation d’une invitation B2B Collaboration](redemption-experience.md).

> [!IMPORTANT]
> Vous devez suivre les étapes de [Guide : ajout des informations de confidentialité de votre organisation dans Azure Active Directory](https://aka.ms/adprivacystatement) pour ajouter l’URL de déclaration de confidentialité de votre organisation. Dans le cadre du processus d’échange lors de la première invitation, un utilisateur invité doit accepter les conditions de confidentialité pour continuer. 

## <a name="add-guest-users-to-the-directory"></a>Ajouter des utilisateurs invités au répertoire

Pour ajouter des utilisateurs B2B Collaboration au répertoire, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
4. Sélectionnez **Nouvel utilisateur invité**.

   ![Indique où le nouvel utilisateur invité se trouve dans l’interface utilisateur](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > L’option **Nouvel utilisateur invité** est également disponible sur la page **Relations organisationnelles**. Dans **Azure Active Directory**, sous **Gérer**, sélectionnez **Relations organisationnelles**.

5. Sous **Nom d’utilisateur**, entrez l’adresse e-mail de l’utilisateur externe. Vous pouvez éventuellement inclure un message d’accueil. Par exemple : 

   ![Indique où le nouvel utilisateur invité se trouve dans l’interface utilisateur](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Certains fournisseurs de messagerie permettent aux utilisateurs d’ajouter un signe plus (+) et du texte à leurs adresses e-mail pour faciliter notamment le filtrage de la boîte de réception. Toutefois, Azure AD ne prend pas en charge les signes plus dans les adresses e-mail pour l’instant. Pour éviter les problèmes de livraison, omettez le signe plus (+) et les caractères après celui-ci jusqu’au symbole @.

6. Sélectionnez **Inviter** pour envoyer automatiquement l’invitation à l’utilisateur invité. 
 
Après avoir envoyé l’invitation, le compte d’utilisateur est automatiquement ajouté au répertoire en tant qu’invité.


![Indique un utilisateur B2B dont le type est invité](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Ajouter des utilisateurs invités à un groupe
En tant qu’administrateur Azure AD, vous pouvez ajouter manuellement des utilisateurs B2B Collaboration à un groupe. Pour cela, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Groupes**.
4. Sélectionnez un groupe (ou cliquez sur **Nouveau groupe** pour en créer un nouveau). Il est judicieux d’inclure dans la description que le groupe contient des utilisateurs invités B2B.
5. Sélectionnez **Membres**. 
6. Effectuez l’une des actions suivantes :
   - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, puis cliquez sur **Sélectionner** pour l’ajouter au groupe.
   - Si l’utilisateur invité ne se trouve pas déjà dans l’annuaire, invitez-le à rejoindre le groupe en tapant son adresse e-mail dans la zone de recherche et un message personnel facultatif, puis en cliquant sur **Sélectionner**. L’invitation est automatiquement envoyée à l’utilisateur invité.
     
     ![Bouton Ajouter un invité pour ajouter des membres invités](./media/add-users-administrator/GroupInvite.png)
   
Vous pouvez également utiliser des groupes dynamiques avec Azure AD B2B Collaboration. Pour plus d’informations sur les groupes dynamiques, consultez [Groupes dynamiques et Azure Active Directory B2B Collaboration](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Ajouter des utilisateurs invités à une application

En tant qu’administrateur Azure AD, vous pouvez ajouter des utilisateurs B2B Collaboration à une application. Pour cela, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
4. Sélectionnez l’application à laquelle vous souhaitez ajouter des utilisateurs invités.
5. Dans le tableau de bord de l’application, sélectionnez **Nombre total d’utilisateurs** pour ouvrir le volet **Utilisateurs et groupes**.

    ![Bouton Nombre total d’utilisateurs pour ouvrir Utilisateurs et groupes](./media/add-users-administrator/AppUsersAndGroups.png)

6. Sélectionnez **Ajouter un utilisateur**.
7. Sous **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
8. Effectuez l’une des actions suivantes :
   - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, cliquez sur **Sélectionner**, puis cliquez sur **Attribuer** pour l’ajouter à l’application.
   - Si l’utilisateur invité n’existe pas dans l’annuaire, sous **Sélectionner un membre ou inviter un utilisateur externe**, tapez l’adresse e-mail de l’utilisateur. Dans la boîte de message, tapez un message personnel facultatif. Sous la zone de message, cliquez sur **Inviter**.
           
       ![Bouton Ajouter un invité pour ajouter des membres invités](./media/add-users-administrator/AppInviteUsers.png)
   
      Cliquez sur **Sélectionner**, puis cliquez sur **Attribuer** pour l’ajouter à l’application. Une invitation est automatiquement envoyée à l’utilisateur invité.

9. L’utilisateur invité s’affiche dans la liste **Utilisateurs et groupes** de l’application, avec le rôle **Accès par défaut**. Si vous souhaitez modifier le rôle, effectuez les étapes suivantes :
   - Sélectionnez l’utilisateur invité, puis cliquez sur **Modifier**. 
   - Sous **Modifier l’affectation**, cliquez sur **Sélectionner un rôle**, puis sélectionnez le rôle que vous souhaitez affecter à l’utilisateur sélectionné.
   - Cliquez sur **Sélectionner**.
   - Cliquez sur **Affecter**.
 
## <a name="resend-invitations-to-guest-users"></a>Renvoyer des invitations aux utilisateurs invités

Si un utilisateur invité n’a pas encore répondu à son invitation, vous pouvez lui renvoyer l’e-mail d’invitation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
5. Sélectionnez le compte d’utilisateur.
6. Sous **Gérer**, sélectionnez **Profil**.
7. Si l’utilisateur n’a pas encore accepté l’invitation, une option **Renvoyer l’invitation** est disponible. Cliquez sur ce bouton pour la lui renvoyer.

   ![Option Renvoyer l’invitation dans le profil utilisateur](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Si vous renvoyez une invitation qui au départ, orientait l’utilisateur vers une application spécifique, sachez que le lien contenu dans la nouvelle invitation va au contraire l’orienter vers le volet d’accès de niveau supérieur.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment des administrateurs non-Azure AD peuvent ajouter des utilisateurs invités B2B, consultez [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-information-worker.md)
- Pour plus d’informations sur l’e-mail d’invitation, consultez les [Éléments de l’e-mail d’invitation de B2B Collaboration](invitation-email-elements.md).

