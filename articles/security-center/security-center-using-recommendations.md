---
title: Utiliser les recommandations Azure Security Center pour améliorer la sécurité | Microsoft Docs
description: " Découvrez comment utiliser les stratégies de sécurité et les recommandations d’Azure Security Center pour prévenir une attaque de sécurité. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 60bb1c3b81ef990993a2ce659a2b189c9d8a0eba
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967973"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Utiliser les recommandations Azure Security Center pour améliorer la sécurité
Vous pouvez réduire les risques d’un incident de sécurité en configurant une stratégie de sécurité, puis en implémentant les recommandations fournies par Azure Security Center. Cet article explique comment utiliser les stratégies de sécurité et les recommandations d'Azure Security Center pour prévenir une attaque de sécurité. 

Security Center s’exécute automatiquement des analyses en continu pour analyser l’état de sécurité de vos ressources Azure. Quand Security Center identifie des failles de sécurité potentielles, des recommandations sont créées. Celles-ci vous guident tout au long du processus de configuration des contrôles de sécurité nécessaires. Security Center met à jour ses recommandations dans les 24 heures, avec les exceptions suivantes :

- Recommandations de configuration de sécurité de système d’exploitation sont mis à jour dans les 48 heures
- Recommandations de problèmes de Protection de point de terminaison sont mis à jour dans les 8 heures

## <a name="scenario"></a>Scénario
Ce scénario vous montre comment utiliser Security Center pour réduire les risques d’incident en supervisant les recommandations de Security Center et en prenant les mesures nécessaires. Le scénario utilise la société fictive Contoso et les rôles présentés dans le [Guide de planification et de fonctionnement](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) de Security Center. Dans ce scénario, nous nous concentrons sur les rôles des personnages suivants :

![Rôles du scénario](./media/security-center-using-recommendations/scenario-roles.png)

Contoso a récemment migré certaines de ses ressources locales vers Azure. Contoso souhaite protéger ses ressources et réduire leur vulnérabilité dans le cloud.

## <a name="use-azure-security-center"></a>Utiliser Azure Security Center
David, de la sécurité informatique de Contoso, a déjà choisi d’intégrer Security Center aux abonnements à Azure Security Center de Contoso pour prévenir et détecter les failles de sécurité. 

Security Center analyse automatiquement l’état de sécurité des ressources Azure de Contoso avant d’appliquer les stratégies de sécurité par défaut. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des **recommandations** en fonction des contrôles définis dans la stratégie de sécurité. 

David exécute le niveau standard d’Azure Security sur tous ses abonnements pour obtenir l’intégralité des recommandations et fonctionnalités de sécurité disponibles. Jeff intègre également tous ses serveurs locaux existants qui n’ont pas encore migré vers le cloud pour pouvoir tirer parti de la prise en charge hybride de Security Center sur l’ensemble de ses serveurs [Windows](quick-onboard-windows-computer.md) et [Linux](quick-onboard-linux-computer.md).

Jeff est propriétaire d’une charge de travail cloud. Il est chargé d’appliquer des contrôles de sécurité conformément aux stratégies de sécurité de Contoso. 

Jeff effectue les tâches suivantes :

- Surveiller les recommandations de sécurité fournies par Security Center
- Évaluer les recommandations de sécurité et décider s’il doit les appliquer ou les ignorer
- Appliquer les recommandations de sécurité

### <a name="remediate-threats-using-recommendations"></a>Remédier aux menaces à l’aide des recommandations
Dans le cadre de ses activités de supervision quotidiennes, Jeff se connecte à Azure et ouvre Security Center. 

1. Jeff sélectionne les abonnements de sa charge de travail.

2. Jeff vérifie son **degré de sécurisation** pour savoir si les abonnements sont bien sécurisés et constate que celui-ci s’élève à 548.

3. Jeff doit déterminer quelles recommandations gérer en premier. Jeff clique donc sur le degré de sécurisation et commence à gérer les recommandations selon l’amélioration qu’elles apportent à l’[impact du degré de sécurisation](security-center-secure-score.md).

4. Étant donné que Jeff a un grand nombre de machines virtuelles et serveurs connectés, il décide de se concentrer sur **Compute et applications**.

5. Quand il clique sur **Compute et applications**, il voit la liste des recommandations et les gère en fonction de leur impact sur le degré de sécurisation.

6. Jeff a de nombreuses machines virtuelles accessibles sur Internet. Étant donné que leurs ports sont exposés, il craint qu’un attaquant ne prenne le contrôle des serveurs. Jeff choisit donc d’utiliser un (**accès aux machines virtuelles juste-à-temps**) [security-center-just-in-time.md].

Jeff continue de parcourir les recommandations de priorité élevée et moyenne et choisit de les appliquer ou pas. Pour chaque recommandation, il examine les informations détaillées fournies par Security Center pour se faire une idée des ressources impactées, de l’impact du degré de sécurisation, de la signification de chaque recommandation et des étapes à suivre pour atténuer chaque problème.

## <a name="conclusion"></a>Conclusion
Superviser les recommandations dans Security Center vous permet d’éliminer les failles de sécurité avant qu’une attaque ne se produise. Quand vous appliquez les recommandations, votre degré de sécurisation et la posture de sécurité de vos charges de travail s’améliorent. Security Center détecte automatiquement les nouvelles ressources que vous déployez, les évalue par rapport à votre stratégie de sécurité et fournit de nouvelles recommandations pour les sécuriser.


## <a name="next-steps"></a>Étapes suivantes
Vérifiez que vous avez un processus de supervision en place, qui vous permet de contrôler régulièrement les recommandations dans Security Center afin de garantir que vos ressources restent bien sécurisées au fil du temps.

Ce scénario vous a montré comment utiliser les stratégies de sécurité et les recommandations dans Security Center pour prévenir une attaque de sécurité. Reportez au [scénario de réponse aux incidents](security-center-incident-response.md) pour découvrir comment mettre en place un plan de réponse aux incidents avant qu’une attaque ne survienne.

Découvrez comment réagir aux menaces avec la fonctionnalité de [réponse aux incidents](security-center-incident-response.md).
