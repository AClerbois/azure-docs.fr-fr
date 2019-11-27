---
title: Inscrire des appareils personnels sur le réseau d’une organisation - Azure AD
description: Découvrez comment inscrire votre appareil personnel sur le réseau de votre organisation afin de pouvoir accéder à des ressources protégées de votre organisation.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 879fa55df422e6039c6830e25e43637fc31b8037
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028485"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Inscrire votre appareil personnel sur le réseau de votre organisation
Inscrivez votre appareil personnel (généralement un téléphone ou une tablette) sur le réseau de votre organisation. Une fois votre appareil inscrit, il peut accéder aux ressources restreintes de votre organisation.

>[!Note]
>Cet article utilise un appareil Windows à des fins de démonstration, mais vous pouvez également inscrire des appareils exécutant iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>Ce qui se passe quand vous inscrivez votre appareil
Quand vous inscrivez votre appareil sur le réseau de votre organisation, les actions suivantes se produisent :

- Windows inscrit votre appareil sur le réseau de votre organisation.

- Selon les choix de votre organisation, vous devrez peut-être configurer une vérification en deux étapes à travers [l’authentification multifacteur](multi-factor-authentication-end-user-first-time.md) ou des [informations de sécurité](user-help-security-info-overview.md).

- Selon les choix de votre organisation, vous pouvez être automatiquement inscrit dans une solution de gestion des appareils mobiles, comme Microsoft Intune. Pour plus d’informations sur l’inscription dans Microsoft Intune, consultez [Inscrire votre appareil sur Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Vous suivrez le processus de connexion en utilisant le nom d’utilisateur et le mot de passe de votre compte professionnel ou scolaire.

## <a name="to-register-your-windows-device"></a>Pour inscrire votre appareil Windows

Suivez ces étapes pour inscrire votre appareil personnel sur votre réseau.

1. Ouvrez **Paramètres**, puis sélectionnez **Comptes**.

    ![Comptes dans l’écran Paramètres](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Sélectionnez **Accès Professionnel ou Scolaire**, puis sélectionnez **Connexion** à partir de l’écran **Accès Professionnel ou Scolaire**.

    ![Écran Accès Professionnel ou Scolaire avec l’option Connexion mise en évidence](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Dans l’écran **Ajouter un compte professionnel ou scolaire**, tapez votre adresse e-mail pour votre compte professionnel ou scolaire, puis sélectionnez **Suivant**. Par exemple : alain@contoso.com.

4. Connectez-vous à votre compte professionnel ou scolaire, puis sélectionnez **Se connecter**.

5. Effectuez le reste du processus d’inscription, y compris l’approbation de votre demande de vérification d’identité (si vous utilisez la vérification en deux étapes) et la configuration de Windows Hello (si nécessaire).

## <a name="to-verify-that-youre-registered"></a>Pour vérifier que vous êtes inscrit
Vous pouvez vérifier que vous êtes inscrit en examinant vos paramètres.

1. Ouvrez **Paramètres**, puis sélectionnez **Comptes**.

    ![Comptes dans l’écran Paramètres](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Sélectionnez **Accès Professionnel ou Scolaire** et vérifiez que vous voyez votre compte professionnel ou scolaire.

    ![Écran Accès Professionnel ou Scolaire avec compte contoso connecté](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Étapes suivantes
Après avoir inscrit votre appareil personnel sur le réseau de votre organisation, vous devriez pouvoir accéder à la plupart de vos ressources.

- Si votre organisation vous demande de joindre votre appareil professionnel, consultez [Joindre votre appareil professionnel au réseau de votre organisation](user-help-join-device-on-network.md).



