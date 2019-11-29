---
title: Vue d’ensemble des versions de Durable Functions - Azure Functions
description: En savoir plus sur les versions de Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 93c35eb4f69cc4f9b16f669d96c2df53f50bcf84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231195"
---
# <a name="durable-functions-versions-overview"></a>Vue d’ensemble des versions de Durable Functions

*Fonctions durables* est une extension d[’Azure Functions](../functions-overview.md) et d[’Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) qui vous permet d’écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. Si vous n’êtes pas encore familiarisé avec Durable Functions, consultez la [documentation générale](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nouvelles fonctionnalités de la version 2.x

Cette section décrit les fonctionnalités de Durable Functions ajoutées à la version 2.x.

### <a name="durable-entities"></a>Entités durables

Dans Durable Functions 2.x, nous avons introduit un nouveau concept de [fonctions d’entité](durable-functions-entities.md).

Les fonctions d’entité définissent les opérations pour la lecture et la mise à jour de petits éléments d’état, connus sous le nom *d’entités durables*. Comme les fonctions d’orchestrateur, les fonctions d’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur d’entité*. Contrairement aux fonctions d’orchestrateur, les fonctions d’entité n’ont pas de contraintes code spécifiques. Les fonctions d’entité gèrent également l’état explicitement plutôt que de représenter implicitement l’état via le flux de contrôle.

Pour plus d’informations, consultez l’article [Entités durables](durable-functions-entities.md).

### <a name="durable-http"></a>Fonctionnalité HTTP durable

Dans Durable Functions v2.x, nous avons introduit une nouvelle fonctionnalité [HTTP durable](durable-functions-http-features.md#consuming-http-apis) qui vous permet d’effectuer les opérations suivantes :

* Appeler les API HTTP directement à partir des fonctions d’orchestration (avec certaines limitations documentées).
* Implémenter une interrogation d’état HTTP 202 automatique côté client.
* Bénéficier d’une prise en charge intégrée des [identités managées Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pour plus d’informations, consultez cet article sur les [fonctionnalités HTTP](durable-functions-http-features.md#consuming-http-apis).

## <a name="migrate-from-1x-to-2x"></a>Migrer de la version 1.x à 2.x

Cette section décrit comment migrer votre extension Durable Functions existante de la version 1.x vers la version 2.x pour tirer parti des nouvelles fonctionnalités.

### <a name="upgrade-the-extension"></a>Mettre à niveau l’extension

Installez la version 2.x de [l’extension de liaisons Durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) dans votre projet. Pour en savoir plus, consultez [Inscrire des extensions de liaison Azure Functions](../functions-bindings-register.md).

### <a name="update-your-code"></a>Mettre à jour votre code

Durable Functions 2.x introduit plusieurs changements cassants. Les applications Durable Functions 1.x ne sont pas compatibles avec Durable Functions 2.x sans modification de code. Cette section répertorie quelques-unes des modifications que vous devez effectuer lors de la mise à niveau de vos fonctions version 1.x vers la version 2.x.

#### <a name="hostjson-schema"></a>Schéma Host.json

Durable Functions 2.x utilise un nouveau schéma host.json. Les principales modifications apportées par rapport à la version 1.x sont les suivantes :

* `"storageProvider"` (et la sous-section `"azureStorage"`) pour la configuration spécifique au stockage.
* `"tracking"` pour la configuration du suivi et de la journalisation.
* `"notifications"` (et la sous-section `"eventGrid"`) pour la configuration de notification de grille d’événement.

Pour plus d’informations, consultez la [documentation de référence pour host.json de Durable Functions](durable-functions-bindings.md#durable-functions-2-0-host-json).

#### <a name="public-interface-changes-net-only"></a>Modifications de l’interface publique (.NET uniquement)

Dans la version 1.x, les divers objets de _contexte_ pris en charge par Durable Functions avaient des classes abstraites prévues pour une utilisation lors des tests unitaires. Dans le cadre de Durable Functions 2.x, ces classes de base abstraites sont remplacées par des interfaces.

Le tableau suivant représente les principales modifications :

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Dans le cas où une classe de base abstraite contenait des méthodes virtuelles, ces méthodes virtuelles ont été remplacées par les méthodes d’extension définies dans `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>Modifications de function.json (script JavaScript et C#)

Dans Durable Functions 1.x, la liaison du client d’orchestration utilise un `type` de `orchestrationClient`. La version 2.x utilise `durableClient` à la place.
