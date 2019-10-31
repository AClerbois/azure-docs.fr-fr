---
title: Planifier des tâches pour gérer les données contiguës - Azure Logic Apps
description: Créer et exécuter des tâches récurrentes qui gèrent les données contiguës à l’aide de fenêtres glissantes dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0312d9480d00d4430cd5d42dc22ef9dac005ee2e
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679062"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Planifier et exécuter des tâches pour les données contiguës à l’aide du déclencheur Fenêtre glissante dans Azure Logic Apps

Pour exécuter régulièrement des tâches, processus ou travaux devant gérer des données dans des blocs contigus, vous pouvez démarrer votre workflow d’application logique avec le déclencheur **Fenêtre glissante**. Vous pouvez définir une date et une heure, ainsi qu’un fuseau horaire, pour démarrer le flux de travail, et une récurrence pour la répétition de ce flux de travail. Si des périodicités sont manquantes pour une raison quelconque, ce déclencheur les traite. Par exemple, lors de la synchronisation de données entre votre base de données et le stockage de sauvegarde, utilisez le déclencheur Fenêtre glissante afin que les données soient synchronisées sans que cela génère des écarts. Pour plus d’informations sur les déclencheurs et actions de Planification intégrés, voir [Planifier et exécuter des tâches et des workflows automatisés et récurrents avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Voici quelques modèles que ce déclencheur prend en charge :

* Exécuter immédiatement et répéter toutes les *n* secondes, minutes ou heures.

* Démarrer à une date et une heure spécifiques, exécuter immédiatement et répéter toutes les *n* secondes, minutes ou heures. Avec ce déclencheur, vous pouvez spécifier une heure de début dans le passé, qui exécute toutes les périodicités passées.

* Retarder chaque périodicité pendant une durée spécifique avant de l’exécuter.

Pour connaître les différences entre ce déclencheur et le déclencheur Périodicité, ou pour plus d’informations sur la planification des flux de travail récurrents, voir [Planifier et exécuter des tâches, processus et workflows automatisés et récurrents avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Pour déclencher votre application logique et l’exécuter une seule fois, voir [Exécuter des travaux une seule fois](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Connaissance de base sur les [applications logiques](../logic-apps/logic-apps-overview.md). Si vous débutez avec les applications logiques, découvrez [comment créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Ajouter un déclencheur Fenêtre glissante

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Créez une application logique vide.

1. Une fois que le concepteur Logic Apps s’affiche, entrez « fenêtre glissante » comme filtre dans la zone de recherche. Dans la liste des déclencheurs, sélectionnez ce déclencheur pour la première étape dans votre workflow d’application logique : **Fenêtre glissante**

   ![Sélectionner le déclencheur « Fenêtre glissante »](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Définissez l’intervalle et la fréquence de la périodicité. Dans cet exemple, définissez ces propriétés de sorte que votre flux de travail soit exécuté chaque semaine.

   ![Définir l’intervalle et la fréquence](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriété | Obligatoire | Nom JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Intervalle** | OUI | interval | Integer | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence. Les intervalles minimaux et maximaux sont les suivants : <p>- Heure : 1-12 000 heures </br>- Minute : 1-72 000 minutes </br>- Seconde : 1-9 999 999 secondes<p>Par exemple, si l’intervalle est défini sur 6 et la fréquence sur « Heure », la périodicité est toutes les 6 heures. |
   | **Fréquence** | OUI | frequency | Chaîne | Unité de temps à utiliser pour la récurrence : **Seconde**, **Minute** ou **Heure** |
   ||||||

   ![Options de périodicité avancées](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Pour plus d’options de périodicité, ouvrez la liste **Ajouter un nouveau paramètre**. 
   Toutes les options que vous sélectionnez s’affichent sur le déclencheur.

   | Propriété | Obligatoire | Nom JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Retard** | Non | delay | Chaîne | Valeur de délai pour retarder chaque périodicité à l’aide de la [spécification date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuseau horaire** | Non | timeZone | Chaîne | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Sélectionnez le fuseau horaire à appliquer. |
   | **Heure de début** | Non | startTime | Chaîne | Indiquez une date et une heure de début au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous sélectionnez un fuseau horaire <p>-ou- <p>AAAA-MM-JJThh:mm:ssZ si vous ne sélectionnez pas de fuseau horaire <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14h00, alors spécifiez « 2017-09-18T14:00:00 » et sélectionnez un fuseau horaire tel que « Pacific Standard Time » (Heure standard du Pacifique). Vous pouvez également spécifier « 2017-09-18T14:00:00Z » sans fuseau horaire. <p>**Remarque :** Cette heure de début doit être conforme à la [spécification de date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) au [format de date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Si vous ne sélectionnez pas de fuseau horaire, vous devez ajouter la lettre « Z » à la fin sans espace. Ce « Z » fait référence au [temps nautique](https://en.wikipedia.org/wiki/Nautical_time) équivalent. <p>Pour les planifications simples, l’heure de début est la première occurrence, tandis que, pour les périodicités complexes, le déclencheur ne s’active pas avant l’heure de début. [*Comment puis-je utiliser la date et l’heure de début ?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Créez maintenant votre flux de travail restant avec d’autres actions. Pour connaître les autres actions que vous pouvez ajouter, voir [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Définition de workflow – Fenêtre glissante

Dans la définition de workflow sous-jacente de votre application logique, qui utilise JSON, vous pouvez afficher la définition de déclencheur Fenêtre glissante avec les options que vous avez choisies. Pour afficher cette définition, dans la barre d’outils Concepteur, choisissez **Mode Code**. Pour revenir au Concepteur, dans la barre d’outils Concepteur, choisissez **Concepteur**.

Cet exemple montre à quoi une définition de déclencheur Fenêtre coulissante peut ressembler dans une définition de workflow sous-jacente dans laquelle le délai pour chaque périodicité est de cinq secondes pour une périodicité horaire :

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Retarder l’action suivante dans des workflows](../connectors/connectors-native-delay.md)
* [Connecteurs pour Logic Apps](../connectors/apis-list.md)