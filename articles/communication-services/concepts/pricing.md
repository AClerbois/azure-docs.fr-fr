---
title: Scénarios de tarifs pour les appels (vocaux/vidéo) et la conversation
titleSuffix: An Azure Communication Services concept document
description: Découvrez le modèle tarifaire de Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0b82d7dc8f6aaaa28a5293966440f058fbf42dc1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460932"
---
# <a name="pricing-scenarios"></a>Scénarios de tarifs

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Les tarifs pour Azure Communication Services sont basés sur un modèle de paiement à l’utilisation sans frais initiaux. Seules votre consommation et votre utilisation des services vous sont facturées.

## <a name="voicevideo-calling-and-screen-sharing"></a>Appels vocaux/vidéo et partage d’écran

Azure Communication Services permet d’ajouter les appels vocaux/vidéo et le partage d’écran dans vos applications. Vous pouvez incorporer cette expérience dans vos applications à l’aide des bibliothèques de client JavaScript, Objective-C (Apple), Java (Android) et .NET. Reportez-vous à notre [liste complète des bibliothèques de client disponibles](./sdk-options.md).

### <a name="pricing"></a>Tarifs

Les services d’appel et de partage d’écran sont facturés à la minute pour chaque participant à un tarif de 0,004 USD par participant par minute pour les appels de groupe. Pour comprendre les différents flux d’appels possibles, reportez-vous à [cette page](./call-flows.md).

Chaque participant d’un appel est comptabilisé dans la facturation pour chaque minute où il est connecté à l’appel. Cela vaut que l’utilisateur effectue un appel vidéo, un appel vocal ou un partage d’écran.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Exemple de tarif : Appel audio/vidéo de groupe à l’aide des bibliothèques de client JS et iOS

Alice a effectué un appel de groupe avec ses collègues, Bob et Charlie. Alice et Bob ont utilisé les bibliothèques de client JS, et Charlie les bibliothèques de client iOS.

- L’appel a duré un total de 60 minutes.
- Alice et Bob ont participé à la totalité de l’appel. Alice a activé la vidéo pendant cinq minutes et a partagé son écran pendant 23 minutes. Bob avait la vidéo activée pendant l’appel entier (60 minutes) et a partagé son écran pendant 12 minutes.
- Charlie a quitté l’appel après 43 minutes. Charlie a utilisé l’audio et la vidéo pendant toute la période où il a participé à l’appel (43 minutes).

**Calculs des coûts**

- 2 participants x 60 minutes x 0,004 USD par participant par minute = 0,48 USD [les flux vidéo et audio sont facturés au même tarif]
- 1 participant x 43 minutes x 0,004 USD par participant par minute = 0,172 USD [les flux vidéo et audio sont facturés au même tarif]

**Coût total de l’appel de groupe** : 0,48 USD + 0,172 USD = 0,652 USD

## <a name="chat"></a>Conversation

Avec Communication Services, vous pouvez améliorer votre application en ajoutant la possibilité d’envoyer et de recevoir des messages de conversation entre 2 utilisateurs ou plus. Les bibliothèques de client Chat sont disponibles pour JavaScript, .NET, Python et Java. Reportez-vous à [cette page pour en savoir plus sur les bibliothèques de client](./sdk-options.md).

### <a name="price"></a>Price

- Il vous est facturé 0,0008 USD pour chaque message de conversation envoyé.

### <a name="pricing-example-chat-between-two-users"></a>Exemple de tarif : Conversation entre deux utilisateurs 

Geeta démarre un fil de conversation avec Emily pour partager une mise à jour et envoie 5 messages. La conversation dure 10 minutes, pendant lesquelles Geeta et Emily envoient encore 15 messages chacune.

**Calculs des coûts** 
- Nombre de messages envoyés (5 + 15 + 15) x 0,0008 USD = 0,028 USD

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemple de tarif : Conversation de groupe avec plusieurs utilisateurs 

Charlie démarre un fil de conversation avec ses amis Casey et Jasmine pour planifier des vacances. Ils discutent pendant un certain temps, au cours duquel Charlie, Casey et Jasmine envoient 20, 30 et 18 messages respectivement. Ils se rendent compte que leur amie Rose pourrait avoir envie de partir avec eux, de sorte qu’ils l’ajoutent au fil de conversation et partagent tout l’historique des messages avec elle. 

Rose consulte les messages et s’engage dans la conversation. Entre-temps, Casey reçoit un appel et décide de reprendre la conversation ultérieurement. Charlie, Jasmine et Rose décident des dates du voyage et envoient encore 30, 25 et 35 messages respectivement.

**Calculs des coûts** 

- Nombre de messages envoyés (20 + 30 + 18 + 30 + 25 + 35) x 0,0008 USD = 0,1264 USD
