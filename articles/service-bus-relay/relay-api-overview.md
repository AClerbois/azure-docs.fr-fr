---
title: Vue d’ensemble de l’API Azure Relay | Microsoft Docs
description: Vue d’ensemble des API Azure Relay disponibles
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 2c7aec700a1077bc1a1b56afb5d8d07f47f4c6e8
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700370"
---
# <a name="available-relay-apis"></a>API Relay disponibles

## <a name="runtime-apis"></a>API de runtime

Vous trouverez ci-dessous un tableau de tous les clients de runtime Relay actuellement disponibles.

La section [Informations supplémentaires](#additional-information) contient plus d’informations sur l’état de chaque bibliothèque runtime.

| Langage/Plateforme | Fonctionnalité disponible | Package client | Référentiel |
| --- | --- | --- | --- |
| .NET Standard | les connexions hybrides | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Relais WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Nœud | les connexions hybrides | [Websockets : `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets : `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Requêtes HTTP : `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informations supplémentaires

#### <a name="net"></a>.NET

L’écosystème .NET ayant plusieurs runtimes, il existe plusieurs bibliothèques .NET pour Relay. La bibliothèque .NET Standard peut être exécutée à l’aide de .NET Core ou de .NET Framework, tandis que la bibliothèque .NET Framework peut uniquement être exécutée dans un environnement .NET Framework. Pour plus d’informations sur .NET Framework, consultez les [versions d’infrastructure](/dotnet/articles/standard/frameworks#framework-versions).

La bibliothèque .NET Framework prend uniquement en charge le modèle de programmation WCF et s’appuie sur un protocole binaire propriétaire basé sur le transport `net.tcp` WCF. Ce protocole et cette bibliothèque sont conservés à des fins de compatibilité descendante avec les applications existantes.

La bibliothèque .NET Standard est basée sur la définition de protocole ouvert pour le Relay Connexions hybrides qui s’appuie sur HTTP et WebSockets. La bibliothèque prend en charge une abstraction de flux sur Websockets et un mouvement d’API de requête-réponse simple pour répondre aux requêtes HTTP. L’exemple d’[API web](https://github.com/Azure/azure-relay-dotnet) montre comment intégrer Connexions hybrides à ASP.NET Core pour les services web.

#### <a name="nodejs"></a>Node.js

Les modules Connexions hybrides répertoriés dans le tableau ci-dessus remplacent ou modifient les modules Node.js existants par d’autres implémentations qui écoutent le service Azure Relay plutôt que la pile de mise en réseau locale.

Le module `hyco-https` modifie et substitue partiellement les modules Node.js de base `http` et `https`, fournissant une implémentation d’écouteur HTTPS compatible avec de nombreux modules Node.js existants et avec les applications qui reposent sur ces modules de base.

Les modules `hyco-ws` et `hyco-websocket` modifient les modules courants `ws` et `websocket` pour Node.js, fournissant d’autres implémentations d’écouteur qui permettent aux modules et aux applications reposant sur l’un ou l’autre module de s’exécuter derrière le Relay Connexions hybrides.

Vous trouverez plus d’informations sur ces modules dans le dépôt GitHub [azure-relay-node](https://github.com/Azure/azure-relay-node).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Relay, consultez les liens suivants :
* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [FAQ Relay](relay-faq.md)