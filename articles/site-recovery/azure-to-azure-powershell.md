---
title: 'Azure Site Recovery : configuration et test de récupération d’urgence pour des machines virtuelles Azure avec Azure PowerShell | Microsoft Docs'
description: Découvrez comment configurer la récupération d’urgence pour des machines virtuelles Azure avec Azure Site Recovery à l’aide d’Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: f09a186ee5626718c7b5e1085dd75d8857e44bb1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705163"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurer la récupération d’urgence pour des machines virtuelles Azure à l’aide d’Azure PowerShell


Dans cet article, vous allez découvrir comment configurer et tester la récupération d’urgence pour des machines virtuelles Azure avec Azure PowerShell.

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créez un coffre Recovery Services.
> - Définissez le contexte du coffre pour la session PowerShell.
> - Préparez le coffre pour commencer la réplication des machines virtuelles Azure.
> - Créez des mappages réseau.
> - Créer des comptes de stockage dans lesquels répliquer les machines virtuelles.
> - Répliquez les machines virtuelles Azure vers une région de récupération pour la récupération d’urgence.
> - Effectuer un test de basculement, puis valider et supprimer le test de basculement.
> - Basculez vers la région de récupération.

> [!NOTE]
> Toutes les fonctionnalités de scénario disponibles via le portail ne seront pas forcément disponibles via Azure PowerShell. Voici quelques-unes des fonctionnalités du scénario actuellement non prises en charge via Azure PowerShell :
> - La capacité à spécifier que tous les disques d’une machine virtuelle doivent être répliqués sans avoir à spécifier explicitement chaque disque de cette machine.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer :
- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](azure-to-azure-architecture.md).
- Vérifiez les [exigences de prise en charge](azure-to-azure-support-matrix.md) pour tous les composants.
- Vous avez Azure PowerShell `Az` module. Si vous devez installer ou mettre à niveau Azure PowerShell, consultez le [guide sur l’installation et la configuration d’Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Connexion à un abonnement Microsoft Azure

Connectez-vous à votre abonnement Azure à l’aide de l’applet de commande Connect-AzAccount

```azurepowershell
Connect-AzAccount
```
Sélectionnez votre abonnement Azure. Utilisez l’applet de commande Get-AzSubscription pour obtenir la liste des abonnements Azure auxquels que vous avez accès à. Sélectionnez l’abonnement Azure à utiliser à l’aide de l’applet de commande Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obtenir les informations détaillées de la ou les machines virtuelles à répliquer

Dans l’exemple de cet article, une machine virtuelle dans la région USA Est sera répliquée vers la région USA Ouest 2, où elle sera également récupérée. La machine virtuelle répliquée dispose d’un disque de système d’exploitation et d’un seul disque de données. Le nom de la machine virtuelle utilisée dans cet exemple est AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenez les détails de disque pour les disques de la machine virtuelle. Les détails du disque seront utilisés plus tard lors de la réplication de la machine virtuelle.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Créez un groupe de ressources dans lequel créer le coffre Recovery Services.

> [!IMPORTANT]
> * Le coffre Recovery Services et les machines virtuelles protégées doivent se trouver dans des emplacements Azure différents.
> * Le groupe de ressources du coffre Recovery Services et les machines virtuelles protégées doivent se trouver dans des emplacements Azure différents.
> * Le coffre Recovery Services et le groupe de ressources auquel il appartient peuvent se trouver dans le même emplacement Azure.

Dans l’exemple de cet article, la machine virtuelle protégée se trouve dans la région USA Est. La région de récupération sélectionnée pour la récupération d’urgence est USA Ouest 2. Le coffre Recovery Services et son groupe de ressources se trouvent tous les deux dans la région de récupération (USA Ouest 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Créez un coffre Recovery Services. Dans l’exemple ci-dessous, un coffre Recovery Services nommé a2aDemoRecoveryVault est créé dans la région USA Ouest.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Définir le contexte du coffre

> [!TIP]
> Le module Azure Site Recovery PowerShell (module Az.RecoveryServices) est fourni avec des alias faciles à utiliser pour la plupart des applets de commande. Les applets de commande dans le module prennent la forme  *\<opération >-**AzRecoveryServicesAsr**\<objet >* et possèdent des alias équivalents qui prennent la forme  *\< Opération >-**ASR**\<objet >*. Cet article utilise les alias de cmdlet afin de faciliter la lecture.

Définissez le contexte du coffre pour l’utiliser dans la session PowerShell. Pour ce faire, téléchargez le fichier de paramètres du coffre, et importez-le dans la session PowerShell pour définir le contexte du coffre.

Ensuite, les opérations suivantes d’Azure Site Recovery dans la session PowerShell sont effectuées dans le contexte du coffre sélectionné.

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Préparation du coffre pour commencer la réplication des machines virtuelles Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Créer un objet de structure Site Recovery pour représenter la région (source) principale

L’objet de structure dans le coffre représente une région Azure. L’objet de structure principal est créé pour représenter la région Azure à laquelle appartiennent les machines virtuelles protégées. Dans l’exemple de cet article, la machine virtuelle protégée se trouve dans la région USA Est.

- Un seul objet de structure peut être créé par région.
- Si vous avez activé la réplication Site Recovery pour une machine virtuelle dans le portail Azure, Site Recovery crée automatiquement un objet de structure. S’il existe un objet de structure pour une région, vous ne pouvez en pas créer.


Avant de commencer, notez que les opérations Azure Site Recovery sont exécutées de façon asynchrone. Lorsque vous lancez une opération, un travail Azure Site Recovery est envoyé et un objet de suivi de travail est renvoyé. Utilisez l’objet de suivi du travail pour obtenir l’état récent du travail (Get-ASRJob), et pour surveiller l’état de l’opération.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Si des machines virtuelles de plusieurs régions Azure sont protégées vers le même coffre, créez un objet de structure pour chaque région Azure source.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Créer un objet de structure Site Recovery pour représenter la région de récupération

L’objet de structure de récupération représente l’emplacement Azure de récupération. Les machines virtuelles seront répliquées vers et récupérées dans (en cas de basculement) la région de récupération représentée par la structure de récupération. La région Azure de récupération utilisée dans cet exemple est USA Ouest 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Créer un conteneur de protection Site Recovery dans la structure principale

Le conteneur de protection est un conteneur utilisé pour répliquer par groupes des éléments au sein d’une structure.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Créer un conteneur de protection Site Recovery dans la structure de récupération

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Créer un mappage de conteneur de protection entre le conteneur de protection de récupération et le conteneur de protection principal.

Un mappage de conteneur de protection mappe le conteneur de protection principal avec un conteneur de protection de récupération et une stratégie de réplication. Créez un mappage pour chaque stratégie de réplication utilisée pour répliquer des machines virtuelles entre une paire de conteneurs de protection.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Créer un mappage de conteneur de protection pour la restauration automatique (réplication inverse après basculement)

Après un basculement, lorsque vous êtes prêt à ramener la machine virtuelle basculée dans la région Azure d’origine, vous effectuez une restauration automatique. Pour l’effectuer, la machine virtuelle basculée est répliquée de façon inverse depuis la région basculée vers la région d’origine. En cas de réplication inverse, les rôles de la région d’origine et de la région de récupération sont inversés. La région d’origine devient alors la nouvelle région de récupération, tandis que celle qui était la région de récupération devient la région principale. Le mappage de conteneur de protection pour une réplication inverse représente les rôles inversés des régions d’origine et de récupération.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Créer des comptes de stockage de cache et des comptes de stockage cible

Un compte de stockage de cache est un compte de stockage standard situé dans la même région Azure que la machine virtuelle répliquée. Il est utilisé pour conserver temporairement les modifications de réplication, avant qu’ils ne soient déplacés vers la région Azure de récupération. Vous pouvez choisir (mais n’y êtes pas obligé) de spécifier différents comptes de stockage de cache pour les différents disques d’une machine virtuelle.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Pour les machines virtuelles qui n’**utilisent pas de disques managés**, le compte de stockage cible est le compte de stockage dans la région de récupération vers lequel les disques de la machine virtuelle sont répliqués. Le compte de stockage cible peut être un compte de stockage standard ou un compte de stockage premium. Sélectionnez le type de compte de stockage nécessaire en fonction du taux de modification des données (taux d’écriture d’E/S) pour les disques, ainsi que les limites d’évolution prises en charge par Azure Site Recovery pour ce type de stockage.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Créer des mappages réseau

Un mappage réseau mappe des réseaux virtuels dans la région principale à des réseaux virtuels dans la région de récupération. Le mappage réseau spécifie le réseau virtuel Azure dans la région de récupération, vers lequel doit basculer une machine virtuelle du réseau virtuel principal. Un réseau virtuel Azure peut être mappé à un seul réseau virtuel Azure seulement, dans une région de récupération.

- Créer un réseau virtuel Azure dans la région de récupération vers laquelle basculer

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Récupérer le réseau virtuel principal (celui auquel est connectée la machine virtuelle)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Créer le mappage entre le réseau virtuel principal et le réseau virtuel de récupération
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Créer le mappage réseau pour la direction inverse (restauration automatique)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Répliquer des machines virtuelles Azure

Répliquez la machine virtuelle Azure avec des **disques managés**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0].StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Répliquez la machine virtuelle Azure avec des **disques non managés**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Une fois l’opération de lancement de réplication terminée, les données de la machine virtuelle sont répliquées vers la région de récupération.

Le processus de réplication démarre en envoyant une copie des disques de réplication de la machine virtuelle dans la région de récupération. Cette phase est appelée phase de réplication initiale.

Une fois cette phase terminée, la réplication passe à la phase de synchronisation différentielle. À ce stade, la machine virtuelle est protégée et une opération de basculement de test peut être effectuée. L’état de réplication de l’élément répliqué qui représente la machine virtuelle passe à l’état « Protégé » après la réplication initiale.

Surveillez l’état de réplication et l’intégrité de la réplication de la machine virtuelle en obtenant les détails sur l’élément de réplication protégé qui lui correspond.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Effectuer un test de basculement, puis valider et supprimer le test de basculement

Une fois la réplication de la machine virtuelle à l’état Protégé, une opération de basculement de test peut être effectuée sur la machine virtuelle (sur l’élément de réplication protégé de la machine virtuelle).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Exécutez un basculement de test.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Attendez que l’opération se termine.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Une fois le travail de basculement de test terminé, vous pouvez vous connecter à la machine virtuelle basculée de test et valider le basculement de test.

Une fois le test terminé sur la machine virtuelle basculée de test, nettoyez la copie de test en démarrant l’opération de nettoyage de basculement de test. Cette opération supprime la copie de test de la machine virtuelle créée par le basculement de test.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Basculement vers Azure

Basculez la machine virtuelle vers un point de récupération spécifique.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Une fois basculée, vous pouvez valider l’opération de basculement.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-failback-to-source-region"></a>La reprotection et la restauration automatique vers la région source

Après un basculement, lorsque vous êtes prêt à revenir en arrière à la région d’origine, lancer la réplication inverse pour la réplication élément protégé à l’aide de l’applet de commande Update-AzRecoveryServicesAsrProtectionDirection.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage accountin West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $RecoveryProtContainer -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.Id
```

Une fois la reprotection terminée, vous pouvez lancer le basculement dans le sens inverse (ouest des États-Unis est des États-Unis) et la restauration automatique vers la région source.

## <a name="next-steps"></a>Étapes suivantes
Afficher le [référence Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/az.RecoveryServices) pour savoir comment vous pouvez effectuer d’autres tâches telles que la création de Plans de récupération et de test de basculement des plans de récupération via PowerShell.
