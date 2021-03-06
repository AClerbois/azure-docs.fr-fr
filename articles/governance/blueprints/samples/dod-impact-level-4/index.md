---
title: Vue d’ensemble de l’exemple de blueprint DoD Impact Level 4
description: Vue d’ensemble de l’exemple DoD Impact Level 4. Cet exemple de blueprint permet aux clients d’évaluer des contrôles DoD Impact Level 4 spécifiques.
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: 1fbcbbf1ca12e9cb235a79ace0e7de82c3c8cbcc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978373"
---
# <a name="overview-of-the-dod-impact-level-4-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint DoD Impact Level 4

L’exemple de blueprint Department of Defense Impact Level 4 (DoD IL4) fournit des garde-fous de gouvernance avec [Azure Policy](../../../policy/overview.md), qui vous permettent d’évaluer des contrôles DoD Impact Level 4 spécifiques. Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies pour toute architecture déployée par Azure devant implémenter des contrôles DoD Impact Level 4. Pour obtenir les dernières informations sur les clouds et services Azure qui satisfont l’autorisation DoD Impact Level 4, consultez [Services Azure par étendue d’audit du FedRAMP et du DoD CC SRG](../../../../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

> [!NOTE]
> Cet exemple de blueprint est disponible dans Azure Government.

## <a name="control-mapping"></a>Correspondance des contrôles

La section sur la correspondance des contrôles fournit des détails sur les stratégies incluses dans ce blueprint et décrit comment ces stratégies permettent de répondre aux différents contrôles dans DoD Impact Level 4. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>Étapes suivantes

Vous venez de parcourir une vue d’ensemble de l’exemple de blueprint DoD Impact Level 4. Continuez avec les articles suivants pour découvrir les correspondances de contrôles et la manière de déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint DoD Impact Level 4 - Correspondance des contrôles](./control-mapping.md)
> [Blueprint DoD Impact Level 4 - Déployer les étapes](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).