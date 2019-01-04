---
title: Limitations dans Azure Database for MariaDB
description: Cet article décrit les limitations dans Azure Database for MariaDB, telles que le nombre de connexions et les options du moteur de stockage.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: e611c5e11d3c86474a7775971918ba95b8487da4
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970283"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitations dans Azure Database for MariaDB
Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données.

## <a name="maximum-connections"></a>Nombre maximal de connexions
Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant :

|**Niveau tarifaire**|**vCore(s)**| **Nombre maximal de connexions**|
|---|---|---|
|De base| 1| 50|
|De base| 2| 100|
|Usage général| 2| 300|
|Usage général| 4| 625|
|Usage général| 8| 1250|
|Usage général| 16| 2 500|
|Usage général| 32| 5 000|
|Mémoire optimisée| 2| 600|
|Mémoire optimisée| 4| 1250|
|Mémoire optimisée| 8| 2 500|
|Mémoire optimisée| 16| 5 000|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> ERREUR 1040 (08004) : Trop de connexions

## <a name="storage-engine-support"></a>Prise en charge du moteur de stockage

### <a name="supported"></a>Pris en charge
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Non pris en charge
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Prise en charge des privilèges

### <a name="unsupported"></a>Non pris en charge
- Rôle d’administrateur de base de données : plusieurs paramètres de serveur peuvent dégrader de façon inattendue les performances du serveur ou nier les propriétés ACID du système de gestion de base de données. Par conséquent, pour préserver l’intégrité du service et le contrat SLA au niveau du produit, ce service n’expose pas le rôle d’administrateur de bases de données. Le compte d’utilisateur par défaut, qui est créé en même temps qu’une instance de base de données, permet à l’utilisateur d’exécuter la plupart des instructions DDL et DML dans l’instance de base de données gérée.
- Privilège de superutilisateur : de la même façon, les [privilèges de superutilisateur](https://mariadb.com/kb/en/library/grant/#global-privileges) sont eux aussi limités.

## <a name="data-manipulation-statement-support"></a>Prise en charge des instructions de manipulation des données

### <a name="supported"></a>Pris en charge
- `LOAD DATA INFILE` est prise en charge, mais le paramètre `[LOCAL]` doit être spécifié et dirigé vers un chemin d'accès UNC (stockage Azure monté via SMB).

### <a name="unsupported"></a>Non pris en charge
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique vers et depuis les niveaux tarifaires de base n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="point-in-time-restore"></a>Restauration dans le temps
- Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec la même configuration que le serveur sur lequel il est basé.
- La restauration d’un serveur supprimé n’est pas prise en charge.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

## <a name="current-known-issues"></a>Problèmes connus
- L’instance de serveur MariaDB affiche la version de serveur incorrecte une fois la connexion établie. Pour obtenir la version de moteur correcte de l’instance de serveur, utilisez la commande `select version();`.

## <a name="next-steps"></a>Étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-pricing-tiers.md)
- [Versions de base de données MariaDB prises en charge](concepts-supported-versions.md)
