---
title: Activer Transparent Data Encryption pour Stretch Database - Azure | Microsoft Docs
description: Activer le chiffrement transparent des données (TDE) pour SQL Server Stretch Database sur Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 61f556476958484b78b9c3dff2583eb6db043637
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "66003036"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Activer le chiffrement transparent des données (TDE) pour Stretch Database sur Azure
> [!div class="op_single_selector"]
> * [Portail Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Le chiffrement transparent des données (Transparent Data Encryption, TDE) protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans qu’il soit nécessaire de modifier l’application.

Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. La clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur Azure. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. Pour obtenir une description générale du chiffrement transparent des données, consultez [Chiffrement transparent des données (TDE)].

## <a name="enabling-encryption"></a>Activation du chiffrement
Pour activer le chiffrement transparent des données pour une base de données Azure qui stocke les données migrées à partir d’une base de données SQL Server compatible avec Stretch, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données**![][1]
4. Sélectionnez le paramètre **Activé**, puis **Enregistrer**
   ![][2]

## <a name="disabling-encryption"></a>Désactivation du chiffrement
Pour désactiver le chiffrement transparent des données pour une base de données Azure qui stocke les données migrées à partir d’une base de données SQL Server compatible avec Stretch, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données**
4. Sélectionnez le paramètre **Désactivé**, puis **Enregistrer**

<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
