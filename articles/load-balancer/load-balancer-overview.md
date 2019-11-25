---
title: Qu’est-ce qu’Azure Load Balancer ?
titlesuffix: Azure Load Balancer
description: Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne Load Balancer et l’exploiter dans le cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: allensu
ms.openlocfilehash: be293a925e507468d96be4c9f6b47f30eea5f025
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888672"
---
# <a name="what-is-azure-load-balancer"></a>Qu’est-ce qu’Azure Load Balancer ?

Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer une haute disponibilité pour vos services. Load Balancer prend en charge les scénarios entrants et sortants, offre une latence faible et un débit élevé, et peut augmenter l’échelle jusqu’à des millions de flux pour toutes les applications TCP et UDP.

Load Balancer distribue les nouveaux flux entrants arrivant sur le serveur frontal de Load Balancer sur des instances de pool principal en fonction des règles et des sondes d’intégrité spécifiées.

Un Load Balancer public peut fournir des connexions sortantes pour des machines virtuelles à l’intérieur de votre réseau virtuel en traduisant leurs adresses IP privées en adresses IP publiques.

Azure Load Balancer se décline en deux niveaux tarifaires ou *références SKU* : De base et Standard. Celles-ci présentent des différences en termes de mise à l’échelle, de fonctionnalités et de tarification. N’importe quel scénario possible avec Load Balancer De base peut également être créé avec Load Balancer Standard, même si l’approche à suivre varie légèrement. Pour bien comprendre le fonctionnement de Load Balancer, familiarisez-vous avec ses capacités de base et les différences propres aux références SKU.

## <a name="why-use-load-balancer"></a>Pourquoi utiliser Load Balancer ?

Vous pouvez utiliser Azure Load Balancer pour :

* équilibrer la charge du trafic Internet entrant sur vos machines virtuelles. Cette configuration est appelée [équilibreur de charge public](#publicloadbalancer).
* Équilibrez la charge du trafic entre les machines virtuelles à l’intérieur d’un réseau virtuel. Vous pouvez également communiquer avec un frontend d’équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride. Ces deux scénarios s’appuient sur une configuration appelée [équilibreur de charge interne](#internalloadbalancer).
* réacheminer le trafic d’un port vers un port spécifique sur des machines virtuelles données avec des règles de traduction d’adresses réseau (NAT) entrantes.
* Fournir une [connectivité sortante](load-balancer-outbound-connections.md) aux machines virtuelles de votre réseau virtuel à l’aide d’un équilibreur de charge public.

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. Si vous recherchez une terminaison de protocole TLS (« déchargement SSL ») ou un traitement de couche d’application par requête HTTP/HTTPS, consultez [Qu’est-ce qu’Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Si vous recherchez un équilibrage de charge DNS global, consultez [Qu’est-ce que Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions.

## <a name="what-are-load-balancer-resources"></a>Que sont les ressources Load Balancer ?

Les ressources Load Balancer sont des objets indiquant la manière dont Azure doit programmer son infrastructure multilocataire pour obtenir le scénario que vous souhaitez créer. Il n’existe aucune relation directe entre les ressources Load Balancer et l’infrastructure réelle. La création d’un équilibreur de charge ne crée pas d’instance et la capacité demeure toujours disponible.

Une ressource Load Balancer peut être un Load Balancer public ou un Load Balancer interne. Les fonctions de la ressource de Load Balancer sont définies par un serveur frontal, une règle, une sonde d’intégrité et un pool principal. Vous placez des machines virtuelles dans le pool principal en spécifiant le pool principal à partir de la machine virtuelle.

## <a name="fundamental-load-balancer-features"></a>Fonctionnalités de base de Load Balancer

Load Balancer offre les fonctionnalités de base suivantes pour les applications TCP et UDP :

* **Équilibrage de charge**

  Azure Load Balancer permet de créer une règle pour distribuer le trafic arrivant sur un serveur frontal sur des instances de pool principal. Load Balancer utilise un algorithme de hachage pour la distribution des flux entrants et réécrit les en-têtes des flux sur les instances de pool principal. Un serveur est disponible pour recevoir les nouveaux flux quand une sonde d’intégrité indique un point de terminaison de serveur principal sain.

  Par défaut, Load Balancer utilise un hachage à 5 tuples. Le hachage est composé de l’adresse IP source, du port source, de l’adresse IP de destination, du port de destination et du numéro de protocole IP pour mapper les flux vers les serveurs disponibles. Vous pouvez créer une affinité avec une adresse IP source en utilisant un hachage à 2 ou 3 tuples pour une règle donnée. Tous les paquets d’un même flux arrivent sur la même instance derrière le serveur frontal soumis à l’équilibrage de charge. Quand le client démarre un nouveau flux à partir de la même adresse IP source, le port source est modifié. Par conséquent, le hachage à 5 tuples peut provoquer l’acheminement du trafic vers un autre point de terminaison de serveur principal.

  Pour plus d’informations, consultez [Configuration du mode de distribution pour Azure Load Balancer](load-balancer-distribution-mode.md). L’image suivante montre la distribution basée sur le hachage :

  ![Distribution basée sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figure : Distribution basée sur le hachage*

* **Réacheminement de port**

  Load Balancer vous permet de créer une règle NAT de trafic entrant. Cette règle NAT réachemine le trafic d’un port spécifique d’une adresse IP donnée du serveur frontal vers un port spécifique d’une instance donnée du serveur principal à l’intérieur du réseau virtuel. Ce réacheminement est accompli à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge. Les scénarios courants pour cette fonctionnalité incluent les sessions de protocole RDP (Remote Desktop Protocol) ou SSH (Secure Shell) avec des instances de machines virtuelles individuelles à l’intérieur du réseau virtuel Azure.
  
  Vous pouvez mapper plusieurs points de terminaison internes à des ports sur la même adresse IP de serveur frontal. Vous pouvez utiliser les adresses IP frontales pour administrer à distance vos machines virtuelles sans serveur de rebond supplémentaire.

* **Indépendance d’application et transparence**

  Load Balancer n’interagit pas directement avec TCP, UDP ou la couche Application. Tous les scénarios d’application TCP ou UDP peuvent être pris en charge. Load Balancer ne met pas fin aux flux ou n’initie pas de flux, n’interagit pas avec la charge utile du flux et ne fournit aucune fonction de passerelle de couche Application. Les liaisons de protocole se produisent toujours directement entre le client et l’instance de pool principal. La réponse à un flux entrant provient toujours d’une machine virtuelle. Lorsque le flux arrive sur la machine virtuelle, l’adresse IP source d’origine est également conservée.

  * Chaque point de terminaison obtient uniquement une réponse d’une machine virtuelle. Par exemple, l’établissement d’une liaison TCP se fait toujours entre le client et la machine virtuelle du serveur principal sélectionnée. La réponse à une demande d’un serveur frontal est une réponse générée par la machine virtuelle du serveur principal. Lorsque vous validez correctement la connectivité à un serveur frontal, vous validez la connectivité de bout en bout à au moins une machine virtuelle du serveur principal.
  * Les charges utiles d’application sont transparentes pour Load Balancer. Toutes les applications UDP ou TCP peuvent être prises en charge. Pour les charges de travail qui nécessitent un traitement par requête HTTP ou une manipulation des charges utiles de couche Application (par exemple, l’analyse des URL HTTP), vous devez utiliser un équilibreur de charge de couche 7 comme [Application Gateway](https://azure.microsoft.com/services/application-gateway).
  * Étant donné que Load Balancer n'interagit pas avec la charge utile TCP et fournit le déchargement TLS, vous pouvez générer des scénarios chiffrés de bout en bout. L’utilisation de Load Balancer permet d’obtenir de vastes systèmes de montée en charge pour les applications TLS en mettant fin à la connexion TLS sur la machine virtuelle proprement dite. Par exemple, la capacité de création de clés pour votre session TLS est uniquement limitée par le type et le nombre de machines virtuelles que vous ajoutez au pool du serveur principal. Si vous avez besoin du déchargement SSL, d’un traitement de couche Application ou si vous souhaitez déléguer la gestion des certificats à Azure, utilisez plutôt l’équilibreur de charge couche 7 Azure, à savoir [Application Gateway](https://azure.microsoft.com/services/application-gateway).

* **Reconfiguration automatique**

  Load Balancer se reconfigure instantanément lors de la mise à l’échelle d’instances vers le haut ou vers le bas. Quand vous ajoutez ou supprimez des machines virtuelles du pool principal, Load Balancer est reconfiguré sans opérations supplémentaires sur la ressource de Load Balancer.

* **Sondes d’intégrité**

  Pour déterminer l’intégrité des instances du pool du serveur principal, Load Balancer fait appel à des sondes d’intégrité définies par vos soins. Quand une sonde ne répond pas, l’équilibreur de charge n’envoie plus de nouvelles connexions aux instances défaillantes. Un échec de la sonde n’affecte pas les connexions existantes. La connexion est maintenue tant que l’application ne met pas fin au flux, qu’un délai d’inactivité n’a pas lieu ou que la machine virtuelle n’est pas arrêtée.

  Load Balancer fournit des types de sonde d’intégrité différents pour les points de terminaison TCP, HTTP et HTTPS. Pour plus d’informations, consultez [Types de sondes](load-balancer-custom-probe-overview.md#types).

  Quand vous utilisez des services cloud classiques, un type supplémentaire est autorisé : [Agent invité](load-balancer-custom-probe-overview.md#guestagent). Un agent invité doit être considéré comme une sonde d’intégrité en dernier recours. Microsoft ne les recommande pas si d’autres options sont disponibles.

* **Connexions sortantes (SNAT)**

  Tous les flux sortants circulant d’adresses IP privées à l’intérieur de votre réseau virtuel vers des adresses IP publiques sur Internet peuvent être traduits en une adresse IP de serveur frontal de Load Balancer. Quand un serveur frontal public est lié à une machine virtuelle de serveur principal par le biais d’une règle d’équilibrage de charge, Azure traduit les connexions sortantes en adresses IP du serveur frontal public. Cette configuration offre les avantages suivants :

  * Elle permet une mise à niveau et une récupération d’urgence simples des services, étant donné que le serveur frontal peut être dynamiquement mappé à une autre instance du service.
  * Elle facilite la gestion des listes de contrôle d’accès (ACL). Les ACL exprimées comme des adresses IP de serveur frontal ne changent pas quand les services montent en puissance, descendent en puissance ou sont redéployés. La traduction des connexions sortantes en un plus petit nombre d’adresses IP que de machines réduit la charge liée à l’implémentation de listes vertes de destinataires.

  Pour plus d’informations, consultez [Connexions sortantes dans Azure](load-balancer-outbound-connections.md).

La référence SKU Standard de Load Balancer offre des fonctionnalités spécifiques en plus de ces capacités de base, comme décrit ci-dessous.

## <a name="skus"></a> Comparaison des références SKU de Load Balancer

Load Balancer prend en charge les références SKU De base et Standard. Ces références SKU présentent des différences en termes d’échelle de scénario, de fonctionnalités et de tarification. N’importe quel scénario possible avec la référence SKU De base de Load Balancer peut être créé avec la référence SKU Standard. Les API sont identiques pour ces deux références SKU et sont appelées en spécifiant une référence SKU. Les références SKU pour Load Balancer et les adresses IP publiques sont prises en charge à partir de la version d’API `2017-08-01`. Les deux références SKU présentent les mêmes API et structure générales.

La configuration du scénario complet peut varier légèrement selon la référence SKU. Dans la documentation relative à Load Balancer, les articles applicables uniquement à une référence SKU spécifique sont signalés. Pour comparer les références SKU et comprendre leurs différences, consultez le tableau ci-dessous. Pour plus d’informations, consultez [Vue d’ensemble du niveau Standard d’Azure Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Les nouvelles conceptions doivent adopter Standard Load Balancer.

Les machines virtuelles autonomes, les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être connectés à une référence SKU, jamais aux deux. Les références SKU de Load Balancer et des adresses IP publiques doivent correspondre lorsque vous les utilisez avec des adresses IP publiques. Les références SKU de Load Balancer et des adresses IP publiques ne sont pas mutables.

La meilleure pratique consiste à spécifier explicitement les références SKU. Pour l’heure, les modifications requises sont réduites au minimum. Si une référence SKU n’est pas spécifiée, la valeur par défaut est la version d’API `2017-08-01` de la référence SKU De base.

>[!IMPORTANT]
>Load Balancer Standard est un nouveau produit de Load Balancer. C’est essentiellement un surensemble de Load Balancer De base, mais il existe d’importantes différences entre les deux produits. N’importe quel scénario complet possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard. Si vous connaissez déjà Load Balancer De base, comparez-le avec Load Balancer Standard pour comprendre les dernières modifications de leur comportement.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Pour plus d’informations, consultez [Limites de Load balancer](https://aka.ms/lblimits). Pour obtenir plus d’informations sur la référence SKU Standard de Load Balancer, consultez également la [présentation](load-balancer-standard-overview.md), la page relative à la [tarification](https://aka.ms/lbpricing) et la page relative au [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Concepts

### <a name = "publicloadbalancer"></a>Équilibreur de charge public

Un Load Balancer public mappe l’adresse IP publique et le port du trafic entrant à l’adresse IP privée et au port de la machine virtuelle. Load Balancer mappe le trafic dans l’autre sens pour le trafic de réponse provenant de la machine virtuelle. Vous pouvez distribuer des types de trafic donnés entre plusieurs machines virtuelles ou services en appliquant des règles d’équilibrage de charge. Par exemple, vous pouvez répartir la charge du trafic des requêtes web entre plusieurs serveurs web.

>[!NOTE]
>Vous ne pouvez implémenter qu’un Load Balancer public et qu’un Load Balancer interne par groupe à haute disponibilité.

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic web partagé entre trois machines virtuelles pour le port TCP 80 et public. Celles-ci sont incluses dans un jeu d’équilibrage de la charge.

![Exemple d’équilibreur de charge public](./media/load-balancer-overview/IC727496.png)

*Figure : Équilibrage du trafic web à l’aide d’un Load Balancer public*

Les clients Internet envoient des requêtes de page web à l’adresse IP publique d’une application web sur le port TCP 80. Azure Load Balancer répartit les requêtes entre les trois machines virtuelles du groupe à charge équilibrée. Pour plus d’informations sur les algorithmes de Load Balancer, consultez les [Fonctionnalités de base de Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features).

Azure Load Balancer répartit par défaut le trafic réseau équitablement sur plusieurs instances de machine virtuelle. Vous pouvez également configurer l’affinité de session. Pour plus d’informations, consultez [Configuration du mode de distribution pour Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Équilibreur de charge interne

Un Load Balancer interne dirige le trafic uniquement vers les ressources qui se trouvent à l’intérieur d’un réseau virtuel ou qui utilisent un VPN pour accéder à l’infrastructure Azure, contrairement à un Load Balancer public. L’infrastructure Azure limite l’accès aux adresses IP du serveur frontal soumis à l’équilibrage de charge au sein d’un réseau virtuel. Les adresses IP du serveur frontal et les réseaux virtuels ne sont jamais directement exposés à un point de terminaison Internet. Les applications métier internes s’exécutent dans Azure et sont accessibles à partir d’Azure ou à partir des ressources locales.

Un équilibreur de charge interne permet d’effectuer les types d’équilibrage de charge suivants :

* **Dans un réseau virtuel** : Équilibrage de charge des machines virtuelles dans le réseau virtuel vers un ensemble de machines virtuelles qui se trouvent dans le même réseau virtuel.
* **Pour un réseau virtuel entre sites locaux** : Équilibrage de charge des ordinateurs locaux vers un ensemble de machines virtuelles qui se trouvent dans le même réseau virtuel.
* **Pour les applications multiniveaux** : Équilibrage de charge pour les applications multiniveaux accessibles sur Internet dans lesquelles les niveaux back-end ne sont pas accessibles sur Internet. Les niveaux principaux nécessitent un équilibrage de la charge du trafic du niveau accessible sur Internet. Voir la figure suivante.
* **Pour les applications métier** : Équilibrage de charge pour les applications métier hébergées dans Azure, sans matériel ou logiciel d’équilibreur de charge supplémentaire. Ce scénario inclut des serveurs locaux dans l’ensemble d’ordinateurs dont la charge du trafic est équilibrée.

![Exemple d’équilibreur de charge interne](./media/load-balancer-overview/IC744147.png)

*Figure : Équilibrage d’applications multiniveaux à l’aide d’un Load Balancer public et d’un Load Balancer interne*

## <a name="pricing"></a>Tarifs

L'utilisation de Standard Load Balancer est facturée.

* Nombre de règles de trafic sortant et d’équilibrage de la charge configurées. Les règles NAT de trafic entrant ne sont pas comptabilisées dans le nombre total de règles.
* Volume entrant et sortant de données traitées indépendamment des règles.

Pour plus d’informations sur la tarification de Load Balancer Standard, consultez [Tarification de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

La référence SKU De base de Load Balancer est proposée gratuitement.

## <a name="sla"></a>Contrat SLA

Pour plus d’informations sur le contrat de niveau de service (SLA) de Load Balancer Standard, consultez [SLA pour Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Limites

* Load Balancer offre un équilibrage de charge et le réacheminement de ports pour les protocoles TCP ou UDP spécifiques. Les règles d’équilibrage de charge et les règles NAT de trafic entrant prennent en charge les protocoles TCP et UDP, mais aucun autre protocole IP, notamment ICMP.

  Load Balancer ne met pas fin, ne répond pas ou n’interagit d’aucune autre manière avec la charge utile d’un flux UDP ou TCP. Il ne s’agit pas d’un proxy. La validation de la connectivité avec un serveur frontal doit avoir lieu dans la bande avec le même protocole que celui utilisé dans un équilibrage de charge ou une règle NAT de trafic entrant. Au moins une de vos machines virtuelles doit générer une réponse pour qu’un client voie une réponse d’un serveur frontal.

  L’absence de réponse de la part du serveur frontal Load Balancer dans la bande indique qu’aucune machine virtuelle n’a pu répondre. Rien ne peut interagir avec un serveur frontal Load Balancer si aucune machine virtuelle ne peut répondre. Ce principe vaut également pour les connexions sortantes où la traduction d’adresses réseau avec masquage de port est uniquement prise en charge pour les protocoles TCP et UDP. Tous les autres protocoles IP, y compris ICMP, échoueront. Assignez une adresse IP publique de niveau d’instance pour résoudre ce problème. Pour plus d’informations, consultez [Compréhension de la traduction d’adresses réseau et des jetons d'accès personnel](load-balancer-outbound-connections.md#snat).

* Les Load Balancer internes ne traduisent pas les connexions sortantes vers le serveur frontal d’un Load Balancer interne, car ils se trouvent tous deux dans un espace d’adressage IP privé. Les Load Balancer publics offrent des [connexions sortantes](load-balancer-outbound-connections.md) d’adresses IP privées à l’intérieur du réseau virtuel vers des adresses IP publiques. Pour les Load Balancer internes, cette approche évite le risque d’épuisement du port SNAT à l’intérieur de l’espace d’adressage IP interne unique lorsque la traduction n’est pas requise.

  Un effet secondaire est que, si un flux sortant d’une machine virtuelle dans le pool principal tente un flux vers le serveur frontal du Load Balancer interne dans son pool _et_ s’il est mappé à lui-même, les deux aspects du flux ne correspondent pas. Étant donné qu’ils ne correspondent pas, le flux échoue. Le flux réussit s’il n’a pas été mappé avec la même machine virtuelle du pool principal que celle qui a créé le flux vers le serveur frontal.

  Lorsque le flux est mappé avec lui-même, le flux sortant apparaît comme provenant de la machine virtuelle vers le serveur frontal et le flux entrant correspondant apparaît comme étant issu de la machine virtuelle vers elle-même. Du point de vue du système d’exploitation invité, les parties entrante et sortante d’un même flux ne correspondent pas à l’intérieur de la machine virtuelle. La pile TCP ne reconnaît pas ces deux moitiés de flux comme faisant partie du même flux. La source et la destination ne correspondent pas. Lorsque le flux est mappé avec une autre machine virtuelle dans le pool principal, les parties du flux correspondent et la machine virtuelle peut répondre au flux.

  Le symptôme pour repérer ce scénario est la présence de délais d’expiration de connexion intermittents lorsque le flux revient au serveur principal qui est à l’origine du flux. Les solutions de contournement courantes incluent l’insertion d’une couche de proxy derrière le Load Balancer interne et l’utilisation de règles de style de retour direct du serveur (DSR). Pour plus d’informations, consultez [Serveurs frontaux multiples dans Azure Load Balancer](load-balancer-multivip-overview.md).

  Vous pouvez combiner un Load Balancer interne avec un proxy tiers ou utiliser une [Application Gateway](../application-gateway/application-gateway-introduction.md) interne pour les scénarios de proxy limités à HTTP/HTTPS. Vous pouvez utiliser un Load Balancer public pour résoudre ce problème, mais le scénario qui en résulte est sujet aux [épuisements SNAT](load-balancer-outbound-connections.md#snat). Évitez cette deuxième approche, sauf si elle est soigneusement gérée.

* En règle générale, les fragments d’adresse IP de transfert ne sont pas pris en charge sur les règles d’équilibrage de charge. La fragmentation IP des paquets UDP et TCP n’est pas prise en charge sur les règles d’équilibrage de charge. Les règles d’équilibrage de charge des ports à haute disponibilité peuvent être utilisées pour transférer des fragments IP existants. Pour plus d’informations, consultez [Présentation des ports de haute disponibilité](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez [Créer un Load Balancer De base public](quickstart-create-basic-load-balancer-portal.md) pour commencer à utiliser un Load Balancer : créez-en un, créez des machines virtuelles avec une extension IIS personnalisée installée et équilibrez la charge de l’application web entre les machines virtuelles.
