---
title: Configurer les informations de sécurité (préversion) pour utiliser une application d’authentification - Azure Active Directory | Microsoft Docs
description: Apprenez à configurer vos informations de sécurité pour vérifier votre identité avec l’application Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4757be00a3633f56aed52dd7af22923e49b0b62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475676"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Configurer les informations de sécurité (préversion) pour utiliser une application d’authentification
Suivez ces étapes pour ajouter vos méthodes de réinitialisation de mot de passe et de vérification en deux étapes. Au terme de la configuration initiale, vous pouvez revenir à la page **Informations de sécurité** pour ajouter, mettre à jour ou supprimer vos informations de sécurité.

Si vous êtes invité à les configurer immédiatement après vous être connecté à votre compte professionnel ou scolaire, reportez-vous aux étapes détaillées de l’article [Configurer vos informations de sécurité à partir de l’invite de la page de connexion](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Si vous ne voyez aucune option d’application d’authentification, il est possible que votre organisation ne vous autorise pas à utiliser une application d’authentification à des fins de vérification. Dans ce cas, vous devez choisir une autre méthode, ou contacter votre administrateur pour obtenir de l’aide.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurer l’application Microsoft Authenticator à partir de la page des informations de sécurité
Selon les paramètres de votre organisation, vous pouvez peut-être utiliser une application d’authentification comme méthode d’informations de sécurité. Vous n’êtes pas obligé d’utiliser l’application Microsoft Authenticator et vous pouvez choisir une autre application au cours du processus de configuration. Cet article, cependant, utilise l’application Microsoft Authenticator. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Pour configurer l’application Microsoft Authenticator

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myprofile.microsoft.com/.

    ![Page Mon profil, avec les liens des informations de sécurité mis en évidence](media/security-info/securityinfo-myprofile-page.png)

2. Sélectionnez **Informations de sécurité** à partir du volet de navigation de gauche ou du lien du bloc **Informations de sécurité**, puis sélectionnez **Ajouter une méthode** dans la page **Informations de sécurité**.

    ![Page Informations de sécurité, avec l’option Ajouter une méthode mise en évidence](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Dans la page **Ajouter une méthode**, choisissez **Application d’authentification** dans la liste déroulante, puis sélectionnez **Ajouter**.

    ![Boîte de dialogue Ajouter une méthode, avec Application d’authentification sélectionnée](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Dans la page **Commencer par obtenir l’application**, sélectionnez **Télécharger maintenant** pour télécharger et installer l’application Microsoft Authenticator sur votre appareil mobile, puis sélectionnez **Suivant**.

    Pour plus d’informations sur le téléchargement et l’installation de l’application, consultez [Télécharger et installer l’application Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Page Commencer par obtenir l’application](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Si vous souhaitez utiliser une autre application d’authentification que Microsoft Authenticator, sélectionnez le lien **Je souhaite utiliser une autre application d’authentification**.
   > 
   > Si votre organisation vous permet de choisir une autre méthode en plus de l’application d’authentification, vous pouvez sélectionner le lien **Je veux configurer une autre méthode**.

5. Gardez la page **Configurer votre compte** ouverte pendant que vous configurez l’application Microsoft Authenticator sur votre appareil mobile.

    ![Configurer la page de l’application d’authentification](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Ouvrez l’application Microsoft Authenticator, choisissez d’autoriser les notifications (si vous y êtes invité), sélectionnez **Ajouter un compte** grâce à l’icône **Personnaliser et contrôler** dans l’angle supérieur droit, puis sélectionnez **Compte professionnel ou scolaire**.

7. Revenez à la page **Configurer votre compte**, puis sélectionnez **Suivant**.

    La page **Scanner le code QR** s’affiche.

    ![Scanner le code QR à l’aide de l’application Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Scannez le code fourni à l’aide du lecteur de code QR de l’application Microsoft Authenticator, qui est apparu sur votre appareil mobile après la création de votre compte professionnel ou scolaire à l’étape 6.

    L’application d’authentification doit ajouter votre compte professionnel ou scolaire sans exiger d’informations supplémentaires de votre part. Toutefois, si le lecteur de code QR ne parvient pas à lire le code, vous pouvez sélectionner le lien **Impossible de scanner le code QR** et entrer manuellement le code et l’URL dans l’application Microsoft Authenticator. Pour plus d’informations sur l’ajout manuel d’un code, consultez [Ajouter manuellement un compte à l’application](user-help-auth-app-add-account-manual.md).

7. Sélectionnez **Suivant** dans la page **Scanner le code QR**.

    Une notification est envoyée à l’application Microsoft Authenticator sur votre appareil mobile pour tester votre compte.

    ![Tester votre compte avec l’application Authenticator](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Approuvez la notification dans l’application Microsoft Authenticator, puis sélectionnez **Suivant**.

     ![Notification de réussite, connexion de l’application et de votre compte](media/security-info/securityinfo-myprofile-successauthapp.png)

     Vos informations de sécurité sont mises à jour pour utiliser l’application Microsoft Authenticator par défaut et vérifier votre identité quand vous utilisez la vérification en deux étapes ou la réinitialisation de mot de passe.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Supprimer votre application d’authentification à partir de vos méthodes d’informations de sécurité
Si vous ne souhaitez plus utiliser votre application d’authentification comme méthode d’informations de sécurité, vous pouvez la supprimer de la page **Informations de sécurité**. Cela fonctionne pour toutes les applications d’authentification, pas seulement pour l’application Microsoft Authenticator. Au terme de la suppression de l’application, vous devez aller dans l’application d’authentification sur votre appareil mobile, et supprimer le compte.

>[!Important]
>Si vous supprimez l’application d’authentification par erreur, il n’existe aucun moyen d’annuler la suppression. Vous devrez à nouveau l’ajouter en suivant les étapes de la section [Configurer la page de l’application d’authentification](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) de cet article.

### <a name="to-delete-the-authenticator-app"></a>Pour supprimer l’application d’authentification

1. Dans la page **Informations de sécurité**, sélectionnez le lien **Supprimer** en regard de l’application Authenticator.

    ![Lien pour supprimer l’application d’authentification à partir des informations de sécurité](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Sélectionnez **Oui** dans la zone de confirmation pour supprimer l’application d’authentification. Une fois l’application d’authentification supprimée, elle disparaît de vos informations de sécurité et de la page **Informations de sécurité**. Si l’application d’authentification constitue votre méthode par défaut, celle-ci est remplacée par une autre méthode disponible.

3. Ouvrez l’application d’authentification sur votre appareil mobile, sélectionnez **Modifier les comptes**, puis supprimez votre compte professionnel ou scolaire à partir de l’application d’authentification.

    Votre compte est intégralement supprimé de l’application d’authentification pour les requêtes de réinitialisation de mot de passe et la vérification à deux facteurs.

## <a name="change-your-default-security-info-method"></a>Changer de méthode d’informations de sécurité par défaut
Pour utiliser l’application d’authentification comme méthode par défaut lorsque vous vous connectez à votre compte professionnel ou scolaire à l’aide de la vérification en deux étapes, ou pour les demandes de réinitialisation de mot de passe, il vous suffit de définir cette méthode dans la page **Informations de sécurité**.

### <a name="to-change-your-default-security-info-method"></a>Pour changer de méthode d’informations de sécurité par défaut

1. Dans la page **Informations de sécurité**, sélectionnez le lien **Changer** en regard des informations relatives à la **Méthode de connexion par défaut**.

    ![Lien Changer pour la méthode de connexion par défaut](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Choisissez **Microsoft Authenticator - notification** dans la liste déroulante des méthodes disponibles. Si vous n’utilisez pas l’application Microsoft Authenticator, sélectionnez l’option **Application Authenticator ou jeton matériel**.

    ![Choisir la méthode de connexion par défaut](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Sélectionnez **Confirmer**.

    La méthode par défaut utilisée pour la connexion devient l’application Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Autres méthodes d’informations de sécurité
Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Appel sur téléphone mobile ou téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).
    
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, il est très probable que votre organisation n’autorise pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).