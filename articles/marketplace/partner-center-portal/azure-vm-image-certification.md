---
title: Validation d’image de machine virtuelle Azure – Place de marché Azure
description: Découvrez comment tester et envoyer une offre de machine virtuelle sur la Place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 2d19098ec82fe9361154d798b981341a86decf97
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647824"
---
# <a name="azure-virtual-machine-image-validation"></a>Validation d’image de machine virtuelle Azure

Cet article explique comment tester et envoyer une image de machine virtuelle vers la Place de marché commerciale afin de vérifier qu’elle répond aux exigences de publication les plus récentes de la Place de marché Azure.

Effectuez les étapes suivantes avant d’envoyer votre offre de machine virtuelle :

- Déployez une machine virtuelle Azure à l’aide de votre image généralisée.
- Effectuez les validations.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Déployer une machine virtuelle Azure à l’aide de votre image généralisée

Cette section explique comment déployer une image de disque dur virtuel (VHD) généralisée pour créer une ressource de machine virtuelle Azure. Pour ce processus, nous allons utiliser le modèle Resource Manager et le script Azure PowerShell fournis.

### <a name="prepare-an-azure-resource-manager-template"></a>Préparer un modèle Azure Resource Manager

Cette section décrit comment créer et déployer une image de machine virtuelle fournie par l’utilisateur. Pour ce faire, vous pouvez fournir des images de disque dur virtuel du système d’exploitation et de disque de données à partir d’un disque dur virtuel déployé par Azure. Ces étapes déploient la machine virtuelle à l’aide du disque dur virtuel généralisé.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Chargez votre disque dur virtuel de système d’exploitation généralisé et vos disques durs virtuels de disques de données sur votre compte de stockage Azure.
3. Dans la page d’accueil, sélectionnez **Créer une ressource**, recherchez « Déploiement de modèle », puis sélectionnez **Créer**.
4. Choisissez **Créer votre propre modèle dans l’éditeur**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Affiche la sélection d’un modèle.":::

5. Collez le modèle JSON suivant dans l’éditeur, puis sélectionnez **Enregistrer**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Indiquez les valeurs des paramètres pour les pages de propriétés Déploiement personnalisé affichées.

| ResourceGroupName | Le nom du groupe de ressources Azure existant. En général, vous devez utiliser le même groupe de ressources que votre coffre de clés. |
| --- | --- |
| TemplateFile | Chemin complet du fichier VHDtoImage.json. |
| userStorageAccountName | Nom du compte de stockage. |
| dnsNameForPublicIP | Nom DNS de l’adresse IP publique. Doit être en minuscules. |
| subscriptionId | Identificateur de l’abonnement Azure. |
| Emplacement | Sélectionnez un emplacement géographique Azure standard pour le groupe de ressources. |
| vmName | Nom de la machine virtuelle. |
| vhdUrl | Adresse web du disque dur virtuel. |
| vmSize | Taille de l’instance de machine virtuelle. |
| publicIPAddressName | Nom de l’adresse IP publique. |
| virtualNetworkName | Nom du réseau virtuel. |
| nicName | Nom de la carte d’interface réseau du réseau virtuel. |
| adminUsername | Nom d’utilisateur du compte administrateur. |
| adminPassword | Mot de passe de l’administrateur. |
|

7. Après avoir indiqué ces valeurs, sélectionnez **Achat**.

Azure commence le déploiement. Une nouvelle machine virtuelle est créée avec le disque dur virtuel non managé spécifié, au chemin du compte de stockage spécifié. Pour en suivre la progression dans le portail Azure, sélectionnez **Machines virtuelles** sur le côté gauche du portail. Une fois la machine virtuelle créée, l’état passe de Démarrage à En cours d’exécution.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Pour le déploiement de machines virtuelles de 2e génération, utilisez ce modèle :

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Déployer une machine virtuelle Azure à l’aide de PowerShell

Copiez et modifiez le script suivant pour indiquer les valeurs des variables `$storageaccount` et `$vhdUrl`. Exécutez-le pour créer une ressource de machine virtuelle Azure à partir de votre disque dur virtuel généralisé existant.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Effectuer les validations

Il existe deux façons d’effectuer des validations sur l’image déployée.

### <a name="use-certification-test-tool-for-azure-certified"></a>Utiliser l’outil Certification Test Tool for Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Télécharger et exécuter l’outil Certification Test

L’outil Certification Test Tool for Azure Certified s’exécute sur une machine Windows locale, mais teste une machine virtuelle Windows ou Linux basée sur Azure. Il vérifie que votre image de machine virtuelle utilisateur peut être utilisée avec Microsoft Azure, autrement dit que les recommandations et conditions requises de la préparation de votre VHD ont été satisfaites.

1. Téléchargez et installez la version la plus récente de l’outil [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Ouvrez l’outil de certification, puis sélectionnez **Démarrer un nouveau test**.
3. Dans l’écran Test Information (Informations sur le test), entrez un nom de test dans le champ **Test Name** (Nom du test) pour la série de tests.
4. Dans le champ Plateforme, sélectionnez **Windows Server** ou **Linux** pour votre machine virtuelle. La plateforme que vous choisissez a une incidence sur le reste des options.
5. Si votre machine virtuelle utilise ce service de base de données, cochez la case **Test pour Azure SQL Database**.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle

1. Sélectionnez le mode SSH Authentication (Authentification SSH) : Password Authentication (authentification par mot de passe) ou Key File Authentication (authentification par fichier de clé).
2. Si vous utilisez une authentification par mot de passe, renseignez les champs **Nom du DNS de machine virtuelle**, **Nom d’utilisateur** et **Mot de passe**. Vous pouvez également modifier la valeur par défaut du champ SSH Port (Port SSH).

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Affiche la sélection des informations de test de machine virtuelle.":::

3. Si vous utilisez l’authentification par fichier de clé, renseignez les champs VM DNS Name (Nom du DNS de machine virtuelle), User Name (Nom d’utilisateur) et Private key (Clé privée). Vous pouvez également renseigner le champ Passphrase (Phrase secrète) ou modifier la valeur par défaut du champ SSH Port (Port SSH).
4. Saisissez le nom de domaine (DNS) complet de la machine virtuelle (par exemple, MyVMName.Cloudapp.net).
5. Saisissez le **nom d’utilisateur** et le **mot de passe**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Affiche la sélection du nom d’utilisateur et du mot de passe de la machine virtuelle.":::

6. Sélectionnez **Suivant**.

#### <a name="run-a-certification-test"></a>Exécuter un test de certification

Après avoir fourni les valeurs des paramètres pour votre image de machine virtuelle dans l’outil de certification, sélectionnez Tester la connexion pour créer une connexion valide vers votre machine virtuelle. Une fois la connexion vérifiée, sélectionnez **Next** (Suivant) pour démarrer le test. Une fois le test terminé, les résultats s’affichent dans un tableau. La colonne Status (État) affiche Pass/Fail/Warning (Réussite/Échec/Avertissement) pour chaque test. Si l’un des tests échoue, votre image n’est pas certifiée. Dans ce cas, examinez les exigences et les messages d’échec, apportez les modifications suggérées, puis réexécutez le test.

Une fois le test automatisé terminé, fournissez des informations supplémentaires sur votre image de machine virtuelle sous les deux onglets de l’écran Questionnaire que sont General Assessment (Évaluation générale) et Kernel Customization (Personnalisation du noyau), puis sélectionnez Next (Suivant).

Le dernier écran vous permet de fournir des informations supplémentaires, telles que les informations d’accès SSH pour une image de machine virtuelle Linux, ainsi qu’une explication des éventuels échecs d’évaluation si vous recherchez des exceptions.

Enfin, sélectionnez Generate Report (Générer le rapport) afin de télécharger les résultats de test et les fichiers journaux pour les cas de test exécutés, ainsi que vos réponses au questionnaire. Enregistrez les résultats dans le même conteneur que vos disques durs virtuels.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Comment utiliser PowerShell pour consommer l’API d’auto-test

### <a name="on-linux-os"></a>Sur le système d’exploitation Linux

Appelez l’API dans PowerShell :

1. Utilisez la commande Invoke-WebRequest pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Spécifiez les paramètres de corps au format JSON.

L’exemple suivant montre un appel PowerShell à l’API :

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Voici un exemple d’appel de l’API dans PowerShell :

[![Screen example for calling the API in PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Cet exemple d’écran, qui affiche `$res.Content`, montre les détails de vos résultats de test au format JSON :

[![Screen example for calling the API in PowerShell with details of test results.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Voici un exemple de résultats de test JSON affichés dans une visionneuse JSON en ligne (par exemple, [Code Beautify](https://codebeautify.org/jsonviewer) ou [Visionneuse JSON](https://jsonformatter.org/json-viewer)).

![D’autres résultats de test dans une visionneuse JSON en ligne.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Sur le système d’exploitation Windows

Appelez l’API dans PowerShell :

1. Utilisez la commande Invoke-WebRequest pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Créez les paramètres de corps au format JSON.

Cet exemple de code montre un appel PowerShell à l’API :

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

Ces captures d’écran montrent un exemple d’appel de l’API dans PowerShell :

**Avec clé SSH** :

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Appel de l’API dans PowerShell avec une clé SSH.":::

**Avec mot de passe** :

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Appel de l’API dans PowerShell avec un mot de passe.":::

<br>À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Cet écran, qui affiche `$res.Content`, montre les détails de vos résultats de test au format JSON :

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Détails des résultats de test au format JSON.":::

<br>Voici un exemple de résultats de test affichés dans une visionneuse JSON en ligne (par exemple, [Code Beautify](https://codebeautify.org/jsonviewer) ou [Visionneuse JSON](https://jsonformatter.org/json-viewer)).

![Résultats de test dans une visionneuse JSON en ligne.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Comment utiliser cURL pour consommer l’API d’auto-test sur le système d’exploitation Linux

Appelez l’API dans cURL :

1. Utilisez la commande curl pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans l’extrait de code suivant.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Voici un exemple d’utilisation de cURL pour appeler l’API :

![Exemple d’utilisation de cURL pour appeler l’API.](media/vm/use-curl-call-api.png)

<br>Voici les résultats JSON de l’appel cURL :

![Résultats JSON de l’appel cURL.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Étape suivante

- Consultez[Problèmes courants des URI SAS et correctifs](common-sas-uri-issues.md).
