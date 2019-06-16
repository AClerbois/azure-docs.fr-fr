---
title: Rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory | Microsoft Docs
description: En savoir plus sur le rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438129"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Rapport sur les utilisateurs associés à un indicateur de risque dans le portail Azure

Azure Active Directory (Azure AD) détecte les actions suspectes liées à vos comptes d’utilisateurs. Pour chaque action détectée, un enregistrement appelé [événement à risque](concept-risk-events.md) est créé.

Vous pouvez accéder aux rapports de sécurité depuis le [portail Azure](https://portal.azure.com) en sélectionnant le panneau **Azure Active Directory** et en accédant à la section **Sécurité**. 

Les événements à risque détectés sont utilisés pour déterminer les valeurs suivantes :

- **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. 

- **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. 

Pour savoir comment configurer les stratégies qui déclenchent ces événements à risque, consultez [Guide pratique pour configurer la stratégie sur les risques liés à l’utilisateur](../identity-protection/howto-user-risk-policy.md). 

![les connexions risquées.](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>De quelle licence Azure AD avez-vous besoin pour accéder au rapport des utilisateurs courant un risque ?  

Toutes les éditions d’Azure Active Directory vous indiquent les rapports d’utilisateurs associés à un indicateur de risque. Toutefois, le niveau de granularité d’un rapport varie entre les éditions : 

- Dans les **éditions Azure Active Directory Free et Basic**, vous obtenez la liste des utilisateurs associés à un indicateur de risque. 

- L’édition **Azure Active Directory Premium 1** vous permet également d’examiner certains événements à risque sous-jacent, qui ont été détectés pour chaque rapport. 

- L’édition **Azure Active Directory Premium 2** vous fournit les informations les plus détaillées sur tous les événements à risque sous-jacent. Elle vous permet également de configurer des stratégies de sécurité répondant automatiquement aux niveaux de risque configurés.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Rapport sur les utilisateurs courant un risque pour les éditions Azure AD Free et Basic

Le rapport sur les utilisateurs associés à un indicateur de risque dans les éditions gratuite et de base d’Azure AD vous fournit une liste de comptes d’utilisateurs dont la sécurité a peut-être été compromise. 

![les connexions risquées.](./media/concept-user-at-risk/03.png)

La sélection d’un utilisateur fournit des informations de connexion. Vous pouvez vérifier l’historique des connexions d’un utilisateur à risque et réinitialiser son mot de passe, le cas échéant.

Cette boîte de dialogue fournit une option permettant de :

- Télécharger le rapport
- Rechercher des utilisateurs

    ![les connexions risquées.](./media/concept-user-at-risk/16.png)

Pour obtenir des informations plus détaillées, vous avez besoin d’une licence Premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Rapport sur les utilisateurs courant un risque pour les éditions Azure AD Premium

Le rapport sur les utilisateurs avec indicateur de risque dans les éditions premium d’Azure AD vous fournit les informations suivantes :

- Une liste des comptes d’utilisateur qui sont peut-être compromis 

- Informations agrégées sur les [types d’événements à risque](concept-risk-events.md) qui ont été détectés

- Une option pour télécharger le rapport

- Une option pour configurer une [stratégie de résolution du risque utilisateur](../identity-protection/howto-user-risk-policy.md)  

![les connexions risquées.](./media/concept-user-at-risk/71.png)

Lorsque vous sélectionnez un utilisateur, vous obtenez une vue de rapport détaillé pour cet utilisateur qui vous permet d’effectuer les opérations suivantes :

- Ouvrir la vue Toutes les connexions

- Réinitialisez le mot de passe de l'utilisateur.

- Ignorer tous les événements

- Analysez les événements à risque signalés pour l’utilisateur. 

![les connexions risquées.](./media/concept-user-at-risk/324.png)

Pour analyser un événement à risque, sélectionnez-en un depuis la liste pour ouvrir le panneau **Détails** de cet événement à risque. Dans le panneau **Détails**, vous avez le choix entre fermer manuellement un événement à risque ou réactiver un événement à risque fermé manuellement. 

![les connexions risquées.](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour configurer la stratégie d'utilisateur à risque](../identity-protection/howto-user-risk-policy.md)
- [Guide pratique pour configurer la stratégie de remédiation des risques](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

