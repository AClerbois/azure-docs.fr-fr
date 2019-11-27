---
title: Extension de script personnalisé Azure pour Windows
description: Automatiser les tâches de configuration de machine virtuelle Windows à l’aide de l’extension de script personnalisé
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: b3c355219fcbebc5fda38c33d6eb7f9126b3b2b8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073824"
---
# <a name="custom-script-extension-for-windows"></a>Extension de script personnalisé pour Windows

L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Elle est utile pour la configuration de post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du Portail Azure ou de l’API REST de machine virtuelle Azure.

Ce document explique en détail l’utilisation de l’extension de script personnalisé à l’aide du module Azure PowerShell, des modèles Azure Resource Manager, et détaille également les étapes de résolution de problèmes sur les systèmes Windows.

## <a name="prerequisites"></a>Prérequis

> [!NOTE]  
> N’utilisez pas l’extension de script personnalisé pour exécuter Update-AzVM avec la même machine virtuelle en tant que paramètre, car elle s’attendra elle-même.  

### <a name="operating-system"></a>Système d’exploitation

L’extension de script personnalisé pour Windows s’exécute sur les systèmes d’exploitation pris en charge par l’extension. Pour plus d’informations, consultez les [systèmes d’exploitation pris en charge par l’extension Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Emplacement du script

L’extension vous permet d’utiliser vos informations d’identification de Stockage Blob Azure pour accéder au stockage d’objets blob Azure. Le script peut être placé n’importe où, tant que la machine virtuelle peut effectuer le routage vers ce point de terminaison, par exemple GitHub, ou un serveur de fichiers interne.

### <a name="internet-connectivity"></a>Connectivité Internet

Si vous devez télécharger un script en externe, par exemple à partir de GitHub ou du Stockage Azure, vous devez ouvrir des ports de pare-feu et de groupe de sécurité réseau supplémentaires. Par exemple, si votre script se trouve dans le Stockage Azure, vous pouvez en autoriser l’accès à l’aide de balises de service du groupe de sécurité réseau Azure pour le [Stockage](../../virtual-network/security-overview.md#service-tags).

Si votre script se trouve sur un serveur local, vous devrez peut-être encore ouvrir des ports de pare-feu et de groupe de sécurité réseau supplémentaires.

### <a name="tips-and-tricks"></a>Astuces et conseils

* Le taux d’échec le plus élevé de cette extension est dû aux erreurs de syntaxe contenues dans le script. Testez les exécutions de script sans erreur et insérez également une journalisation supplémentaire dans le script pour trouver plus facilement l’emplacement de l’échec.
* Écrivez des scripts idempotents. Cela garantit que, s’ils sont exécutés accidentellement, cela n’entraînera de modifications du système.
* Vérifiez que l’exécution des scripts ne nécessite pas d’entrée de l’utilisateur.
* L’exécution du script est autorisée pendant 90 minutes. Toute exécution d’une durée supérieure entraîne l’échec du provisionnement de l’extension.
* Ne placez pas de redémarrage dans le script. Cette action provoque des problèmes avec les autres extensions qui sont en cours d’installation. Après le redémarrage, l’extension ne continuera pas.
* Si vous avez un script qui entraîne un redémarrage, installe des applications puis exécute des scripts, vous pouvez planifier le redémarrage à l’aide d’une tâche planifiée Windows, ou utiliser des outils tels que les extensions DSC, Chef ou Puppet.
* L’extension n’exécute un script qu’une seule fois. Si vous voulez exécuter un script à chaque démarrage, vous devez utiliser l’extension pour créer une tâche planifiée Windows.
* Si vous souhaitez planifier le moment de l’exécution d’un script, vous devez utiliser l’extension pour créer une tâche planifiée Windows.
* Lors de l’exécution du script, vous voyez seulement l’état de l’extension « transition en cours » dans le portail Azure ou l’interface Azure CLI. Si vous souhaitez que les mises à jour de l’état d’un script en cours d’exécution soient plus fréquentes, vous devez créer votre propre solution.
* L’extension de script personnalisé ne prend pas en charge les serveurs proxy en mode natif, mais vous pouvez utiliser un outil de transfert de fichiers prenant en charge les serveurs proxy dans votre script, par exemple *Curl*.
* Tenez compte des emplacements de répertoire autres que par défaut, et qui sont susceptibles d’être utilisés pour vos scripts ou commandes. Gérez cette situation de façon logique.
* L’extension de script personnalisé s’exécutera sous le compte LocalSystem

## <a name="extension-schema"></a>Schéma d’extensions

La configuration de l’extension de script personnalisé spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration peut être stockée dans des fichiers de configuration, ou spécifiée en ligne de commande ou dans un modèle Azure Resource Manager.

Les données sensibles peuvent être stockées dans une configuration protégée qui n’est chiffrée et déchiffrée qu’à l’intérieur de la machine virtuelle. La configuration protégée est utile lorsque la commande d’exécution comprend des secrets tels qu’un mot de passe.

Ces éléments doivent être traités comme des données sensibles et spécifiés dans la configuration du paramètre de protection des extensions. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Il n’est possible d’installer qu’une seule version d’une extension sur une machine virtuelle à un moment donné. Spécifier deux fois un script personnalisé dans le même modèle Azure Resource Manager pour une même machine virtuelle aboutira à un échec.

> [!NOTE]
> Nous pouvons utiliser ce schéma dans la ressource VirtualMachine ou en tant que ressource autonome. Le nom de la ressource doit être au format « NomMachineVirtuelle / NomExtension », si cette extension est utilisée en tant que ressource autonome dans le modèle Resource Manager. 

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| Type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (p. ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (p. ex.) | 123456789 | Entier 32 bits |
| commandToExecute (p. ex.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (p. ex.) | examplestorageacct | string |
| storageAccountKey (p. ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Ces noms de propriétés respectent la casse. Pour éviter des problèmes de déploiement, utilisez les noms présentés ici.

#### <a name="property-value-details"></a>Détails des valeurs de propriété

* `commandToExecute` : (**obligatoire**, chaîne) script de point d’entrée à exécuter. Utilisez plutôt ce champ si votre commande contient des secrets tels que des mots de passe ou si vos URI de fichier sont sensibles.
* `fileUris` : (facultatif, tableau de chaînes) URL des fichiers à télécharger.
* `timestamp` : (facultatif, entier 32 bits) utilisez ce champ uniquement pour déclencher la réexécution du script en modifiant la valeur de ce champ.  Toutes les valeurs sont autorisées pour l’entier. Cette valeur doit uniquement être différente de la valeur précédente.
* `storageAccountName` : (facultatif, chaîne) nom du compte de stockage. Si vous spécifiez des informations d’identification de stockage, toutes les propriétés `fileUris` doivent être des URL d’objets blob Azure.
* `storageAccountKey` : (facultatif, chaîne) clé d’accès du compte de stockage.

Les valeurs suivantes peuvent être définies dans les paramètres publics ou protégés. L’extension rejette les configurations dans lesquelles les valeurs indiquées ci-dessous sont définies à la fois dans les paramètres publics et protégés.

* `commandToExecute`

L’utilisation des paramètres publics peut être utile pour le débogage, mais nous vous recommandons d’utiliser des paramètres protégés.

Les paramètres publics sont envoyés en texte clair à la machine virtuelle sur laquelle le script est exécuté.  Les paramètres protégés sont chiffrés à l’aide d’une clé connue uniquement d’Azure et de la machine virtuelle. Les paramètres sont enregistrés sur la machine virtuelle tels qu’ils ont été envoyés. Autrement dit, si les paramètres ont été chiffrés, ils sont enregistrés chiffrés sur la machine virtuelle. Le certificat utilisé pour déchiffrer les valeurs chiffrées est stocké sur la machine virtuelle et permet de déchiffrer les paramètres (si nécessaire) lors de l’exécution.

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension de script personnalisé pendant un déploiement. Les exemples suivants montrent comment utiliser l’extension de script personnalisé :

* [Tutoriel : Déployer des extensions de machines virtuelles avec des modèles Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Déployer une application à deux niveaux sur Windows et Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzVMCustomScriptExtension` pour ajouter l’extension de script personnalisé sur une machine virtuelle existante. Pour plus d’informations, consultez [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Exemples supplémentaires

### <a name="using-multiple-scripts"></a>Utilisation de plusieurs scripts

Dans cet exemple, vous avez trois scripts utilisés pour créer votre serveur. **commandToExecute** appelle le premier script, vous pouvez alors choisir comment les autres sont appelés. Par exemple, vous pouvez avoir un script principal qui contrôle l’exécution, avec la gestion des erreurs, la journalisation et la gestion de l’état appropriées. Les scripts sont téléchargés sur l’ordinateur local pour l’exécution. Par exemple, dans `1_Add_Tools.ps1`, vous pourriez appeler `2_Add_Features.ps1` en ajoutant `.\2_Add_Features.ps1` au script, puis répéter ce processus pour les autres scripts que vous définissez dans `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Exécution de scripts à partir d’un partage local

Dans cet exemple, vous souhaiterez peut-être utiliser un serveur SMB local pour l’emplacement de votre script. Vous ne devez ainsi pas fournir d’autres paramètres, à l’exception de **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Comment exécuter un script personnalisé plusieurs fois avec l’interface de ligne de commande

Si vous souhaitez exécuter plusieurs fois l’extension de script personnalisé, vous ne pouvez le faire que dans les conditions suivantes :

* Le paramètre **Name** de l’extension est le même que pour le déploiement précédent de celle-ci.
* Mettez à jour la configuration, sinon la commande ne sera pas réexécutée. Vous pouvez ajouter une propriété dynamique dans la commande, comme un horodateur.

Vous pouvez également définir la propriété [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) sur **true**.

### <a name="using-invoke-webrequest"></a>Utilisation d’Invoke-WebRequest

Si vous utilisez [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) dans votre script, vous devez spécifier le paramètre `-UseBasicParsing` ; sinon, vous recevrez l’erreur suivante lors de la vérification de l’état détaillé :

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>les machines virtuelles Classic,

Pour déployer l’extension de script personnalisé sur des machines virtuelles classiques, vous pouvez utiliser le Portail Azure ou les applets de commande Azure PowerShell classiques.

### <a name="azure-portal"></a>Portail Azure

Accédez à votre ressource de machine virtuelle classique. Sélectionnez **Extensions** sous **Paramètres**.

Cliquez sur **+ Ajouter** puis, dans la liste des ressources, choisissez **Custom Script Extension** (Extension de script personnalisé).

Dans la page **Installer l’extension**, sélectionnez le fichier PowerShell local et remplissez tous les arguments, puis cliquez sur **OK**.

### <a name="powershell"></a>PowerShell

Utilisez la cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) pour ajouter l’extension de script personnalisé sur une machine virtuelle existante.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide du module Azure PowerShell. Pour voir l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante :

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

La sortie de l’extension est enregistrée dans les fichiers qui se trouvent dans le dossier suivant sur la machine virtuelle.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Les fichiers spécifiés sont téléchargés dans le dossier suivant sur la machine virtuelle cible.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

où `<n>` est un entier décimal, qui peut changer d’une exécution de l’extension à l’autre.  La valeur `1.*` correspond à la valeur`typeHandlerVersion` actuelle et réelle de l’extension.  Par exemple, le répertoire réel peut être `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Lors de l’exécution de la commande `commandToExecute`, l’extension définit ce répertoire (par exemple, `...\Downloads\2`) comme répertoire de travail actif. Ce processus permet d’utiliser des chemins relatifs pour localiser les fichiers téléchargés par le biais de la propriété `fileURIs`. Consultez le tableau ci-dessous pour en voir des exemples.

Étant donné que le chemin de téléchargement absolu peut varier au fil du temps, il est préférable d’opter pour des chemins de script/fichier relatifs dans la chaîne `commandToExecute`, dans la mesure du possible. Par exemple :

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Les informations de chemin d’accès après le premier segment d’URI sont conservées pour les fichiers téléchargés par le biais de la liste de propriétés `fileUris`.  Comme l’indique le tableau ci-dessous, les fichiers téléchargés sont mappés avec des sous-répertoires de téléchargement afin de refléter la structure des valeurs `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemples de fichiers téléchargés

| URI dans fileUris | Emplacement téléchargé relatif | Emplacement téléchargé absolu <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Les chemins absolus d’accès au répertoire évoluent pendant la durée de vie de la machine virtuelle, mais pas au sein d’une même exécution de l’extension CustomScript.

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
