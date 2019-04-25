---
title: Opérations de Protection de mot de passe AD Azure et de création de rapports - Azure Active Directory
description: Opérations de post-déploiement de Protection de mot de passe AD Azure et création de rapports
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358178"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Protection par mot de passe Azure AD - Procédures opérationnelles

Au terme de l'[installation locale de la Protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-deploy.md), quelques éléments doivent être configurés sur le portail Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurer la liste des mots de passe interdits personnalisée

Suivez les instructions dans l’article [Configurer la liste des mots de passe interdits personnalisée](howto-password-ban-bad-configure.md) pour savoir comment personnaliser la liste des mots de passe interdits pour votre organisation.

## <a name="enable-password-protection"></a>Activer la Protection par mot de passe

1. Se connecter à la [Azure portal](https://portal.azure.com) et accédez à **Azure Active Directory**, **méthodes d’authentification**, puis **Protection de mot de passe**.
1. Définissez **Activer la Protection par mot de passe sur Windows Server Active Directory** sur**Oui**
1. Comme mentionné dans le [Guide de déploiement](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), il est recommandé de régler de base le **Mode** sur **Audit**
   * Une fois que vous êtes familiarisé avec la fonctionnalité, vous pouvez basculer le **Mode** sur **Appliqué**
1. Cliquez sur **Enregistrer**.

![Activation des composants de la Protection par mot de passe Azure AD sur le portail Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Mode Audit

Le mode Audit est conçu comme un moyen d’exécuter le logiciel dans un mode de « simulation ». Chaque service d’agent DC évalue un mot de passe entrant en fonction de la stratégie active. Si la stratégie actuelle est configurée pour être en mode Audit, les « mauvais » mots de passe entraînent l’apparition de messages dans le journal des événements, mais sont acceptés. C’est la seule différence entre le mode d’Audit et le mode Appliquer ; toutes les autres opérations s’exécuteront de la même manière.

> [!NOTE]
> Microsoft recommande que les déploiement et test initiaux soient toujours démarrés en mode Audit. Les événements dans le journal des événements doivent ensuite être surveillés pour tenter d’anticiper si un processus opérationnel existant serait dérangé une fois que le mode Appliquer est activé.

## <a name="enforce-mode"></a>Mode Appliquer

Le mode Appliquer est conçu comme la configuration finale. Comme dans le mode Audit, chaque service d’agent DC évalue les mots de passe entrant en fonction de la stratégie active. Cependant, si le mode Appliquer est activé, un mot de passe qui est considéré comme non sécurisé, en accord avec la stratégie sera rejeté.

Lorsqu'un mot de passe est refusé en mode Appliquer par l'agent DC de la Protection par mot de passe Azure AD, l'utilisateur final voit la même chose que si son mot de passe était rejeté par l'application en local de la règle traditionnelle de complexité du mot de passe. Par exemple, un utilisateur peut voir le message d’erreur traditionnel suivant sur l’écran de mot de passe logon\change Windows :

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ce message n’est qu’un seul exemple de plusieurs résultats possibles. Le message d’erreur spécifique peut varier selon le logiciel réel ou le scénario qui tente de définir un mot de passe non sécurisé.

Les utilisateurs finaux concernés devront peut-être travailler avec leur service informatique pour comprendre les nouvelles exigences et être plus en mesure de choisir des mots de passe sécurisés.

## <a name="enable-mode"></a>Mode d’activation

Ce paramètre doit normalement être laissé à son état par défaut (Oui). Si vous désactivez ce paramètre (Non), tous les agents DC de la Protection par mot de passe Azure AD déployés passeront en mode inactif : tous les mots de passe seront acceptés tels quels et aucune activité de validation ne sera exécutée (par exemple, aucun événement d'audit ne sera émis).

## <a name="next-steps"></a>Étapes suivantes

[Supervision de la Protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-monitor.md)
