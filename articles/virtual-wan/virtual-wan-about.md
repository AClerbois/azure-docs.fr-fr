---
title: Présentation du WAN virtuel Azure | Microsoft Docs
description: Découvrez plus d’informations sur la connectivité branche à branche scalable et automatisée de Virtual WAN, les régions disponibles et les partenaires.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6f2f002c5ff08e21741927d07a0facfd09ec0914
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295689"
---
# <a name="what-is-azure-virtual-wan"></a>Qu’est-ce que le WAN virtuel Azure ?

Azure Virtual WAN est un service réseau qui offre une connectivité de branche optimisée et automatisée via Azure. Les régions Azure servent de hubs auxquels vous pouvez connecter vos branches. Une fois les branches connectées, vous pouvez exploiter la structure principale d’Azure pour établir une connectivité branche à réseau virtuel et branche à branche. Pour obtenir la liste des partenaires et des emplacements qui prennent en charge le VPN Virtual WAN, consultez l’article [Partenaires et emplacements Virtual WAN](virtual-wan-locations-partners.md).

Azure Virtual WAN regroupe plusieurs services de connectivité cloud Azure comme le VPN site à site (mis en disposition générale), ExpressRoute (préversion), VPN utilisateur point à site (préversion) dans une seule interface opérationnelle. La connectivité aux réseaux virtuels Azure est établie à l’aide de connexions de réseau virtuel.

![Diagramme WAN virtuel](./media/virtual-wan-about/vwangraphic.png)

Cet article fournit un aperçu rapide de la connectivité réseau dans Azure Virtual WAN. Le WAN virtuel offre les avantages suivants :

* **Solutions de connectivité intégrée dans une topologie Hub-and-Spoke :** Automatisez la configuration et la connectivité site à site entre les sites locaux et un hub Azure.
* **Installation et configuration de spokes automatisés :** Connectez vos charges de travail et vos réseaux virtuels au hub Azure sans interruption.
* **Résolution de problèmes intuitive :** Vous pouvez voir le flux de bout en bout au sein d’Azure et utiliser ces informations pour prendre les mesures nécessaires.

## <a name="resources"></a>Ressources du WAN virtuel

Pour configurer un WAN virtuel de bout en bout, vous devez créer les ressources suivantes :

* **virtualWAN :** La ressource virtualWAN représente une superposition virtuelle de votre réseau Azure et est une collection de plusieurs ressources. Elle contient des liens vers tous les hubs virtuels que vous souhaitez faire figurer dans le WAN virtuel. Les ressources du WAN virtuel sont isolées les unes des autres et ne peuvent pas contenir de hub commun. Les hubs virtuels d’un WAN virtuel ne communiquent pas entre eux. La propriété « Allow branch to branch traffic » (Autoriser le trafic de branche à branche) autorise le trafic entre sites VPN ainsi que trafic entre les sites compatibles VPN et ExpressRoute (actuellement en préversion).

* **Hub :** Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Le hub est le cœur de votre réseau au sein d’une région spécifique. Il ne peut exister qu’un seul hub par région Azure. Lorsque vous créez un hub à l’aide du portail Azure, le système crée un réseau virtuel et une passerelle VPN pour le hub virtuel.

  Une passerelle de hub virtuel est différente d’une passerelle de réseau virtuel que vous utilisez pour les services ExpressRoute et Passerelle VPN. Par exemple, quand vous utilisez Virtual WAN, vous ne créez pas de connexion site à site directe entre votre site local et votre réseau virtuel. Au lieu de cela, vous établissez une connexion site à site avec le hub. Le trafic passe toujours par la passerelle du hub. Cela signifie que vos réseaux virtuels n’ont pas besoin d’avoir leur propre passerelle de réseau virtuel. Le WAN virtuel simplifie la mise à l’échelle de vos réseaux virtuels via le hub virtuel et la passerelle de hub virtuel.

* **Connexion entre hub et réseau virtuel :** La ressource Connexion entre hub et réseau virtuel permet de connecter facilement le hub à votre réseau virtuel. À ce stade, vous pouvez uniquement vous connecter à des réseaux virtuels qui se trouvent dans la même région que le hub.

* **Table de routage de hub :**  Vous pouvez créer une route de hub virtuel et l’appliquer à la table de routage de hub virtuel. Vous pouvez appliquer plusieurs itinéraires à la table de routage du hub virtuel.

**Ressources Virtual WAN supplémentaires**

  * **Site :** Cette ressource est utilisée pour les connexions site à site uniquement. La ressource de site est **vpnsite**. Elle représente votre périphérique VPN local et ses paramètres. En faisant appel à un partenaire de WAN virtuel, vous disposez d’une solution intégrée pour exporter automatiquement ces informations vers Azure.

## <a name="connectivity"></a>Connectivité

Virtual WAN permet trois types de connectivité : site à site, point à site (préversion) et ExpressRoute (préversion).

### <a name="s2s"></a>Connexion VPN site à site

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

Quand vous créez une connexion Virtual WAN site à site, vous pouvez travailler avec un partenaire disponible. Si vous ne voulez pas utiliser de partenaire, vous pouvez configurer manuellement la connexion. Pour plus d’informations, consultez [Créer une connexion site à site à l’aide de Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Workflow des partenaires Virtual WAN

Quand vous travaillez avec un partenaire Virtual WAN, le workflow est le suivant :

1. Le contrôleur de l’appareil de branche (VPN/SDWAN) est authentifié pour exporter des informations orientées Site vers Azure à l’aide d’un [principal de service Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Le contrôleur de l’appareil de branche (VPN/SDWAN) obtient la configuration de la connectivité Azure et met à jour l’appareil local. Cela permet d’automatiser le téléchargement, la modification et la mise à jour de la configuration sur le périphérique VPN local.
3. Une fois que l’appareil possède la bonne configuration Azure, une connexion de site à site (deux tunnels actifs) est établie avec le WAN Azure. Azure prend en charge IKEv1 et IKEv2. La prise en charge du protocole BGP est facultative.

#### <a name="partners"></a>Partenaires pour les connexions Virtual WAN site à site

Pour obtenir la liste des partenaires et des emplacements disponibles, consultez l’article [Partenaires et emplacements Virtual WAN](virtual-wan-locations-partners.md).

### <a name="p2s"></a>Connexions VPN point à site (préversion)

Une connexion point à site (P2S) vous permet de créer une connexion sécurisée à votre hub virtuel à partir d’un ordinateur de client individuel. Une connexion P2S est établie en étant démarrée à partir de l’ordinateur client. Cette solution est utile pour les télétravailleurs souhaitant se connecter à partir d’un emplacement distant, comme depuis leur domicile ou pendant une conférence. De même, l’utilisation d’un VPN P2S est une solution utile qui constitue une alternative au VPN site à site (S2S) lorsqu’un nombre restreint de clients doit se connecter.

Pour créer la connexion, consultez [Créer une connexion point à site à l’aide de Virtual WAN](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Connexions ExpressRoute connections (préversion)

ExpressRoute vous permet de connecter votre réseau local à Azure via une connexion privée. Pour établir la connexion, consultez [Créer une connexion ExpressRoute à l’aide de Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Emplacements

Pour plus d’informations sur les emplacements, consultez l’article [Partenaires et emplacements Virtual WAN](virtual-wan-locations-partners.md).

## <a name="faq"></a>Forum Aux Questions

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes

[Créer une connexion site à site à l’aide de Virtual WAN](virtual-wan-site-to-site-portal.md)
