---
title: Azure Front Door Service - Prise en charge du protocole HTTP2 | Microsoft Docs
description: Cet article vous aide à en savoir plus sur la prise en charge du protocole HTTP/2 dans Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736445"
---
# <a name="http2-support-in-azure-front-door-service"></a>Prise en charge du protocole HTTP/2 dans Azure Front Door Service
HTTP/2 est une révision majeure du protocole HTTP/1.1. Il offre des performances web plus rapides, des meilleurs temps de réponse et une expérience utilisateur améliorée, tout en conservant les méthodes HTTP, les codes d’état et la sémantique habituels. Bien que HTTP/2 soit conçu pour fonctionner avec HTTP et HTTPS, de nombreux navigateurs web clients prennent en charge seulement HTTP/2 sur TLS (Transport Layer Security).

### <a name="http2-benefits"></a>Avantages de HTTP/2

Les avantages de HTTP/2 sont les suivants :

*   **Multiplexage et accès concurrentiel**

    Avec HTTP 1.1, effectuer plusieurs demandes de ressources nécessite plusieurs connexions TCP, et chaque connexion représente une surcharge qui a un impact sur les performances. HTTP/2 permet de demander plusieurs ressources sur une même connexion TCP.

*   **Compression des en-têtes**

    Grâce à la compression des en-têtes HTTP pour les ressources traitées, le temps passé sur le réseau est sensiblement réduit.

*   **Dépendances de flux**

    Les dépendances de flux permettent au client indiquer au serveur quelles ressources ont la priorité.


## <a name="http2-browser-support"></a>Prise en charge de HTTP/2 par les navigateurs

Tous les principaux navigateurs ont implémenté la prise en charge de HTTP/2 dans leur version actuelle. Les navigateurs non pris en charge passent automatiquement à HTTP/1.1.

|Browser|Version minimale|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Activation de la prise en charge de HTTP/2 dans Azure Front Door Service

Actuellement, la prise en charge de HTTP/2 est active pour toutes les configurations Front Door. Aucune action supplémentaire n’est nécessaire de la part des clients.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les HTTP/2, consultez les ressources suivantes :

- [Page d’accueil de la spécification HTTP/2](https://http2.github.io/)
- [Forum Aux Questions officiel de HTTP/2](https://http2.github.io/faq/)
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
