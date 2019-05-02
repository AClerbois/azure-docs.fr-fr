---
title: Azure Front Door Service | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Front Door. Déterminez si Azure Traffic Manager est adapté à votre application pour équilibrer la charge de trafic utilisateur.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/29/2018
ms.author: sharadag
ms.openlocfilehash: 2d4c93b7ef707f5b09bc2f5f93d56c50c1bb3458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736324"
---
# <a name="what-is-azure-front-door-service"></a>Qu’est-ce que le service Azure Front Door Service ?
Azure Front Door Service vous permet de définir, de gérer et de superviser le routage global de votre trafic web en privilégiant l’optimisation des performances et le basculement instantané global à des fins de haute disponibilité. Avec Front Door, vous pouvez transformer vos applications grand public et professionnelles multirégions en applications, API et contenus modernes fiables, personnalisés et hautes performances bénéficiant avec Azure d’une audience mondiale.

Front Door fonctionne au niveau de la couche 7 ou HTTP/HTTPS et utilise le protocole anycast avec Split TCP et le réseau global de Microsoft pour améliorer la connectivité globale. Veillez donc à sélectionner la méthode de routage appropriée dans la configuration pour que Front Door route les requêtes de vos clients vers le backend d’application offrant les plus hautes performances et la meilleure disponibilité. Un backend d’application est un service accessible sur Internet hébergé dans ou hors Azure. Front Door fournit un large éventail de [méthodes de routage du trafic](front-door-routing-methods.md) et [d’options de supervision de l’intégrité du backend](front-door-health-probes.md) pour répondre aux besoins variés des applications et aux divers modèles de basculement automatique. À l’instar de [Traffic Manager](../traffic-manager/traffic-manager-overview.md), Front Door est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. Si vous cherchez à mettre en place un routage global basé sur DNS et que vous **ne répondez pas** aux exigences concernant la terminaison de protocole TLS (« déchargement SSL ») ou le traitement de la couche Application par requête HTTP/HTTPS, passez en revue [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Si vous cherchez à mettre en place un équilibrage de charge entre vos serveurs dans une région, passez en revue [Application Gateway](../application-gateway/application-gateway-introduction.md) pour la couche Application et [Équilibreur de charge](../load-balancer/load-balancer-overview.md) pour l’équilibrage de charge de la couche réseau. Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.

Les fonctionnalités suivantes sont incluses dans Front Door :

## <a name="accelerate-application-performance"></a>Accélération des performances des applications
Grâce au protocole anycast basé sur Split TCP, Front Door garantit la connexion rapide des utilisateurs finaux au point de présence (POP) Front Door le plus proche. L’utilisation du réseau global de Microsoft pour la connexion aux backends d’application à partir des POP Front Door garantit des niveaux plus élevés de disponibilité et de fiabilité sans dégradation des performances. Cette connectivité à votre backend est également basée sur la latence du réseau la moins élevée. Découvrez plus en détail les techniques de routage Front Door comme [Split TCP](front-door-routing-architecture.md#splittcp) et le protocole [Anycast](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Amélioration de la disponibilité des applications avec des sondes d’intégrité actives

Pour garantir la haute disponibilité de vos applications critiques, Front Door utilise des sondes d’intégrité actives qui supervisent la latence et la disponibilité de vos backends et qui assurent le basculement automatique instantané en cas de panne d’un backend. Vous pouvez donc effectuer des opérations de maintenance planifiées sur vos applications sans temps d’arrêt. Front Door dirige le trafic vers les autres backends pendant la maintenance.

## <a name="url-based-routing"></a>Routage basé sur des URL
Le routage basé sur le chemin d’accès de l’URL vous permet de router le trafic vers des pools de backends en fonction des chemins d’URL de la requête. L’un des scénarios consiste à router les requêtes pour différents types de contenu vers des pools de backends différents.

Par exemple, les requêtes adressées à `http://www.contoso.com/users/*` sont routées vers UserProfilePool et celles adressées à `http://www.contoso.com/products/*` vers ProductInventoryPool.  Front Door prend en charge des scénarios de mise en correspondance des routes encore plus complexes à l’aide de l’algorithme de correspondance optimale. Si aucun des modèles de chemin ne correspond, votre règle de routage par défaut pour `http://www.contoso.com/*` est sélectionnée et le trafic est dirigé vers la règle de routage fourre-tout par défaut. Découvrez plus en détail la [mise en correspondance des routes](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Hébergement de plusieurs sites
L’hébergement de plusieurs sites vous permet de configurer plusieurs sites web sur la même configuration Front Door. Cette fonctionnalité vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant différent sites web à une seule configuration Front Door. Selon l’architecture de votre application, vous pouvez configurer Azure Front Door Service pour diriger chaque site web vers son propre pool de backends ou diriger divers sites web vers le même pool de backends. Par exemple, Front Door peut assurer le trafic pour `images.contoso.com` et `videos.contoso.com` à partir de deux pools de backends appelés ImagePool et VideoPool. Vous pouvez également configurer les deux hôtes front-end pour diriger le trafic vers un pool de backends unique appelé MediaPool.

De même, vous pouvez avoir deux domaines différents, `www.contoso.com` et `www.fabrikam.com`, configurés sur le même Front Door.

## <a name="session-affinity"></a>Affinité de session
La fonctionnalité d’affinité de session basée sur les cookies est utile quand vous souhaitez conserver une session utilisateur sur le même backend d’application. Grâce aux cookies gérés par Front Door, le trafic suivant en provenance d’une session utilisateur est dirigé vers le même backend d’application pour traitement. Cette fonctionnalité est importante quand l’état de la session est enregistré localement sur le backend pour une session utilisateur.

## <a name="secure-sockets-layer-ssl-termination"></a>Terminaison SSL (Secure Sockets Layer)
Front Door prend en charge la terminaison SSL au niveau de la périphérie. Les utilisateurs individuels peuvent ainsi configurer une connexion SSL avec des environnements Front Door au lieu de l’établir sur des connexions longue distance avec le backend d’application. Front Door prend également en charge la connectivité HTTP et HTTPS entre les environnements Front Door et vos backends. Vous pouvez donc configurer le chiffrement SSL de bout en bout. Prenons un exemple de charge de travail d’application où Front Door reçoit plus de 5 000 demandes par minute. En raison de la réutilisation des connexions à chaud pour les services actifs, Front Door n’établit peut-être que 500 connexions à votre backend d’application. La charge sur vos backends est donc considérablement réduite.

## <a name="custom-domains-and-certificate-management"></a>Domaines personnalisés et gestion des certificats
Quand vous utilisez Front Door pour distribuer du contenu, un domaine personnalisé est nécessaire si vous souhaitez que votre nom de domaine soit visible dans l’URL de Front Door. Un nom de domaine visible peut être pratique pour vos clients et utile à des fins de personnalisation.
Front Door prend également en charge HTTPS pour les noms de domaine personnalisé. Pour utiliser cette fonctionnalité, choisissez des certificats gérés par Front Door pour votre trafic ou chargez votre propre certificat SSL personnalisé.

## <a name="application-layer-security"></a>Sécurité de la couche Application
Azure Front Door vous permet de créer des règles de pare-feu d’applications web (WAF) personnalisées pour le contrôle d’accès afin de protéger votre charge de travail HTTP/HTTPS de l’exploitation basée sur les adresses IP des clients, les codes de pays et les paramètres HTTP. Front Door vous permet également de créer des règles de limitation du débit pour lutter contre le trafic de bots malveillants. 

La plateforme Front Door elle-même est protégée par [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Pour renforcer la protection, vous pouvez activer Azure DDoS Protection Standard au niveau de vos réseaux virtuels et protéger les ressources contre les attaques de couche réseau (TCP/UDP) par le biais du réglage automatique et de l’atténuation. Front Door étant un proxy inverse de couche 7, il autorise uniquement l’acheminement du trafic web aux backends et bloque les autres types de trafic par défaut.

## <a name="url-rewrite"></a>Réécrire URL
Front Door prend en charge la [réécriture d’URL](front-door-url-rewrite.md). Vous pouvez ainsi configurer un chemin de transfert personnalisé facultatif à utiliser lors de la construction d’une requête à transférer au backend. Front Door vous permet par ailleurs de configurer l’en-tête de l’hôte à envoyer lors du transfert de la requête au backend.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Prise en charge des protocoles - Trafic IPv6 et HTTP/2
Azure Front Door prend en charge la connectivité IPv6 de bout en bout et le protocole HTTP/2 en mode natif. 

Le protocole HTTP/2 permet une communication en duplex intégral entre les backends d’application et le client par le biais d’une connexion TCP de longue durée. HTTP/2 assure une communication plus interactive entre le backend et le client, qui peut être bidirectionnelle sans nécessiter d’interrogations, comme c’est le cas pour les implémentations basées sur le protocole HTTP. Contrairement au protocole HTTP, le protocole HTTP/2 génère une faible surcharge et peut réutiliser la même connexion TCP pour plusieurs requêtes ou réponses, ce qui entraîne une utilisation plus efficace des ressources. Découvrez plus en détail la [prise en charge de HTTP/2 dans Azure Front Door Service](front-door-http2.md).

## <a name="pricing"></a>Tarifs

Pour obtenir des informations sur les prix, consultez [Prix de Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
