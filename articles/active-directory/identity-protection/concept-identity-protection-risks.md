---
title: Quel est le risque ? Azure AD Identity Protection
description: Explication du risque dans Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd0b562ae0570917c9127e301964d089d49b087
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886720"
---
# <a name="what-is-risk"></a>Quel est le risque ?

Les détections de risques dans Azure AD Identity Protection incluent toutes les actions suspectes identifiées liées aux comptes d’utilisateur dans l’annuaire.

Identity Protection offre aux organisations un accès à des ressources puissantes pour voir et traiter rapidement ces actions suspectes. 

![Vue d’ensemble de la sécurité montrant les utilisateurs et les connexions à risque](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Types de risques et détection

Il existe deux types d’**utilisateurs** et de **connexions** à risque, ainsi que deux types de détections ou de calculs **en temps réel** et **hors connexion**.

### <a name="user-risk"></a>Risque de l’utilisateur

Un risque utilisateur reflète la probabilité qu’une identité ou un compte donné soit compromis. 

Ces risques sont calculés hors connexion à l’aide de sources d’informations sur les menaces internes et externes de Microsoft, dont des chercheurs en sécurité, des professionnels de la justice, des équipes de sécurité de Microsoft et d’autres sources approuvées.

| Détection d’événements à risque | Description |
| --- | --- |
| Informations d’identification divulguées | Ce type de détection d’événement à risque indique que les informations d’identification valides de l’utilisateur ont fuité. Souvent, lorsque les cybercriminels compromettent les mots de passe valides d’utilisateurs légitimes, ils le font dans le but de les rendre publics. Ce partage se fait généralement en publiant publiquement sur le « dark web », via des sites de pastebin, ou en échangeant et vendant des informations d’identification sur le marché noir. Lorsque le service d’informations de connexion fuitées de Microsoft acquiert des informations d’identification utilisateur sur le dark web, des ou d’autres sources, ces informations sont comparées aux informations d’identification valides actuelles des utilisateurs d’Azure AD pour rechercher des correspondances valides. |
| Azure AD Threat Intelligence | Ce type de détection d’événement à risque indique une activité utilisateur inhabituelle pour l’utilisateur donné ou qui est cohérente avec des modèles d’attaque connus selon les sources internes et externes de Microsoft Threat Intelligence. |

### <a name="sign-in-risk"></a>Risque à la connexion

Un risque de connexion reflète la probabilité qu'une demande d'authentification donnée soit rejetée par le propriétaire de l'identité. 

Ces risques peuvent être calculés en temps réel ou hors connexion à l’aide de sources d’informations sur les menaces internes et externes de Microsoft, dont des chercheurs en sécurité, des professionnels de la justice, des équipes de sécurité de Microsoft et d’autres sources approuvées.

| Détection d’événements à risque | Type de détection | Description |
| --- | --- | --- |
| Adresse IP anonyme | Temps réel | Ce type de détection des risque détecte des connexions à partir d’adresses IP anonymes (par exemple, navigateur Tor VPN anonyme). Ces adresses IP sont généralement utilisées par des acteurs souhaitant masquer leur télémétrie de connexion (adresse IP, emplacement, appareil, etc.) dans un but potentiellement malveillant. |
| Voyage inhabituel | Hors ligne | Ce type de détection d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. Entre autres facteurs, cet algorithme Machine Learning prend en compte le délai écoulé entre les deux connexions et le temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification. <br><br> L’algorithme ignore les « faux positifs » évidents contribuant aux conditions de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale la plus proche de 14 jours ou de 10 connexions lui servant à assimiler le comportement de connexion des nouveaux utilisateurs. |
| Adresse IP liée à un programme malveillant | Hors ligne | Ce type de détection d’événement à risque indique les connexions depuis des adresses IP infectées par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur bot, Cette détection est effectuée en mettant en corrélation des adresses IP d’appareils d’utilisateurs avec des adresses ayant été en contact avec un serveur robot actif. |
| Propriétés de connexion inhabituelles | Temps réel | Ce type de détection d’événement à risque prend en compte l’historique des connexions antérieures (IP, latitude / longitude et NSA) pour rechercher des connexions anormales. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». La détection d’événement à risque est déclenchée quand la connexion a lieu depuis un emplacement qui ne figure pas déjà dans la liste des emplacements connus. Les utilisateurs nouvellement créés seront en « mode d’apprentissage » pendant une période de temps durant laquelle les détections d’événements à risque des propriétés de connexion inconnues seront désactivées alors que nos algorithmes apprennent le comportement de l’utilisateur. La durée du mode d’apprentissage est dynamique et varie selon le temps qu’il faut à l’algorithme pour collecter suffisamment d’informations sur les modèles de connexion de l’utilisateur. La durée minimale est de 5 jours. Un utilisateur peut revenir en mode d’apprentissage après une longue période d’inactivité. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. <br><br> Nous exécutons également cette détection pour l’authentification de base (ou les protocoles existants). Étant donné que ces protocoles ne proposent pas les propriétés modernes, telles que l’ID client, les données de télémétrie pour réduire le nombre de faux positifs sont limitées. Nous recommandons à nos clients de passer à l’authentification moderne. |
| L’administrateur a confirmé que cet utilisateur est compromis | Hors ligne | Cette détection indique qu’un administrateur a sélectionné « Confirmer que l’utilisateur est compromis » dans l’interface utilisateur Utilisateurs à risque ou à l’aide de l’API riskyUsers. Pour voir quel administrateur a confirmé que cet utilisateur est compromis, consultez l’historique des risques de l’utilisateur (par le biais de l’interface utilisateur ou de l’API). |
| Adresse IP malveillante | Hors ligne | Cette détection indique une connexion à partir d’une adresse IP malveillante. Une adresse IP est considérée comme malveillante quand elle présente un taux d’échecs élevé en raison d’informations d’identification non valides qu’elle envoie ou d’autres sources relatives à la réputation des adresses IP. |

### <a name="other-risk-detections"></a>Autres détections de risques

| Détection d’événements à risque | Type de détection | Description |
| --- | --- | --- |
| Risque supplémentaire détecté | Temps réel ou hors connexion | Cette détection indique que l’une des détections Premium ci-dessus a eu lieu. Étant donné que les détections Premium ne sont visibles que pour les clients Azure AD Premium P2, on les appelle « Risque supplémentaire détecté » pour les clients dépourvus de licences Azure AD Premium P2. |

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies disponibles pour atténuer les risques](concept-identity-protection-policies.md)

- [vue d’ensemble de la sécurité](concept-identity-protection-security-overview.md)
