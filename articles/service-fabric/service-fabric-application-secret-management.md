---
title: Gérer des secrets d’applications Azure Service Fabric | Microsoft Docs
description: Découvrez comment sécuriser des valeurs secrètes dans une application Service Fabric (non dépendante de la plateforme).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: a0003ee02c09ad8c99d6fa94935f96527c146e7d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063809"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gérer les secrets chiffrés dans les applications Service Fabric
Ce guide vous guide à travers les étapes de la gestion des secrets dans une application Service Fabric. Les secrets peuvent être des informations sensibles quelconques, notamment des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs qui ne doivent pas être traitées en texte brut.

L’utilisation de secrets chiffrés dans une application Service Fabric implique trois étapes :
* Configurer un certificat de chiffrement et chiffrer les secrets
* Spécifier les secrets chiffrés dans une application
* Déchiffrer des secrets chiffrés à partir du code de service

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurer un certificat de chiffrement et chiffrer les secrets
La configuration d’un certificat de chiffrement et son utilisation en vue de chiffrer des secrets varient selon que vous utilisez Windows ou Linux.
* [Configurez un certificat de chiffrement et chiffrer des secrets sur clusters Windows.][secret-management-windows-specific-link]
* [Configurez un certificat de chiffrement et chiffrer des secrets sur des clusters Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Spécifier les secrets chiffrés dans une application
L’étape précédente explique comment chiffrer un secret avec un certificat et produire une chaîne codée en base 64 à utiliser dans une application. Cette chaîne codée en base 64 peut être spécifiée comme un [paramètre][parameters-link] chiffré dans le fichier Settings.xml d’un service, ou comme une [variable d’environnement][environment-variables-link] chiffrée dans le fichier ServiceManifest.xml d’un service.

Spécifiez un [paramètre][parameters-link] chiffré dans le fichier de configuration Settings.xml de votre service avec l’attribut `IsEncrypted` défini sur la valeur `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Spécifiez une [variable d’environnement][environment-variables-link] chiffrée dans le fichier ServiceManifest.xml de votre service avec l’attribut `Type` défini sur la valeur `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Injection de secrets d’application dans des instances d’application
Idéalement, un déploiement dans différents environnements doit être aussi automatisé que possible. Cela est possible en effectuant un chiffrement de secret dans un environnement de construction et en fournissant les secrets chiffrés en tant que paramètres lors de la création d’instances d’application.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilisation de paramètres substituables dans Settings.xml
Le fichier de configuration Settings.xml autorise des paramètres substituables qui peuvent être fournis au moment de la création d’une application. Utilisez l’attribut `MustOverride` au lieu de fournir une valeur pour un paramètre :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Pour remplacer des valeurs dans le fichier Settings.xml, déclarez un paramètre de remplacement pour le service dans le fichier ApplicationManifest.xml :

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

À présent, la valeur peut être spécifiée en tant que *paramètre d’application* lors de la création d’une instance de l’application. La création d’une instance d’application peut être scriptée à l’aide de PowerShell ou écrite en C# pour faciliter l’intégration dans un processus de génération.

Avec PowerShell, le paramètre est fourni à la commande `New-ServiceFabricApplication` en tant que [table de hachage](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Avec C#, les paramètres de l’application sont spécifiés dans un `ApplicationDescription` en tant que `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Déchiffrer des secrets chiffrés à partir du code de service
Les API permettant d’accéder aux [paramètres][parameters-link] et aux [variables d’environnement][environment-variables-link] permettent de déchiffrer facilement les valeurs chiffrées. Étant donné que la chaîne chiffrée contient des informations sur le certificat utilisé pour le chiffrement, il est inutile de spécifier manuellement le certificat. Le certificat doit uniquement être installé sur le nœud sur lequel le service est exécuté.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [la sécurité des applications et des services](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
