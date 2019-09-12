---
title: Résoudre les problèmes liés aux vues des coûts d’entreprise Azure
description: Découvrez comment résoudre les problèmes que vous pourriez rencontrer avec les vues des coûts d’organisation dans le portail Azure.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491277"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Résoudre les problèmes liés aux vues des coûts d’entreprise

Dans les inscriptions d’entreprise, plusieurs paramètres peuvent empêcher les utilisateurs au sein de l’inscription d’afficher les coûts.  Ces paramètres sont gérés par l’administrateur d’inscription. Ou, si l’inscription n’est pas achetée directement via Microsoft, les paramètres sont gérés par le partenaire.  Cet article vous aide à comprendre quels sont les paramètres et dans quelle mesure ils impactent l’inscription. Ces paramètres sont indépendants des rôles de contrôle d’accès en fonction du rôle (RBAC) Azure.

## <a name="enable-access-to-costs"></a>Activer l’accès aux coûts

Voyez-vous un message « Non autorisé » ou *« Les vues des coûts sont désactivées dans votre inscription. »* quand vous recherchez des informations sur les coûts ?
![Capture d’écran affichant « non autorisé » dans le champ Coût actuel, pour l’abonnement.](media/billing-enterprise-mgmt-groups/unauthorized.png)

L’une des raisons suivantes peut en être la cause :

1. Vous avez acheté Azure par l’intermédiaire d’un partenaire d’entreprise qui n’a pas encore publié de tarifs. Contactez votre partenaire afin qu’il mette à jour le paramètre des tarifs dans [Enterprise portal](https://ea.azure.com).
2. Si vous êtes client EA Direct, les possibilités sont les suivantes :
    * Vous êtes propriétaire du compte et l’administrateur en charge de votre inscription a désactivé le paramètre **AO view charges** (Afficher les frais pour le propriétaire du compte).  
    * Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre **DA view charges** (Afficher les frais pour l’administrateur de service).
    * Contactez l’administrateur en charge de votre inscription pour obtenir l’accès. L’administrateur en charge de l’inscription peut mettre à jour les paramètres dans [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Capture d’écran montrant les paramètres d’Enterprise Portal pour l’affichage des frais.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Une ressource est indisponible

L’affichage du message d’erreur **La ressource n’est pas disponible** quand vous essayez d’accéder à un abonnement ou à un groupe d’administration signifie que vous n’avez pas le rôle approprié pour afficher cet élément.  

![Capture d’écran montrant le message qu’une « ressource n’est pas disponible ».](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Demandez l’accès à votre administrateur de l’abonnement ou du groupe d’administration Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide de RBAC et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes
- Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
