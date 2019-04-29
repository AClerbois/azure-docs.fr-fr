---
title: Sauvegarde et restauration de SQL Server sur des machines virtuelles Azure | Microsoft Docs
description: Décrit les considérations relatives à la sauvegarde et à la restauration des bases de données SQL Server s’exécutant sur des machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ab239d0546508d74874c6b6be03f6afc06b08fa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563413"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure

Cet article fournit des conseils sur les options de sauvegarde et de restauration disponibles pour SQL Server s’exécutant sur une machine virtuelle Windows dans Azure. Stockage Azure conserve trois copies de chaque disque de machine virtuelle Azure pour garantir la protection contre la perte de données ou l’altération physique des données. Par conséquent, contrairement aux configurations locales, vous n’avez pas besoin de vous occuper des défaillances matérielles. Toutefois, vous devez toujours sauvegarder vos bases de données SQL Server pour les protéger des erreurs d’application ou d’utilisateur, telles que les insertions ou les suppressions de données effectuées par inadvertance. Dans ce cas, il est important de pouvoir procéder à une restauration à un point donné dans le temps.

La première partie de cet article présente les options de restauration et de sauvegarde disponibles. Elle est suivie de sections qui contiennent des informations supplémentaires sur chaque stratégie.

## <a name="backup-and-restore-options"></a>Options de sauvegarde et de restauration

Le tableau suivant contient des informations sur les différentes options de sauvegarde et de restauration de SQL Server exécuté sur des machines virtuelles Azure :

| Stratégie | Versions de SQL | Description |
|---|---|---|
| [Sauvegarde automatisée](#automated) | 2014<br/> 2016<br/> 2017 | La sauvegarde automatisée vous permet de planifier des sauvegardes régulières pour toutes les bases de données d’une machine virtuelle SQL Server. Les sauvegardes sont stockées dans Stockage Azure pendant 30 jours maximum. Depuis SQL Server 2016, Sauvegarde automatisée v2 offre des options supplémentaires telles que la configuration d’une planification manuelle et la fréquence des sauvegardes complètes et des sauvegardes de fichiers journaux. |
| [Sauvegarde Azure pour machines virtuelles SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Sauvegarde Azure contient une fonctionnalité de sauvegarde Entreprise pour SQL Server exécuté sur des machines virtuelles Azure. Avec ce service, vous pouvez gérer de manière centralisée les sauvegardes de plusieurs serveurs et de milliers de bases de données. Les bases de données peuvent être restaurées à un point spécifique dans le temps dans le portail. Ce service propose une stratégie de rétention personnalisable qui peut conserver des sauvegardes pendant des années. Cette fonctionnalité est actuellement disponible en préversion publique. |
| [Sauvegarde manuelle](#manual) | Tous | En fonction de votre version de SQL Server, il existe différentes techniques permettant de sauvegarder et de restaurer manuellement SQL Server s’exécutant sur une machine virtuelle Azure. Dans ce scénario, vous êtes chargé du mode de sauvegarde de vos bases de données ainsi que de l’emplacement et la gestion du stockage de ces sauvegardes. |

Les sections suivantes décrivent plus en détail chacune de ces options. La dernière section de cet article contient un résumé sous la forme d’une matrice des fonctionnalités.

## <a id="automated"></a> Sauvegarde automatisée

La sauvegarde automatisée fournit un service de sauvegarde automatique pour les éditions SQL Server Standard et Entreprise s’exécutant sur une machine virtuelle Windows dans Azure. Ce service est fourni par [l’extension SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md), qui est automatiquement installée sur les images de machine virtuelle Windows SQL Server dans le portail Azure.

Toutes les bases de données sont sauvegardées sur un compte de stockage Azure que vous configurez. Les sauvegardes peuvent être chiffrées et conservées pendant 30 jours.

Les machines virtuelles SQL Server versions 2016 et supérieures proposent des options de personnalisation supplémentaires avec Sauvegarde automatisée v2. Ces améliorations sont notamment les suivantes :

- Sauvegardes de bases de données système
- Fenêtre de temps et planification de sauvegarde manuelle
- Fréquence des sauvegardes complètes et de fichier journal

Pour restaurer une base de données, vous devez localiser le ou les fichiers de sauvegarde requis dans le compte de stockage, puis effectuer une restauration sur votre machine virtuelle SQL à l’aide de SQL Server Management Studio (SSMS) ou de commandes Transact-SQL.

Pour plus d’informations sur la configuration de la sauvegarde automatisée des machines virtuelles SQL, consultez les articles suivants :

- **SQL Server 2016/2017** : [Sauvegarde automatisée version 2 pour les Machines virtuelles Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014** : [Sauvegarde automatisée pour les machines virtuelles SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Sauvegarde Azure pour les machines virtuelles SQL

[Sauvegarde Azure](/azure/backup/) contient une fonctionnalité de sauvegarde Entreprise pour SQL Server exécuté sur des machines virtuelles Azure. Toutes les sauvegardes sont stockées et gérées dans un coffre Recovery Services. Cette solution apporte plusieurs avantages, en particulier pour les entreprises :

- **Sauvegarde sans infrastructure** : vous n’avez pas à gérer les serveurs de sauvegarde ni les emplacements de stockage.
- **Mise à l’échelle** : assurez la protection de nombreuses machines virtuelles SQL et de milliers de bases de données.
- **Paiement à l’utilisation** : cette fonctionnalité est un service distinct fourni par Sauvegarde Azure, mais comme pour tous les services Azure, vous ne payez que ce que vous utilisez.
- **Surveillance et gestion centralisées** : gérez de manière centralisée toutes vos sauvegardes, y compris les autres charges de travail prises en charge par Sauvegarde Azure, depuis un tableau de bord unique d’Azure.
- **Rétention et sauvegarde basées sur des stratégies** : créez des stratégies de sauvegarde standard pour procéder à des sauvegardes régulières. Établissez des stratégies de rétention pour gérer des sauvegardes pendant des années.
- **Prise en charge de SQL Always On** : détectez et protégez une configuration de SQL Server Always On et respectez la préférence de sauvegarde du groupe de disponibilité des sauvegardes.
- **Objectif de point de récupération (RPO) de 15 minutes** : configurez des sauvegardes de fichiers journaux de transactions SQL toutes les 15 minutes.
- **Limite de restauration dans le temps** : utilisez le portail pour récupérer des bases de données à un point précis dans le temps sans avoir à restaurer manuellement plusieurs sauvegardes complètes, différentielles et de fichiers journaux.
- **Alertes par e-mail consolidées pour informer des défaillances** : configurez des notifications par e-mail consolidées pour informer des défaillances.
- **Contrôle d’accès en fonction du rôle** : déterminez qui peut gérer les opérations de sauvegarde et de restauration via le portail.

Pour une vue d’ensemble rapide de son fonctionnement et une démonstration, regardez la vidéo suivante :

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Cette solution de sauvegarde Azure pour les machines virtuelles SQL est généralement disponible. Pour plus d’informations, consultez [Sauvegarder une base de données SQL Server dans Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Sauvegarde manuelle

Si vous souhaitez gérer manuellement les opérations de sauvegarde et de restauration sur vos machines virtuelles SQL, plusieurs options sont à votre disposition selon la version de SQL Server que vous utilisez. Pour une vue d’ensemble de la sauvegarde et de la restauration, consultez les articles suivants en fonction de votre version de SQL Server :

- [Backup and Restore for SQL Server 2016 and later](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (Sauvegarde et restauration pour SQL Server 2016 et ultérieur)
- [Backup and Restore for SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx) (Sauvegarde et restauration pour SQL Server 2014)
- [Backup and Restore for SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx) (Sauvegarde et restauration pour SQL Server 2012)
- [Backup and Restore for SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx) (Sauvegarde et restauration pour SQL Server 2008 R2)
- [Backup and Restore for SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx) (Sauvegarde et restauration pour SQL Server 2008)

Les sections suivantes décrivent de façon plus détaillée plusieurs options de sauvegarde et de restauration manuelles.

### <a name="backup-to-attached-disks"></a>Sauvegarde sur des disques attachés

Pour SQL Server s’exécutant sur des machines virtuelles Azure, vous pouvez utiliser les techniques de sauvegarde et restauration en mode natif en utilisant des disques attachés sur la machine virtuelle comme destination des fichiers de sauvegarde. Néanmoins, le nombre de disques que vous pouvez attacher à une machine virtuelle Azure est limité et basé sur la [taille de la machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Il convient également de tenir compte du traitement de la gestion des disques.

Pour obtenir un exemple de création manuelle d’une sauvegarde complète de base de données à l’aide de SQL Server Management Studio (SSMS) ou de Transact-SQL, consultez [Créer une sauvegarde complète de base de données](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Sauvegarde vers une URL

Depuis SQL Server 2012 SP1 CU2, vous pouvez effectuer des sauvegardes et restaurations directement vers le stockage d’objets blob Microsoft Azure. Cette procédure directe est également appelée sauvegarde vers une URL. SQL Server 2016 contient également les améliorations suivantes pour cette fonctionnalité :

| Améliorations en 2016 | Détails |
| --- | --- |
| **Entrelacement** |Lors de la sauvegarde dans le stockage d’objets blob Microsoft Azure, SQL Server 2016 prend en charge la sauvegarde dans plusieurs objets blob, ce qui permet de sauvegarder des bases de données volumineuses allant jusqu’à 12,8 To. |
| **Sauvegarde instantanée** |Avec l’utilisation d’instantanés Azure, la fonctionnalité Sauvegarde instantanée de fichier SQL Server fournit des sauvegardes quasi instantanées et des restaurations rapides des fichiers de base de données stockés à l’aide du service de stockage d’objets blob Azure. Cette fonctionnalité vous permet de simplifier vos stratégies de sauvegarde et de restauration. La sauvegarde instantanée de fichier prend également en charge la limite de restauration dans le temps. Pour plus d'informations, consultez [File-Snapshot Backups for Database Files in Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)(en anglais). |

Pour plus d’informations, consultez les articles suivants en fonction de votre version de SQL Server :

- **SQL Server 2016/2017** : [Sauvegarde SQL Server vers une URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014** : [Sauvegarde SQL Server 2014 vers une URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012** : [Sauvegarde SQL Server 2012 vers une URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Sauvegarde managée

Depuis SQL Server 2014, la fonctionnalité Sauvegarde managée automatise la création de sauvegardes dans le stockage Azure. En arrière-plan, elle utilise la fonctionnalité Sauvegarde vers une URL décrite dans la section précédente de cet article. La fonctionnalité Sauvegarde managée est également la fonctionnalité sous-jacente qui prend en charge le service Sauvegarde automatisée sur les machines virtuelles SQL Server.

Depuis SQL Server 2016, la fonctionnalité Sauvegarde managée comporte des options supplémentaires pour la planification, la sauvegarde de bases de données système, ainsi que la fréquence des sauvegardes complètes et de fichiers journaux.

Pour plus d’informations, consultez les articles suivants en fonction de votre version de SQL Server :

- [Managed Backup to Microsoft Azure for SQL Server 2016 and later](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) (Sauvegarde managée dans Microsoft Azure pour SQL Server 2016 et ultérieur)
- [Managed Backup to Microsoft Azure for SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx) (Sauvegarde managée dans Microsoft Azure pour SQL Server 2014)

## <a name="decision-matrix"></a>Matrice décisionnelle

Le tableau ci-dessous récapitule les fonctionnalités de chaque option de sauvegarde et de restauration pour les machines virtuelles SQL Server dans Azure.

|| **Sauvegarde automatisée** | **Sauvegarde Azure pour SQL** | **Sauvegarde manuelle** |
|---|---|---|---|
| Service Azure supplémentaire nécessaire |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configuration d’une stratégie de sauvegarde dans le portail Azure | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restauration des bases de données dans le portail Azure |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gestion de plusieurs serveurs dans un tableau de bord |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Limite de restauration dans le temps | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![OUI](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Objectif de point de récupération (RPO) de 15 minutes | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![OUI](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Stratégie de rétention de sauvegarde à court terme (jours) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Stratégie de rétention de sauvegarde à long terme (mois, années) |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Prise en charge intégrée de SQL Server Always On |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Sauvegarde vers des comptes de stockage Azure | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatique) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatique) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gérée par le client) |
| Gestion des fichiers de sauvegarde et de stockage | | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Sauvegarde sur des disques attachés sur la machine virtuelle |   |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Rapports de sauvegarde personnalisables établis de façon centralisée |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alertes par e-mail consolidées pour informer des défaillances |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personnaliser la surveillance basée sur les journaux d’Azure Monitor |   | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Surveillance des travaux de sauvegarde avec SSMS ou des scripts Transact-SQL | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![OUI](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Restauration des bases de données avec SSMS ou des scripts Transact-SQL | ![Oui](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![OUI](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Étapes suivantes

Si vous planifiez le déploiement de SQL Server sur une machine virtuelle Azure, vous trouverez des conseils d’approvisionnement dans le guide suivant : [Guide pratique pour provisionner une machine virtuelle Windows SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

Même si la sauvegarde et la restauration permettent de migrer vos données, il existe des chemins de migration de données potentiellement plus simples vers SQL Server sur une machine virtuelle Azure. Pour une description complète des options de migration et des recommandations connexes, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).
