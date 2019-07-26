---
title: Ajout de votre compte professionnel ou scolaire à l’application Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Décrit comment ajouter vos comptes professionnels ou scolaires à l’application Microsoft Authenticator pour la vérification en deux étapes.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: a73500d17a0dd5d55e60700f7c0b6dbe92a3f96b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382529"
---
# <a name="add-your-work-or-school-account"></a>Ajout de votre compte professionnel ou scolaire

Si votre organisation utilise la vérification en deux étapes, vous pouvez configurer votre compte professionnel ou scolaire pour utiliser l’application Microsoft Authenticator comme l’une des méthodes de vérification.

>[!Important]
>Avant de pouvoir ajouter votre compte, vous devez télécharger et installer l’application Microsoft Authenticator. Si ce n’est pas déjà fait, suivez la procédure décrite dans l’article [Télécharger et installer l’application](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Ajout de votre compte professionnel ou scolaire

1. Sur votre ordinateur, accédez à la page [Vérification de sécurité supplémentaire](https://aka.ms/mfasetup).

    >[!Note]
    >Si la page **Vérification de sécurité supplémentaire** n’est pas affichée, il se peut que votre administrateur ait activé l’expérience des informations de sécurité (préversion). Si tel est le cas, vous devez suivre les instructions de la section [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md). Si ce n’est pas le cas, vous devez contacter le support technique de votre organisation pour obtenir de l’aide. Pour en savoir plus sur les informations de sécurité, consultez [Vue d’ensemble des informations de sécurité (préversion)](user-help-security-info-overview.md).

2. Cochez la case **Application d’authentification**, puis sélectionnez **Configurer**.

    La page **Configurer l’application mobile** s’affiche.

    ![Écran qui fournit le code QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Ouvrez l’application Microsoft Authenticator, sélectionnez **Ajouter un compte** grâce à l’icône **Personnaliser et contrôler** dans l’angle supérieur droit, puis sélectionnez **Compte professionnel ou scolaire**.

4. Utilisez la caméra de votre appareil pour analyser le code QR à partir de l’écran **Configurer l’application mobile** de votre ordinateur, puis sélectionnez **Terminé**.

    >[!Note]
    >Si la caméra ne peut pas capturer le code QR, vous pouvez ajouter manuellement les informations de votre compte à l’application Microsoft Authenticator pour la vérification en deux étapes. Pour en savoir plus, consultez l’article [Ajouter manuellement un compte](user-help-auth-app-add-account-manual.md).

5. Passez en revue l’écran **Comptes** de l’application sur votre appareil pour vous assurer que votre compte est correct et qu’un code de vérification à six chiffres lui est associé. Pour renforcer la sécurité, le code de vérification change toutes les 30 secondes, ce qui empêche quiconque d’utiliser plusieurs fois un même code.

    ![Écran de comptes](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Étapes suivantes

- Après avoir ajouté vos comptes à l’application, vous pouvez vous connecter à l’aide de l’application Authenticator sur votre appareil. Pour plus d’informations, consultez l’article [Se connecter à l’aide de l’application Microsoft Authenticator](user-help-auth-app-sign-in.md).

- Pour les appareils exécutant iOS, vous pouvez également sauvegarder les informations d’identification de votre compte et les paramètres de l’application associée, comme l’ordre de vos comptes, dans le cloud. Pour plus d’informations, consultez l’article [Sauvegarder et récupérer des informations d’authentification de compte avec l’application Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
