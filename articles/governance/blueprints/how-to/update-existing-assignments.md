---
title: Comment mettre à jour une affectation existante
description: Découvrez le mécanisme de mise à jour d’une affectation existante dans les blueprints Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ada0ccc1286677300c005da4fa0e92be94a3ca8
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314823"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Comment mettre à jour une affectation de blueprint existante

Quand un blueprint est affecté, l’affectation peut être mise à jour. Plusieurs raisons peuvent motiver la mise à jour d’une affectation existante, notamment quand il s’agit de :

- Ajouter ou supprimer un [verrouillage de ressource](../concepts/resource-locking.md)
- Modifier la valeur de [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters)
- Mettre à niveau l’affectation vers une version **publiée** plus récente du blueprint

## <a name="updating-assignments"></a>Mise à jour des affectations

1. Cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Blueprints affectés** dans la page de gauche.

1. Dans la liste des blueprints, cliquez sur l’affectation de blueprint. Cliquez ensuite sur le bouton **Mettre à jour l’affectation** OU cliquez avec le bouton droit sur l’affectation de blueprint, puis sélectionnez **Mettre à jour l’affectation**.

   ![Mettre à jour l’affectation](../media/update-existing-assignments/update-assignment.png)

1. La page **Affecter le blueprint** se charge préremplie avec toutes les valeurs de l’affectation d’origine. Vous pouvez modifier la **version de définition du blueprint**, l’état **Verrouiller l’affectation**, ainsi que tous les paramètres dynamiques qui existent dans la définition du blueprint. Cliquez sur **Affecter** une fois que vous avez terminé les modifications.

1. Dans la page de détails de l’affectation mise à jour, examinez le nouvel état. Dans cet exemple, nous avons ajouté **Verrouillage** à l’affectation.

   ![Affectation mise à jour - verrouillée](../media/update-existing-assignments/updated-assignment.png)

1. Examinez les détails des autres **Opérations d’affectation** via la liste déroulante. Le tableau **Ressources gérées** est mis à jour en fonction de l’opération d’affectation sélectionnée.

   ![Opérations d’affectation](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Règles de mise à jour des affectations

Le déploiement des affectations mises à jour obéit à quelques règles importantes. Ces règles déterminent les effets sur les ressources déjà déployées. La modification demandée et le type de la ressource d’artefact déployée ou mise à jour déterminent les actions entreprises.

- Affectations de rôles
  - Si le rôle ou son destinataire (utilisateur, groupe ou application) change, une nouvelle affectation de rôle est créée. Les affectations de rôles déployées précédemment sont maintenues en place.
- Affectations de stratégies
  - Si les paramètres de l’affectation de stratégie sont modifiés, l’affectation existante est mise à jour.
  - Si la définition de l’affectation de stratégie est modifiée, une autre affectation de stratégie est créée. Les affectations de stratégies déployées précédemment sont maintenues en place.
  - Si l’artefact d’affectation de stratégie est supprimé du blueprint, les affectations de stratégies déployées sont maintenues en place.
- Modèles Microsoft Azure Resource Manager
  - Le modèle est traité via Resource Manager en tant que **PUT**. Sachant que chaque type de ressource gère cette action différemment, consultez la documentation de chaque ressource incluse pour déterminer l’impact de cette action quand elle est exécutée par des blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Erreurs possibles pendant la mise à jour d’affectations

Pendant la mise à jour d’affectations, il est possible d’apporter des modifications qui sont rompues au moment de l’exécution. Cela peut se produire par exemple quand l’emplacement d’un groupe de ressources est modifié après avoir déjà été déployé. Il est possible d’apporter n’importe quelle modification prise en charge par [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), mais celles susceptibles d’entraîner une erreur via Azure Resource Manager entraîneront aussi l’échec de l’affectation.

Il n’existe aucune limite quant au nombre de fois qu’une affectation peut être mise à jour. Si une erreur se produit, déterminez la cause et apportez une autre mise à jour à l’affectation.  Exemples de scénarios d’erreur :

- Paramètre incorrect
- Objet déjà existant
- Modification non prise en charge par Azure Resource Manager

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md)
- Apprendre à personnaliser [l’ordre de séquencement des blueprints](../concepts/sequencing-order.md)
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md)
- Résoudre les problèmes qui se produisent durant l’affectation d’un blueprint avec la [résolution des problèmes d’ordre général](../troubleshoot/general.md)
