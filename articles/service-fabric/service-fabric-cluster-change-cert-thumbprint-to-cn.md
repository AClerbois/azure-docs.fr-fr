---
title: Mettre à jour un cluster Azure Service Fabric pour qu’il utilise un nom commun de certificat | Microsoft Docs
description: Découvrez comment basculer un cluster Service Fabric de l’utilisation d’empreintes de certificat à l’utilisation du nom commun du certificat.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/01/2019
ms.author: ryanwi
ms.openlocfilehash: 6e596b0db1a03efbf6b029487ed956105b632edb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972802"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Modifier un cluster pour qu’il passe de l’utilisation d’une empreinte de certificat à l’utilisation d’un nom commun
Deux certificats ne peuvent pas avoir la même empreinte, ce qui complique la gestion ou le renouvellement des certificats de cluster. Toutefois, plusieurs certificats peuvent avoir le même nom commun ou objet.  Basculer un cluster déployé de l’utilisation des empreintes de certificat à l’utilisation des noms communs de certificat simplifie considérablement la gestion des certificats. Cet article décrit la mise à jour d’un cluster Service Fabric en cours d’exécution pour utiliser le nom commun d’un certificat à la place de son empreinte.

>[!NOTE]
> Si vous avez deux empreintes déclarées dans votre modèle, vous devez effectuer deux déploiements.  Le premier déploiement est effectué avant de suivre les étapes décrites dans cet article.  Le premier déploiement définit votre propriété **empreinte** (thumbprint) dans le modèle sur le certificat utilisé et supprime la propriété **thumbprintSecondary**.  Pour le deuxième déploiement, suivez les étapes décrites dans cet article.
 
## <a name="get-a-certificate"></a>Obtenir un certificat
Commencez par obtenir un certificat auprès d’une [autorité de certification (AC)](https://wikipedia.org/wiki/Certificate_authority).  Le nom commun du certificat doit être le nom d’hôte du cluster.  Exemple : « myclustername.southcentralus.cloudapp.azure.com ».  

À des fins de test, vous pouvez obtenir un certificat signé par une autorité de certification auprès d’une autorité de certification ouverte ou gratuite.

> [!NOTE]
> Les certificats auto-signés, notamment ceux qui sont générés lors du déploiement d’un cluster Service Fabric dans le portail Azure, ne sont pas pris en charge.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Charger le certificat et l’installer dans le groupe identique
Dans Azure, un cluster Service Fabric est déployé sur un groupe de machines virtuelles identiques.  Chargez le certificat dans un coffre de clés, puis installez-le sur le groupe de machines virtuelles identiques sur lequel le cluster s’exécute.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

## <a name="download-and-update-the-template-from-the-portal"></a>Télécharger et mettre à jour le modèle à partir du portail
Le certificat a été installé sur le groupe identique sous-jacent, mais vous devez également mettre à jour le cluster Service Fabric pour qu’il utilise ce certificat et son nom commun.  À présent, téléchargez le modèle de déploiement du cluster.  Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez au groupe de ressources qui héberge le cluster.  Dans **Paramètres**, sélectionnez **Déploiements**.  Sélectionnez le déploiement le plus récent, puis cliquez sur **Afficher le modèle**.

![Afficher les modèles][image1]

Téléchargez les fichiers JSON de modèle et de paramètres sur votre ordinateur local.

Tout d’abord, ouvrez le fichier de paramètres dans un éditeur de texte, puis ajoutez la valeur de paramètre suivante :
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ouvrez le fichier de modèle dans un éditeur de texte et effectuez trois mises à jour pour prendre en charge le nom commun du certificat.

1. Dans la section **parameters**, ajoutez un paramètre *certificateCommonName* :
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Pensez également à supprimer le paramètre *certificateThumbprint*, car il n’est sans doute plus utile.

2. Dans la ressource **Microsoft.Compute/virtualMachineScaleSets**, mettez à jour l’extension de machine virtuelle pour utiliser le nom commun dans les paramètres du certificat au lieu de l’empreinte.  Dans **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate**, ajoutez `"commonNames": ["[parameters('certificateCommonName')]"],` et supprimez `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  Dans la ressource **Microsoft.ServiceFabric/clusters**, mettez à jour la version de l’API vers « 2018-02-01 ».  Ajoutez également un paramètre **certificateCommonNames** avec une propriété **commonNames** et supprimez le paramètre **certificate** (avec la propriété d’empreinte) comme dans l’exemple suivant :
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Déployer le modèle mis à jour
Redéployez le modèle mis à jour après avoir apporté les modifications.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* Découvrez comment [renouveler un certificat de cluster](service-fabric-cluster-rollover-cert-cn.md).
* [Mettre à jour et gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
