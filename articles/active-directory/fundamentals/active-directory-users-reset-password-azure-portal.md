---
title: Réinitialiser le mot de passe d’un utilisateur - Azure Active Directory | Microsoft Docs
description: Instructions de réinitialisation du mot de passe d’un utilisateur à l’aide d’Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 198b8d3a5d0ac8ea3458fd476df0894e509d3b1b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452120"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Réinitialiser le mot de passe d’un utilisateur à l’aide d’Azure Active Directory
En tant qu’administrateur, vous pouvez réinitialiser le mot de passe d’un utilisateur en cas d’oubli, si l’utilisateur ne peut plus accéder à un appareil verrouillé, ou bien s’il n’a jamais reçu son mot de passe.

>[!Note]
>Vous ne serez pas en mesure de réinitialiser le mot de passe d’un utilisateur si votre locataire Azure AD n’est pas son répertoire de base. Cela signifie que si votre utilisateur se connecte à votre organisation à l’aide d’un compte venant d’une autre organisation, un compte Microsoft ou un compte Google, vous ne pourrez réinitialiser son mot de passe.<br><br>Si votre utilisateur a une source d’autorité comme Windows Server Active Directory, vous ne serez en mesure de réinitialiser le mot de passe que si vous avez activé la réécriture du mot de passe.<br><br>Si votre utilisateur a une source d’autorité comme Azure AD externe, il se peut que vous ne puissiez pas réinitialiser le mot de passe. Seuls l’utilisateur ou un administrateur dans Azure AD externe peuvent réinitialiser le mot de passe.

>[!Note]
>Si vous n’êtes pas administrateur et si vous recherchez des instructions de réinitialisation pour votre propre mot de passe professionnel ou scolaire, consultez [Réinitialiser votre mot de passe professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Pour réinitialiser un mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général, administrateur d’utilisateurs ou administrateur de mots de passe. Pour obtenir plus d’informations sur les rôles disponibles, consultez l’article [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles).

2. Sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, recherchez et sélectionnez l’utilisateur qui a besoin de la réinitialisation, puis sélectionnez **Réinitialiser le mot de passe**.

    La page **Alain Charon - profil** s’affiche avec l’option **Réinitialiser le mot de passe**.

    ![Page du profil de l’utilisateur, avec l’option Réinitialiser le mot de passe mis en surbrillance](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Sur la page **Réinitialiser le mot de passe**, sélectionnez **Réinitialiser le mot de passe**.

    Un mot de passe temporaire est généré automatiquement pour l’utilisateur.

4. Copiez le mot de passe et donnez-le à l’utilisateur. L’utilisateur devra le modifier lors de sa prochaine connexion.

    >[!Note]
    >Le mot de passe temporaire n’expire jamais. La prochaine fois que l’utilisateur se connectera, le mot de passe fonctionnera toujours, quel que soit le temps écoulé dans depuis sa création.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez réinitialisé le mot de passe de votre utilisateur, vous pouvez exécuter les procédures de base suivantes :

- [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md)

- [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md)

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

Vous pouvez également suivre des scénarios utilisateur plus complexes, comme l’affectation de délégués, l’utilisation de stratégies et le partage de comptes d’utilisateur. Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).
