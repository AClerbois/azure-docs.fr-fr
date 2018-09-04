---
title: Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store
description: Un didacticiel qui vous montre comment utiliser la Managed Service Identity pour une machine virtuelle Linux pour accéder au Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 4a9d147d1605f4efa638ff258df2667b6b95230e
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885156"
---
# <a name="tutorial-use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Didacticiel : Utiliser une identité Managed Service Identity de machine virtuelle Linux pour accéder à Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité affectée par le système pour une machine virtuelle Linux afin d’accéder à Azure Data Lake Store. Azure gère automatiquement les identités créées via la Managed Service Identity. Vous pouvez utiliser la Managed Service Identity pour vous identifier auprès des services qui prennent en charge l’authentification d’Azure Active Directory (Azure AD), sans avoir besoin d’entrer des informations d'identification dans votre code. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder à votre machine virtuelle l’accès à Azure Data Lake Store
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour accéder à Azure Data Lake Store

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Connectez-vous au Portail Azure](https://portal.azure.com).

- [Créez une machine virtuelle Linux](/azure/virtual-machines/linux/quick-create-portal).

- [Activez l’identité affectée par le système sur votre machine virtuelle](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Accorder à votre machine virtuelle l’accès à Azure Data Lake Store

Maintenant, vous pouvez accorder à votre machine virtuelle l’accès à des fichiers et des dossiers dans Azure Data Lake Store. Pour cette étape, vous pouvez utiliser une instance Data Lake Store existante ou bien en créer une. Pour créer une nouvelle instance Data Lake Store via le portail Azure, suivez ce [Démarrage rapide de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Des procédures de démarrage rapide utilisant Azure CLI et Azure PowerShell sont également décrites dans la [Documentation Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

Dans Data Lake Store, créez un dossier et accordez à Managed Service Identity la permission pour lire, écrire et exécuter des fichiers dans ce dossier :

1. Dans le volet de gauche du portail Azure, sélectionnez **Data Lake Store**.
2. Sélectionnez l’instance Data Lake Store que vous souhaitez utiliser.
3. Cliquez sur **Explorateur de données** dans la barre de commandes.
4. Le dossier racine de l’instance Data Lake Store est sélectionné. Dans la barre de commandes, sélectionnez **Accès**.
5. Sélectionnez **Ajouter**.  Dans le champ **Sélectionner**, saisissez le nom de votre machine virtuelle, par exemple **DevTestVM**. Sélectionnez votre machine virtuelle à partir des résultats de recherche, puis cliquez sur **Sélectionner**.
6. Cliquez sur **Sélectionner les autorisations**.  Sélectionnez **Lire** et **Exécuter**, ajoutez à **Ce dossier** et ajoutez en tant qu’une **Autorisation d’accès uniquement**. Sélectionnez **OK**.  L’autorisation doit être ajoutée avec succès.
7. Fermez le panneau **Accès**.
8. Pour ce didacticiel, créons un nouveau dossier. Cliquez sur **Nouveau dossier** dans la barre de commandes et donnez-lui un nom, par exemple **TestFolder**.  Sélectionnez **OK**.
9. Sélectionnez le dossier que vous avez créé, puis sélectionnez **accès** sur la barre de commandes.
10. Comme à l’étape 5, sélectionnez **Ajouter**. Dans le champ **Sélectionner**, saisissez le nom de votre machine virtuelle. Sélectionnez votre machine virtuelle à partir des résultats de recherche, puis cliquez sur **Sélectionner**.
11. Comme à l’étape 6, cliquez sur **Sélectionner les autorisations**. Cliquez sur **Lire**, **Écrire** et **Exécuter**, ajoutez **Ce dossier**, et ajoutez en tant qu’**Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut**. Sélectionnez **OK**.  L’autorisation doit être ajoutée avec succès.

La MSI peut désormais effectuer toutes les opérations sur les fichiers du dossier que vous avez créé. Pour plus d’informations sur la gestion de l’accès à Data Lake Store, lisez cet article sur le [Contrôle d’accès dans Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Obtenir un jeton d’accès et appeler le système de fichiers Data Lake Store

Azure Data Lake Store prenant en charge l’authentification Azure AD en mode natif, ainsi il peut accepter directement les jetons d’accès obtenus à l’aide de la Managed Service Identity. Pour s’authentifier sur le système de fichiers de Data Lake Store, vous envoyez un jeton d’accès émis par Azure AD pour votre point de terminaison de système de fichiers de Data Lake Store. Le jeton d’accès est un en-tête d’autorisation au format « Porteur \<ACCESS_TOKEN_VALUE\> ».  Pour en savoir plus sur la prise en charge de Data Lake Store pour Azure AD Authentication, lire [Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

Dans ce didacticiel, vous vous authentifiez sur l’API REST du système de fichiers de Data Lake Store à l’aide de cURL pour effectuer des requêtes REST.

> [!NOTE]
> Les kits de développement logiciel (SDK) clients du système de fichiers de Data Lake Store ne prennent pas encore en charge la MSI.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH p.ur les machines virtuelles Linux dans Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail, accédez à votre machine virtuelle Linux. Dans **Vue d’ensemble**, sélectionnez **Connecter**.  
2. Connectez-vous à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre du terminal, à l’aide de cURL, envoyez une requête au point de terminaison de la Managed Service Identity locale pour obtenir un jeton d’accès au système de fichiers de Data Lake Store. L’identificateur de ressources pour Data Lake Store est « https://datalake.azure.net/ ».  Il est important d’inclure la barre oblique finale dans l’identificateur de ressource.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Une réponse réussie retourne le jeton d’accès utilisé pour s’authentifier auprès de Data Lake Store :

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. À l’aide de cURL, faites une requête au point de terminaison REST du système de fichiers de votre instance Data Lake Store pour répertorier les dossiers dans le dossier racine. Il s’agit d’un moyen simple de vérifier que tout est correctement configuré. Copiez la valeur du jeton d’accès de l’étape précédente. Il est important que la chaîne « Porteur » de l’en-tête d’autorisation comporte un « P » majuscule. Vous pouvez rechercher le nom de votre instance Data Lake Store dans la section **Vue d’ensemble** du panneau **Data Lake Store** dans le portail Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Une réponse correcte se présente ainsi :

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Vous pouvez maintenant essayer de charger un fichier sur votre instance Data Lake Store. Commencez par créer un fichier à charger.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. À l’aide de cURL, faites une requête au point de terminaison REST du système de fichiers de votre instance Data Lake Store pour charger le fichier dans le dossier créé précédemment. Le chargement implique une redirection, que cURL suit automatiquement. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Une réponse correcte se présente ainsi :

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

À l’aide d’autres API de système de fichiers de Data Lake Store, vous pouvez ajouter à des fichiers, télécharger des fichiers et bien plus encore.

Félicitations ! Vous avez été authentifié auprès du système de fichiers de Data Lake Store à l’aide de la Managed Service Identity d’une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une MSI pour une machine virtuelle Linux afin d’accéder à Azure Data Lake Store. Pour en savoir plus sur Azure Data Lake Store, consultez :

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
