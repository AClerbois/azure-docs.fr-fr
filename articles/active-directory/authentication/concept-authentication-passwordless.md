---
title: Authentification sans mot de passe pour Azure Active Directory (préversion)
description: Authentification sans mot de passe pour Azure AD à l’aide de clés de sécurité FIDO2 ou de l’application Microsoft Authenticator (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b52f356b112cff51105ed44c79788ee4542c6e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430496"
---
# <a name="passwordless-authentication-options"></a>Options d’authentification sans mot de passe

L’authentification multifacteur (MFA) est un excellent moyen de sécuriser votre organisation, mais les utilisateurs se lasseront de la couche supplémentaire en plus de devoir se souvenir de leurs mots de passe. Les méthodes d’authentification sans mot de passe sont plus pratiques, car le mot de passe est supprimé et remplacé par quelque chose que vous avez et quelque chose que vous savez.

|   | Quelque chose que vous avez | Quelque chose que vous êtes ou savez |
| --- | --- | --- |
| Sans mot de passe | Appareil Windows 10, téléphone ou clé de sécurité | Biométrie ou code confidentiel |

Chaque organisation a des besoins différents en matière d’authentification. Microsoft propose trois options d’authentification sans mot de passe :

- Windows Hello Entreprise 
- Application Microsoft Authenticator 
- Clés de sécurité FIDO2

![Authentification : Sécurité et commodité](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello Entreprise 

Windows Hello Entreprise est idéal pour les professionnels de l’information qui disposent de leur propre PC Windows. Les données biométriques et le code confidentiel sont directement liés au PC de l’utilisateur, personne à part son propriétaire ne peut donc y accéder. Avec l’intégration PKI et la prise en charge intégrée de l'authentification unique (SSO), Windows Hello Entreprise offre une méthode d'accès simple et pratique aux ressources d'entreprise localement et dans le cloud.

Le [guide de planification](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello Entreprise peut vous aider à déterminer le type de déploiement de Windows Hello Entreprise dont vous avez besoin, ainsi que les options à prendre en compte.

## <a name="microsoft-authenticator-app"></a>Application Microsoft Authenticator

Autorisez le téléphone de votre employé à devenir une méthode d’authentification sans mot de passe. Vous utilisez peut-être déjà l’application Microsoft Authenticator comme une option pratique d’authentification multifacteur en plus d’un mot de passe. Elle est désormais disponible en tant qu’option sans mot de passe.

![Connectez-vous à Microsoft Edge avec l’application Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Elle transforme n’importe quel téléphone iOS ou Android en méthode d’identification forte et sans mot de passe permettant aux utilisateurs de se connecter à toute plateforme ou tout navigateur en obtenant une notification sur leur téléphone, en faisant correspondre un numéro affiché sur l’écran à celui affiché sur le téléphone puis en utilisant leurs données biométriques (toucher ou visage) ou un code confidentiel pour confirmer.

## <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

Les clés de sécurité FIDO2 sont une méthode d’authentification sans mot de passe basée sur une norme. Elles ne peuvent pas être usurpées et peuvent s’afficher dans n’importe quel facteur de forme. Fast Identity Online (FIDO) est une norme ouverte d’authentification sans mot de passe. Elle permet aux utilisateurs et aux organisations de tirer parti de la norme pour se connecter à leurs ressources sans nom d’utilisateur ou mot de passe, à l’aide d’une clé de sécurité externe ou d’une clé de plateforme intégrée à un appareil.

Avec la préversion publique, les employés peuvent utiliser des clés de sécurité externes pour se connecter à leurs machines Windows 10 (version 1809 ou ultérieure) jointes à Azure Active Directory et bénéficier de l’authentification unique pour leurs ressources cloud. Ils peuvent également se connecter sur les navigateurs pris en charge.

![Connectez-vous à Microsoft Edge avec une clé de sécurité](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Bien qu’il existe de nombreuses clés FIDO2 certifiées par la FIDO Alliance, Microsoft exige que certaines des extensions facultatives de la spécification FIDO2 CTAP (Client-to-Authenticator Protocol) soient implémentées par le fournisseur, afin de garantir une sécurité maximale et la meilleure expérience possible.

Une clé de sécurité **doit** implémenter les fonctionnalités et extensions du protocole FIDO2 CTAP suivantes pour être compatible avec Microsoft :

| # | Fonctionnalité/Extension de confiance | Pourquoi cette fonctionnalité ou extension est-elle nécessaire ? |
| --- | --- | --- |
| 1 | Clé résidente | Cette fonctionnalité permet à la clé de sécurité d’être portable ; vos informations d’identification sont stockées sur la clé de sécurité. |
| 2 | Code confidentiel client | Cette fonctionnalité vous permet de protéger vos informations d’identification avec un deuxième facteur et s’applique aux clés de sécurité qui n’ont pas d’interface utilisateur. |
| 3 | hmac-secret | Cette extension garantit que vous pouvez vous connecter à votre appareil lorsqu’il est hors connexion ou en mode avion. |
| 4 | Plusieurs comptes par fournisseur de ressources | Cette fonctionnalité garantit que vous pouvez utiliser la même clé de sécurité dans plusieurs services, comme un compte Microsoft et Azure Active Directory. |

Les fournisseurs suivants offrent des clés de sécurité FIDO2 de différents facteurs de forme, qui sont connues pour être compatibles avec l’expérience sans mot de passe. Microsoft encourage les clients à évaluer les propriétés de sécurité de ces clés en contactant le fournisseur, ainsi que la FIDO Alliance.

| Fournisseur | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Si vous êtes un fournisseur et que vous voulez que votre appareil figure dans cette liste, contactez [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

Les clés de sécurité FIDO2 constituent une excellente solution pour les entreprises qui sont très sensibles à la sécurité ou ayant des scénarios ou des employés qui ne sont pas prêts à ou capables d’utiliser leur téléphone comme deuxième facteur.

## <a name="what-scenarios-work-with-the-preview"></a>Scénarios fonctionnant avec la préversion

- Les administrateurs peuvent activer des méthodes d’authentification sans mot de passe pour leur locataire
- Les administrateurs peuvent cibler tous les utilisateurs ou sélectionner des utilisateurs/groupes au sein de leur locataire pour chaque méthode
- Les utilisateurs finaux peuvent inscrire et gérer ces méthodes d’authentification sans mot de passe sur le portail de leur compte
- Les utilisateurs finaux peuvent se connecter avec ces méthodes d’authentification sans mot de passe
   - Application Microsoft Authenticator : Fonctionnera dans les scénarios dans lequel l’authentification Azure AD est utilisée, y compris sur tous les navigateurs, pendant la configuration de Windows 10 (OOBE) et avec les applications mobiles intégrées sur n’importe quel système d’exploitation.
   - Clés de sécurité : Fonctionnent sur l’écran de verrouillage Windows 10 version 1809 ou ultérieure et les navigateurs web pris en charge, comme Microsoft Edge.

## <a name="next-steps"></a>Étapes suivantes

[Activer les options de clé de sécurité FIDO2 sans mot de passe dans votre organisation](howto-authentication-passwordless-security-key.md)

[Activer les options sans mot de passe par téléphone dans votre organisation](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Liens externes

[FIDO Alliance](https://fidoalliance.org/)

[Spécification de FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
