---
title: Prérequis du module Azure IoT Edge | Microsoft Docs
description: Prérequis à la publication d’une offre de module IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432925"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prérequis à la publication de module IoT Edge

Cet article décrit les prérequis à la publication d’une nouvelle offre de module IoT Edge.

Pour en savoir plus sur les modules IoT Edge et les avantages de la publication d’un module sur la place de marché Azure, consultez le [guide de publication des modules IoT Edge](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Publication des conditions préalables

Pour publier un module IoT Edge sur la Place de marché Azure, vous devez satisfaire aux prérequis suivants :

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accéder au [Portail Cloud Partner](https://cloudpartner.azure.com/). Pour plus d’informations, consultez le [Guide de publication sur AppSource et la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Acceptation des [conditions de la Place de marché Azure](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hébergez votre ressource technique de module IoT Edge dans un registre Azure Container Registry.  Pour plus d’informations, consultez [How to prepare your IoT Edge module technical asset](./cpp-create-technical-assets.md) (Comment préparer votre ressource technique de module IoT Edge).
- Préparez vos métadonnées de module IoT Edge. Par exemple (liste non exhaustive) :
    - Un titre
    - Une description (au format HTML)
    - Une image de logo (format PNG et tailles d’image fixes, y compris 40x40px, 90x90px, 115x115px, 255x115px)
    - Conditions d’utilisation et politique de confidentialité
    - Une configuration du module par défaut qui comprend : routes, propriétés souhaitées du jumeau, createOptions et variables d’environnement.
    - Documentation
    - Contacts du support

## <a name="next-steps"></a>Étapes suivantes

- [Prepare your IoT Edge module technical asset](./cpp-create-technical-assets.md) (Préparer votre ressource technique de module IoT Edge)
- [Create your IoT Edge module offer](./cpp-create-offer.md) (Créer votre offre de module IoT Edge)
