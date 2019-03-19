---
title: Connecter des réseaux locaux à Microsoft Cloud à l’aide de Global Reach - Azure ExpressRoute | Microsoft Docs
description: Cet article présente ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 17ae4b930d259dedd3e4e202fae69de32d9bf593
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819546"
---
# <a name="expressroute-global-reach"></a>Portée mondiale ExpressRoute
ExpressRoute vous permet de connecter vos réseaux locaux à Microsoft Cloud de manière privée et résiliente. Vous pouvez accéder à de nombreux services Microsoft Cloud, tels que Azure, Office 365 et Dynamics 365, depuis votre centre de données privé ou le réseau de votre entreprise. Par exemple, vous pouvez avoir une succursale à San Francisco avec un circuit ExpressRoute dans la Silicon Valley, et une autre à Londres avec un circuit ExpressRoute dans cette même ville. Les deux succursales peuvent disposer d’une connectivité haut débit avec les ressources Azure situées dans les régions USA Ouest et Royaume-Uni Sud, mais elles ne peuvent pas échanger de données directement entre elles. En d’autres termes, 10.0.1.0/24 peut envoyer des données à 10.0.3.0/24 et 10.0.4.0/24, mais PAS à 10.0.2.0/24.

![without][1]

Avec **ExpressRoute Global Reach**, vous pouvez associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux. Dans l’exemple ci-dessus, grâce à l’ajout d’ExpressRoute Global Reach, votre bureau de San Francisco (10.0.1.0/24) peut échanger directement des données avec le bureau de Londres (10.0.2.0/24) par le biais des circuits ExpressRoute existants et du réseau mondial de Microsoft. 

![with][2]

## <a name="use-case"></a>Cas d’utilisation
ExpressRoute Global Reach est conçu pour compléter l’implémentation du réseau étendu (WAN) de votre fournisseur de services et connecter vos succursales à travers le monde. Par exemple, si votre fournisseur de services opère principalement aux États-Unis et a connecté toutes vos branches dans ce pays, mais qu’il n’opère ni au Japon ni à Hong Kong, ExpressRoute Global Reach vous permet de travailler avec un fournisseur de services local, et Microsoft connecte vos branches situées là-bas à celles figurant aux États-Unis à l’aide d’ExpressRoute et de notre réseau mondial.

![Cas d’usage][3]

## <a name="availability"></a>Disponibilité 
ExpressRoute Global Reach est actuellement pris en charge dans les pays et dans le territoire suivants.

* Australie
* Canada
* France
* Hong Kong (R.A.S.)
* Irlande
* Japon
* Corée du Sud
* Pays-bas
* Royaume-Uni
* États-Unis

Vos circuits ExpressRoute doivent être créés sur les [sites d’homologation ExpressRoute](expressroute-locations.md) dans le territoire ou les pays indiqués ci-dessus. Pour activer ExpressRoute Global Reach entre [différentes régions](expressroute-locations.md), vous devez disposer de circuits Premium.

## <a name="next-steps"></a>Étapes suivantes
1. [En savoir plus sur ExpressRoute Global Reach](expressroute-faqs.md)
2. [Configuration d’ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "schéma sans portée globale"
[2]: ./media/expressroute-global-reach/2.png "schéma avec portée globale"
[3]: ./media/expressroute-global-reach/3.png "cas d’usage de global reach"
