---
title: Référence Standard d’Azure Load Balancer et zones de disponibilité | Microsoft Docs
description: Référence Standard de Load Balancer et zones de disponibilité
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2018
ms.author: kumud
ms.openlocfilehash: 1f34a9319b8bbfba3f4a6f7446f949fc576aa4fa
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869055"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Référence Standard de Load Balancer et zones de disponibilité

La référence SKU Standard d’Azure Load Balancer prend en charge les scénarios des [zones de disponibilité](../availability-zones/az-overview.md). Plusieurs nouveaux concepts sont disponibles avec Standard Load Balancer, vous permettant d’optimiser la disponibilité de votre scénario de bout en bout en alignant les ressources sur des zones et en les distribuant parmi les zones.  Consultez [Zones de disponibilité](../availability-zones/az-overview.md) pour obtenir des informations sur les zones de disponibilité, les régions qui les prennent actuellement en charge et d’autres concepts et produits connexes. Les Zones de disponibilité Azure associées à Standard Load Balancer constituent un ensemble de fonctionnalités vaste et flexible capable de créer de nombreux scénarios différents.  Consultez ce document pour comprendre ces [concepts](#concepts) et obtenir des [conseils de conception](#design) de scénarios.

>[!NOTE]
>Pour d’autres rubriques connexes, voir [Zones de disponibilité](https://aka.ms/availabilityzones). 

## <a name="concepts"></a> Concepts des zones de disponibilité appliqués à Load Balancer

Il n’existe aucune relation directe entre les ressources Load Balancer et l’infrastructure réelle ; la création d’un équilibrage de charge ne crée pas d’instance. Les ressources Load Balancer sont des objets dans lesquels vous pouvez exprimer la manière dont Azure doit programmer son infrastructure multilocataire prédéfinie pour obtenir le scénario que vous souhaitez créer.  Ces ressources revêtent une grande importance dans le contexte des zones de disponibilité, car une ressource Load Balancer peut à elle seule contrôler la programmation de l’infrastructure dans plusieurs zones de disponibilité alors qu’un service redondant interzone apparaît comme une ressource unique du point de vue du client.

Les fonctions d’une ressource Load Balancer s’expriment sous forme de définition de frontend, règle, sonde d’intégrité et pool backend.

Dans le contexte des zones de disponibilité, le comportement et les propriétés d’une ressource Load Balancer sont décrits comme étant redondants interzone ou zonaux.  Ces adjectifs décrivent la zonalité d’une propriété.  Dans le contexte de Load Balancer, la redondance interzone implique toujours *toutes les zones* tandis que l’adjectif zonal implique une garantie du service dans une *zone unique*.

À la fois public et interne, Load Balancer prend en charge les scénarios zonaux et redondants interzone. Le trafic peut être dirigé vers les zones en fonction des besoins (*équilibrage de charge entre les zones*).

Une ressource Load Balancer elle-même est régionale mais jamais zonale.  Un réseau virtuel et un sous-réseau sont toujours régionaux mais jamais zonaux.

### <a name="frontend"></a>Serveur frontal

Un frontend Load Balancer est une configuration IP frontend qui fait référence à une ressource d’adresse IP publique ou à une adresse IP privée au sein du sous-réseau d’une ressource de réseau virtuel.  Il forme le point de terminaison à charge équilibrée où votre service est exposé.

Une ressource Load Balancer peut contenir des frontends zonaux et redondants interzone en même temps. 

Quand une ressource IP publique est garantie dans une zone, la zonalité (ou son manque) n’est pas mutable.  Pour modifier ou omettre la zonalité d’un frontend IP public, vous devez recréer l’adresse IP publique dans la zone appropriée.  

Vous pouvez modifier la zonalité d’un frontend Load Balancer interne en supprimant et en recréant le frontend, pour ensuite modifier ou omettre la zonalité.

Quand vous utilisez plusieurs frontends, consultez [ Frontends multiples pour Load Balancer](load-balancer-multivip-overview.md) pour prendre connaissance de considérations importantes.

#### <a name="zone-redundant-by-default"></a>Redondance dans une zone par défaut

Dans une région avec des zones de disponibilité, un frontend de référence Standard de Load Balancer est redondant interzone par défaut.  Une adresse IP frontend unique peut survivre à des échecs de zone et être utilisée pour atteindre tous les membres du pool backend indépendamment de la zone. Il ne s’agit pas d’un chemin de données sans réponse, car toute nouvelle tentative ou tout rétablissement réussissent. Les schémas de redondance DNS ne sont pas nécessaires. L’adresse IP unique du frontend est simultanément servie par plusieurs déploiements d’infrastructure indépendants dans plusieurs Zones de disponibilité Azure.  La redondance interzone signifie que tous les flux entrants ou sortants sont servis simultanément par plusieurs Zones de disponibilité Azure dans une région à l’aide d’une adresse IP unique.

Une ou plusieurs zones de disponibilité peuvent échouer sans empêcher le chemin de données de survivre dans la mesure où une seule zone de la région reste saine. La configuration redondante interzone est la valeur par défaut. Aucune action supplémentaire n’est nécessaire.  Quand une région obtient la possibilité de prendre en charge des zones de disponibilité, un frontend existant devient automatiquement redondant interzone.

Utilisez le script suivant pour créer une adresse IP publique redondante interzone pour votre référence Standard de Load Balancer interne. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Utilisez le script suivant pour créer une adresse IP de frontend redondante interzone pour votre référence Standard de Load Balancer interne. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "[resourceGroup().location]",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-guarantee"></a>Garantie de zone facultative

Vous pouvez choisir de garantir un frontend dans une seule zone, ce qui donne un *frontend zonal*.  Cela signifie que tout flux entrant ou sortant est servi par une seule zone dans une région.  Votre frontend connaît le même sort que l’intégrité de la zone.  Le chemin de données n’est pas affecté par les défaillances des zones autres que celles dans lesquelles il a été garanti. Vous pouvez utiliser des frontends zonaux pour exposer une adresse IP par zone de disponibilité.  En outre, vous pouvez utiliser directement des frontends zonaux ou, lorsque le frontend se compose d’adresses IP publiques, les intégrer à un produit d’équilibrage de charge DNS comme [Traffic Manager](../traffic-manager/traffic-manager-overview.md) et utiliser un seul nom DNS, qu’un client pourra résoudre en plusieurs adresses IP zonales.  Vous pouvez également procéder ainsi pour exposer des points de terminaison à charge équilibrée par zone afin de surveiller individuellement chaque zone.  Pour fusionner ces concepts (le même backend à la fois redondant interzone et zonal), passez en revue [Frontends multiples pour Azure Load Balancer](load-balancer-multivip-overview.md).

Pour un frontend Load Balancer public, vous ajoutez un paramètre *zones* à l’adresse IP publique référencée par la configuration IP frontend.  

Pour un frontend Load Balancer interne, ajoutez un paramètre *zones* à la configuration IP de frontend Load Balancer interne. Le frontend zonal amène Load Balancer à garantir une adresse IP d’un sous-réseau dans une zone spécifique.

Utilisez le script suivant pour créer une adresse IP publique standard zonale dans la zone de disponibilité 1. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "[resourceGroup().location]",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Utilisez le script suivant pour créer un frontend de référence Standard de Load Balancer interne dans la zone de disponibilité 1.

Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles. Définissez également la propriété **zones** dans la configuration IP frontend de la ressource enfant.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Équilibrage de charge entre les zones

L’équilibrage de charge entre les zones est la capacité de Load Balancer à atteindre un point de terminaison backend dans n’importe quelle zone. Cette capacité est indépendante du frontend et de sa zonalité.

Pour aligner et garantir votre déploiement au sein d’une zone unique, alignez les ressources zonales frontales et principales sur la même zone. Aucune action supplémentaire n’est requise.

### <a name="backend"></a>Backend

Load Balancer fonctionne avec Machines virtuelles.  Toute machine virtuelle incluse dans un réseau virtuel unique peut faire partie du pool backend, qu’elle ait été ou non garantie dans une zone ou quelle que soit la zone dans laquelle elle a été garantie.

Pour aligner et de garantir votre frontend et votre backend avec une seule zone, placez uniquement des machines virtuelles au sein de la même zone dans le pool backend respectif.

Pour traiter des machines virtuelles réparties dans plusieurs zones, placez-les simplement dans le même pool backend.  Quand vous utilisez des groupes de machines virtuelles identiques, vous pouvez en placer un ou plusieurs dans le même pool backend.  Et chacun d’eux peut se trouver dans une seule ou plusieurs zones.

### <a name="outbound-connections"></a>Connexions sortantes

Les [connexions sortantes](load-balancer-outbound-connections.md) sont servies par toutes les zones et sont automatiquement redondantes dans une zone dans une région qui a des zones de disponibilité quand une machine virtuelle est associée à un Load Balancer public et à un frontend redondant dans une zone.  Les allocations de port SNAT de connexion sortante survivent aux échecs de zone.  

À leur tour, si la machine virtuelle est associée à un Load Balancer public et à un frontend zonal, le service des connexions sortantes par une seule zone est garanti.  Les connexions sortantes partagent leur sort avec l’intégrité de la zone concernée.

La préallocation de port SNAT et l’algorithme sont les mêmes avec ou sans zones.

### <a name="health-probes"></a>Sondes d’intégrité

Vos définitions de sonde d’intégrité existantes restent telles qu’elles sont sans zones de disponibilité.  Mais nous avons développé le modèle d’intégrité au niveau de l’infrastructure. 

Quand vous utilisez des frontends redondants interzone, Load Balancer développe son modèle d’intégrité interne pour sonder indépendamment l’accessibilité d’une machine virtuelle à partir de chaque zone de disponibilité et arrête les chemins entre les zones susceptibles d’avoir été en échec sans intervention du client.  Si un chemin donné n’est pas disponible à partir de l’infrastructure Load Balancer d’une zone vers une machine virtuelle dans une autre zone, Load Balancer peut détecter et éviter cet échec. Les autres zones qui peuvent accéder à cette machine virtuelle peuvent continuer de servir la machine virtuelle à partir de leurs frontends respectifs.  Par conséquent, il est possible que pendant des événements d’échec, chaque zone ait des distributions de flux légèrement différentes pendant qu’elle protège l’intégrité globale de votre service de bout en bout.

## <a name="design"></a> Remarques relatives à la conception

Load Balancer est volontairement flexible dans le contexte des zones de disponibilité. Vous pouvez choisir de vous aligner sur des zones ou d’être redondant interzone.  Une disponibilité accrue peut s’obtenir au prix d’une complexité accrue. C’est pourquoi vous devez concevoir la disponibilité à des fins de performances optimales.  Examinons certains points importants liés à la conception.

### <a name="automatic-zone-redundancy"></a>Redondance dans une zone automatique

Avec Load Balancer, il est simple d’avoir une adresse IP unique comme frontend redondant interzone. Une adresse IP redondante interzone peut servir en toute sécurité une ressource zonale dans toute zone et survivre à un ou plusieurs échecs de zone tant qu’une seule zone reste saine au sein de la région. Inversement, un frontend zonal est une réduction du service à une seule zone et partage son sort avec la zone concernée.

La redondance dans une zone n’implique pas de chemin de données ni de plan de contrôle sans réponse ; il s’agit expressément d’un plan de données. Les flux redondants interzone peuvent utiliser toutes les zones et les flux d’un client utilise toutes les zones saines dans une région. En cas d’échec de zone, les flux de trafic qui utilisent les zones saines à ce moment-là ne sont pas impactés.  Les flux de trafic qui utilisent une zone au moment de l’échec de zone peuvent être impactés, mais les applications peuvent récupérer et ces flux peuvent continuer dans les zones saines restantes dans la région après retransmission ou rétablissement une fois qu’Azure a convergé autour de l’échec de zone.

### <a name="xzonedesign"></a> Limites entre les zones

Il est important de comprendre que dès lors qu’un service de bout en bout traverse des zones, vous partagez le sort non pas d’une seule zone mais éventuellement de plusieurs.  Par conséquent, votre service de bout en bout n’a peut-être pas obtenu de disponibilité sur des déploiements non zonaux.

Évitez d’introduire involontairement des dépendances entre les zones qui invalident les gains de disponibilité quand vous utilisez des Zones de disponibilité Azure.  Quand votre application comprend plusieurs composants que vous souhaitez résistants aux échecs de zone, veillez à garantir la survie de suffisamment de composants critiques en cas d’échec de zone.  Par exemple, un seul composant critique de votre application peut affecter toute votre application s’il se trouve dans une seule zone autre que les zones survivantes.  De plus, prévoyez également la restauration de la zone et la façon dont votre application converge. Intéressons-nous à quelques points importants et utilisons-les pour poser les questions qui surviennent quand vous réfléchissez à votre scénario spécifique.

- Si votre application comporte deux composants, comme une adresse IP et une machine virtuelle avec disque managé, et qu’ils sont garantis dans la zone 1 et la zone 2, quand la zone 1 est en situation d’échec, votre service de bout en bout ne survit pas.  Ne franchissez pas de zones, sauf si vous comprenez bien que vous créez un mode d’échec potentiellement dangereux.

- Si votre application comporte deux composants comme une adresse IP et une machine virtuelle avec disque managé, et qu’ils sont garantis comme redondants interzone et se trouvent dans la zone 1, votre service de bout en bout survit à un échec de la zone 2, de la zone 3, ou des deux, sauf en cas d’échec de la zone 1.  Toutefois, vous perdez une certaine capacité à raisonner sur l’intégrité de votre service si tout ce que vous observez, c’est l’accessibilité du frontend.  Envisagez de développer un modèle d’intégrité et de capacité plus étendu.  Vous pouvez utiliser les concepts de redondance interzone et de caractéristique zonale ensemble pour développer un insight et une facilité de gestion.

- Si votre application comporte deux composants, comme un frontend Load Balancer redondant interzone et un groupe de machines virtuelles identiques entre trois zones, vos ressources incluses dans les zones non impactées par l’échec sont disponibles mais la capacité de votre service de bout en bout peut être dégradée pendant l’échec de zone. Du point de vue de l’infrastructure, votre déploiement peut survivre à un ou plusieurs échecs de zone. Ainsi, les questions suivantes se posent :
  - Est-ce que vous comprenez comment votre application raisonne sur ces échecs et cette capacité détériorée ?
  - Avez-vous besoin de mesures de protection pour votre service afin de forcer un basculement vers une paire de régions si nécessaire ?
  - Comment allez-vous surveiller, détecter et atténuer un tel scénario ? Vous pouvez peut-être utiliser les diagnostics de la référence Standard de Load Balancer pour renforcer la surveillance des performances de votre service de bout en bout. Examinez ce qui est disponible et ce qui peut nécessiter une augmentation pour vous faire une idée exhaustive.

- Les zones permettent de comprendre et contenir les échecs plus facilement.  Toutefois, un échec de zone n’est pas différent des autres échecs quand il s’agit de concepts comme les délais d’attente, les nouvelles tentatives et les algorithmes d’interruption. Même si Azure Load Balancer fournit des chemins redondants interzone pour tenter une récupération rapide, au niveau du paquet en temps réel, les retransmissions ou rétablissements peuvent se produire dès le début d’un échec et il est important de comprendre comment votre application gère ces échecs. Votre schéma d’équilibrage de charge survit, mais vous avez besoin de prévoir ce qui suit :
  - Lorsqu’une zone est en échec, votre service de bout en bout le comprend-il et en cas de perte de l’état, comment allez-vous récupérer ?
  - Quand une zone est rétablie, votre application comprend-elle comment converger en toute sécurité ?

### <a name="zonalityguidance"></a> Redondant dans une zone et zonal

La redondance interzone offre une option non spécifique d’une zone et en même temps résiliente avec une adresse IP unique pour le service.  À son tour, la complexité s’en retrouve réduite.  La redondance interzone permet également une mobilité entre les zones et peut s’utiliser sans risque sur les ressources de toute zone.  De plus, elle sera testée dans les régions sans zones de disponibilité, ce qui permettra de limiter les modifications nécessaires une fois qu’une région obtiendra réellement des zones de disponibilité.  La syntaxe de configuration d’une adresse IP ou d’un frontend redondants interzone réussit dans n’importe quelle région, y compris celles dépourvues de zones de disponibilité.

Le mode zonal peut fournir une garantie explicite dans une zone, en partageant son sort avec l’intégrité de la zone. L’association d’une adresse IP zonale ou d’un frontend Load Balancer zonal peut être un attribut souhaitable ou raisonnable, particulièrement si votre ressource attachée est une machine virtuelle zonale dans la même zone.  Ou peut-être que votre application nécessite des connaissances explicites sur la zone dans laquelle une ressource se trouve et que vous voulez réfléchir explicitement à la disponibilité dans des zones distinctes.  Vous pouvez choisir d’exposer plusieurs frontends zonaux pour un service de bout en bout distribué entre des zones (autrement dit, des frontends zonaux par zone pour plusieurs groupes zonaux de machines virtuelles identiques).  Et si vos frontends zonaux sont des adresses IP publiques, vous pouvez les utiliser pour exposer votre service avec [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Ou bien, vous pouvez utiliser plusieurs frontends zonaux pour obtenir des insights d’intégrité et de performance par zone par le biais de solutions de surveillance tierces et exposer le service global avec un frontend redondant interzone. Vous devez uniquement servir des ressources zonales avec des frontends zonaux alignés sur la même zone et éviter les scénarios entre zones potentiellement dangereux pour les ressources zonales.  Les ressources zonales existent uniquement dans les régions où des zones de disponibilité existent.

Il n’est pas possible de déterminer le meilleur choix sans connaître l’architecture du service.

## <a name="limitations"></a>Limites

- Alors que le plan de données est entièrement redondant interzone (sauf si une garantie zonale a été spécifiée), les opérations de plan de contrôle ne sont pas entièrement redondantes interzone.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- En savoir plus sur la [référence Standard de Load Balancer](load-balancer-standard-overview.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans une zone à l’aide de Load Balancer standard avec un serveur frontal zonal](load-balancer-standard-public-zonal-cli.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans des zones à l’aide de Load Balancer standard avec un serveur frontal redondant interzone](load-balancer-standard-public-zone-redundant-cli.md)
