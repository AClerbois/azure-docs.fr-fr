---
title: Sécuriser la connexion SQL Database à l'aide d'une identité managée - Azure App Service | Microsoft Docs
description: Découvrez comment sécuriser la connectivité de la base de données à l’aide d’une identité managée, et comment appliquer cette technique à d’autres services Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8a0b974e9b64d477e53c37757b4f2fa952befba2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061866"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Didacticiel : Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité managée

[App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques dans Azure. Il offre également une [identité managée](overview-managed-identity.md) pour votre application, qui constitue une solution clé en main permettant de sécuriser l’accès à [Azure SQL Database](/azure/sql-database/) et à d’autres services Azure. Les identités managées dans App Service sécurisent votre application en en éliminant les secrets, par exemple les informations d’identification dans les chaînes de connexion. Dans ce tutoriel, vous allez ajouter l’identité managée à l’exemple d’application web que vous avez créé dans l’un des tutoriels suivants : 

- [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutoriel : Créer une application ASP.NET Core et SQL Database dans Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Lorsque vous aurez terminé, votre exemple d’application se connectera à SQL Database en toute sécurité sans nécessiter aucun nom d’utilisateur ni mot de passe.

> [!NOTE]
> Les étapes décrites dans ce tutoriel prennent en charge les versions suivantes :
> 
> - .NET Framework 4.7.2 et ultérieur.
> - .NET Core 2.2 et ultérieur.
>

Contenu :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

> [!NOTE]
>L’authentification Azure AD est _différente_ de [l’authentification Windows intégrée](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) dans Active Directory (AD DS) local. AD DS et Azure AD utilisent des protocoles d’authentification totalement différents. Pour plus d’informations, consultez la [documentation sur Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Cet article se poursuit là où vous l'avez laissé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) ou [Tutoriel : Créer une application ASP.NET Core et SQL Database dans Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Si vous ne l’avez pas déjà fait, suivez d’abord l’un des deux tutoriels. Vous pouvez également adapter la procédure à votre propre application .NET avec SQL Database.

Pour déboguer votre application à l’aide de SQL Database en tant que back-end, assurez-vous que vous avez autorisé la connexion cliente à partir de votre machine. Si ce n’est pas le cas, ajoutez l’adresse IP cliente en suivant les étapes décrites dans [Gérer les règles de pare-feu IP au niveau du serveur avec le portail Azure](../sql-database/sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Accorder à l’utilisateur Azure AD un accès à la base de données

Tout d’abord, activez l’authentification Azure AD à SQL Database en attribuant un utilisateur Azure AD en tant qu’administrateur Active Directory du serveur SQL Database. Cet utilisateur est différent du compte Microsoft que vous avez utilisé pour vous inscrire à votre abonnement Azure. Il doit s’agir d’un utilisateur créé, importé, synchronisé ou invité à Azure AD. Pour plus d’informations sur les utilisateurs Azure AD autorisés, consultez [Fonctionnalités Azure AD et limitations dans SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Si votre locataire Azure AD n’a pas encore d’utilisateur, créez-en un en suivant les étapes de la section [Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Recherchez l’ID d’objet de l’utilisateur Azure AD en utilisant [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) et remplacez *\<user-principal-name>* par votre propre valeur. Le résultat est enregistré dans une variable.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Pour afficher la liste de tous les noms d’utilisateur dans Azure AD, exécutez `az ad user list --query [].userPrincipalName`.
>

Ajoutez cet utilisateur Azure AD en tant qu’administrateur Active Directory à l’aide de la commande [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) dans Cloud Shell. Dans la commande suivante, remplacez *\<server-name>* par le nom du serveur SQL Database (sans le suffixe `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Pour plus d’informations sur l’ajout d’un administrateur Active Directory, consultez [Provisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configuration de Visual Studio

Pour activer le développement et le débogage dans Visual Studio, ajoutez votre utilisateur Azure AD dans Visual Studio en sélectionnant **Fichier** > **Paramètres du compte** à partir du menu, puis cliquez sur **Ajouter un compte**.

Pour définir l’utilisateur Azure AD pour l’authentification de service Azure, sélectionnez **Outils** > **Options** dans le menu, puis sélectionnez **Authentification du service Azure** > **Sélection du compte**. Sélectionnez l’utilisateur Azure AD que vous avez ajouté, puis cliquez sur **OK**.

Vous êtes maintenant prêt à développer et déboguer votre application avec SQL Database en tant que back-end, à l’aide de l’authentification Azure AD.

## <a name="modify-your-project"></a>Modifier votre projet

Les étapes que vous suivez pour votre projet varient selon qu’il s’agit d’un projet ASP.NET ou d’un projet ASP.NET Core.

- [Modifier ASP.NET](#modify-aspnet)
- [Modifier ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modifier ASP.NET

Dans Visual Studio, ouvrez la Console du gestionnaire de package et ajoutez le package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

Dans *Web.config*, depuis le début du fichier, apportez les modifications suivantes :

- Dans `<configSections>`, ajoutez la déclaration de section suivante :

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Sous la balise `</configSections>` de fermeture, ajoutez le code XML suivant pour `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Recherchez la chaîne de connexion appelée `MyDbConnection` et remplacez sa valeur `connectionString` par `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Remplacez _\<server-name>_ et _\<db-name>_ par le nom de votre serveur et le nom de votre base de données.

C’est tout ce dont vous avez besoin pour vous connecter à SQL Database. Lors du débogage dans Visual Studio, votre code utilise l’utilisateur Azure AD que vous avez configuré dans [Configurer Visual Studio](#set-up-visual-studio). Vous configurerez le serveur SQL Database ultérieurement pour autoriser la connexion à partir de l’identité managée de votre application App Service.

Entrez `Ctrl+F5` pour réexécuter l’application. La même application CRUD dans votre navigateur se connecte maintenant à Azure SQL Database directement, à l’aide de l’authentification Azure AD. Cette configuration vous permet d’exécuter des migrations de base de données à partir de Visual Studio.

### <a name="modify-aspnet-core"></a>Modifier ASP.NET Core

Dans Visual Studio, ouvrez la Console du gestionnaire de package et ajoutez le package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

Dans le [tutoriel ASP.NET Core et SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md), la chaîne de connexion `MyDbConnection` n’est pas du tout utilisée, car l’environnement de développement local utilise un fichier de base de données SQLite, et l’environnement de production Azure, une chaîne de connexion depuis App Service. Avec l’authentification Active Directory, vous voulez que les deux environnements utilisent la même chaîne de connexion. Dans le fichier *appsettings. json*, remplacez la valeur de la chaîne de connexion `MyDbConnection` par :

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Dans *Startup.cs*, supprimez la section de code que vous avez ajoutée précédemment :

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Remplacez-la par le code suivant :

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Ensuite, vous fournissez le contexte de base de données Entity Framework avec le jeton d’accès pour l’instance SQL Database. Dans *Data\MyDatabaseContext.cs*, ajoutez le code suivant à l’intérieur des accolades du constructeur `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` vide :

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Par souci de clarté, ce code de démonstration est synchrone. Pour plus d’informations, consultez [Guide asynchrone pour les constructeurs](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

C’est tout ce dont vous avez besoin pour vous connecter à SQL Database. Lors du débogage dans Visual Studio, votre code utilise l’utilisateur Azure AD que vous avez configuré dans [Configurer Visual Studio](#set-up-visual-studio). Vous configurerez le serveur SQL Database ultérieurement pour autoriser la connexion à partir de l’identité managée de votre application App Service. La classe `AzureServiceTokenProvider` met en cache le jeton en mémoire et le récupère à partir d’Azure AD, juste avant l’expiration. Vous n’avez pas besoin de code personnalisé pour actualiser le jeton.

Entrez `Ctrl+F5` pour réexécuter l’application. La même application CRUD dans votre navigateur se connecte maintenant à Azure SQL Database directement, à l’aide de l’authentification Azure AD. Cette configuration vous permet d’exécuter des migrations de base de données à partir de Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Utiliser la connectivité de l’identité managée

Ensuite, configurez votre application App Service pour qu’elle se connecte à SQL Database avec une identité managée attribuée par le système.

### <a name="enable-managed-identity-on-app"></a>Activer une identité managée sur l’application

Pour activer une identité managée pour votre application Azure, utilisez la commande [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) dans Cloud Shell. Dans la commande ci-après, remplacez *\<app-name>* par votre propre valeur.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Voici un exemple de sortie :

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Ajouter l’identité managée à un groupe Azure AD

Pour accorder cet accès d’identité à votre base de données SQL Database, vous devez l’ajouter à un [groupe Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Dans Cloud Shell, ajoutez l’identité managée dans un nouveau groupe nommé _myAzureSQLDBAccessGroup_, comme dans le script suivant :

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Si vous souhaitez visualiser la sortie JSON complète pour chaque commande, supprimez les paramètres `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Accorder des autorisations à un groupe Azure AD

Dans le Cloud Shell, connectez-vous à SQL Database en utilisant la commande SQLCMD. Remplacez _\<server-name>_ par le nom de votre serveur SQL Database, puis _\<dbname>_ par le nom de base de données utilisé par votre application, puis _\<aad-user-name>_ et _\<aad-password>_ par les informations d’identification de votre utilisateur Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

À l’invite SQL de la base de données souhaitée, exécutez les commandes suivantes pour ajouter le groupe Azure AD et accorder les autorisations dont votre application a besoin. Par exemple, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Tapez `EXIT` pour revenir à l’invite Cloud Shell.

### <a name="modify-connection-string"></a>Modifier la chaîne de connexion

Rappelez-vous que les modifications apportées dans *Web.config* ou *appsettings.json* fonctionnent avec l’identité managée. Par conséquent, la seule chose à faire consiste à supprimer la chaîne de connexion existante d’App Service, que Visual Studio a créée lors du premier déploiement de votre application. Utilisez la commande suivante en remplaçant *\<app-name>* par le nom de votre application.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publier vos modifications

Il ne vous reste plus qu’à publier vos modifications sur Azure.

**Si vous êtes parti du [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , publiez vos modifications dans Visual Studio. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Dans la page de publication, cliquez sur **Publier**. 

**Si vous êtes parti du [Tutoriel : Créer une application ASP.NET Core et SQL Database dans Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)** , publiez vos modifications avec Git, au moyen des commandes suivantes :

```bash
git commit -am "configure managed identity"
git push azure master
```

Lorsque la nouvelle page web affiche votre liste des tâches, votre application se connecte à la base de données à l’aide de l’identité managée.

![Application Azure après l’activation des Migrations Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Vous devriez désormais être en mesure de modifier la liste des tâches comme auparavant.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md)
