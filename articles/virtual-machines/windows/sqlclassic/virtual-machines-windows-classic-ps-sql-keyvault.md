---
title: Intégrer Key Vault à une machine virtuelle Azure SQL Server classique
description: Apprenez à automatiser la configuration du chiffrement de SQL Server pour une utilisation avec Azure Key Vault. Cette rubrique explique comment utiliser l’intégration Azure Key Vault avec des machines virtuelles SQL Server créées avec un modèle de déploiement classique.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: ddf23126154f5bc62c49f62ac4adf517d6987091
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033471"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurer Azure Key Vault Integration pour SQL Server sur des machines virtuelles Azure (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classique](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Il existe plusieurs fonctionnalités de chiffrement SQL Server, telles que le [chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), le [chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) et le [chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces types de chiffrement nécessitent que vous gériez et stockiez les clés de chiffrement que vous utilisez pour le chiffrement. Le service Azure Key Vault (coffre de clés Azure, AKV) est conçu pour optimiser la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [connecteur SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d’utiliser ces clés depuis le coffre de clés Azure.

> [!IMPORTANT] 
> Azure a deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Si vous exécutez SQL Server sur des ordinateurs locaux, vous devez [suivre la procédure d'accès à Azure Key Vault à partir de votre ordinateur SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Mais, pour SQL Server dans des machines virtuelles Azure, vous pouvez gagner du temps à l'aide de la fonctionnalité *Azure Key Vault Integration* . Avec quelques applets de commande Azure PowerShell pour activer cette fonctionnalité, vous pouvez automatiser la configuration nécessaire pour qu'une machine virtuelle SQL puisse accéder à votre coffre de clés.

Lorsque cette fonctionnalité est activée, elle installe automatiquement le connecteur SQL Server, configure le fournisseur EKM pour accéder à Azure Key Vault et crée les informations d'identification vous permettant d'accéder à votre coffre. Si vous avez examiné les étapes décrites dans la documentation locale mentionnée précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule étape que vous devez encore exécuter manuellement consiste à créer le coffre de clé et des clés. À partir de là, la configuration complète de votre machine virtuelle SQL est automatisée. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter des instructions T-SQL pour crypter vos bases de données et vos sauvegardes comme vous y êtes habitué.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configuration d'AKV Integration
Utilisez PowerShell pour configurer Azure Key Vault Integration. Les sections suivantes fournissent une vue d'ensemble des paramètres requis, puis un exemple de script PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Installer l’extension IaaS SQL Server
[Tout d’abord, installez l’extension IaaS SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprendre les paramètres d’entrée
Le tableau suivant répertorie les paramètres requis pour exécuter le script PowerShell dans la section suivante.

| Paramètre | Description | Exemples |
| --- | --- | --- |
| **$akvURL** |**L'URL du coffre de clés** |« https:\//contosokeyvault.vault.azure.net/ » |
| **$spName** |**Nom du principal du service** |« fde2b411-33d5-4e11-af04eb07b669ccf2 » |
| **$spSecret** |**Secret du principal du service** |« 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= » |
| **$credName** |**Nom des informations d’identification** : L’intégration du coffre de clés Azure crée des informations d’identification dans SQL Server, permettant ainsi à la machine virtuelle d’accéder au coffre de clés. Choisissez un nom pour cette identification. |« mycred1 » |
| **$vmName** |**Nom de la machine virtuelle** : le nom d’une machine virtuelle SQL créée précédemment. |« myvmname » |
| **$serviceName** |**Nom du service** : le nom du service cloud associé à la machine virtuelle SQL. |« mycloudservicename » |

### <a name="enable-akv-integration-with-powershell"></a>Activation d'AKV Integration avec PowerShell
L'applet de commande **New-AzureVMSqlServerKeyVaultCredentialConfig** crée un objet de configuration pour la fonctionnalité Azure Key Vault Integration. **Set-AzureVMSqlServerExtension** configure cette intégration avec le paramètre **KeyVaultCredentialSettings**. Les étapes suivantes indiquent comment utiliser ces commandes.

1. Dans Azure PowerShell, configurez tout d’abord les paramètres d’entrée avec des valeurs spécifiques comme décrit dans les sections précédentes de cette rubrique. Vous trouverez ci-dessous un exemple de script.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Utilisez ensuite le script suivant pour configurer et activer AKV Integration.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L'extension de l'Agent SQL IaaS met à jour la machine virtuelle SQL avec cette nouvelle configuration.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

