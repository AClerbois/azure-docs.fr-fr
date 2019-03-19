---
title: Configurer les informations de sécurité (préversion) pour utiliser les questions de sécurité - Azure Active Directory | Microsoft Docs
description: Comment configurer vos informations de sécurité pour vérifier votre identité au moyen de questions de sécurité prédéfinies.
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901880"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Configurer les informations de sécurité (préversion) pour utiliser les questions de sécurité
Procédez comme suit pour ajouter votre méthode de réinitialisation de mot de passe. Au terme de la configuration initiale, vous pourrez revenir à la page **Informations de sécurité** pour ajouter, mettre à jour ou supprimer vos informations de sécurité.

Après avoir configuré votre méthode de réinitialisation de mot de passe, vous devez également configurer votre méthode de vérification à deux facteurs, à l’aide d’une [application d’authentification](security-info-setup-auth-app.md), de la [messagerie texte](security-info-setup-text-msg.md), ou d’un [appel téléphonique](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurer vos questions de sécurité à partir de la page d’informations de sécurité
Selon les paramètres de votre entreprise, vous pourrez peut-être choisir et répondre à quelques questions de sécurité parmi vos méthodes d’information de sécurité. Votre administrateur définit le nombre de questions de sécurité que vous devez choisir et auxquelles vous devez répondre.

Si vous utilisez des questions de sécurité, nous vous recommandons de les utiliser conjointement avec une autre méthode. Les questions de sécurité peuvent être moins sécurisées que d’autres méthodes, car certaines personnes peuvent connaître les réponses à vos questions.

> [!Note]
> Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire, elles ne peuvent être posées qu’à vous au moment de l’inscription. L’administrateur ne peut pas lire ni modifier vos questions ou vos réponses.
> 
> Si vous ne voyez pas l’option des questions de sécurité, il est possible que votre organisation ne vous autorise pas à l’utiliser comme moyen de vérification. Si tel est le cas, vous devez choisir une autre méthode, ou contacter votre administrateur pour obtenir de l’aide.
> 
> Comptes d’administrateur ne sont pas autorisés à utiliser les Questions de sécurité comme un mot de passe réinitialisé (méthode). Si vous êtes connecté sous un compte de niveau administrateur, vous ne verrez pas ces options.

### <a name="to-set-up-your-security-questions"></a>Pour configurer vos questions de sécurité

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myprofile.microsoft.com/.

    ![Page Mon profil, avec les liens des informations de sécurité mis en évidence](media/security-info/securityinfo-myprofile-page.png)

2. Sélectionnez **Informations de sécurité** à partir du volet de navigation de gauche ou du lien du bloc **Informations de sécurité**, puis sélectionnez **Ajouter une méthode** dans la page **Informations de sécurité**.

    ![Page Informations de sécurité, avec l’option Ajouter une méthode en surbrillance](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sur la page **Ajouter une méthode**, choisissez **Questions de sécurité** dans la liste déroulante, puis sélectionnez **Ajouter**.

    ![Zone Ajouter une méthode, avec les questions de sécurité sélectionnées](media/security-info/securityinfo-myprofile-addquestions.png)

4. Sur la page **Questions de sécurité**, choisissez vos questions de sécurité, répondez-y, puis sélectionnez **Enregistrer**.

    ![Ajouter le numéro de téléphone et choisir la méthode de l’appel téléphonique](media/security-info/securityinfo-myprofile-securityquestions.png)

    Vos informations de sécurité sont mises à jour et vous pouvez utiliser vos question de sécurité pour vérifier votre identité lors de la réinitialisation de mot de passe.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Supprimer les questions de sécurité de vos méthodes d’informations de sécurité
Si vous ne souhaitez plus utiliser vos question de sécurité comme méthode d’informations de sécurité, vous pouvez supprimer celles-ci de la page **Informations de sécurité**.

>[!Important]
>Si vous supprimez vos question de sécurité par erreur, il n’existe aucun moyen d’annuler la suppression. Vous devrez à nouveau ajouter la méthode en suivant les étapes de la section [Configurer vos question de sécurité](#set-up-your-security-questions-from-the-security-info-page) de cet article.

### <a name="to-delete-your-security-questions"></a>Pour supprimer vos questions de sécurité

1. Sur la page **Informations de sécurité**, sélectionnez le lien **Supprimer** en regard de l’option **Question de sécurité**.

    ![Lien permettant de supprimer la méthode Téléphone sur la page Informations de sécurité](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Sélectionnez **Oui** dans la zone de confirmation pour supprimer vos **Question de sécurité**. Une fois vos question de sécurité supprimées, la méthode disparaît de vos informations de sécurité et de la page **Informations de sécurité**.

## <a name="additional-security-info-methods"></a>Autres méthodes d’informations de sécurité
Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md).

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Appel sur téléphone mobile ou téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, votre organisation n’autorise très probablement pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
