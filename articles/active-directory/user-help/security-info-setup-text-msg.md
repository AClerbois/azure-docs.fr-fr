---
title: Configurer les informations de sécurité (préversion) pour utiliser la messagerie texte - Azure Active Directory | Microsoft Docs
description: Procédure de configuration de vos informations de sécurité pour vérifier votre identité à l’aide de la messagerie texte et de votre appareil mobile.
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
ms.openlocfilehash: ea9e4ae21ecc6538b33aed1566c10ddcd22b86c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482257"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Configurer les informations de sécurité (préversion) pour utiliser la messagerie texte
Procédez comme suit pour ajouter vos méthodes de vérification en deux étapes et de réinitialisation de mot de passe. Au terme de la configuration initiale, vous pouvez revenir à la page **Informations de sécurité** pour ajouter, mettre à jour ou supprimer vos informations de sécurité.

Si vous êtes invité à les configurer immédiatement après vous être connecté à votre compte professionnel ou scolaire, reportez-vous aux étapes détaillées de l’article [Configurer vos informations de sécurité à partir de l’invite de la page de connexion](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Si vous ne voyez pas l’option de téléphone, il est possible que votre organisation ne vous autorise pas à utiliser un numéro de téléphone à des fins de vérification. Dans ce cas, vous devez choisir une autre méthode, ou contacter votre administrateur pour obtenir de l’aide.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurer les informations de sécurité pour utiliser les SMS
Selon les paramètres de votre organisation, vous pourrez peut-être utiliser la messagerie texte comme méthode d’informations de sécurité. L’option de message texte fait partie des options téléphoniques, ce qui signifie que vous devez configurer tous les éléments de la même manière que pour votre numéro de téléphone. Mais dans ce cas de figure, au lieu de demander à Microsoft de vous appeler, vous devez choisir d’utiliser un message texte.

>[!Note]
>Si vous souhaitez recevoir un appel téléphonique plutôt qu’un SMS, suivez les étapes indiquées dans l’article [Set up security info to use phone calls](security-info-setup-phone-number.md) (Configurer les informations de sécurité pour utiliser des appels téléphoniques).

### <a name="to-set-up-text-messages"></a>Pour configurer les SMS

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myprofile.microsoft.com/.

    ![Page Mon profil, avec les liens des informations de sécurité mis en évidence](media/security-info/securityinfo-myprofile-page.png)

2. Sélectionnez **Informations de sécurité** à partir du volet de navigation de gauche ou du lien du bloc **Informations de sécurité**, puis sélectionnez **Ajouter une méthode** dans la page **Informations de sécurité**.

    ![Page Informations de sécurité, avec l’option Ajouter une méthode en surbrillance](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sur la page **Ajouter une méthode**, choisissez **Téléphone** dans la liste déroulante, puis sélectionnez **Ajouter**.

    ![Zone Ajouter une méthode, avec l’option Téléphone sélectionnée](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Sur la page **Téléphone**, entrez le numéro de téléphone de votre appareil mobile, choisissez **M’envoyer un code par SMS**, puis sélectionnez **Suivant**.

    ![Ajouter le numéro de téléphone et choisir la méthode de l’envoi de SMS](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Entrez le code qui vous a été envoyé par SMS sur votre appareil mobile, puis sélectionnez **Suivant**.

    ![Ajouter le numéro de téléphone et choisir la méthode de l’envoi de SMS](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    La page change pour indiquer que vous avez réussi.

    ![Notification de réussite, connectant le numéro de téléphone, le choix de recevoir des SMS et votre compte](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Vos informations de sécurité sont alors mises à jour pour utiliser les SMS, afin de contrôler votre identité lorsque vous utilisez la vérification en deux étapes ou une réinitialisation du mot de passe en libre-service. Si vous souhaitez que la messagerie texte constitue votre méthode par défaut, reportez-vous à la section [Changer de méthode d’informations de sécurité par défaut](#change-your-default-security-info-method) de cet article.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Supprimer la messagerie texte de vos méthodes d’informations de sécurité
Si vous ne souhaitez plus utiliser un SMS comme méthode d’informations de sécurité, vous pouvez supprimer celle-ci de la page **Informations de sécurité**.

>[!Important]
>Si vous supprimez cette méthode par erreur, il n’existe aucun moyen d’annuler la suppression. Vous devrez à nouveau l’ajouter en suivant les étapes de la section [Configurer la méthode des messages texte](#set-up-text-messages-from-the-security-info-page) de cet article.

### <a name="to-delete-text-messaging"></a>Pour supprimer la messagerie au format texte

1. Sur la page **Informations de sécurité**, sélectionnez le lien **Supprimer** en regard de l’option **Téléphone**.

    ![Lien permettant de supprimer la méthode Téléphone et messagerie texte sur la page Informations de sécurité](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Sélectionnez **Oui** dans la zone de confirmation pour supprimer le numéro de **téléphone**. Une fois votre numéro de téléphone supprimé, celui-ci disparaît de vos informations de sécurité et de la page **Informations de sécurité**. Si **Téléphone** est votre méthode par défaut, celle-ci est remplacée par une autre méthode disponible.

## <a name="change-your-default-security-info-method"></a>Changer de méthode d’informations de sécurité par défaut
Pour utiliser la messagerie texte comme méthode par défaut lorsque vous vous connectez à votre compte professionnel ou scolaire à l’aide de la vérification en deux étapes ou pour les demandes de réinitialisation de mot de passe, il vous suffit de définir cette méthode sur la page **Informations de sécurité**.

### <a name="to-change-your-default-security-info-method"></a>Pour changer de méthode d’informations de sécurité par défaut

1. Dans la page **Informations de sécurité**, sélectionnez le lien **Changer** en regard des informations relatives à la **Méthode de connexion par défaut**.

    ![Lien Changer pour la méthode de connexion par défaut](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Sélectionnez **Téléphone - texte ( *_votre_numéro_de_téléphone_* )** dans la liste déroulante des méthodes disponibles, puis sélectionnez **Confirmer**.

    ![Choisir la méthode de connexion par défaut](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    La méthode par défaut utilisée pour la connexion devient **Téléphone - texte ( *_votre_numéro_de_téléphone_* )** .

## <a name="additional-security-info-methods"></a>Autres méthodes d’informations de sécurité
Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Configurer des informations de sécurité pour utiliser une application d’authentification).

- **Appel sur un appareil mobile ou un téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).
    
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, il est très probable que votre organisation n’autorise pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).