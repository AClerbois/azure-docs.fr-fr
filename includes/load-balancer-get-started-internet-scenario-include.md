---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269983"
---
Les tâches suivantes seront effectuées dans ce scénario :

* Créer un équilibrage de charge recevant le trafic réseau sur le port 80 et envoyer un trafic d’équilibrage de charge vers les machines virtuelles « web1 » et « web2 »
* Créer des règles NAT pour l’accès Bureau à distance/SSH pour les machines virtuelles situées derrière l’équilibrage de charge
* Créer des sondes d’intégrité

![Scénario d’équilibreur de charge](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
