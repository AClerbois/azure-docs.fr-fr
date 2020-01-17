---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0c04e7812d023cd394b54cf03bcca11a5589b18a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564739"
---
Les erreurs signalées dans Azure Functions peuvent avoir l’une des origines suivantes :

- Utilisation des [déclencheurs et liaisons intégrés](..\articles\azure-functions\functions-triggers-bindings.md) Azure Functions
- Appels aux API des services Azure sous-jacents
- Appels aux points de terminaison REST
- Appels aux bibliothèques clientes, aux packages ou aux API tierces

Il est important de suivre des pratiques de gestion des erreurs solides afin d’éviter de perdre des données ou de manquer des messages. Les pratiques recommandées concernant la gestion des erreurs impliquent les actions suivantes :

- [Activer Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Utiliser la gestion structurée des erreurs](#use-structured-error-handling)
- [Concevoir en ayant en vue l’idempotence](../articles/azure-functions/functions-idempotent.md)
- [Implémenter des stratégies relatives aux nouvelles tentatives](../articles/azure-functions/functions-reliable-event-processing.md) (si nécessaire)

### <a name="use-structured-error-handling"></a>Utiliser la gestion structurée des erreurs

La capture et la publication des erreurs sont essentielles pour superviser l’intégrité de votre application. Le niveau le plus élevé de tout code de fonction doit inclure un bloc try/catch. Dans le bloc catch, vous pouvez capturer et publier des erreurs.

### <a name="retry-support"></a>Prise en charge des nouvelles tentatives

Les déclencheurs suivants prennent en charge les nouvelles tentatives intégrées :

* [stockage d’objets blob Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Stockage File d’attente Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (file d’attente/rubrique)](../articles/azure-functions/functions-bindings-service-bus.md)

Par défaut, ces déclencheurs retentent les requêtes jusqu’à cinq fois. Après la cinquième tentative, le Stockage File d’attente Azure et les déclencheurs Azure Service Bus écrivent un message dans une [file d’attente de messages incohérents](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Pour tous les autres types de déclencheurs ou de liaisons, vous devez implémenter manuellement les stratégies de nouvelle tentative. Les implémentations manuelles peuvent impliquer l’écriture d’informations sur les erreurs dans une [file d’attente de messages incohérents](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Le fait d’écrire des données dans une file d’attente de messages incohérents vous permet de retenter des opérations ultérieurement. Cette approche est la même que celle utilisée par le déclencheur du stockage Blob.
