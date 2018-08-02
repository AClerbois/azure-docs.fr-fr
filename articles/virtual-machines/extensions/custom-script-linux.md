---
title: Exécuter des scripts personnalisés sur des machines virtuelles Linux dans Azure | Microsoft Docs
description: Automatiser les tâches de configuration de machine virtuelle Linux à l’aide de l’extension de script personnalisé v2
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 439b38af14da85a9ad263645be8a0961c1266d73
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414723"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Utiliser l’extension de script personnalisé Azure version 2 avec des machines virtuelles Linux
L’extension de script personnalisé version 2 télécharge et exécute des scripts sur des machines virtuelles Azure. Elle est utile pour la configuration de post-déploiement, l’installation de logiciels ou toute autre tâche de configuration/gestion. Il est possible de télécharger des scripts à partir du Stockage Azure ou de tout autre emplacement Internet accessible, ou de les fournir au runtime de l’extension. 

L’extension de script personnalisé est compatible avec les modèles Azure Resource Manager. Elle est également exécutable avec Azure CLI, PowerShell, le Portail Azure et l’API REST Machines virtuelles Azure.

Cet article explique en détail comment utiliser l’extension de script personnalisé avec Azure CLI et l’exécuter à l’aide d’un modèle Azure Resource Manager. Il indique également la procédure de résolution des problèmes pour les systèmes Linux.


Il existe deux extensions de script personnalisé Linux :
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 : Microsoft.Azure.Extensions.CustomScript

Basculez entre les déploiements existant et nouveau pour utiliser plutôt la nouvelle version 2. Cette nouvelle version est prévue comme un remplacement instantané. Ainsi, pour procéder à la migration, il vous suffit de changer le nom et la version ; il est inutile de modifier la configuration de l’extension.


### <a name="operating-system"></a>Système d’exploitation

L’extension de script personnalisé pour Linux s’exécute sur les systèmes d’exploitation pris en charge par l’extension. Pour en savoir plus, voir cet [article](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Emplacement du script

L’extension vous permet d’utiliser vos informations d’identification de stockage d’objets blob Azure pour accéder au stockage d’objets blob Azure. Par ailleurs, le script peut être placé n’importe où, tant que la machine virtuelle peut effectuer le routage vers ce point de terminaison, par exemple GitHub, un serveur de fichiers interne, etc.

### <a name="internet-connectivity"></a>Connectivité Internet
Si vous devez télécharger un script en externe, par exemple à partir de GitHub ou du stockage Azure, vous devez ouvrir des ports de pare-feu/de groupe de sécurité réseau supplémentaires. Par exemple, si votre script se trouve dans le Stockage Azure, vous pouvez en autoriser l’accès à l’aide de balises de service du groupe de sécurité réseau Azure pour le [Stockage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Si votre script se trouve sur un serveur local, vous devrez peut-être encore ouvrir des ports de pare-feu/de groupe de sécurité réseau supplémentaires.

### <a name="tips-and-tricks"></a>Astuces et conseils
* Le taux d’échec le plus élevé de cette extension est dû aux erreurs de syntaxe contenues dans le script. Testez les exécutions de script sans erreur et insérez également une journalisation supplémentaire dans le script pour trouver plus facilement l’emplacement de l’échec.
* Écrivez des scripts idempotents ; s’ils sont exécutés plusieurs fois par erreur, ils n’entraîneront pas de modification du système.
* Vérifiez que l’exécution des scripts ne nécessite pas d’entrée utilisateur.
* L’exécution du script est autorisée pendant 90 minutes. Toute exécution d’une durée supérieure entraîne l’échec du provisionnement de l’extension.
* N’insérez pas de redémarrages dans le script, car cela entraîne des problèmes avec d’autres extensions en cours d’installation : après un redémarrage, l’extension ne poursuit pas son exécution. 
* Si l’un de vos scripts provoque un redémarrage, installez des applications, puis exécutez des scripts, etc. Vous devez planifier le redémarrage à l’aide d’une tâche Cron, d’outils tels que DSC ou d’extensions Chef ou Puppet.
* L’extension n’exécute un script qu’une seule fois. Si vous souhaitez exécuter un script à chaque démarrage, vous pouvez utiliser [une image cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) et utiliser un module [Scripts au démarrage](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Vous pouvez également utiliser le script pour créer une unité de service Systemd.
* Si vous souhaitez planifier le moment de l’exécution d’un script, vous devez utiliser l’extension pour créer une tâche Cron. 
* Lors de l’exécution du script, vous voyez seulement l’état de l’extension « transition en cours » dans le portail Azure ou l’interface Azure CLI. Si vous souhaitez que les mises à jour de l’état d’un script en cours d’exécution soient plus fréquentes, vous devez créer votre propre solution.
* L’extension de script personnalisé ne prend pas en charge les serveurs proxy en mode natif, en revanche vous pouvez utiliser un outil de transfert de fichiers qui prend en charge les serveurs proxy dans votre script, par exemple *Curl*. 
* Tenez compte des emplacements de répertoire autres que par défaut, et qui sont susceptibles d’être utilisés pour vos scripts ou commandes. Gérez cette situation de façon logique.



## <a name="extension-schema"></a>Schéma d’extensions

La configuration de l’extension de script personnalisé spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration peut être stockée dans des fichiers de configuration, ou spécifiée en ligne de commande ou dans un modèle Azure Resource Manager. 

Les données sensibles peuvent être stockées dans une configuration protégée qui n’est chiffrée et déchiffrée qu’à l’intérieur de la machine virtuelle. La configuration protégée est utile lorsque la commande d’exécution comprend des secrets tels qu’un mot de passe.

Ces éléments doivent être traités comme des données sensibles et spécifiés dans la configuration du paramètre de protection des extensions. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible.

```json
{
  "name": "config-app",
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":"123456789",          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute.Extensions | chaîne |
| Type | CustomScript | chaîne |
| typeHandlerVersion | 2.0 | int |
| fileUris (p. ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (p. ex.) | python MyPythonScript.py <my-param1> | chaîne |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | chaîne |
| skipDos2Unix (p. ex.) | false | booléenne |
| timestamp (p. ex.) | 123456789 | Entier 32 bits |
| storageAccountName (p. ex.) | examplestorageacct | chaîne |
| storageAccountKey (p. ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | chaîne |

### <a name="property-value-details"></a>Détails des valeurs de propriété
* `skipDos2Unix` : (facultatif, booléen) ignore la conversion dos2unix d’un script ou des URL de fichier basé sur un script.
* `timestamp` : (facultatif, entier 32 bits) utilisez ce champ uniquement pour déclencher la réexécution du script en modifiant la valeur de ce champ.  Toutes les valeurs sont autorisées pour l’entier. Cette valeur doit uniquement être différente de la valeur précédente.
 * `commandToExecute` : (**obligatoire** si aucun script défini, chaîne) script de point d’entrée à exécuter. Utilisez plutôt ce champ si votre commande contient des secrets tels que des mots de passe.
* `script` : (**obligatoire** si la propriété commandToExecute n’est pas définie, chaîne) script codé en Base64 (et éventuellement compressé au format GZip) exécuté par /bin/sh.
* `fileUris` : (facultatif, tableau de chaînes) URL des fichiers à télécharger.
* `storageAccountName` : (facultatif, chaîne) nom du compte de stockage. Si vous spécifiez des informations d’identification de stockage, toutes les propriétés `fileUris` doivent être des URL d’objets blob Azure.
* `storageAccountKey` : (facultatif, chaîne) clé d’accès du compte de stockage.


Les valeurs suivantes peuvent être définies dans les paramètres publics ou protégés. L’extension rejette les configurations dans lesquelles les valeurs indiquées ci-dessous sont définies à la fois dans les paramètres publics et protégés.
* `commandToExecute`
* `script`
* `fileUris`

L’utilisation des paramètres publics peut être utile pour le débogage, mais il est vivement recommandé d’utiliser les paramètres protégés.

Les paramètres publics sont envoyés en texte clair à la machine virtuelle sur laquelle le script est exécuté.  Les paramètres protégés sont chiffrés à l’aide d’une clé connue uniquement d’Azure et de la machine virtuelle. Les paramètres sont enregistrés sur la machine virtuelle tels qu’ils ont été envoyés. Autrement dit, si les paramètres ont été chiffrés, ils sont enregistrés chiffrés sur la machine virtuelle. Le certificat utilisé pour déchiffrer les valeurs chiffrées est stocké sur la machine virtuelle et permet de déchiffrer les paramètres (si nécessaire) lors de l’exécution.

#### <a name="property-skipdos2unix"></a>Propriété : skipDos2Unix

La valeur par défaut est false, ce qui signifie que la conversion dos2unix **est** exécutée.

La version précédente de CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, convertit automatiquement les fichiers DOS en fichiers UNIX en convertissant `\r\n` en `\n`. Cette conversion existe toujours et est activée par défaut. Elle est appliquée à tous les fichiers téléchargés à partir de fileUris ou du paramètre de script en fonction des critères suivants.

* Si l’extension est `.sh`, `.txt`, `.py` ou `.pl`, elle est convertie. Le paramètre de script correspond toujours à ce critère, car il est censé être un script exécuté avec /bin/sh et est enregistré sous le nom script.sh sur la machine virtuelle.
* Si le fichier commence par `#!`.

La conversion dos2unix peut être ignorée en définissant la propriété skipDos2Unix sur true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriété : script

CustomScript prend en charge l’exécution d’un script défini par l’utilisateur. Les paramètres du script permettent de combiner les propriétés commandToExecute et fileUris en un seul paramètre. Au lieu de devoir configurer un fichier à télécharger à partir du stockage Azure ou de GitHub, vous pouvez simplement encoder le script en tant que paramètre. Le script peut être utilisé pour remplacer les propriétés commandToExecute et fileUris.

Le script **doit** être encodé en Base64.  Le script peut **éventuellement** être compressé au format GZip. Le paramètre de script peut être utilisé dans les paramètres publics ou protégés. La taille maximale des données du paramètre de script est de 256 Ko. Si le script dépasse cette taille, il n’est pas exécuté.

Prenez l’exemple du script suivant enregistré dans le fichier /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Le paramètre de script CustomScript correct est construit en prenant la sortie de la commande suivante.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Le script peut éventuellement être compressé au format GZip pour réduire davantage la taille (dans la plupart des cas). (CustomScript détecte automatiquement l’utilisation de la compression au format GZip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript utilise l’algorithme suivant pour exécuter un script.

 1. Affirmez que la longueur de la valeur du script ne dépasse pas 256 Ko.
 1. Décodez la valeur du script en Base64.
 1. _Essayez_ de décompresser la valeur décodée en Base64.
 1. Écrivez la valeur décodée (et éventuellement décompressée) sur le disque (/var/lib/waagent/custom-script/#/script.sh).
 1. Exécutez le script à l’aide de _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Déploiement de modèle
Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension de script personnalisé pendant un déploiement de modèle Azure Resource Manager. Un exemple de modèle qui inclut l’extension de script personnalisé est disponible ici, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Ces noms de propriétés respectent la casse. Pour éviter des problèmes de déploiement, utilisez les noms présentés ici.

## <a name="azure-cli"></a>Azure CLI
Si vous utilisez Azure CLI pour exécuter l’extension de script personnalisé, créez un fichier de configuration ou des fichiers. Vous devez indiquer au moins la propriété « commandToExecute ».

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Vous pouvez éventuellement spécifier les paramètres dans la commande sous forme de chaînes au format JSON. Cela permet de spécifier la configuration lors de l’exécution et sans fichier de configuration distinct.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemples d’interface de ligne de commande Azure

#### <a name="public-configuration-with-script-file"></a>Configuration publique avec fichier de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Configuration publique sans fichier de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Fichiers de configuration publique et protégée

Un fichier de configuration publique permet de spécifier l’URI du fichier de script. Un fichier de configuration protégée permet de spécifier la commande à exécuter.

Fichier de configuration publique :

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Fichier de configuration protégée :  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Commande d’interface de ligne de commande Azure :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Résolution de problèmes
Lors de l’exécution de l’extension de script personnalisé, le script est créé ou téléchargé dans un répertoire semblable à l’exemple suivant. La sortie de la commande y est également enregistrée, dans les fichiers `stdout` et `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Pour résoudre les problèmes, recherchez d’abord le journal de l’agent Linux, assurez-vous de l’exécution de l’extension, puis vérifiez :

```bash
/var/log/waagent.log 
```

Vous devez rechercher l’exécution de l’extension, qui doit se présenter ainsi :
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Points à noter :
1. La commande Enable correspond au début de l’exécution de la commande.
2. La commande Download se rapporte au téléchargement du package d’extension CustomScript à partir d’Azure, et non aux fichiers de script spécifiés dans fileUris.


L’extension de script Azure génère un journal qui se trouve à cet emplacement :

```bash
/var/log/azure/custom-script/handler.log
```

Vous devez rechercher l’exécution individuelle, qui doit se présenter ainsi :
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
Là, vous pouvez voir :
* La commande Enable démarrant dans ce journal
* Les paramètres transmis à l’extension
* L’extension téléchargeant des fichiers et le résultat de cette opération
* La commande en cours d’exécution et le résultat

Il est également possible de récupérer l’état d’exécution de l’extension de script personnalisé avec Azure CLI :

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

La sortie ressemble au texte suivant :

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Étapes suivantes
Pour afficher le code, les problèmes et les versions actuels, consultez le [référentiel custom-script-extension-linux](https://github.com/Azure/custom-script-extension-linux).

