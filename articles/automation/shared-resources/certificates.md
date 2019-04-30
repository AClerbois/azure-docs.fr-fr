---
title: Ressources de certificats dans Azure Automation
description: Les certificats sont en toute sécurité dans Azure Automation pour qu’ils soient accessibles par les runbooks ou configurations DSC pour s’authentifier auprès des ressources Azure et tiers.  Cet article présente les certificats et leur utilisation dans la création textuelle et graphique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216031"
---
# <a name="certificate-assets-in-azure-automation"></a>Ressources de certificats dans Azure Automation

Les certificats sont stockés en toute sécurité dans Azure Automation pour qu’ils soient accessibles par les runbooks ou configurations DSC à l’aide de la **Get-AzureRmAutomationCertificate** activité pour les ressources Azure Resource Manager. Cette fonctionnalité vous permet de créer des runbooks et des configurations DSC qui utilisent des certificats pour l’authentification, ou les ajoute aux ressources Azure ou tierces.

>[!NOTE]
>Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans un coffre de clés managé par le système. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource. Ce processus est managé par Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Applets de commande AzureRM PowerShell

Pour AzureRM, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell. Elles font partie de la [module AzureRM.Automation](/powershell/azure/overview), qui est disponible pour une utilisation dans des runbooks Automation et les configurations DSC.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Récupère des informations sur un certificat à utiliser dans un Runbook ou dans une configuration DSC. Vous pouvez uniquement récupérer le certificat lui-même à partir de l’activité Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Crée un nouveau certificat dans Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Supprime un certificat dans Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Définit les propriétés d’un certificat existant, y compris le téléchargement du fichier de certificat et le mot de passe d’un fichier .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Charge un certificat de service pour le service cloud spécifié.|

## <a name="activities"></a>Activités

Les activités du tableau suivant sont utilisées pour accéder aux certificats dans un Runbook et dans des configurations DSC.

| Activités | Description |
|:---|:---|
|Get-AutomationCertificate|Obtient un certificat à utiliser dans un Runbook ou dans une configuration DSC. Retourne un objet [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Évitez d’utiliser des variables dans le paramètre –Name de **Get-AutomationCertificate** dans un runbook ou une configuration DSC, car cela complique la découverte des dépendances entre les runbooks ou la configuration DSC et les variables Automation, au moment de la conception.

## <a name="python2-functions"></a>Fonctions Python2

La fonction dans le tableau suivant est utilisée pour accéder aux certificats dans un runbook Python2.

| Fonction | Description |
|:---|:---|
| automationassets.get_automation_certificate | Récupère des informations sur les ressources d’un certificat. |

> [!NOTE]
> Vous devez importer le module **automationassets** au début de votre runbook Python afin d’accéder aux fonctions des ressources.

## <a name="creating-a-new-certificate"></a>Création d’un certificat

Lorsque vous créez un certificat, vous téléchargez un fichier .cer ou .pfx dans Azure Automation. Si vous marquez le certificat comme exportable, vous pouvez également le transférer du magasin de certificats Azure Automation. Si elle n’est pas exportable, puis il peut uniquement être utilisé pour la signature dans le runbook ou la configuration de DSC. Azure Automation exige que le certificat provienne du fournisseur : **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Pour créer un certificat avec le portail Azure

1. À partir de votre compte Automation, cliquez sur le **actifs** vignette pour ouvrir le **actifs** page.
2. Cliquez sur le **certificats** vignette pour ouvrir le **certificats** page.
3. Cliquez sur **ajouter un certificat** en haut de la page.
4. Tapez un nom pour le certificat dans la zone **Nom** .
5. Pour rechercher un fichier .cer ou .pfx, cliquez sur **Sélectionner un fichier** sous **Charger un fichier de certificat**. Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et si elle peut être exportée.
6. Cliquez sur **Créer** pour enregistrer la nouvelle ressource de certificat.

### <a name="to-create-a-new-certificate-with-powershell"></a>Pour créer un nouveau certificat avec PowerShell

L’exemple suivant démontre la création d’un certificat Automation et sont marquage comme exportable. Cette opération importe un fichier pfx existant.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Créer un nouveau certificat avec le modèle Resource Manager

L’exemple suivant montre comment déployer un certificat à votre compte Automation à l’aide d’un modèle Resource Manager via PowerShell :

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Utilisation d’un certificat

Pour utiliser un certificat, vous devez utiliser l’activité **Get-AutomationCertificate**. Vous ne pouvez pas utiliser le [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) applet de commande, car celle-ci renvoie des informations sur la ressource de certificat, mais pas le certificat lui-même.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel

L’exemple de code suivant montre comment ajouter un certificat à un service cloud dans un Runbook. Dans cet exemple, le mot de passe est récupéré à partir d’une variable Automation chiffrée.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exemple de Runbook graphique

Vous ajoutez un **Get-AutomationCertificate** à un runbook graphique en cliquant sur le certificat dans le volet bibliothèque et en sélectionnant **ajouter au canevas**.

![Ajouter un certificat à la zone de dessin](../media/certificates/automation-certificate-add-to-canvas.png)

L’image suivante montre un exemple d’utilisation d’un certificat dans un Runbook graphique. Cela est identique à l’exemple précédent qui montre comment ajouter un certificat à un service cloud à partir d’un runbook textuel.

![Exemple de création graphique](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemple de Python2

L’exemple suivant montre comment accéder aux certificats dans les runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation des liens pour contrôler le flux logique d’activités que votre runbook est conçu pour effectuer, consultez [Liens de création graphique](../automation-graphical-authoring-intro.md#links-and-workflow). 
