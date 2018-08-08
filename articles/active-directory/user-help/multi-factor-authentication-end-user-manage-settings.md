---
title: Gérer les paramètres de la vérification en deux étapes - Azure Active Directory | Microsoft Docs
description: Gérez l’utilisation d’Azure Multi-Factor Authentication, notamment la modification des informations de contact ou la configuration des appareils.
services: active-directory
keywords: client de l'authentification multifacteur, problème d'authentification, ID de corrélation
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.openlocfilehash: c3fd74731dbed2c2f36d97b3cb42b383f8e4ca0f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345087"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Gérer les paramètres de la vérification en deux étapes
Cet article répond aux questions en rapport avec la mise à jour des paramètres de Multi-Factor Authentication ou de la vérification en deux étapes. Si vous rencontrez des problèmes de connexion à votre compte, consultez [Problèmes avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour obtenir de l’aide.

## <a name="where-to-find-the-settings-page"></a>Où trouver la page des paramètres
Selon la façon dont vous utilisez Azure Multi-Factor Authentication, vous pouvez modifier vos paramètres (comme votre numéro de téléphone) de différentes manières.

Si le support de votre entreprise vous a envoyé une URL spécifique ou des étapes pour gérer la vérification en deux étapes, suivez ces instructions. Dans le cas contraire, suivez celles indiquées ci-dessous. Si vous effectuez ces étapes et que vous ne voyez pas les mêmes options, cela signifie que votre entreprise ou établissement scolaire a personnalisé le portail. Demandez à l’administrateur de vous donner le lien à votre portail Azure Multi-Factor Authentication.

**Accéder à la page Vérification de sécurité supplémentaire**

- Accédez à https://aka.ms/MFASetup

    ![Vérification](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Si cliquer sur ce lien n’a pas d’effet, vous pouvez également accéder à la page **Vérification de sécurité supplémentaire** en effectuant les étapes suivantes :

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2. Sélectionnez votre nom de compte dans la partie supérieure droite, puis sélectionnez **profil**.

3. Sélectionnez **Vérification de sécurité supplémentaire**.  

    ![MyApps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. La page Vérification de sécurité supplémentaire se charge avec vos paramètres.

    ![Vérification](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Je souhaite modifier mon numéro de téléphone ou ajouter un numéro secondaire
Il est important de configurer un numéro de téléphone d’authentification secondaire.  Étant donné que vous utilisez probablement le même téléphone pour votre numéro de téléphone principal et votre application mobile, le numéro de téléphone secondaire est le seul moyen dont vous disposez pour revenir à votre compte en cas de perte ou de vol de votre téléphone.

> [!NOTE]
> Si vous n’avez pas accès à votre numéro de téléphone principal et que vous avez besoin d’aide pour accéder à votre compte, consultez l’article [Difficultés avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour obtenir de l’aide.  

**Pour modifier votre numéro de téléphone principal :**  

1. Dans la page **Vérification de sécurité supplémentaire**, sélectionnez la zone de texte contenant votre numéro de téléphone, puis tapez votre nouveau numéro.  
2. Sélectionnez **Enregistrer**.  
3. Si ce numéro de téléphone constitue votre option de vérification préférée, vous devez vérifier le nouveau numéro avant de pouvoir l’enregistrer.  

**Pour ajouter un numéro de téléphone secondaire :**  

1. Dans la page Vérification de sécurité supplémentaire, cochez la case **Autre téléphone d’authentification**.  
2. Entrez votre numéro de téléphone secondaire dans la zone de texte.  
3. Sélectionnez **Enregistrer**. Vos modifications sont apportées.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Exiger une nouvelle vérification en deux étapes sur un appareil marqué comme approuvé

Selon les paramètres de votre organisation, vous pouvez disposer d’une case à cocher libellée « Ne plus me le demander pendant **X** jours » lorsque vous effectuez une vérification en deux étapes sur votre navigateur. Si vous cochez cette case, puis que vous perdez votre appareil ou que vous pensez que l’intégrité de votre compte est compromise, vous devez restaurer la vérification en deux étapes pour tous vos appareils.

1. Sur la page de vérification de sécurité supplémentaire, sélectionnez l’option **Restaurer Multi-Factor Authentication sur des appareils précédemment définis comme étant de confiance**.
2. La prochaine fois que vous vous connecterez sur l’un des appareils, vous serez invité à effectuer une vérification en deux étapes.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Comment nettoyer Microsoft Authenticator sur mon ancien appareil et le transférer vers un autre ?
Quand vous désinstallez l’application de votre appareil ou que vous réinitialisez ce dernier, l’application n’est pas désactivée sur le serveur principal. Pour plus d’informations, consultez [Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

## <a name="next-steps"></a>Étapes suivantes
* Obtenir des conseils de dépannage et de l’aide dans [Problèmes avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md)
* Configurer des [mots de passe d’application](multi-factor-authentication-end-user-app-passwords.md) pour les applications qui ne prennent pas en charge la vérification en deux étapes
