---
title: Tester des requêtes dans Azure Stream Analytics
description: Cet article décrit comment tester des requêtes à l’aide d’exemples de fichiers de données dans des tâches Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Tester les requêtes Azure Stream Analytics dans le portail Azure

Avec Azure Stream Analytics, vous pouvez tester des requêtes dans le portail sans avoir à démarrer ou arrêter un travail.

## <a name="test-the-input"></a>Tester l’entrée

1. Pour tester les exemples de données d’entrée, cliquez sur une de vos entrées, puis sélectionnez **Charger un exemple de données du fichier**. Actuellement, vous pouvez charger uniquement des données au format JSON. Si vos données sont dans un autre format tel que CSV, vous devez les convertir au format JSON avant de les charger. Vous pouvez utiliser n’importe quel outil de conversion Open Source comme le [convertisseur CSV à JSON](http://www.convertcsv.com/csv-to-json.htm) pour convertir vos données au format JSON.

    ![Test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Une fois que le téléchargement est terminé, cliquez sur **Tester** pour tester cette requête dans les exemples de données que vous avez fournis.

    ![Exemple de données de test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Si vous souhaitez enregistrer la sortie de test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec le lien Télécharger les résultats. Vous pouvez maintenant facilement et de manière itérative modifier votre requête et la tester à plusieurs reprises pour voir comment la sortie change.

![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Avec plusieurs sorties utilisées dans une requête, vous pouvez voir les résultats des deux sorties séparément et basculer facilement entre elles.

Une fois que vous êtes satisfait des résultats affichés dans le navigateur, vous pouvez enregistrer votre requête, lancer votre travail et le regarder traiter les événements sans erreur.

## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
