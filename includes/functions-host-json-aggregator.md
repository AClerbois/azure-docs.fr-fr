---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251262"
---
Spécifie le nombre d’appels de fonction agrégés lorsque vous [calculez des métriques pour Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriété |Default  | Description |
|---------|---------|---------| 
|batchSize|1 000|Nombre maximal de requêtes à agréger.| 
|flushTimeout|00:00:30|Période maximale d’agrégation.| 

Les appels de fonction sont agrégés lorsque la première des deux limites est atteinte.