---
title: Créer des stratégies Guest Configuration
description: Découvrez comment créer une stratégie Guest Configuration pour des machines virtuelles Windows ou Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ee8a17846495a122f7432e66c3e343a00dd0a015
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194624"
---
# <a name="how-to-create-guest-configuration-policies"></a>Créer des stratégies Guest Configuration

Guest Configuration utilise un module de ressources [Desired State Configuration](/powershell/dsc) (DSC) pour créer la configuration pour l’audit de machines Azure. La configuration DSC définit la condition dans laquelle la machine doit se trouver. Si l’évaluation de la configuration échoue, l’**auditIfNotExists** d’effet de stratégie est déclenché et la machine est considérée comme **non conforme**.

La [configuration d’invité Azure Policy](/azure/governance/policy/concepts/guest-configuration) peut être utilisée uniquement pour auditer les paramètres à l’intérieur des machines. La correction des paramètres à l’intérieur des machines n’est pas encore disponible.

Utilisez les actions suivantes pour créer votre propre configuration pour la validation de l’état d’une machine Azure.

> [!IMPORTANT]
> Les stratégies personnalisées avec Guest Configuration sont une fonctionnalité en préversion.

## <a name="add-the-guestconfiguration-resource-module"></a>Ajouter le module de ressource GuestConfiguration

Pour créer une stratégie Guest Configuration, le module de ressources doit être ajouté. Vous pouvez utiliser ce module de ressources avec PowerShell installé localement, avec [Azure Cloud Shell](https://shell.azure.com) ou avec l’[image Docker Azure PowerShell](https://hub.docker.com/rsdk-powershell/).

### <a name="base-requirements"></a>Configuration de base requise

Le module de ressource Guest Configuration nécessite les logiciels suivants :

- PowerShell. S’il n’est pas installé, suivez [ces instructions](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou ultérieur. S’il n’est pas installé, suivez [ces instructions](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Installer le module

Guest Configuration utilise le module de ressource **GuestConfiguration** pour la création de configurations DSC et leur publication dans Azure Policy :

1. À partir d’une invite de commandes PowerShell, exécutez la commande suivante :

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Vérifiez que le module a été importé :

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration"></a>Créer une configuration Guest Configuration personnalisée

La première étape de création d’une stratégie personnalisée pour Guest Configuration consiste à créer la configuration DSC. Pour obtenir une vue d’ensemble des concepts et de la terminologie DSC, consultez [Vue d’ensemble PowerShell DSC](/powershell/dsc/overview/overview).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuration Guest Configuration personnalisée sur Linux

La configuration DSC pour Guest Configuration sur Linux utilise la ressource `ChefInSpecResource` pour fournir au moteur le nom de la définition [Chef InSpec](https://www.chef.io/inspec/). **Name** est la seule propriété de ressource obligatoire.

L’exemple suivant crée une configuration nommée **baseline**, importe le module de ressources **GuestConfiguration** et utilise la ressource `ChefInSpecResource` pour définir le nom de la définition InSpec sur **linux-patch-baseline** :

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Pour plus d’informations, consultez [Écrire, compiler et appliquer une configuration](/powershell/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Configuration Guest Configuration personnalisée sur Windows

La configuration DSC d’une configuration Guest Configuration d’Azure Policy est utilisée par l’agent Guest Configuration uniquement. Elle n’est pas en conflit avec Windows PowerShell Desired State Configuration.

L’exemple suivant crée une configuration nommée **AuditBitLocker**, importe le module de ressources **GuestConfiguration** et utilise la ressource `Service` pour faire l’audit d’un service en cours d’exécution :

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Pour plus d’informations, consultez [Écrire, compiler et appliquer une configuration](/powershell/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Créer un package de stratégies personnalisées Guest Configuration

Une fois la compilation du fichier MOF terminée, les fichiers de prise en charge doivent être regroupés en un package. Le package obtenu est utilisé par Guest Configuration pour créer les définitions d’Azure Policy. Le package comprend les éléments suivants :

- La configuration DSC compilée au format MOF
- Dossier Modules
  - Module GuestConfiguration
  - Module DscNativeResources
  - (Linux) Un dossier avec la définition Chef InSpec et du contenu supplémentaire
  - (Windows) Modules de ressources DSC non intégrés

La cmdlet `New-GuestConfigurationPackage` crée le package. Le format suivant est utilisé pour créer un package personnalisé :

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Paramètres de la cmdlet `New-GuestConfigurationPackage` :

- **Nom** : Nom du package Guest Configuration.
- **Configuration** : Chemin d’accès complet au document de configuration DSC compilé.
- **Chemin d’accès** : Chemin d’accès au dossier de sortie. Ce paramètre est facultatif. S’il n’est pas spécifié, le package est créé dans le répertoire actif.
- **ChefProfilePath** : Chemin d’accès complet au profil InSpec. Ce paramètre est pris en charge uniquement lors de la création de contenu pour auditer Linux.

Le package terminé doit être stocké à un emplacement accessible par les machines virtuelles gérées. Exemples : référentiels GitHub, référentiel Azure ou stockage Azure. Si vous préférez ne pas rendre le package public, vous pouvez inclure un [jeton SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) dans l’URL. Vous pouvez également implémenter le [point de terminaison de service](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pour les machines dans un réseau privé, bien que cette configuration s’applique uniquement à l’accès au package et ne communique pas avec le service.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Utilisation des secrets dans les packages Guest Configuration

Dans la configuration Guest Configuration d’Azure Policy, le meilleur moyen de gérer les secrets utilisés lors de l’exécution est de les stocker dans Azure Key Vault. Cette conception est implémentée au sein des ressources DSC personnalisées.

Créez d’abord une identité managée affectée par l’utilisateur dans Azure. L’identité est utilisée par les machines pour accéder aux secrets stockés dans Key Vault. Pour plus d’informations, consultez [Créer, répertorier ou supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

Créez ensuite une instance Key Vault. Pour plus d’informations, consultez [Définir et récupérer un secret : PowerShell](../../../key-vault/quick-create-powershell.md).
Affectez des autorisations à l’instance pour donner à l’identité affectée par l’utilisateur l’accès aux secrets stockés dans Key Vault. Pour plus d’informations, consultez [Définir et récupérer un secret : .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

Attribuez ensuite l’identité affectée par l’utilisateur à votre machine. Pour plus d’informations, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
À l’échelle, attribuez cette identité à l’aide d’Azure Resource Manager via Azure Policy. Pour plus d’informations, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un modèle](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

Enfin, dans votre ressource personnalisée, utilisez l’ID client généré ci-dessus pour accéder à Key Vault à l’aide du jeton disponible à partir de l’ordinateur. Le `client_id` et l’URL de l’instance Key Vault peuvent être passés à la ressource en tant que [propriétés](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) pour que la ressource n’ait pas à être mise à jour pour plusieurs environnement, ou si les valeurs doivent être modifiées.

L’exemple de code suivant peut être utilisé dans une ressource personnalisée pour récupérer des secrets à partir de Key Vault à l’aide d’une identité affectée par l’utilisateur. La valeur retournée de la requête à Key Vault est en texte brut. Il est recommandé de la stocker dans un objet d’informations d’identification.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Tester un package Guest Configuration

Après avoir créé le package de configuration, et avant de le publier sur Azure, vous pouvez tester la fonctionnalité du package à partir de votre station de travail ou de votre environnement d’intégration CI/CD. Le module GuestConfiguration comprend une applet de commande `Test-GuestConfigurationPackage` qui charge le même agent dans votre environnement de développement que celui utilisé dans les machines Azure. Via cette solution, vous pouvez effectuer un test d’intégration en local avant la publication dans des environnements de production/AQ/test facturés.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Paramètres de la cmdlet `Test-GuestConfigurationPackage` :

- **Nom** : Nom de la stratégie Guest Configuration.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.
- **Chemin d’accès** : Chemin d’accès complet du package Guest Configuration.

La cmdlet prend aussi en charge l’entrée depuis le pipeline PowerShell. Dirige la sortie de la cmdlet `New-GuestConfigurationPackage` vers la cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Pour savoir comment tester avec des paramètres, consultez la section ci-dessous [Utilisation des paramètres dans des stratégies personnalisées Guest Configuration](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Créer la définition Azure Policy et les fichiers de déploiement d’initiative

Une fois qu’un package de stratégie personnalisée Guest Configuration a été créé et chargé dans un emplacement accessible par les machines, créez la définition de la stratégie Guest Configuration pour Azure Policy. La cmdlet `New-GuestConfigurationPolicy` prend un package de stratégie personnalisée Guest Configuration accessible publiquement et crée une définition de stratégie **auditIfNotExists** et **deployIfNotExists**. Une définition d’initiative de stratégie qui comprend les deux définitions de stratégie est également créée.

L’exemple suivant crée les définitions de stratégie et d’initiative dans un chemin d’accès spécifié à partir d’un package de stratégie personnalisée Guest Configuration pour Windows et fournit un nom, une description et une version :

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Paramètres de la cmdlet `New-GuestConfigurationPolicy` :

- **ContentUri** : URI http(s) publique du package de contenu Guest Configuration.
- **DisplayName** : Nom d'affichage de la stratégie.
- **Description** : Description de la stratégie.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.
- **Version** : Version de stratégie.
- **Chemin d’accès** : Chemin de destination où les définitions de stratégie sont créées.
- **Plateforme** : Plateforme cible (Windows/Linux) pour la stratégie et le package de contenu Guest Configuration.

Les fichiers suivants sont créés par `New-GuestConfigurationPolicy` :

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

La sortie de la cmdlet retourne un objet contenant le nom complet de l’initiative et le chemin d’accès aux fichiers de stratégie.

Si vous souhaitez utiliser cette commande pour structurer un projet de stratégie personnalisée, vous pouvez effectuer les modifications à ces fichiers. Par exemple, vous pouvez modifier la section « if » pour évaluer si une balise spécifique est présente pour les machines. Pour plus d’informations sur la création de stratégies, consultez [Créer des stratégies par programmation](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilisation de paramètres dans des stratégies personnalisées Guest Configuration

Guest Configuration prend en charge la substitution des propriétés d’une configuration lors d’une exécution. Cette fonctionnalité signifie que les valeurs du fichier MOF dans le package n’ont pas à être considérées comme statiques. Les valeurs de substitution sont fournies via Azure Policy et n’impactent pas la création ni la compilation des configurations.

Les cmdlets `New-GuestConfigurationPolicy` et `Test-GuestConfigurationPolicyPackage` incluent un paramètre nommé **Paramètres**.
Ce paramètre prend une définition au format Hashtable qui comprend toutes les informations concernant chaque paramètre et crée automatiquement toutes les sections nécessaires des fichiers utilisés pour créer chaque définition Azure Policy.

L’exemple suivant crée une stratégie Azure Policy pour auditer un service, où l’utilisateur sélectionne depuis une liste des services au moment de l’attribution de la stratégie.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Pour les stratégies Linux, incluez la propriété `AttributesYmlContent` dans votre configuration et remplacez les valeurs en conséquence. L’agent Guest Configuration crée automatiquement le fichier YaML utilisé par InSpec pour stocker les attributs. Reportez-vous à l’exemple ci-dessous.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Pour chaque paramètre supplémentaire, ajoutez une table de hachage au tableau. Dans les fichiers de stratégie, vous verrez des propriétés ajoutées au nom de la configuration Guest Configuration qui identifient le type de ressource, le nom, la propriété et la valeur.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publier dans Azure Policy

Le module de ressources **GuestConfiguration** offre un moyen de créer des définitions de stratégie et la définition d’initiative dans Azure en une étape via la cmdlet `Publish-GuestConfigurationPolicy`.
La cmdlet ne dispose que du paramètre **Path** qui pointe vers l’emplacement des trois fichiers JSON créés par `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

La cmdlet `Publish-GuestConfigurationPolicy` accepte le chemin à partir du pipeline PowerShell. Cette fonctionnalité signifie que vous pouvez créer les fichiers de stratégie et les publier dans un ensemble unique de commandes redirigées.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Avec les définitions de stratégie et d’initiative créées dans Azure, la dernière étape consiste à assigner l’initiative. Découvrez comment assigner l’initiative avec le [portail](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) et [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Les stratégies Guest Configuration doivent **toujours** être assignées via l’initiative qui combine les stratégies _AuditIfNotExists_ et _DeployIfNotExists_. Si seule la stratégie _AuditIfNotExists_ est assignée, les prérequis ne sont pas déployés et la stratégie montre toujours que « 0 » serveur est conforme.

## <a name="policy-lifecycle"></a>Cycle de vie de la stratégie

Une fois que vous avez publié une stratégie Azure Policy personnalisée à l’aide de votre package de contenu personnalisé, vous devez mettre à jour deux champs si vous souhaitez publier une nouvelle version.

- **Version** : Lorsque vous exécutez l’applet de commande `New-GuestConfigurationPolicy`, vous devez spécifier un numéro de version supérieur à celui actuellement publié.  Cette propriété met à jour la version de l’attribution Guest Configuration dans le nouveau fichier de stratégie pour que l’extension reconnaisse que le package a été mis à jour.
- **contentHash** : Cette propriété est automatiquement mise à jour par l’applet de commande `New-GuestConfigurationPolicy`.  Il s’agit d’une valeur de hachage du package créé par `New-GuestConfigurationPackage`.  Cette propriété doit être correcte pour le fichier `.zip` que vous publiez.  Si seule la propriété `contentUri` est mise à jour, par exemple si une personne peut apporter une modification manuelle à la définition de stratégie à partir du portail, l’extension n’acceptera pas le package de contenu.

Le moyen le plus simple de publier un package mis à jour consiste à répéter le processus décrit dans cet article et à fournir un numéro de version mis à jour.
Ce processus garantit que toutes les propriétés ont été correctement mises à jour.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Conversion de contenu de stratégie de groupe Windows en configuration d’invité Azure Policy

La configuration d’invité, lors de l’audit de machines Windows, est une implémentation de la syntaxe de Desired State Configuration PowerShell.
La communauté DSC a publié des outils pour convertir les modèles de stratégie de groupe exportés au format DSC.
En utilisant cet outil avec les cmdlets de configuration d’invité décrites ci-dessus, vous pouvez convertir du contenu de stratégie de groupe Windows et le compresser/publier pour Azure Policy à des fins d’audit.
Pour plus d’informations sur l’utilisation de l’outil, consultez l’article [Démarrage rapide : Convertir une stratégie de groupe en DSC](/powershell/dsc/quickstarts/gpo-quickstart).
Une fois que le contenu a été converti, les étapes ci-dessus pour créer un package et le publier en tant qu’Azure Policy sont les mêmes que pour tout contenu DSC.

## <a name="optional-signing-guest-configuration-packages"></a>FACULTATIF : Signature des packages Guest Configuration

Les stratégies personnalisées Guest Configuration utilisent par défaut le hachage SHA256 pour valider que le package de stratégie n’a pas été modifié entre sa publication et sa lecture par le secteur qui est l’objet d’un audit.
Si vous le souhaitez, les clients peuvent également utiliser un certificat pour signer des packages et forcer l’extension Guest Configuration à autoriser uniquement le contenu signé.

Pour activer ce scénario, vous devez effectuer deux étapes. Exécutez l’applet de commande pour signer le package de contenu et ajoutez une balise aux machines qui doivent nécessiter du code pour être signées.

Pour utiliser la fonctionnalité de validation de signature, exécutez la cmdlet `Protect-GuestConfigurationPackage` pour signer le package avant sa publication. Cette cmdlet nécessite un certificat de « signature du code ».

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Paramètres de la cmdlet `Protect-GuestConfigurationPackage` :

- **Chemin d’accès** : Chemin d’accès complet du package Guest Configuration.
- **Certificat** : Certificat de signature de code pour signer le package. Ce paramètre est uniquement pris en charge lors de la signature de contenu pour Windows.
- **PrivateGpgKeyPath** : Chemin d'accès à la clé GPG privée. Ce paramètre est uniquement pris en charge lors de la signature de contenu pour Linux.
- **PublicGpgKeyPath** : Chemin d'accès à la clé GPG publique. Ce paramètre est uniquement pris en charge lors de la signature de contenu pour Linux.

L’agent GuestConfiguration s’attend à trouver la clé publique du certificat dans « Autorités de certification racines de confiance » sur des machines Windows et dans le chemin `/usr/local/share/ca-certificates/extra` sur des machines Linux. Pour que le nœud vérifie le contenu signé, installez la clé publique du certificat sur la machine avant d’appliquer la stratégie personnalisée. Ce processus peut être effectué à l’aide de n’importe quelle technique à l’intérieur de la machine virtuelle, ou à l’aide d’Azure Policy. Vous trouverez un exemple modèle en suivant [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
La stratégie d’accès Key Vault doit autoriser le fournisseur de ressources de calcul à accéder aux certificats lors des déploiements. Pour les étapes détaillées, consultez [Configurer Key Vault pour des machines virtuelles dans Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Voici un exemple d’exportation de la clé publique à partir d’un certificat de signature, à importer vers la machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Vous trouverez une bonne référence de création de clés GPG à utiliser avec les machines Linux dans cet article sur GitHub, [Génération d’une clé GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Une fois votre contenu publié, ajoutez une balise nommée `GuestConfigPolicyCertificateValidation` et avec une valeur `enabled` à toutes les machines virtuelles où la signature du code doit être requise. Cette balise peut être fournie à l’échelle à l’aide d’Azure Policy. Consultez l’exemple [Appliquer la balise et sa valeur par défaut](../samples/apply-tag-default-value.md).
Une fois cette balise en place, la définition de stratégie générée via la cmdlet `New-GuestConfigurationPolicy` met en œuvre l’exigence via l’extension Guest Configuration.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>[PRÉVERSION] Résolution des problèmes liés aux attributions de stratégie Guest Configuration

Un outil est disponible en préversion pour favoriser la résolution des problèmes liés aux attributions de configuration d’invité Azure Policy.
L’outil est en préversion et a été publié sur PowerShell Gallery avec le nom de module [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Pour plus d’informations sur les applets de commande de cet outil, utilisez la commande Get-Help dans PowerShell pour afficher les conseils intégrés.  Comme l’outil fait l’objet de mises à jour fréquentes, c’est la meilleure façon d’obtenir les informations les plus récentes.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’audit des machines virtuelles avec [Guest Configuration](../concepts/guest-configuration.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](getting-compliance-data.md).
