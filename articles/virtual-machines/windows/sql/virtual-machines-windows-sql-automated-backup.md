---
title: Sauvegarde automatisée pour les machines virtuelles Azure SQL Server 2014 | Microsoft Docs
description: Décrit la fonctionnalité de sauvegarde automatisée pour les machines virtuelles exécutant SQL Server 2014 dans Azure. Cet article est spécifique aux machines virtuelles utilisant Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 0a1ad6d50c624115bab7ad09ff0e30a36e7df500
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256610"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Sauvegarde automatisée pour les machines virtuelles SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

La sauvegarde automatisée configure automatiquement une [sauvegarde managée sur Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données nouvelles et existantes sur une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de base de données utilisant le stockage d’objets blob Azure durable. La sauvegarde automatisée dépend l’ [extension de l’agent IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prérequis
Pour utiliser la sauvegarde automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Édition/version de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> La sauvegarde automatisée fonctionne avec SQL Server 2014. Si vous utilisez SQL Server 2016/2017, vous pouvez utiliser la sauvegarde automatisée (version 2) pour sauvegarder vos bases de données. Pour plus d’informations, consultez [Sauvegarde automatisée en version 2 pour les machines virtuelles Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuration de la base de données**:

- Les bases de données cibles doivent utiliser le modèle de récupération complète. Pour plus d’informations sur l’impact du modèle de récupération complète sur les sauvegardes, consultez [Sauvegarde en mode de récupération complète](https://technet.microsoft.com/library/ms190217.aspx).
- Les bases de données cibles doivent se trouver sur l’instance SQL Server par défaut. L’extension IaaS SQL Server ne prend pas en charge les instances nommées.

> [!NOTE]
> La sauvegarde automatisée utilise l’extension de l’agent IaaS de SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)(Extension de l’agent IaaS SQL Server).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

| Paramètre | Plage (par défaut) | Description |
| --- | --- | --- |
| **Sauvegarde automatisée** | Activer/Désactiver (désactivé) | Active ou désactive la sauvegarde automatisée d’une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. |
| **Période de rétention** | 1 à 30 jours (30 jours) | Nombre de jours durant lesquels une sauvegarde est conservée. |
| **Compte de stockage** | Compte Azure Storage | Compte de stockage Azure à utiliser pour stocker les fichiers de sauvegarde automatisée dans le stockage d’objets blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention de dénomination des fichiers de sauvegarde inclut la date, l’heure et le nom de la machine. |
| **Chiffrement** | Activer/Désactiver (désactivé) | Active ou désactive le chiffrement. Quand le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent dans le compte de stockage spécifié dans le même conteneur `automaticbackup` avec la même convention de dénomination. Si le mot de passe change, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat est conservé pour restaurer les sauvegardes antérieures. |
| **Mot de passe** | Texte du mot de passe | Mot de passe pour les clés de chiffrement. Il est uniquement requis si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et du certificat associé qui a été utilisé lorsque la sauvegarde a été effectuée. |

## <a name="configure-in-the-portal"></a>Configurer dans le portail

Vous pouvez utiliser le portail Azure pour configurer la sauvegarde automatisée lors de l’approvisionnement ou pour des machines virtuelles SQL Server 2014 existantes.

## <a name="configure-new-vms"></a>Configurer de nouvelles machines virtuelles

Utilisez le portail Azure pour configurer la sauvegarde automatisée quand vous créez une machine virtuelle SQL Server 2014 dans le modèle de déploiement Resource Manager.

Dans le volet **Paramètres SQL Server**, sélectionnez **Sauvegarde automatisée**. La capture d’écran suivante du portail Azure montre les paramètres de **Sauvegarde automatisée SQL**.

![Configuration d’une sauvegarde automatisée SQL dans le portail Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurer des machines virtuelles existantes

Pour les machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Sélectionnez ensuite la section **Configuration de SQL Server** dans les **Paramètres** de la machine virtuelle.

![Sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Dans le volet **Configuration de SQL Server**, cliquez sur le bouton **Modifier** dans la section Sauvegarde automatisée.

![Configurer la sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quand vous avez terminé, cliquez sur le bouton **OK** en bas des paramètres de **Configuration de SQL Server** pour enregistrer vos changements.

Si vous activez la sauvegarde automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indiquera peut-être pas que la sauvegarde automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

> [!NOTE]
> Vous pouvez également configurer la sauvegarde automatisée à l’aide d’un modèle. Pour plus d’informations, consultez l’article [Azure quickstart template for Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)(Modèle de démarrage rapide d’Azure pour la sauvegarde automatisée).

## <a name="configure-with-powershell"></a>Configurer avec PowerShell

Vous pouvez utiliser PowerShell pour configurer une sauvegarde automatisée. Avant de commencer, vous devez :

- [Télécharger et installer la version la plus récente d’Azure PowerShell](https://aka.ms/webpi-azps).
- Ouvrez Windows PowerShell et associez-le à votre compte avec la commande **Connect-AzureRmAccount**.

### <a name="install-the-sql-iaas-extension"></a>Installer l’extension IaaS SQL
Si vous avez configuré une machine virtuelle SQL Server à partir du portail Azure, l’extension IaaS SQL Server devrait déjà être installée. Vous pouvez déterminer si cette extension est installée pour votre machine virtuelle en appelant la commande **Get-AzureRmVM** puis en examinant la propriété **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Si l’extension de l’agent IaaS SQL Server est installée, elle devrait s’afficher sous la forme « SqlIaaSAgent » ou « SQLIaaSExtension ». La propriété **ProvisioningState** de l’extension devrait également indiquer « Succeeded » (Réussie).

Si elle n’est pas installée ou n’a pas pu être configurée, vous pouvez l’installer avec la commande suivante. Outre le nom de la machine virtuelle et le groupe de ressources, vous devez également spécifier la région (**$region**) où se trouve votre machine virtuelle.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Si elle n’a pas encore été réalisée, l’installation de l’extension redémarre le service SQL Server.

### <a id="verifysettings"></a> Vérifier les paramètres actuels

Si vous avez activé la sauvegarde automatisée lors de la configuration, vous pouvez utiliser PowerShell pour vérifier votre configuration actuelle. Exécutez la commande **Get-AzureRmVMSqlServerExtension** et examinez la propriété **AutoBackupSettings** :

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

La sortie doit ressembler à ce qui suit :

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Si votre sortie montre que l’option **Enable** (Activer) est définie sur **False**, vous devez activer la sauvegarde automatisée. La bonne nouvelle est que vous activez et configurez la sauvegarde automatisée de la même façon. Pour en savoir plus, consultez la section suivante.

> [!NOTE] 
> Si vous vérifiez les paramètres immédiatement après une modification, les anciennes valeurs de configuration peuvent s’afficher. Patientez quelques minutes et revérifiez les paramètres pour vous assurer que vos modifications ont bien été appliquées.

### <a name="configure-automated-backup"></a>Configurer une sauvegarde automatisée
Vous pouvez utiliser PowerShell pour activer la sauvegarde automatisée ainsi que pour modifier sa configuration et son comportement à tout moment.

Tout d’abord, sélectionnez ou créez un compte de stockage pour les fichiers de sauvegarde. Le script suivant sélectionne un compte de stockage ou le crée s’il n’existe pas.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> La sauvegarde automatisée ne prend pas en charge le stockage des sauvegardes dans un stockage Premium, mais elle peut effectuer des sauvegardes à partir de disques de machines virtuelles qui utilisent le stockage Premium.

Utilisez ensuite la commande **New-AzureRmVMSqlServerAutoBackupConfig** pour activer et configurer les paramètres de sauvegarde automatisée afin de stocker les sauvegardes dans le compte de stockage Azure. Dans cet exemple, les sauvegardes sont conservées pendant 10 jours. La seconde commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle Azure spécifiée avec ces paramètres.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

> [!NOTE]
> Il existe d’autres paramètres pour **New-AzureRmVMSqlServerAutoBackupConfig** qui s’appliquent uniquement à SQL Server 2016 et à la sauvegarde automatisée version 2. SQL Server 2014 ne prend pas en charge les paramètres suivants : **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** et **LogBackupFrequencyInMinutes**. Si vous essayez de configurer ces paramètres sur une machine virtuelle SQL Server 2014, aucune erreur n’apparaît, mais les paramètres ne sont pas appliqués. Si vous souhaitez utiliser ces paramètres sur une machine virtuelle SQL Server 2016, consultez [Sauvegarde automatisée version 2 pour SQL Server 2016 dans des machines virtuelles Azure](virtual-machines-windows-sql-automated-backup-v2.md).

Pour activer le chiffrement, modifiez le script précédent pour transmettre le paramètre **EnableEncryption** avec un mot de passe (chaîne sécurisée) pour le paramètre **CertificatePassword**. Le script suivant active les paramètres de sauvegarde automatisée dans l’exemple précédent et ajoute le chiffrement.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pour confirmer que vos paramètres ont été appliqués, [vérifiez la configuration de la sauvegarde automatisée](#verifysettings).

### <a name="disable-automated-backup"></a>Désactiver la sauvegarde automatisée

Pour désactiver la sauvegarde automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **New-AzureRmVMSqlServerAutoBackupConfig**. L’absence du paramètre **-Enable** indique à la commande de désactiver la fonctionnalité. À l’instar de l’installation, la désactivation de la sauvegarde automatisée peut prendre plusieurs minutes.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exemple de script

Le script suivant fournit un ensemble de variables que vous pouvez personnaliser afin d’activer et de configurer la sauvegarde automatisée pour votre machine virtuelle. Dans votre cas, vous devrez peut-être personnaliser le script selon vos besoins. Par exemple, vous devrez apporter des modifications si vous souhaitez désactiver la sauvegarde des bases de données système ou activer le chiffrement.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Surveillance

Pour monitorer la sauvegarde automatisée sur SQL Server 2014, deux options s’offrent à vous. Étant donné que la sauvegarde automatisée utilise la fonctionnalité de gestion de sauvegarde de SQL Server, les mêmes techniques de monitoring s’appliquent.

Vous pouvez d’abord interroger l’état en appelant [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vous pouvez aussi interroger la fonction table [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql).

> [!NOTE]
> Dans SQL Server 2014, le schéma de gestion de sauvegarde est **msdb.smart_admin**. Dans SQL Server 2016, il est remplacé par **msdb.managed_backup**, et les rubriques de référence utilisent ce schéma plus récent. Mais pour SQL Server 2014, vous devez continuer à utiliser le schéma **smart_admin** pour tous les objets de gestion de sauvegarde.

Une autre option consiste à tirer parti de la fonctionnalité intégrée de messagerie de base de données pour les notifications.

1. Appelez la procédure stockée [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) pour affecter une adresse e-mail au paramètre **SSMBackup2WANotificationEmailIds**. 
1. Activez [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) pour envoyer les e-mails à partir de la machine virtuelle Azure.
1. Utilisez le nom d’utilisateur et le serveur SMTP pour configurer la messagerie de base de données. Vous pouvez configurer la messagerie de base de données dans SQL Server Management Studio ou à l’aide de commandes Transact-SQL. Pour plus d’informations, consultez [Messagerie de base de données](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configurez SQL Server Agent pour qu’il utilise la messagerie de base de données](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Vérifiez que le port SMTP est autorisé dans le pare-feu local de la machine virtuelle et le groupe de sécurité réseau pour la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

La sauvegarde automatisée configure une sauvegarde managée sur les machines virtuelles Azure. Il est donc important de [passer en revue la documentation relative à la gestion de sauvegarde sur SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Vous trouverez des conseils supplémentaires pour la sauvegarde et la restauration de SQL Server sur les machines virtuelles Azure dans l’article suivant : [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur les autres tâches d’automatisation disponibles, voir [Extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
