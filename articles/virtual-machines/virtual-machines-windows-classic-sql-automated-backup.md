---
title: Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (classiques) | Microsoft Docs
description: 'Décrit la fonctionnalité de sauvegarde automatisée pour SQL Server s’exécutant dans Azure Virtual Machines à l’aide de Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jroth

---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (classiques)
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
> * [Classique](virtual-machines-windows-classic-sql-automated-backup.md)
> 
> 

La sauvegarde automatisée configure automatiquement une [sauvegarde managée sur Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données nouvelles et existantes sur une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de base de données utilisant le stockage d’objets blob Azure durable. La sauvegarde automatisée dépend l’ [extension de l’agent IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Pour afficher la version Resource Manager de cet article, consultez [Automated Backup for SQL Server in Azure Virtual Machines Resource Manager (Sauvegarde automatisée pour SQL Server dans les machines virtuelles SQL Server (Resource Manager))](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Composants requis
Pour utiliser la sauvegarde automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

* Windows Server 2012
* Windows Server 2012 R2

**Édition/version de SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 n’est pas encore pris en charge pour la sauvegarde automatisée.
> 
> 

**Configuration de la base de données**:

* Les bases de données cibles doivent utiliser le modèle de récupération complète.

**Azure PowerShell**:

* [Installez les commandes de la version la plus récente d’Azure PowerShell](../powershell-install-configure.md).

**Extension IaaS SQL Server**:

* [Installez l’extension IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres
Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatisée. Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

| Paramètre | Plage (par défaut) | Description |
| --- | --- | --- |
| **Sauvegarde automatisée** |Activer/Désactiver (désactivé) |Active ou désactive la sauvegarde automatisée d’une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. |
| **Période de rétention** |1 à 30 jours (30 jours) |Nombre de jours durant lesquels une sauvegarde est conservée. |
| **Compte de stockage** |Compte de stockage Azure (compte de stockage créé pour la machine virtuelle spécifiée) |Compte de stockage Azure à utiliser pour stocker les fichiers de sauvegarde automatisée dans le stockage d’objets blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention de dénomination des fichiers de sauvegarde inclut la date, l’heure et le nom de la machine. |
| **Chiffrement** |Activer/Désactiver (désactivé) |Active ou désactive le chiffrement. Lorsque le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent dans le compte de stockage spécifié dans le même conteneur automaticbackup à l’aide de la même convention de dénomination. Si le mot de passe change, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat est conservé pour restaurer les sauvegardes antérieures. |
| **Mot de passe** |Texte du mot de passe (aucun) |Mot de passe pour les clés de chiffrement. Il est uniquement requis si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et du certificat associé qui a été utilisé lorsque la sauvegarde a été effectuée. |

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell
Dans l’exemple PowerShell suivant, une sauvegarde automatisée est configurée pour une machine virtuelle SQL Server 2014 existante. La commande **New-AzureVMSqlServerAutoBackupConfig** configure les paramètres de sauvegarde automatisée pour stocker les sauvegardes dans le compte de stockage Azure spécifié par la variable $storageaccount. Ces sauvegardes sont conservées pendant 10 jours. La commande **Set-AzureVMSqlServerExtension** met à jour la machine virtuelle Azure spécifiée avec ces paramètres.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

Pour activer le chiffrement, modifiez le script précédent pour transmettre le paramètre EnableEncryption avec un mot de passe (chaîne sécurisée) pour le paramètre CertificatePassword. Le script suivant active les paramètres de sauvegarde automatisée dans l’exemple précédent et ajoute le chiffrement.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pour désactiver la sauvegarde automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **New-AzureVMSqlServerAutoBackupConfig**. À l’instar de l’installation, la désactivation de la sauvegarde automatisée peut prendre plusieurs minutes.

> [!NOTE]
> La désactivation et la désinstallation de l’agent IaaS de SQL Server ne suppriment pas les paramètres de sauvegarde managée précédemment configurés. Vous devez désactiver la sauvegarde automatisée avant de désactiver ou de désinstaller l’agent IaaS de SQL Server.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
La sauvegarde automatisée configure une sauvegarde managée sur les machines virtuelles Azure. Il est donc important de [lire la documentation relative à la sauvegarde gérée](https://msdn.microsoft.com/library/dn449496.aspx) pour comprendre son comportement et ses implications.

Vous trouverez des conseils supplémentaires pour la sauvegarde et la restauration de SQL Server sur les machines virtuelles Azure dans la rubrique suivante : [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur les autres tâches d’automatisation disponibles, voir [Extension de l’agent IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=Oct16_HO2-->


