---
title: Résolution des erreurs courantes
description: Découvrez comment résoudre les problèmes de création, d’affectation et de suppression des blueprints.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42fdd6645a7a0e7cd9a2f0a7bc969e8eee62758c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874958"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Résoudre les erreurs à l’aide de blueprints Azure

Vous pouvez rencontrer des erreurs lors de la création ou de l’affectation des blueprints. Cet article décrit différentes erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="finding-error-details"></a>Recherche des détails de l’erreur

De nombreuses erreurs sont dues à l’affectation d’un blueprint à une étendue. Quand une affectation échoue, le blueprint fournit des détails sur le déploiement ayant échoué. Ces informations précisent le problème afin que vous puissiez le résoudre et que le déploiement suivant réussisse.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez **affecté plans** à partir de la page sur la gauche et l’utilisation de la zone de recherche pour filtrer les affectations de plan pour rechercher l’attribution a échoué. Vous pouvez également trier la table des affectations d’après la colonne **État de l’approvisionnement** pour regrouper toutes les affectations ayant échoué.

1. Clic gauche sur le plan avec le _échec_ état ou avec le bouton droit et sélectionnez **afficher les détails de l’affectation**.

1. Une bannière rouge signalant que l’affectation a échoué figure en haut de la page d’affectation de blueprint. Cliquez n’importe où sur la bannière pour obtenir plus de détails.

Il est courant que l’erreur soit due à un artefact et non au blueprint lui-même. Si un artefact crée un coffre de clés, mais que la stratégie Azure empêche la création du coffre de clés, l’affectation entière échoue.

## <a name="general-errors"></a>Erreurs générales.

### <a name="policy-violation"></a>Scénario : Violation de stratégie

#### <a name="issue"></a>Problème

Le déploiement du modèle a échoué à cause de la violation de stratégie.

#### <a name="cause"></a>Cause :

Une stratégie peut entrer en conflit avec le déploiement pour plusieurs raisons :

- La ressource en cours de création est limitée par la stratégie (généralement des restrictions de référence SKU ou d’emplacement)
- Le déploiement définit des champs qui sont configurés par la stratégie (courant avec les étiquettes)

#### <a name="resolution"></a>Résolution :

Modifiez le blueprint pour qu’il ne soit pas en conflit avec les stratégies indiquées dans les détails de l’erreur. Si cette modification n’est pas possible, une autre option consiste à changer l’étendue de l’affectation de stratégie de sorte que le blueprint ne soit plus en conflit avec la stratégie.

### <a name="escape-function-parameter"></a>Scénario : le paramètre de blueprint est une fonction

#### <a name="issue"></a>Problème

Les paramètres de blueprint qui sont des fonctions sont traitées avant d’être transmises aux artefacts.

#### <a name="cause"></a>Cause :

En passant un paramètre de blueprint qui utilise une fonction, comme `[resourceGroup().tags.myTag]`, à un artefact fait que le résultat traité de la fonction est défini sur l’artefact et non sur la fonction dynamique.

#### <a name="resolution"></a>Résolution :

Pour passer une fonction en tant que paramètre, ajoutez un caractère d’échappement devant toute la chaîne avec `[` pour que le paramètre de blueprint se présente sous la forme `[[resourceGroup().tags.myTag]`. Le caractère d’échappement fait que les blueprints traitent la valeur comme une chaîne lors du traitement du blueprint. Le blueprint place ensuite la fonction sur l’artefact, ce qui lui permet d’être dynamique comme prévu. Pour plus d’informations, consultez [la structure de fichier de modèle - syntaxe](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses d’experts Azure via [Forums Azure](https://azure.microsoft.com/support/forums/).
- Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
- Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.