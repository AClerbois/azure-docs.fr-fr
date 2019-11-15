---
title: Découvrir le pare-feu intégré d’instance gérée
description: Découvrez comment vérifier la protection par pare-feu intégré dans Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821801"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Vérification du pare-feu intégré Managed Instance

Les [règles de sécurité entrante obligatoires](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) de Managed Instance nécessitent l’ouverture des ports de gestion 9000, 9003, 1438, 1440 et 1452 à partir de **n’importe quelle source** sur le groupe de sécurité réseau (NSG) qui protège l’instance gérée. Bien que ces ports soient ouverts au niveau du groupe de sécurité réseau, ils sont protégés au niveau du réseau par le pare-feu intégré.

## <a name="verify-firewall"></a>Vérification du pare-feu

Utilisez n’importe quel outil scanneur de sécurité pour tester ces ports. La capture d’écran suivante montre comment utiliser l’un de ces outils.

![Vérification du pare-feu intégré](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’information sur les instances managées et la connectivité, consultez [Architecture de la connectivité d’Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).