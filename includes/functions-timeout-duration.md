---
title: Fichier Include
description: Fichier Include
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177625"
---
## <a name="timeout"></a>Durée du délai d’expiration du conteneur de fonctions 

La durée d’expiration d’un conteneur de fonctions est définie par la propriété functionTimeout dans le fichier projet [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). Le tableau suivant présente les valeurs par défaut et les valeurs maximales en minutes pour les deux plans et dans les deux versions du runtime :

| Planification | Version du runtime | Default | Maximale |
|------|---------|---------|---------|
| Consommation | 1.x | 5\. | 10 |
| Consommation | 2.x | 5\. | 10 |
| App Service | 1.x | Illimité | Illimité |
| App Service | 2.x | 30 | Illimité |

> [!NOTE] 
> Quel que soit le paramètre de délai d’expiration du conteneur de fonctions, 230 secondes est le temps maximum que peut prendre une fonction déclenchée via HTTP pour répondre à une demande. Cela est dû au [délai d’inactivité par défaut d’Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pour des temps de traitement plus longs, pensez à utiliser un [modèle asynchrone Durable Functions](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) ou [différez le travail actuel et renvoyez une réponse immédiate](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
