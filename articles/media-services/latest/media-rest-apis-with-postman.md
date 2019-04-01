---
title: Configurer Postman pour les appels d’API REST Azure Media Services
description: Découvrez comment configurer Postman pour les appels d’API REST Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: cecfd0fafd17e9c463e0c0a74f1e30290a53d82f
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758201"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurer Postman pour les appels d’API REST Media Services

Ce tutoriel vous montre comment configurer **Postman** afin de l’utiliser pour appeler l’API REST Azure Media Services (AMS). Le tutoriel montre comment importer des fichiers d’environnement et de collection dans **Postman**. La collection contient des définitions groupées de requêtes HTTP qui appellent les API REST de Azure Media Services (AMS). Le fichier d’environnement contient des variables qui sont utilisées par la collection.

## <a name="prerequisites"></a>Conditions préalables

- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- Obtenir les informations nécessaires pour [accéder aux API](access-api-cli-how-to.md)
- Installez le client REST [Postman](https://www.getpostman.com/) pour exécuter les API REST indiquées dans certains des didacticiels REST AMS. 

    Nous utilisons **Postman** mais n’importe quel outil REST serait approprié. Les autres solutions possibles sont : **Visual Studio Code** avec le plug-in REST ou **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Télécharger les fichiers Postman

Cloner un référentiel GitHub contenant les fichiers de collection et d’environnement Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurer Postman

Cette section configure le Postman.

### <a name="configure-the-environment"></a>Configurer l’environnement 

1. Ouvrez **Postman**.
2. Sur la droite de l’écran, sélectionnez l’option **Gérer environnement**.

    ![Gérer environnement](./media/develop-with-postman/postman-import-env.png)
4. Dans la boîte de dialogue **Gérer environnement**, cliquez sur **Importer**.
2. Accédez au `Azure Media Service v3 Environment.postman_environment.json` fichier qui a été téléchargé lorsque vous avez effectué le clonage `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. L’environnement **Azure Media Services v3** est ajouté.

    > [!Note]
    > Mettre à jour les variables d’accès avec les valeurs que vous avez obtenues dans la section **Accéder à l’API Media Services** ci-dessus.

7. Double-cliquez sur le fichier sélectionné et entrez les valeurs que vous avez obtenues en suivant les étapes pour accéder aux API.
8. Fermez la boîte de dialogue.
9. Sélectionnez l’environnement **Azure Media Services v3 Environnement** dans la liste déroulante.

    ![Choisir l’environnement](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurer la collection

1. Cliquez sur **Importer** pour importer le fichier de la collection.
1. Accédez au `Media Services v3.postman_collection.json` fichier qui a été téléchargé lorsque vous avez effectué le clonage `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Choisissez le fichier **Media Services v3.postman_collection.json**.

    ![Importer un fichier](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obtenir un jeton Azure AD 

Avant de commencer à manipuler des ressources AMS v3, vous devez obtenir et définir le jeton Azure AD pour l’authentification de principal du service.

1. Dans la fenêtre de gauche de Postman, sélectionnez « Étape 1 : Obtenir le jeton d’authentification AAD ».
2. Ensuite, sélectionnez « obtenir un jeton Azure AD pour l’authentification du principal du service ».
3. Appuyez sur **Envoyer**.

    L’opération **POST** suivante est envoyée.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La réponse inclut le jeton et définit la variable d’environnement « AccessToken » sur la valeur du jeton.  

    ![Obtenir le jeton AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>Voir aussi

- [Charger des fichiers dans un compte Media Services - REST](upload-files-rest-how-to.md)
- [Créer des filtres avec Media Services - REST](filters-dynamic-manifest-rest-howto.md)
- [API Azure Resource Manager basé sur l’API REST](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Étapes suivantes

- [Diffuser des fichiers en continu avec REST](stream-files-tutorial-with-rest.md).  
- [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md)
