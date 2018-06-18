---
title: Modifications apportées à un projet WebAPI quand vous vous connectez à Azure AD
description: Décrit ce qui se passe dans votre projet WebApi quand vous vous connectez à Azure AD en utilisant Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: b8303ccf6fc96e8d0708c9e414fdf511e0cf2fdf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31785158"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Qu’est-il arrivé à mon projet WebApi (service connecté Azure Active Directory de Visual Studio)

> [!div class="op_single_selector"]
> - [Prise en main](vs-active-directory-webapi-getting-started.md)
> - [Que s'est-il passé ?](vs-active-directory-webapi-what-happened.md)

Cet article identifie les modifications exactes apportées aux projets ASP.NET Azure API, Application monopage ASP.NET et ASP.NET Azure API lors de l’ajout du [service connecté Azure Active Directory à l’aide de Visual Studio](vs-active-directory-add-connected-service.md). S’applique également aux projets ASP.NET Azure Mobile Service dans Visual Studio 2015.

Pour plus d’informations sur l’utilisation du service connecté, consultez [Mise en route](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Références ajoutées

Affecte les références *.NET du fichier projet et `packages.config` (références NuGet).

| type | Informations de référence |
| --- | --- |
| .NET ; NuGet | Microsoft.Owin |
| .NET ; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET ; NuGet | Microsoft.Owin.Security |
| .NET ; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET ; NuGet | Microsoft.Owin.Security.Jwt |
| .NET ; NuGet | Microsoft.Owin.Security.OAuth |
| .NET ; NuGet | Owin |
| .NET ; NuGet | System.IdentityModel.Tokens.Jwt |

Références supplémentaires si l’option **Lire les données d’annuaire** est sélectionnée :

| type | Informations de référence |
| --- | --- |
| .NET ; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 uniquement) |
| .NET ; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET ; NuGet | Microsoft.Data.Edm |
| .NET ; NuGet | Microsoft.Data.OData |
| .NET ; NuGet | Microsoft.Data.Services.Client |
| .NET ; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 uniquement) |
| .NET ; NuGet | System.Spatial |

Les références suivantes sont supprimées (projets ASP.NET 4 uniquement, comme dans Visual Studio 2015) :

| type | Informations de référence |
| --- | --- |
| .NET ; NuGet | Microsoft.AspNet.Identity.Core |
| .NET ; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET ; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Modifications apportées au fichier projet

- Définissez la propriété `IISExpressSSLPort` sur un nombre distinct.
- Définissez la propriété `WebProject_DirectoryAccessLevelKey` sur 0, ou 1 si l’option **Lire les données d’annuaire** est sélectionnée.
- Définissez la propriété `IISUrl` sur `https://localhost:<port>/`, où `<port>` correspond à la valeur `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>Modifications apportées à web.config ou à app.config

- Vous avez ajouté les entrées de configuration suivantes :

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 uniquement : vous avez également ajouté l’entrée suivante sous `<appSettings>` »

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Vous avez ajouté les éléments `<dependentAssembly>` sous le nœud `<runtime><assemblyBinding>` pour `System.IdentityModel.Tokens.Jwt`.

- Si vous avez sélectionné l’option **Lire les données de l’annuaire**, vous avez ajouté l’entrée de configuration suivante sous `<appSettings>` :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Ajouts et modifications de code

- Vous avez ajouté l’attribut `[Authorize]` à `Controllers/ValueController.cs` et tous les autres contrôleurs existants.

- Vous avez ajouté une classe de démarrage de l’authentification, `App_Start/Startup.Auth.cs`, qui contient la logique de démarrage pour l’authentification Azure AD ou modifié en conséquence. Si vous avez sélectionné l’option **Lire les données de l’annuaire**, ce fichier contient également le code pour recevoir un code OAuth et l’échanger contre un jeton d’accès.

- (Visual Studio 2015 avec l’application ASP.NET 4 uniquement) Vous avez supprimé `App_Start/IdentityConfig.cs` et ajouté `Controllers/AccountController.cs`, `Models/IdentityModel.cs` et `Providers/ApplicationAuthProvider.cs`.

- Vous avez ajouté `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contenant des informations que Visual Studio utilise pour effectuer le suivi de l’ajout d’un service connecté.

### <a name="file-backup-visual-studio-2015"></a>Sauvegarde de fichiers (Visual Studio 2015)

Lors de l’ajout du service connecté, Visual Studio 2015 sauvegarde les fichiers modifiés et supprimés. Tous les fichiers affectés sont enregistrés dans le dossier `Backup/AzureAD`. Visual Studio 2017 ne crée pas de sauvegardes.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Modifications apportées à Azure

- Création d’une application Azure AD dans le domaine sélectionné lors de l’ajout du service connecté.
- Mise à jour de l’application pour inclure l’autorisation **Lire les données d’annuaire** si cette option est sélectionnée.

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Étapes suivantes

- [Scénarios d’authentification pour Azure Active Directory](active-directory-authentication-scenarios.md)
- [Ajouter la connexion avec Microsoft à une application web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)