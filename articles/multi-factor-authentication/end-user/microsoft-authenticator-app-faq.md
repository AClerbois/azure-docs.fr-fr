---
title: Aide et support pour l’application Microsoft Authenticator | Microsoft Docs
description: Fournit une liste de questions fréquentes et les réponses relatives à l’application Microsoft Authentication et à l’authentification multifacteur Azure.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: e9c152fddbfcd603a84caf2c168ec4f12368dcfa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795865"
---
# <a name="microsoft-authenticator-app-faq"></a>Forum aux questions sur l’application Microsoft Authenticator

Cet article répond aux questions courantes sur l’application Microsoft Authenticator. Si vous ne trouvez pas la réponse à votre question, consultez le [Forum de l’application Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Vous pouvez également consulter une autre FAQ sur une fonctionnalité de l’application en particulier, [FAQ Se connecter sur un téléphone](microsoft-authenticator-app-phone-signin-faq.md).

L’application Microsoft Authenticator a remplacé l’application Azure Authenticator et devient l’application recommandée lorsque vous utilisez l’authentification multifacteur Azure. L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Quelles données Authenticator stocke-t-il en mon nom et comment puis-je les supprimer ?

Microsoft Authenticator stocke les informations de compte créées lors de l’ajout d’un compte. Lorsque vous utilisez Authenticator, un journal de diagnostic est créé pour le débogage et stocke des données utiles afin d’aider Microsoft à diagnostiquer des problèmes imprévus. Vous pouvez accéder aux données de journal en ouvrant **Aide** > **Envoyer des journaux** > **Afficher des journaux**.

Vous pouvez supprimer les données en supprimant la mosaïque du compte. La suppression de la mosaïque du compte supprime également toutes les informations de compte utilisées par l’application, notamment les journaux. 

Pour plus d’informations sur la façon dont Microsoft utilise vos données, visitez : https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>À quoi servent les codes de l’application ? Pourquoi le nombre ne cesse de diminuer ?

Lorsque vous ouvrez l’application Microsoft Authenticator, vous voyez les comptes que vous avez ajoutés ainsi qu’un numéro à six ou huit chiffres à côté de chacun d’eux. Vous pouvez voir un minuteur de 30 secondes exécutant un compte à rebours.

Ces codes sont utilisés lorsque vous vous connectez à votre compte. Après avoir entré votre nom d’utilisateur et votre mot de passe, vous serez peut-être invité à entrer un code de vérification. Ouvrez l’application Microsoft Authenticator et copiez le code qui est actuellement affiché. Entrez ce code dans la page de connexion pour terminer.

Comme les codes changent toutes les 30 secondes, vous n’utilisez donc jamais deux fois le même code. Ce n’est pas comme un mot de passe que vous êtes censé mémoriser. L’idée est que seules les personnes ayant accès à votre téléphone connaissent votre code de vérification.

Comme les codes ne nécessitent ni Internet ni données, vous n’avez pas à vous soucier de disposer d’un service téléphonique pour vous connecter. Lorsque vous fermez l’application, elle arrête de s’exécuter en arrière-plan et ne décharge donc pas la batterie. Vous pouvez fermer l’application et l’ignorer jusqu’à la prochaine connexion.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Je reçois des notifications uniquement lorsque l’application est ouverte. Si l’application n’est pas ouverte, je ne reçois pas de notifications.

Si vous recevez des notifications, mais qu’elles ne font pas de bruit ou ne vibrent pas même si votre sonnerie est activée, vérifiez tout d’abord les paramètres de l’application. Activez le son ou la vibration dans l’application pour qu’elle les utilise dans les notifications.

Si vous ne recevez pas de notifications, vérifiez les cas suivants :

- Votre téléphone est-il en mode silencieux ou Ne pas déranger ? Ce mode peut empêcher l’envoi de notifications par les applications.
- Vous recevez des notifications d’autres applications ? Si ce n’est pas le cas, il peut y avoir un problème avec les connexions réseau sur votre téléphone avec le canal de notifications d’Android ou Apple. Vous pouvez résoudre le premier problème dans les paramètres de votre téléphone, mais vous devrez peut-être contacter votre fournisseur de services pour vous aider dans le deuxième cas.
- Vous pouvez recevoir des notifications de certains comptes sur l’application, mais pas d’autres ? Si c’est le cas, supprimez de votre application le compte qui pose problème et ajoutez-le à nouveau pour réactiver les notifications push.

Si vous avez tenté ces suggestions de dépannage mais que les problèmes persistent, vous pouvez nous envoyer vos journaux pour que nous les vérifiions. Accédez aux paramètres de l’application, puis sélectionnez **Aide et commentaires** et **Envoyer les journaux**. Ensuite, accédez au [forum de l’application Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) et indiquez-nous le problème que vous rencontrez et les étapes que vous avez essayées jusqu’à présent.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>J’utilise déjà l’application Microsoft Authenticator pour les codes de vérification. Comment puis-je activer les notifications push en un clic ?
L’approbation d’une connexion via notification push est disponible uniquement pour les comptes Microsoft personnels, professionnels ou scolaires. Les comptes tiers tels que Google ou Facebook ne bénéficient pas de cette fonctionnalité. Si vous disposez d’un compte Microsoft professionnel ou scolaire, votre organisation peut choisir de désactiver cette option.

Si vous utilisez un compte Microsoft pour votre compte personnel et souhaitez activer les notifications push, vous devez ajouter à nouveau votre compte. Réinscrivez l’appareil avec votre compte et configurez les notifications push.  

Si vous utilisez Microsoft Authenticator pour votre compte professionnel ou scolaire, votre organisation décide s’il faut autoriser ou non les notifications en un clic.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Les notifications push en un clic fonctionnent-elles pour les comptes non Microsoft ?
Non, les notifications push fonctionnent uniquement avec les comptes Microsoft et Azure Active Directory. Si votre entreprise ou école utilise des comptes Azure AD, vous pouvez désactiver cette fonctionnalité.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>J’ai un nouvel appareil ou bien j’ai restauré mon appareil à partir d’une sauvegarde. Comment configurer à nouveau mes comptes dans l’application Microsoft Authenticator ?
Si vous utilisez un appareil iOS, que vous avez activé l’option **Sauvegarde automatique** et que vous avez créé une sauvegarde de vos comptes sur votre ancien périphérique, vous pouvez utiliser cette sauvegarde pour récupérer les informations d’identification de votre compte sur votre nouvel appareil. Pour en savoir plus, consultez l’article [Sauvegarder et récupérer des informations d’identification de compte avec l’application Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md). 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>J’ai perdu mon appareil ou j’utilise un nouveau périphérique. Comment m’assurer que les notifications ne sont plus envoyées sur mon ancien périphérique ?  
L’ajout de l’application Microsoft Authenticator sur votre nouvel appareil iOS ne supprime pas automatiquement l’application de votre ancien périphérique. Même la suppression de l’application de votre ancien périphérique ne suffit pas. Vous devez supprimer l’application de votre ancien périphérique et dire à Microsoft ou votre entreprise d’oublier l’ancien périphérique et d’annuler son inscription auprès de votre compte.
- **Pour supprimer l’application d’un appareil à l’aide d’un compte Microsoft personnel :** Accédez à la zone de vérification en deux étapes de votre page [Sécurité du compte](https://account.microsoft.com/security)  et choisissez de désactiver la vérification de votre ancien périphérique.  
- **Pour supprimer l’application d’un appareil à l’aide d’un compte Microsoft scolaire ou professionnel :** Accédez à la zone de vérification en deux étapes de votre page [MyApps](https://myapps.microsoft.com/)ou au portail personnalisé de votre entreprise et choisissez de désactiver la vérification de votre ancien périphérique. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Comment supprimer un compte de l’application ?
* iOS : dans l’écran principal, effectuez un balayage vers la gauche sur une vignette de compte. Sélectionnez **Supprimer**.
* Windows Phone : dans l’écran principal, sélectionnez le bouton de menu, puis **Modifier les comptes**. Cliquez sur le symbole **X** en regard du nom de compte.
* Android : dans l’écran principal, sélectionnez le bouton de menu, puis **Modifier les comptes**. Cliquez sur le symbole **X** en regard du nom de compte.

Si vous utilisez un appareil enregistré auprès de votre organisation, vous devrez peut-être effectuer une étape supplémentaire pour supprimer votre compte. Sur ces appareils, l’application Microsoft Authenticator est automatiquement enregistrée en tant qu’administrateur de l’appareil. Si vous souhaitez désinstaller complètement l’application, vous devez tout d’abord annuler l’inscription de l’application dans les paramètres d’application.

### <a name="why-does-the-app-request-so-many-permissions"></a>Pourquoi l’application demande-t-elle autant d’autorisations ?
Voici la liste complète des autorisations qui peuvent être demandées, ainsi que la manière dont elles sont utilisées dans l’application. Les autorisations qui apparaissent dépendent du type de téléphone que vous utilisez.

* **Appareil photo** : utilisée pour analyser les codes QR lorsque vous ajoutez un compte professionnel, scolaire ou tiers.
* **Contacts and phone (Contacts et téléphone)**  : utilisée pour simplifier le processus en recherchant les comptes existants sur votre téléphone lorsque vous vous connectez avec votre compte Microsoft personnel.
* **SMS** : utilisée pour vérifier que votre numéro de téléphone correspond au numéro figurant dans le dossier. Lorsque vous vous connectez avec votre compte Microsoft personnel pour la première fois.  Nous envoyons un SMS contenant un code de vérification à 6-8 chiffres sur le téléphone que vous avez utilisé pour télécharger l’application. Au lieu de vous demander de rechercher ce code et de le saisir dans l’application, nous le faisons pour vous dans le message texte.
* **Dessiner sur les autres applications** : la notification que vous recevez et qui vérifie que votre identité s’affiche également sur les autres applications en cours d’exécution.
* **Receive data from the internet (Recevoir des données à partir d’Internet)**  : cette autorisation est requise pour l’envoi de notifications.
* **Prevent phone from sleeping (Empêcher la mise en veille du téléphone)**  : si vous enregistrez votre appareil auprès de votre entreprise, elle peut modifier cette stratégie sur votre téléphone.
* **Control vibration (Contrôler les vibrations)**  : vous pouvez choisir si vous souhaitez que votre téléphone vibre chaque fois que vous recevez une notification visant à vérifier votre identité.
* **Use fingerprint hardware (Utiliser du matériel de détection de l’empreinte digitale** : certains comptes professionnels et scolaires demandent un code PIN supplémentaire chaque fois que vous vérifiez votre identité. Pour faciliter le processus, nous vous autorisons à utiliser vos empreintes digitales au lieu d’avoir à saisir votre code PIN.
* **Afficher les connexions réseau** : lorsque vous ajoutez un compte Microsoft, l’application exige une connexion réseau/Internet.
* **Read the contents of your storage (Lire le contenu de votre stockage)**  : cette autorisation est utilisée uniquement lorsque vous signalez un problème technique via les paramètres de l’application. Certaines informations sont collectées à partir de votre espace de stockage afin de diagnostiquer le problème.
* **Full network access (Accès complet au réseau)**  : cette autorisation est requise pour l’envoi de notifications visant à vérifier votre identité.
* **Exécuter au démarrage** : si vous redémarrez votre téléphone, cette autorisation vous permet de continuer de recevoir des notifications visant à vérifier votre identité.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Pourquoi l’application Microsoft Authenticator permet-elle d’approuver une demande sans déverrouiller l’appareil ?

Vous n’êtes pas obligé de déverrouiller votre appareil pour approuver les requêtes de vérification, car vous devez simplement prouver que vous avez votre téléphone avec vous. La vérification en deux étapes fait appel à deux éléments : un que vous connaissez, et un autre que vous possédez. L’élément que vous connaissez est votre mot de passe. L’élément que vous possédez est votre téléphone (configuré avec l’application Microsoft Authenticator et enregistré comme une preuve MFA). Par conséquent, le fait d’être en possession du téléphone et d’approuver la demande remplit les critères du second facteur d’authentification.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Que signifie l’icône de cadenas dans la liste des comptes ?

L’icône de cadenas indique que l’appareil est inscrit dans Azure AD et enregistré sur le compte. L’inscription de l’appareil pour iOS a lieu lors de l’inscription de Microsoft Intune.

## <a name="next-steps"></a>Étapes suivantes

### <a name="contact-us"></a>Nous contacter
Si vous n’avez pas trouvé la réponse à votre question ici, rendez-vous sur le [Forum de l’application Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) pour publier votre question et obtenir de l’aide de la part de la communauté ou laissez un commentaire sur cette page.


### <a name="related-topics"></a>Rubriques connexes
* [À propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour les comptes Microsoft
* Vous rencontrez des [difficultés avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour votre compte professionnel ou scolaire ?
* [Utiliser Microsoft Authenticator pour se connecter sur un téléphone](microsoft-authenticator-app-phone-signin-faq.md)
