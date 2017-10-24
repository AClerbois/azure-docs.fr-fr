---
title: "Authentification de service à service : Kit de développement logiciel (SDK) .NET avec Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment procéder à une authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory au moyen du Kit de développement logiciel (SDK) .NET."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: 72e9e2e10992d928dcfd85538497ba12c6e1c6f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Authentification de service à service auprès de Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Dans cet article, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) .NET pour effectuer une authentification de service à service auprès d’Azure Data Lake Store. Pour plus d’informations sur l’authentification des utilisateurs finals auprès d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET, consultez l’article [End-user authentication with Data Lake Store using .NET SDK (Authentification des utilisateurs finals auprès de Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET)](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Composants requis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous reposent sur Visual Studio 2017.

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « web » Azure Active Directory**. Vous devez avoir suivi la procédure [Authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Créer une application .NET
1. Ouvrez Visual Studio et créez une application console.
2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | --- | --- |
   | Catégorie |Modèles/Visual C#/Windows |
   | Modèle |Application console |
   | Nom |CreateADLApplication |
4. Cliquez sur **OK** pour créer le projet.

5. Ajoutez les packages NuGet à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.Management.DataLake.Store` - Ce didacticiel utilise v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Ce didacticiel utilise v2.2.12.

        ![Ajouter une source NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Créer un compte Azure Data Lake")
   4. Fermez le **Gestionnaire de package NuGet**.

6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;
        
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="service-to-service-authentication-with-client-secret"></a>Authentification de service à service avec une clé secrète client
Ajoutez cet extrait de code dans votre application cliente .NET. Remplacez les valeurs d’espace réservé par les valeurs récupérées à partir d’une application web Azure AD (répertoriée comme condition préalable).  Cet extrait de code vous permet d’authentifier votre application **de façon non interactive** auprès de Data Lake Store à l’aide de la clé secrète client/clé de l’application web Azure AD. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientSecret = "<AAD-application-client-secret>";
        var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
        var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;
    }

## <a name="service-to-service-authentication-with-certificate"></a>Authentification de service à service avec un certificat

Ajoutez cet extrait de code dans votre application cliente .NET. Remplacez les valeurs d’espace réservé par les valeurs récupérées à partir d’une application web Azure AD (répertoriée comme condition préalable). Cet extrait de code vous permet d’authentifier votre application **de façon non interactive** auprès de Data Lake Store à l’aide du certificat d’une application web Azure AD. Pour plus d’informations sur la création d’une application Azure AD, consultez [Créer un principal du service avec un certificat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientCert = <AAD-application-client-certificate>
        var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
        var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;
    }


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser l’authentification de service à service auprès d’Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser le Kit de développement logiciel (SDK) .NET pour travailler avec Azure Data Lake Store.

* [Opérations de gestion des comptes sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Opérations de données sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)


