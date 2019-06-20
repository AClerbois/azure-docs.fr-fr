---
title: Implémenter la synchronisation de hachage de mot de passe avec la synchronisation Azure AD Connect | Microsoft Docs
description: Cet article explique comment fonctionne la synchronisation de hachage de mot de passe et comment la configurer.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ff1cb57bb3317adc7ab6208ebf5029361b7c54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235153"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implémenter la synchronisation de hachage de mot de passe avec la synchronisation Azure AD Connect
Cet article vous fournit les informations nécessaires pour synchroniser vos mots de passe utilisateur à partir d’une instance Active Directory (AD) locale vers une instance Azure Active Directory (Azure AD) dans le cloud.

## <a name="how-password-hash-synchronization-works"></a>Fonctionnement de la synchronisation de hachage de mot de passe
Le service de domaine Active Directory stocke les mots de passe sous forme de valeur de hachage du mot de passe réel de l’utilisateur. Une valeur de hachage est le résultat d’une fonction mathématique unidirectionnelle (« *algorithme de hachage* »). Il n’existe aucune méthode pour retrouver la version en texte brut du mot de passe à partir du résultat d’une fonction unidirectionnelle. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local.

Pour synchroniser votre mot de passe, Azure AD Connect Sync extrait le hachage de votre mot de passe à partir de l’instance Active Directory local. Un traitement de sécurité supplémentaire est appliqué au hachage du mot de passe avant sa synchronisation avec le service d’authentification Azure Active Directory. Les mots de passe sont synchronisés pour chaque utilisateur et par ordre chronologique.

Le flux de données réel du processus de synchronisation du hachage de mot de passe est similaire à celui de la synchronisation des données de l’utilisateur. Cependant, les mots de passe sont synchronisés plus fréquemment que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Le processus de hachage de synchronisation de mot de passe s’exécute toutes les deux minutes. Vous ne pouvez pas modifier la fréquence de ce processus. Quand vous synchronisez un mot de passe, il remplace le mot de passe cloud existant.

La première fois que vous activez la fonctionnalité de synchronisation de hachage de mot de passe, elle effectue une synchronisation initiale des mots de passe de tous les utilisateurs concernés. Vous ne pouvez pas définir explicitement un sous-ensemble de mots de passe utilisateur à synchroniser.

Lorsque vous modifiez un mot de passe local, le mot de passe mis à jour est synchronisé, plus souvent en quelques minutes.
La fonctionnalité de synchronisation de hachage de mot de passe tente automatiquement d’effectuer à nouveau les tentatives de synchronisation ayant échoué. Si une erreur se produit lors d’une tentative de synchronisation de mot de passe, une erreur est enregistrée dans l’Observateur d’événements.

La synchronisation d’un mot de passe n’a aucun impact sur l’utilisateur actuellement connecté.
La session en cours n’est pas immédiatement affectée par une modification du mot de passe synchronisé effectuée tout en étant connecté à un service cloud. Toutefois, lorsque le service cloud vous oblige à vous authentifier à nouveau, vous devez fournir votre nouveau mot de passe.

Un utilisateur doit entrer ses informations d’identification d’entreprise une deuxième fois pour s’authentifier auprès d’Azure AD, qu’il soit connecté à son réseau d’entreprise ou non. Ce modèle peut être réduit, cependant, si l’utilisateur coche la case « Maintenir la connexion » lors de la connexion. Cette sélection définit un cookie de session qui ignore l’authentification pendant 180 jours. Le comportement KMSI peut être activé ou désactivé par l’administrateur Azure AD. De plus, vous pouvez réduire les invites de mot de passe en activant [l’authentification unique invisible](how-to-connect-sso.md) qui connecte automatiquement les utilisateurs utilisant des appareils d’entreprise connectés au réseau de l’entreprise.

> [!NOTE]
> La synchronisation de mot de passe est uniquement prise en charge pour l'utilisateur de type d'objet dans Active Directory. Elle n'est pas prise en charge pour le type d'objet iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Description détaillée du fonctionnement de la synchronisation de hachage de mot de passe
La section suivante explique en détail comment fonctionne la synchronisation du hachage de mot de passe entre Active Directory et Azure AD.

![Flux de travail détaillé des mots de passe](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Toutes les deux minutes, l’agent de synchronisation du hachage de mot de passe qui se trouve sur le serveur AD Connect demande des hachages de mots de passe stockés (l’attribut unicodePwd) à un contrôleur de domaine.  Cette demande utilise le protocole de réplication [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) permettant de synchroniser les données entre les contrôleurs de domaine. Le compte de service doit disposer des autorisations Répliquer les changements d’annuaires et Répliquer les changements d’annuaire Tout d’Active Directory (accordées par défaut lors de l’installation) pour obtenir les hachages de mot de passe.
2. Avant l’envoi, le contrôleur de domaine chiffre le hachage de mot de passe MD4 à l’aide d’une clé qui est un hachage [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) de la clé de session RPC et un salt. Il envoie ensuite le résultat à l’agent de synchronisation de hachage de mot de passe via RPC. Le contrôleur de domaine passe également le salt à l’agent de synchronisation à l’aide du protocole de réplication du contrôleur de domaine, pour que l’agent puisse déchiffrer l’enveloppe.
3.  Une fois que l’agent de synchronisation de hachage de mot de passe dispose de l’enveloppe chiffrée, il utilise [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) et le salt pour générer une clé afin de déchiffrer les données reçues dans leur format MD4 d’origine. L’agent de synchronisation du hachage de mot de passe n’a jamais accès au mot de passe en texte clair. L’utilisation de MD5 par l’agent de synchronisation de hachage de mot de passe est strictement destinée à assurer la compatibilité du protocole de réplication avec le contrôleur de domaine, et uniquement en local entre le contrôleur de domaine et l’agent de synchronisation de hachage de mot de passe.
4.  L’agent de synchronisation de hachage de mot de passe étend le hachage de mot de passe binaire de 16 octets à 64 octets en convertissant d’abord le hachage en chaîne hexadécimale de 32 octets, puis en reconvertissant cette chaîne au format binaire avec l’encodage UTF-16.
5.  L’agent de synchronisation de hachage de mot de passe ajoute pour chaque utilisateur un salt de 10 octets de long au fichier binaire de 64 octets pour renforcer la protection du hachage d’origine.
6.  L’agent de synchronisation de hachage de mot de passe combine alors le hachage MD4 et le salt par utilisateur, puis place le tout dans la fonction [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). 1 000 itérations de l’algorithme de hachage à clé [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) sont utilisées. 
7.  L’agent de synchronisation de hachage de mot de passe prend le hachage de 32 octets résultant, concatène le salt par utilisateur et le nombre d’itérations SHA256 (pour une utilisation par Azure AD), puis transmet la chaîne d’Azure AD Connect à Azure AD par SSL.</br> 
8.  Lorsqu’un utilisateur tente de se connecter à Azure AD et entre son mot de passe, le mot de passe est traité par le même processus MD4+salt+PBKDF2+HMAC-SHA256. Si le hachage résultant correspond au hachage stocké dans Azure AD, l’utilisateur a entré le bon mot de passe et est authentifié. 

>[!Note] 
>Le hachage MD4 d’origine n’est pas transmis à Azure AD. Au lieu de cela, le hachage SHA256 du hachage MD4 d’origine est transmis. Par conséquent, si le hachage stocké dans Azure AD est obtenu, il ne peut pas être utilisé dans une attaque de type pass-the-hash locale.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Fonctionnement de la synchronisation de hachage de mot de passe avec Azure Active Directory Domain Services
Vous pouvez également utiliser la fonctionnalité de synchronisation de hachage de mot de passe pour synchroniser vos mots de passe locaux avec [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Ce scénario permet à l’instance Azure Active Directory Domain Services d’authentifier vos utilisateurs dans le cloud avec toutes les méthodes disponibles dans votre instance Active Directory locale. L’expérience de ce scénario est similaire à l’utilisation de l’outil de migration Active Directory (ADMT) dans un environnement local.

### <a name="security-considerations"></a>Considérations relatives à la sécurité
Lors de la synchronisation des mots de passe, la version en texte brut de votre mot de passe n’est exposée ni à la fonctionnalité de synchronisation de hachage de mot de passe, ni à Azure AD, ni à l’un des services associés.

L’authentification de l’utilisateur s’effectue par rapport à Azure AD plutôt que sur l’instance Active Directory de l’organisation. Les données de mot de passe SHA256 stockées dans Azure AD (hachage du hachage MD4 d’origine) sont plus sécurisées que celles qui sont stockées dans Active Directory. En outre, étant donné que ce hachage SHA256 ne peut pas être déchiffré, il ne peut pas être réimporté dans l’environnement Active Directory de l’organisation et présenté sous la forme d’un mot de passe utilisateur valide dans une attaque de type pass-the-hash.

### <a name="password-policy-considerations"></a>Remarques sur les stratégies de mot de passe
Deux types de stratégies de mot de passe sont affectés par l’activation de la synchronisation de hachage de mot de passe :

* Stratégie de complexité de mot de passe
* Stratégie d’expiration de mot de passe

#### <a name="password-complexity-policy"></a>Stratégie de complexité de mot de passe  
Quand vous activez la synchronisation de hachage de mot de passe, les stratégies de complexité de mot de passe dans votre instance Active Directory locale remplacent les stratégies de complexité dans le cloud pour les utilisateurs synchronisés. Vous pouvez utiliser tous les mots de passe valides de votre instance Active Directory locale pour accéder aux services Azure AD.

> [!NOTE]
> Les mots de passe des utilisateurs créés directement dans le cloud sont toujours soumis aux stratégies de mot de passe définies dans le cloud.

#### <a name="password-expiration-policy"></a>Stratégie d’expiration de mot de passe  
Si un utilisateur est concerné par la synchronisation de hachage de mot de passe, le mot de passe de compte cloud est défini sur *Ne jamais expirer*.

Vous pouvez continuer à vous connecter aux services cloud à l’aide d’un mot de passe synchronisé qui a expiré dans votre environnement local. Votre mot de passe cloud est mis à jour la prochaine fois que vous modifiez le mot de passe dans l’environnement local.

#### <a name="account-expiration"></a>Expiration du compte
Si votre organisation utilise l’attribut accountExpires dans le cadre de la gestion des comptes d’utilisateur, il n’est pas synchronisé avec Azure AD. Par conséquent, un compte Active Directory expiré dans un environnement configuré pour la synchronisation de hachage de mot de passe sera toujours actif dans Azure AD. Nous recommandons, si le compte a expiré, qu’une action de workflow déclenche un script PowerShell qui désactive le compte Azure AD de l’utilisateur (via l’applet de commande [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)). Inversement, lorsque le compte est activé, l’instance Azure AD doit être activée.

### <a name="overwrite-synchronized-passwords"></a>Remplacement des mots de passe synchronisés
Un administrateur peut réinitialiser manuellement votre mot de passe à l’aide de Windows PowerShell.

Dans ce cas, le nouveau mot de passe remplace votre mot de passe synchronisé et toutes les stratégies de mot de passe définies dans le cloud s’appliquent au nouveau mot de passe.

Si vous modifiez de nouveau votre mot de passe local, le nouveau mot de passe est synchronisé avec le cloud et il remplace le mot de passe mis à jour manuellement.

La synchronisation d’un mot de passe n’a aucun impact sur l’utilisateur Azure connecté. Votre session de service cloud en cours n’est pas immédiatement affectée par une modification de mot de passe synchronisé effectuée lorsque vous êtes connecté à un service cloud. L’option Maintenir la connexion (KMSI) étend la durée de cette différence. Lorsque le service cloud vous oblige à vous authentifier à nouveau, vous devez fournir votre nouveau mot de passe.

### <a name="additional-advantages"></a>Autres avantages

- En règle générale, la synchronisation de hachage de mot de passe est plus simple à implémenter qu’un service de fédération. Elle ne nécessite pas de serveurs supplémentaires et élimine la dépendance vis-à-vis d’un service de fédération hautement disponible pour authentifier les utilisateurs.
- La synchronisation de hachage de mot de passe peut également être activée en plus de la fédération. Vous pouvez l’utiliser comme solution de secours si votre service de fédération connaît une défaillance.

## <a name="enable-password-hash-synchronization"></a>Activer la synchronisation de hachage de mot de passe

>[!IMPORTANT]
>Si vous procédez à une migration depuis AD FS (ou d’autres technologies de fédération) vers la synchronisation de hachage du mot de passe, nous vous recommandons vivement de vous référer à notre guide de déploiement détaillé, publié [ici](https://aka.ms/adfstophsdpdownload).

La synchronisation de hachage de mot de passe est activée automatiquement si vous installez Azure AD Connect avec la **configuration rapide**. Pour plus d’informations, voir [Bien démarrer avec Azure AD Connect à l’aide de paramètres rapides](how-to-connect-install-express.md).

Si vous utilisez des paramètres personnalisés lors de l’installation d’Azure AD Connect, la synchronisation de hachage de mot de passe est disponible dans la page de connexion utilisateur. Pour plus d’informations, consultez [Installation personnalisée d’Azure AD Connect](how-to-connect-install-custom.md).

![Activation de la synchronisation de hachage de mot de passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronisation de hachage de mot de passe et FIPS
Si votre serveur a été verrouillé selon la norme Federal Information Processing Standard (FIPS), MD5 est désactivé.

**Pour activer MD5 pour la synchronisation de hachage de mot de passe, procédez comme suit :**

1. Accédez à %programfiles%\Azure AD Sync\Bin.
2. Ouvrez miiserver.exe.config.
3. Accédez au nœud configuration/runtime (à la fin du fichier).
4. Ajoutez le nœud suivant : `<enforceFIPSPolicy enabled="false"/>`
5. Enregistrez vos modifications.

Pour référence, cet extrait de code indique ce que vous devez obtenir :

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Pour plus d’informations sur la sécurité et FIPS, voir [Synchronisation, chiffrement et conformité à la norme FIPS du hachage de mot de passe Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Résoudre les problèmes de synchronisation de hachage de mot de passe
Si vous rencontrez des problèmes avec la synchronisation de hachage de mot de passe, consultez [Troubleshoot password hash synchronization](tshoot-connect-password-hash-synchronization.md) (Résoudre les problèmes de synchronisation de hachage de mot de passe).

## <a name="next-steps"></a>Étapes suivantes
* [Synchronisation Azure AD Connect : personnaliser les options de synchronisation](how-to-connect-sync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)
* [Obtenir un plan de déploiement étape par étape pour la migration à partir des services AD FS vers la synchronisation de hachage de mot de passe](https://aka.ms/authenticationDeploymentPlan)
