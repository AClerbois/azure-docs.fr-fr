---
title: Exemples Azure PowerShell pour réseau virtuel | Documents Microsoft
description: Exemples Azure PowerShell pour réseau virtuel.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: ab3a0935f23cda70cfef49af3563f6b1e85d1d8b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Exemples Azure PowerShell pour réseau virtuel

Le tableau suivant contient des liens vers des scripts Azure PowerShell :

| | |
|----|----|
| [Créer un réseau virtuel pour les applications multiniveau](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic vers le sous-réseau frontal est limité à HTTP, tandis que le trafic vers le sous-réseau principal est limité à SQL, port 1433. |
| [Homologuer deux réseaux virtuels](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Crée et connecte deux réseaux virtuels dans la même région. |
| [Acheminer le trafic via une appliance virtuelle réseau](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Crée un réseau virtuel avec des sous-réseaux frontal et principal et une machine virtuelle en mesure d’acheminer le trafic entre les deux sous-réseaux. |
| [Filtrer le trafic réseau de machine virtuelle entrant et sortant](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic réseau entrant vers le sous-réseau frontal est limité à HTTP et HTTPS. Le trafic sortant vers Internet à partir du sous-réseau principal n’est pas autorisé. |
