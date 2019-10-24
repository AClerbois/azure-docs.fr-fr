---
title: Utiliser la méthode d’évaluation hors connexion - Personalizer
titleSuffix: Azure Cognitive Services
description: Créez une boucle de rétroaction dans ce guide de démarrage rapide C# avec le service Personalizer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f382c07dea75cb534632061c986dd8044c4aafa9
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515574"
---
# <a name="offline-evaluation"></a>Évaluation hors connexion

L’évaluation hors connexion est une méthode qui vous permet de tester et d’évaluer l’efficacité du service Personalizer sans changer votre code ou affecter l’expérience utilisateur. L’évaluation hors connexion utilise des données du passé, envoyées depuis votre application vers l’API de classement, pour comparer les performances des différents classements.

L’évaluation hors connexion est effectuée sur une plage de dates. La plage peut se terminer au plus tard à la date/heure actuelle. Le début de la plage ne peut pas être supérieur au nombre de jours spécifié pour la [conservation des données](how-to-settings.md).

L’évaluation hors connexion peut vous aider à répondre aux questions suivantes :

* Dans quelle mesure les classements de Personalizer sont-ils efficaces pour la réussite de la personnalisation ?
    * Quelles sont les récompenses moyennes obtenues par la stratégie de machine learning en ligne de Personalizer ?
    * Comment Personalizer se compare à l’efficacité de ce que l’application aurait fait par défaut ?
    * Quelle aurait été l’efficacité comparée d’un choix aléatoire pour la personnalisation ?
    * Quelle aurait été l’efficacité comparée de stratégies d’apprentissage différentes spécifiées manuellement ?
* Quelles caractéristiques du contexte contribuent le plus ou le moins à la réussite de la personnalisation ?
* Quelles caractéristiques des actions contribuent le plus ou le moins à la réussite de la personnalisation ?

De plus, l’évaluation hors connexion peut être utilisée pour découvrir des stratégies d’apprentissage plus optimisées, qui peuvent être utilisées par Personalizer pour améliorer les résultats.

Les évaluations hors connexion ne fournissent pas de conseils quant au pourcentage d’événements à utiliser pour l’exploration.

## <a name="prerequisites-for-offline-evaluation"></a>Prérequis pour l’évaluation hors connexion

Voici des considérations importantes pour une évaluation hors connexion représentative :

* Disposez de suffisamment de données. Le minimum recommandé est d’au moins 50 000 événements.
* Collectez des données pendant des périodes où le comportement des utilisateurs et le trafic sont représentatifs.

## <a name="discovering-the-optimized-learning-policy"></a>Découverte de la stratégie d’apprentissage optimisée

Personalizer peut utiliser le processus d’évaluation hors connexion pour découvrir automatiquement une stratégie d’apprentissage plus optimale.

Après avoir effectué l’évaluation hors connexion, vous pouvez voir l’efficacité comparée de Personalizer avec cette nouvelle stratégie par rapport à la stratégie en ligne en cours. Vous pouvez ensuite appliquer la stratégie d’apprentissage pour la rendre effective immédiatement dans Personalizer, en la téléchargeant et en la chargeant dans le panneau Modèles et stratégie. Vous pouvez également la télécharger en vue d’une analyse ou d’une utilisation ultérieure.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Comprendre la pertinence des résultats de l’évaluation hors connexion

Quand vous exécutez une évaluation hors connexion, il est très important d’analyser l’_intervalle de confiance_ des résultats. S’il est étendu, cela signifie que votre application n’a pas reçu suffisamment de données pour que les estimations de la récompense soient précises ou significatives. Plus le système accumule des données et plus les périodes sur lesquelles vous exécutez des évaluations hors connexion sont longues, plus les intervalles de confiance deviennent étroits.

## <a name="how-offline-evaluations-are-done"></a>Comment les évaluations hors connexion sont effectuées

Les évaluations hors connexion sont effectuées selon une méthode appelée **évaluation contrefactuelle**. 

Personalizer repose sur l’hypothèse que le comportement des utilisateurs (et donc les récompenses) sont impossibles à prédire rétrospectivement (Personalizer ne peut pas savoir ce qui se serait passé si quelque chose d’autre que ce que l’utilisateur a vu lui avait été montré) et seulement pour apprendre à partir de récompenses mesurées. 

Voici le processus conceptuel utilisé pour les évaluations :

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

L’évaluation hors connexion utilise seulement le comportement des utilisateurs observés. Ce processus ignore les grands volumes de données, en particulier si votre application effectue des appels de classement avec un grand nombre d’actions.


## <a name="evaluation-of-features"></a>Évaluation des caractéristiques

Les évaluations hors connexion peuvent fournir des informations sur la quantité de caractéristiques spécifiques pour les actions ou le contexte qui comptent pour des récompenses plus importantes. Les informations sont calculées en utilisant l’évaluation sur la période de temps et les données spécifiées, et elles peuvent varier avec le temps.

Nous vous recommandons d’examiner les évaluations des caractéristiques et de vous poser les questions suivantes :

* Quelles autres caractéristiques supplémentaires pourrait fournir votre application ou votre système en plus de celles qui sont plus efficaces ?
* Quelles caractéristiques peuvent être supprimées en raison de leur faible efficacité ? Les caractéristiques à faible efficacité ajoutent du _bruit_ dans le machine learning.
* Des caractéristiques sont incluses accidentellement? En voici des exemples : informations d’identification personnelle (PII), ID en double, etc.
* Existe-t-il des caractéristiques indésirables qui ne doivent pas être utilisées pour la personnalisation en raison de réglementations ou de considérations sur une utilisation responsable ? Existe-t-il des caractéristiques qui pourraient suppléer (c’est-à-dire refléter de façon proche ou être en corrélation avec) des caractéristiques indésirables ?


## <a name="next-steps"></a>Étapes suivantes

[Configurer Personalizer](how-to-settings.md)
[Exécuter des évaluations hors connexion](how-to-offline-evaluation.md) Comprendre le [Fonctionnement de Personalizer](how-personalizer-works.md)
