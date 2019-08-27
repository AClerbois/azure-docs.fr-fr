---
title: Versions de registre, mise à jour corrective et mise à niveau prises en charge par Azure Blockchain Service
description: Vue d’ensemble des versions de registre prises en charge dans Azure Blockchain Service, y compris des stratégies en matière de mise à jour corrective des systèmes et de mises à niveau gérées par le système et gérées par l’utilisateur.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 6cded49f14ae90f84b18c0a37059677c6c1fed99
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562795"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versions de registre d’Azure Blockchain Service prises en charge

Azure Blockchain Service utilise le registre [Quorum](https://www.goquorum.com/developers) basé sur Ethereum et conçu pour le traitement des transactions privées au sein d’un groupe de participants connus, identifié en tant que consortium dans Azure Blockchain Service.

Actuellement, Azure Blockchain Service prend en charge [Quorum version 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) et le [gestionnaire de transactions Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau

Le contrôle de version dans Quorum s’effectue via les versions majeure, mineure et de correctifs. Par exemple, si la version de Quorum est 2.0.1, le type de version est catégorisé comme suit :

|Majeure | Secondaire  | Correctif  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service met à jour automatiquement les versions de correctifs de Quorum sur les membres en cours d’exécution dans les 30 jours de leur mise à disposition à partir de Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilité des nouvelles versions de registre

Azure Blockchain Service fournit les dernières versions majeure et mineure du registre Quorum dans les 60 jours suivant leur mise à disposition par le fabricant de Quorum. Un maximum de quatre versions mineures sont fournies au choix aux consortiums lors du provisionnement d’un nouveau membre et d’un consortium. La mise à niveau de/vers une version majeure ou mineure n’est actuellement pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Limites dans Azure Blockchain Service](limits.md)
