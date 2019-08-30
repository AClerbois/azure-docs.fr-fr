---
title: Labs de salle de classe dans Azure Lab Services - FAQ | Microsoft Docs
description: Trouvez des réponses aux questions courantes sur les labs de salle de classe dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648573"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Labs de salle de classe dans Azure Lab Services - Forum aux questions (FAQ)
Obtenez des réponses aux questions les plus fréquemment posées sur les labs de salle de classe dans Azure Lab Services. 

## <a name="quotas"></a>Quotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Le quota est-il appliqué par utilisateur, par semaine ou pour toute la durée du lab ? 
Le quota que vous définissez pour un lab s’applique à chaque étudiant pour toute la durée du lab. Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un utilisateur. Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées.  Pour plus d’informations sur les quotas, consultez [Définir des quotas pour les utilisateurs](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Planifications

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Toutes les machines virtuelles du lab démarrent-elles automatiquement quand un calendrier est défini ? 
Non. Cela ne concerne pas toutes les machines virtuelles, mais uniquement celles qui sont attribuées à des utilisateurs selon un calendrier. Les machines virtuelles qui ne sont pas attribuées à un utilisateur ne démarrent pas automatiquement. Ce comportement est normal. 

## <a name="lab-accounts"></a>Comptes lab

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Pourquoi ne puis-je pas créer un lab en cas d’indisponibilité de la plage d’adresses ? 
Les labs de salle de classe peuvent créer des machines virtuelles de lab dans une plage d’adresses IP que vous spécifiez lors de la création de votre compte lab dans le Portail Azure. Quand une plage d’adresses est fournie, 512 adresses IP sont allouées à chaque lab créé par la suite pour les machines virtuelles de lab. La plage d’adresses du compte lab doit être suffisamment étendue pour prendre en charge tous les labs que vous envisagez de créer sous le compte lab. 

Par exemple, si vous avez un bloc de /19 - 10.0.0.0/19, cette plage d’adresses prend en charge 8 192 adresses IP et 16 labs (8 192/512 = 16 labs). Dans ce cas, la création du 17e lab échouera.

## <a name="blog-post"></a>Billet de blog
Abonnez-vous au [blog Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notifications de mise à jour
Abonnez-vous aux [mises à jour de Lab Services](https://azure.microsoft.com/updates/?product=lab-services) pour rester informé des nouvelles fonctionnalités de Lab Services.

## <a name="general"></a>Généralités
### <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?
Si votre question n’est pas répertoriée ici, faites-le-nous savoir pour que nous puissions vous aider à trouver une réponse.

- Postez une question à la fin de ce Forum aux questions. 
- Pour atteindre un public plus large, postez une question sur le [forum Stack Overflow consacré à Azure Lab Services](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Concernant les demandes de fonctionnalité, transmettez vos questions et idées à [Azure Lab Services - User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

