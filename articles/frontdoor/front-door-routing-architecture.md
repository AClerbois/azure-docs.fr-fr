---
title: Azure Front Door Service - Architecture de routage | Microsoft Docs
description: Cet article vous aide à comprendre l’architecture globale de Front Door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736278"
---
# <a name="routing-architecture-overview"></a>Vue d’ensemble de l’architecture de routage

Quand Azure Front Door Service reçoit les requêtes de vos client, soit il leur répond (si la mise en cache est activée), soit il les transfère vers le backend d’application approprié (en tant que proxy inverse).

</br>Il existe des opportunités pour optimiser le trafic lors du routage vers Azure Front Door, ainsi que lors du routage vers le backend.

## <a name = "anycast"></a>Sélection de l’environnement Front Door pour le routage du trafic (Anycast)

Le routage vers les environnements Azure Front Door utilise [Anycast](https://en.wikipedia.org/wiki/Anycast) pour le trafic DNS (Domain Name System) et HTTP (Hypertext Transfer Protocol). Ainsi, le trafic utilisateur sera acheminé vers l’environnement le plus proche en termes de topologie réseau (nombre de sauts le plus faible). Cette architecture offre généralement des durées de parcours circulaire plus courtes pour les utilisateurs finaux (optimisant ainsi les bénéfices du fractionnement TCP). Front Door organise ses environnements en « anneaux » principaux et de secours.  L’anneau externe a des environnements qui sont plus proches des utilisateurs, offrant des latences réduites.  L’anneau interne a des environnements capables de gérer le basculement pour l’environnement d’anneau externe en cas de problème. L’anneau externe est la cible par défaut pour tout le trafic, mais l’anneau interne est nécessaire pour gérer le dépassement de capacité de trafic de l’anneau externe. En termes d’adresses IP virtuelles (adresses VIP), chaque hôte frontal ou domaine pris en charge par Front Door reçoit une adresse VIP principale, qui est annoncée par les environnements dans les anneaux interne et externe, ainsi qu’une adresse VIP de secours, qui est annoncée uniquement par les environnements dans l’anneau interne. 

</br>Cette stratégie globale garantit que les requêtes de vos utilisateurs finaux parviennent toujours à l’environnement Front Door le plus proche, et que même en cas de non-intégrité de l’environnement Front Door par défaut le trafic bascule automatiquement vers l’environnement le plus proche suivant.

## <a name = "splittcp"></a>Connexion à l’environnement Front Door (fractionnement TCP)

Le [TCP de fractionnement](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) est une technique permettant de réduire les latences et les problèmes liés aux protocole TCP en fractionnant les connexions dont les durées de parcours circulaire seraient trop élevées.  En plaçant les environnements Front Door plus près des utilisateurs finaux et en fermant les connexions TCP à l’intérieur de l’environnement Front Door, une connexion TCP avec une durée de parcours circulaire élevée vers le backend d’application est fractionnée en deux connexions TCP. La courte connexion entre l’utilisateur final et l’environnement Front Door signifie que la connexion est établie sur trois brefs parcours circulaires plutôt que trois longs parcours circulaires, réduisant ainsi la latence.  La longue connexion entre l’environnement Front Door et le backend peut être préalablement établie et réutilisée pour plusieurs appels de l’utilisateur final, réduisant là encore la durée de connexion TCP.  L’effet est multipliée lors de l’établissement d’une connexion SSL/TLS (Transport Layer Security), car il y a davantage de parcours circulaires pour sécuriser la connexion.

## <a name="processing-request-to-match-a-routing-rule"></a>Traitement de la requête pour la mise en correspondance avec une règle de routage
Une fois la connexion établie et la négociation SSL effectuée, quand une requête parvient à un environnement Front Door, la mise en correspondance avec une règle de routage est la première étape. Cette étape permet de déterminer, parmi toutes les configurations Front Door, avec quelle règle de routage spécifique mettre la requête en correspondance. Pour en savoir plus sur la façon dont Front Door effectue la mise en correspondance d’itinéraire, consultez [cet article](front-door-route-matching.md).

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identification des backends disponibles dans le pool de backends pour la règle de routage
Une fois que Front Door a établi une correspondance pour une règle de routage en fonction de la requête entrante, et s’il n’existe aucune mise en cache, l’étape suivante consiste à extraire l’état de la sonde d’intégrité pour le pool de backends associé à l’itinéraire correspondant. Pour en savoir plus sur la façon dont Front Door supervise l’intégrité des backends à l’aide de sondes d’intégrité, consultez [cet article](front-door-health-probes.md).

## <a name="forwarding-the-request-to-your-application-backend"></a>Transfert de la requête à votre backend d’application
Pour finir, en supposant qu’aucune mise en cache n’est configurée, la requête de l’utilisateur est transférée au « meilleur » backend en fonction de votre configuration de [méthode de routage Front Door](front-door-routing-methods.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
