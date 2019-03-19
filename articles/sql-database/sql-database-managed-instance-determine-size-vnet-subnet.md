---
title: Azure SQL Database Managed Instance - Déterminer la taille du réseau virtuel/sous-réseau | Microsoft Docs
description: Cette rubrique explique comment calculer la taille du sous-réseau dans lequel les instances Azure SQL Database Managed Instance seront déployées.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: 05440698d40a2175fd47384fa2db94ad80d79aa4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773594"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Déterminer la taille du sous-réseau d’un réseau virtuel pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure.

Le nombre d’instances managées pouvant être déployées dans le sous-réseau du réseau virtuel dépend de la taille du sous-réseau (plage de sous-réseau).

Quand vous créez une option Managed Instance, Azure alloue plusieurs machines virtuelles en fonction du niveau sélectionné pendant l’approvisionnement. Étant donné que ces machines virtuelles sont associées à votre sous-réseau, elles nécessitent des adresses IP. Pour garantir la haute disponibilité pendant les opérations normales et la maintenance du service, Azure peut allouer des machines virtuelles supplémentaires. Par conséquent, le nombre d’adresses IP nécessaires dans un sous-réseau est supérieur au nombre d’options Managed Instance dans ce sous-réseau.

Par défaut, une option Managed Instance a besoin d’un minimum de 16 adresses IP dans un sous-réseau et peut en utiliser jusqu’à 256. Par conséquent, vous pouvez utiliser des masques de sous-réseau entre la taille/28 et /24 lors de la définition de plages IP de votre sous-réseau. Un bit du masque de réseau de/28 (14 hôtes par réseau) est une bonne taille pour un usage général unique ou un déploiement critique pour l’entreprise. Un bit du masque de/27 (30 hôtes par réseau) est idéal pour une plusieurs déploiements de Managed Instance dans le même réseau virtuel. Paramètres de bit de masque de /26 (62 hôtes) et /24 (254 hôtes) permet davantage montée en puissance parallèle le réseau virtuel pour prendre en charge supplémentaire des Instances gérées.

> [!IMPORTANT]
> Une taille de sous-réseau avec 16 adresses IP est le minimum absolu avec risque limité pour l’autres Managed Instance montée. Le choix d'un sous-réseau avec le préfixe /27 ou un préfixe inférieur est fortement recommandé.

## <a name="determine-subnet-size"></a>Déterminer la taille du sous-réseau

Si vous envisagez de déployer plusieurs options Managed Instance à l’intérieur du sous-réseau et avez besoin d’optimiser la taille de ce dernier, utilisez ces paramètres pour former un calcul :

- Azure utilise cinq adresses IP dans le sous-réseau pour ses besoins propres.
- Chaque instance à usage général nécessite deux adresses.
- Chaque instance Critique pour l’entreprise a besoin de quatre adresses

**Exemple**: vous prévoyez d’utiliser trois instances managées de type Usage général et deux de type Critique pour l’entreprise. Cela signifie que vous avez besoin de 5 + 3 * 2 + 2 * 4 = 19 adresses IP. Comme les plages d’adresses IP sont définies par puissance de 2, vous avez besoin d’une plage de 32 (2 ^ 5) adresses IP. Ainsi, vous devez réserver le sous-réseau avec un masque de sous-réseau de /27.

> [!IMPORTANT]
> Le calcul affiché ci-dessus deviendra obsolète au fur et à mesure des améliorations.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Qu’est-ce qu’une instance managée ?](sql-database-managed-instance.md)
- Découvrez plus en détail l’[architecture de connectivité pour Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Découvrez comment [créer le réseau virtuel dans lequel seront déployées des instances managées](sql-database-managed-instance-create-vnet-subnet.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
