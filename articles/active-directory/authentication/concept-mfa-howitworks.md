---
title: Microsoft Azure Multi-Factor Authentication - Fonctionnement - Azure Active Directory
description: Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d4f19c16149dbed34c16d021ff7100cace9a06a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879143"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Fonctionnement : Azure Multi-Factor Authentication

La sécurité de la vérification en deux étapes repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l’utilisateur, celui-ci lui est inutile sans posséder la méthode d’authentification supplémentaire. Le principe fonctionne sur l’imposition au minimum de deux des méthodes d’authentification suivantes :

* Un élément que vous connaissez (généralement un mot de passe)
* Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
* Un élément vous concernant particulièrement (biométrie)

<center>

![Image conceptuelle des méthodes d’authentification](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) participe à la sécurisation de l’accès aux données et aux applications tout en maintenant une simplicité de gestion pour les utilisateurs. MFA fournit une sécurité supplémentaire en exigeant une deuxième forme d’authentification, et procure une authentification renforcée par le biais d’un éventail de [méthodes d’authentification](concept-authentication-methods.md) faciles à utiliser. Les utilisateurs peuvent devoir s'authentifier via MFA selon les choix de configuration de l'administrateur.

## <a name="how-to-get-multi-factor-authentication"></a>Comment obtenir Multi-Factor Authentication ?

Le service Multi-Factor Authentication est fourni avec les offres suivantes :

* **Azure Active Directory Premium** ou **Microsoft 365 Business** : utilisation complète d’Azure Multi-Factor Authentication basé sur des stratégies d’accès conditionnel pour exiger une authentification multifacteur.

* Licences **Azure AD Free** ou **Office 365** autonomes : utilisez des [stratégies de protection de base d’accès conditionnel](../conditional-access/concept-baseline-protection.md) pour exiger une authentification multifacteur pour vos utilisateurs et administrateurs.

* **Administrateurs généraux Azure Active Directory** - Une sélection de fonctionnalités Azure Multi-Factor Authentication disponibles comme moyen de protection des comptes d’administrateur général.

> [!NOTE]
> De nouveaux clients ne pourront peut-être plus acheter une authentification multifacteur Azure en tant qu’offre autonome à partir du 1er septembre 2018. L’authentification multifacteur restera une fonctionnalité disponible dans les licences Azure AD Premium.

## <a name="supportability"></a>Prise en charge

Dans la mesure où la plupart des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre organisation informe l’ensemble des utilisateurs sur ce processus. Le fait d’en être averti peut réduire la probabilité que les utilisateurs appellent votre support technique pour des problèmes mineurs liés à l’authentification MFA. Toutefois, pour certains scénarios, il est nécessaire de désactiver provisoirement l’authentification Multifacteur. Suivez les indications suivantes pour comprendre comment gérer ces scénarios :

* Formez le personnel de votre support technique à la gestion de scénarios dans lesquels l’utilisateur ne parvient pas à se connecter, car il n’a pas accès à ses méthodes d’authentification, ou parce que celles-ci ne fonctionnent pas correctement.
   * À l’aide des stratégies d’accès conditionnel pour le service Azure MFA, votre personnel du support technique peut ajouter un utilisateur à un groupe qui est exclu d’une stratégie nécessitant MFA.
* Pensez à utiliser des emplacements nommés à l’aide de l’accès conditionnel comme moyen de réduire les invites de vérification en deux étapes. Avec cette fonctionnalité, les administrateurs peuvent contourner la vérification en deux étapes des utilisateurs qui se connectent à partir d’un emplacement réseau approuvé sécurisé tel qu’un segment réseau utilisé pour l’intégration de nouveaux utilisateurs.
* Déployez [Azure AD Identity Protection](../active-directory-identityprotection.md) et déclenchez la vérification en deux étapes en fonction des événements à risque.

## <a name="next-steps"></a>Étapes suivantes

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md) (Déploiement pas à pas de Microsoft Azure Multi-Factor Authentication)
