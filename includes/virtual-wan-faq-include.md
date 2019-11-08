---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0ba69715998f110d88015bdba2fbf340a6b64089
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491637"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Quelle est la différence entre une passerelle de réseau virtuel Azure (passerelle VPN) et une passerelle VPN Azure Virtual WAN ?

WAN virtuel fournit une connectivité de site à site à grande échelle et est conçu pour le débit, l’évolutivité et la facilité d’utilisation. Lorsque vous connectez un site à une passerelle VPN Virtual WAN, il est différent d’une passerelle de réseau virtuel normale qui utilise le type de passerelle « VPN ». De même, lorsque vous connectez un circuit ExpressRoute à un hub Virtual WAN, il utilise une ressource différente pour la passerelle ExpressRoute que la passerelle de réseau virtuel normale utilisant le type de passerelle « ExpressRoute ». Virtual WAN prend en charge un débit agrégé pouvant atteindre 20 Gbits/s pour VPN et ExpressRoute. Virtual WAN dispose également d’une automatisation pour la connectivité avec un écosystème de partenaires d’appareils de branche CPE. Les appareils de branche CPE intègrent une automatisation qui provisionne et se connecte automatiquement à Azure Virtual WAN. Ces périphériques sont disponibles à partir d’un écosystème croissant des partenaires SD-WAN et VPN. Consultez la [liste des partenaires préférés](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>En quoi Virtual WAN est-il différent d’une passerelle de réseau virtuel Azure ?

Le VPN d’une passerelle de réseau virtuel est limité à 30 tunnels. Pour les connexions, vous devez utiliser un WAN virtuel pour le VPN à grande échelle. Vous pouvez connecter jusqu’à 1 000 connexions de branche par région (hub virtuel) avec un agrégat de 20 Gbits/s par hub. Une connexion est un tunnel actif-actif entre l’appareil VPN local et le hub virtuel. Vous pouvez disposer d’un hub par région, ce qui signifie que vous pouvez connecter plus de 1000 branches parmi tous les hubs.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quels sont les fournisseurs d’appareils (partenaires Virtual WAN) pris en charge ?

À ce stade, de nombreux partenaires prennent en charge l’expérience Virtual WAN entièrement automatisée. Pour plus d’informations, consultez [Partenaires WAN virtuel](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quelles sont les étapes d’automatisation des partenaires Virtual WAN ?

Pour découvrir les étapes d’automatisation des partenaires, consultez l’article [Partenaires Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Est-il nécessaire d’utiliser un périphérique de partenaire préféré ?

Non. Vous pouvez utiliser n’importe quel appareil prenant en charge les VPN et qui respecte les exigences Azure pour la prise en charge de IPsec IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Comment les partenaires WAN virtuel automatisent-ils la connectivité avec le WAN virtuel Azure ?

Les solutions de connectivité à définition logicielle gèrent généralement leurs appareils de branche à l’aide d’un contrôleur ou un centre de provisionnement des appareils. Le contrôleur peut utiliser des API Azure pour automatiser la connectivité au WAN virtuel Azure. Pour plus d’informations, consultez Virtual WAN partner automation (Automation de partenaire WAN virtuel).

Les solutions de connectivité à définition logicielle gèrent généralement leurs appareils de branche à l’aide d’un contrôleur ou un centre de provisionnement des appareils. Le contrôleur peut utiliser des API Azure pour automatiser la connectivité au WAN virtuel Azure. L’automatisation comprend le chargement des informations de branche, le téléchargement de la configuration Azure, la configuration de tunnels IPSec dans des hubs virtuels Azure et la configuration automatique de la connectivité à l’appareil de branche à Azure Virtual WAN. Lorsque vous avez des centaines de branches, la connexion à l’aide de partenaires CPE Virtual WAN est simple, car l’expérience d’intégration n’a plus besoin de configurer et de gérer une connectivité IPsec à grande échelle. Pour plus d’informations, consultez [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automation de partenaire WAN virtuel).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Est-il nécessaire d’utiliser un périphérique de partenaire préféré ?

Non. Vous pouvez utiliser n’importe quel appareil prenant en charge les VPN et qui respecte les exigences Azure pour la prise en charge de IPsec IKEv2/IKEv1.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Comment Virtual WAN prend en charge les appareils SD-WAN ?

Les partenaires Virtual WAN automatisent la connectivité IPsec vers les points de terminaison du VPN Azure. Si le partenaire Virtual WAN est un fournisseur de SD-WAN, il est nécessaire que le contrôleur SD-WAN gère l’automatisation et la connectivité IPsec vers les points de terminaison de VPN Azure. Si l’appareil SD-WAN nécessite son propre point de terminaison au lieu du VPN Azure pour toutes les fonctionnalités SD-WAN propriétaires, vous pouvez déployer le point de terminaison SD-WAN dans un réseau virtuel Azure et le faire coexister avec Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>WAN virtuel modifie-t-il les fonctionnalités de connectivité existantes ?

Aucune modification n’est apportée aux fonctionnalités de connectivité Azure existantes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existe-t-il des nouvelles ressources de Resource Manager disponibles pour le WAN virtuel ?
  
Oui, WAN virtuel introduit de nouvelles ressources de Resource Manager. Pour plus d’informations, consultez la [Vue d’ensemble](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Combien de périphériques VPN peuvent se connecter à un même hub ?

Chaque hub virtuel prend en charge jusqu’à 1000 connexions. Chaque connexion se compose de quatre liens et chaque connexion de lien prend en charge deux tunnels qui sont dans une configuration actif-actif. Les tunnels se terminent par une passerelle VPN de hub virtuel Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>L’appareil VPN local peut-il se connecter à plusieurs hubs ?

Oui. Au départ, le flux du trafic s’effectue de l’appareil local vers le périmètre réseau de Microsoft le plus proche, puis vers le hub virtuel.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Puis-je déployer et utiliser l’appliance virtuelle de mon réseau favori (dans un réseau virtuel NVA) avec le WAN virtuel Azure ?

Oui, vous pouvez connecter le réseau virtuel VNet de l’appliance virtuelle (NVA) au WAN virtuel Azure. Connectez d’abord le réseau virtuel VNet de l’appliance virtuelle au hub à l’aide d’une connexion au réseau virtuel du hub. Créez ensuite un itinéraire de hub virtuel dont le prochain tronçon pointe vers l’appliance virtuelle. Vous pouvez appliquer plusieurs itinéraires à la table de route du hub virtuel. Par ailleurs, les membres spoke connectés au réseau virtuel VNet de l’appliance virtuelle (NVA) doivent être connectés au hub virtuel pour s’assurer que leurs itinéraires VNet sont propagés vers les systèmes locaux.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Puis-je créer une appliance virtuelle réseau à l’intérieur du hub virtuel ?

Une appliance virtuelle réseau (NVA, pour Network Virtual Appliance) ne peut pas être déployée à l’intérieur d’un hub virtuel. Toutefois, vous pouvez la créer dans un réseau virtuel spoke connecté au hub virtuel et activer un itinéraire dans le hub pour diriger le trafic pour le réseau virtuel de destination via l’adresse IP NVA (de la carte réseau).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Un réseau virtuel spoke peut-il avoir une passerelle de réseau virtuel ?

Non. Le réseau virtuel spoke ne peut pas avoir de passerelle de réseau virtuel s’il est connecté au hub virtuel.

### <a name="is-there-support-for-bgp"></a>Le protocole BGP est-il pris en charge ?

Oui, BGP est pris en charge. Quand vous créez un site VPN, vous pouvez fournir les paramètres BGP qu’il contient. Cela implique que toutes les connexions créées dans Azure pour ce site sont activées pour BGP. Par ailleurs, si vous avez un réseau virtuel avec une appliance virtuelle réseau, et si ce réseau virtuel d’appliance virtuelle réseau est attaché à un hub Virtual WAN, vous devez désactiver BGP pour les spokes attachés au réseau virtuel d’appliance virtuelle réseau pour assurer la publication appropriée des routes de ce dernier. Enfin, connectez ces réseaux virtuels de spokes au réseau virtuel Virtual Hub pour assurer la propagation des routes de réseau virtuel sur les systèmes locaux.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Puis-je diriger le trafic à l’aide de UDR dans le hub virtuel ?

Oui, vous pouvez diriger le trafic vers un réseau virtuel à l’aide de la table de route du hub virtuel. Cela vous permet de définir des itinéraires pour les réseaux virtuels de destination dans Azure via une adresse IP spécifique (en général, la carte réseau de la NVA).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Y a-t-il des informations de licence ou de tarification pour le WAN virtuel ?

Oui. Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Comment calculer le prix d’un hub ?

* Vous payez les frais de services dans le hub. Par exemple, si vous voulez connecter 10 branches ou appareils locaux à Azure Virtual WAN, vous devez établir une connexion aux points de terminaison VPN du hub. S’il s’agit d’un VPN de 1 unité d’échelle = 500 Mbits/s, vous êtes facturé 0,361 USD/h. Chaque connexion est facturée 0,05 USD/h. Pour 10 connexions, le total des frais de service/h est 0,361 USD + 0,5 USD/h. Les frais de données pour le trafic quittant Azure s’appliquent.

* Des frais supplémentaires sont facturés pour le hub. Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Si vous aviez une passerelle ExpressRoute en raison des circuits ExpressRoute qui se connectent à un hub virtuel, vous payez le prix unitaire d’échelle. Chaque unité d’échelle dans ER est de 2 Gbits/s et chaque unité de connexion est facturée au même taux que l’unité de connexion VPN.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Comment les nouveaux partenaires qui ne figurent pas dans votre liste de partenaires de lancement sont-ils intégrés ?

Toutes les API de réseau étendu virtuel sont des API ouvertes. Vous pouvez consulter la documentation pour évaluer la faisabilité technique. Si vous avez des questions, envoyez un e-mail à azurevirtualwan@microsoft.com. Un partenaire idéal dispose d’un appareil qui peut être approvisionné pour une connectivité IPsec IKEv1 ou IKEv2.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Que se passe-t-il si j’utilise un appareil qui n’est pas dans la liste des partenaires Virtual WAN ? Puis-je toujours l’utiliser pour me connecter au VPN Azure Virtual WAN ?

Oui, si l’appareil prend en charge IPsec IKEv1 ou IKEv2. Les partenaires Virtual WAN automatisent la connectivité de l’appareil aux points de terminaison du VPN Azure. Cela implique des étapes d’automatisation comme « chargement des informations de branche », « IPsec et configuration » et « connectivité ». Comme votre appareil ne vient pas d’un écosystème de partenaires Virtual WAN, vous devez vous-même récupérer la configuration d’Azure et mettre à jour votre appareil pour configurer la connectivité IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Est-il possible de construire le WAN virtuel Azure avec un modèle Resource Manager ?

La configuration simple d’un Virtual WAN avec un hub et un site VPN peut être créée en utilisant un [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN est principalement un service piloté par REST ou le portail.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Est-ce que le peering Global VNet est pris en charge par le WAN virtuel Azure ? 

Vous pouvez connecter un réseau virtuel dans une région différente de celle de votre réseau étendu virtuel.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Est-ce que les réseaux virtuels membres spoke et connectés à un hub virtuel peuvent communiquer entre eux ?

Oui. Les réseaux virtuels Spoke peuvent communiquer directement par le biais du peering de réseau virtuel. Pour en savoir plus, consultez [Peering de réseaux virtuels](../articles/virtual-network/virtual-network-peering-overview.md). Nous prenons également en charge les réseaux virtuels qui communiquent transitivement par le biais du hub.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Qu’est-ce qu’une connexion de branche à Azure Virtual WAN ?

Une connexion à partir d’un appareil de branche dans Azure Virtual WAN prend en charge jusqu’à quatre liens. Un lien désigne le lien de connectivité physique à l’emplacement de la branche (par exemple : ATT, Verizon etc.). Chaque connexion de lien est composée de deux tunnels IPsec actifs/actifs.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connectivité de branche à branche est-elle autorisée dans le WAN virtuel ?

Oui, la connectivité de branche à branche est autorisée dans le WAN virtuel pour VPN et VPN vers ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Le trafic branche à branche traverse-t-il Azure Virtual WAN ?

Oui.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Virtual WAN nécessite-t-il ExpressRoute à partir de chaque site ?

Non, ce WAN ne nécessite pas ExpressRoute à partir de chaque site. Il utilise la connectivité site à site IPsec standard via des liaisons Internet à partir de l’appareil vers un hub Azure Virtual WAN. Vos sites peuvent être connectés à un réseau de fournisseur à l’aide d’un circuit ExpressRoute. Pour les sites connectés à l’aide d’ExpressRoute dans un hub virtuel, les sites peuvent afficher un trafic de branche à branche entre VPN et ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Existe-t-il une limite de débit réseau lors de l’utilisation du WAN virtuel Azure ?

Le nombre de branches est limité à 1 000 connexions par hub/région et à un total de 20 Gbits/s dans le hub. Vous pouvez avoir 1 hub par région.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Quel est le nombre de connexions VPN prises en charge par un hub Virtual WAN ?

Un hub Azure Virtual WAN peut prendre en charge jusqu’à 1 000 connexions S2S, 10 000 connexions P2S et 4 connexions ExpressRoute simultanément.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Quel est le débit VPN total d’un tunnel VPN et d’une connexion ?

Le débit VPN total maximal d’un concentrateur est de 20 Gbit/s, en fonction de l’unité d’échelle choisie. Le débit est partagé par toutes les connexions existantes. Chaque tunnel dans une connexion peut prendre en charge jusqu’à 1 Gbit/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Je ne vois pas le paramètre 20 Gbits/s pour le hub virtuel dans le portail. Comment le configurer ?

Accédez à la passerelle VPN à l’intérieur d’un hub sur le portail, puis cliquez sur l’unité d’échelle pour la remplacer par le paramètre approprié.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN autorise-t-il l’appareil en local à utiliser plusieurs ISP en parallèle, ou faut-il toujours un tunnel VPN unique ?

Une connexion entrante dans un VPN Virtual WAN est toujours un tunnel actif-actif (pour assurer la résilience dans la même région ou le même hub) utilisant un lien disponible au niveau de la branche. Ce lien peut être un lien de fournisseur de services Internet (ISP) au niveau de la branche locale. Le « VPNSite » de Virtual WAN offre la possibilité d’ajouter des informations de lien au site. Si vous avez plusieurs ISP au niveau de la branche et que chacun des ISP a fourni un lien, ces informations peuvent être configurées dans les informations du site VPN dans Azure. Toutefois, la gestion du basculement entre les ISP au niveau de la branche est une opération de routage entièrement centrée sur les branches.

### <a name="what-is-global-transit-architecture"></a>Qu’est-ce que l’architecture de transit global ?

Pour plus d’informations sur l’architecture de transit global, consultez [Architecture du réseau de transit global et Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Comment le trafic est-il acheminé sur la dorsale principale d’Azure ?

Le trafic suit le modèle suivant : appareil de branche -> ISP -> périmètre réseau de Microsoft - > Microsoft DC (réseau virtuel du hub) -> périmètre réseau de Microsoft -> ISP -> appareil de branche

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Dans ce modèle, de quoi avez-vous besoin sur chaque site ? Seulement d’une connexion Internet ?

Oui. Une connexion Internet et un appareil physique qui prend en charge IPsec, de préférence parmi nos [partenaires Virtual WAN](../articles/virtual-wan/virtual-wan-locations-partners.md) intégrés. Si vous le souhaitez, vous pouvez gérer manuellement la configuration et la connectivité à Azure à partir de l’appareil de votre choix.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Comment activer l’itinéraire par défaut (0.0.0.0/0) dans une connexion (VPN, ExpressRoute ou Réseau virtuel Microsoft Azure) :

Un hub virtuel peut propager un itinéraire par défaut appris à une connexion de réseau virtuel/VPN site à site/ExpressRoute si l’indicateur est « Activé » sur la connexion. Cet indicateur est visible lorsque l’utilisateur modifie une connexion de réseau virtuel, une connexion VPN ou une connexion ExpressRoute. Par défaut, cet indicateur est désactivé lorsqu’un site ou un circuit ExpressRoute est connecté à un hub. Il est activé par défaut lorsqu’une connexion de réseau virtuel est ajoutée pour connecter un réseau virtuel à un hub virtuel. L’itinéraire par défaut ne provient pas du hub Virtual WAN ; il est propagé s’il est déjà appris par le hub Virtual WAN suite au déploiement d’un pare-feu dans le hub, ou si le tunneling forcé est activé sur un autre site connecté.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quelles sont les différences entre les types de Virtual WAN (de base et standard) ?

Le type de réseau étendu « de base » vous permet de créer un hub de base (SKU = de base). Un type de réseau étendu « standard » vous permet de créer un hub standard (SKU = standard). Les hubs de base sont limités à la fonctionnalité VPN site à site. Les hubs standard vous permettent d’utiliser ExpressRoute, le VPN d’utilisateur (P2S), le hub à maille pleine et le transit de réseau virtuel à réseau virtuel par leur intermédiaire. Vous payez des frais de base de 0,25 USD/h pour les hubs standard et des frais de traitement des données pour le transit via les hubs au cours de la connectivité de réseau virtuel à réseau virtuel, ainsi que le traitement des données pour le trafic hub à hub. Pour plus d’informations, consultez [Réseaux étendus virtuels de base et standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Pour connaître les tarifs, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/virtual-wan/).
