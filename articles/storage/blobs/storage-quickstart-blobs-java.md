---
title: Guide pratique pour créer un objet blob dans Stockage Azure avec le kit Java Storage SDK v7 | Microsoft Docs
description: Créez un compte de stockage et un conteneur dans un stockage d’objets (blob). Ensuite, utilisez la bibliothèque de client Stockage Azure pour Java v7 afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de lister les objets blob dans un conteneur.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 342b19927d290f5ff519d3fae5b84bcfa2b9d9cf
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711750"
---
# <a name="how-to-upload-download-and-list-blobs-using-java-sdk-v7"></a>Guide pratique pour charger, télécharger et lister des objets blob avec Java SDK v7

Dans ce guide pratique, vous apprenez à utiliser Java pour charger, télécharger et lister des objets blob de blocs dans un conteneur dans Stockage Blob Azure.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Créez également un compte de stockage Azure dans le [portail Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Si vous avez besoin d’aide pour créer un compte de stockage, consultez [Créez un compte de stockage](../common/storage-quickstart-create-account.md).

Assurez-vous que vous disposez des prérequis suivants :

* Installez un IDE avec une intégration Maven.

* Vous pouvez aussi installer et configurer Maven pour qu’il fonctionne à partir de la ligne de commande.

Ce guide utilise [Eclipse](http://www.eclipse.org/downloads/) avec la configuration « IDE Eclipse pour les développeurs Java ».

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

L’[exemple d’application](https://github.com/Azure-Samples/storage-blobs-java-quickstart) est une application console de base.  

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir le projet, lancez Eclipse et fermez l’écran d’accueil. Sélectionnez **Fichier**, puis **Open Projects from File System** (Ouvrir des projets à partir du système de fichiers). Vérifiez que l’option **Detect and configure project natures** (Détecter et configurer les natures de projet) est activée. Sélectionnez **Répertoire**, puis accédez à l’emplacement où vous avez stocké le référentiel cloné. Dans celui-ci, sélectionnez le dossier **blobAzureApp**. Vérifiez que le projet **blobAzureApp** s’affiche en tant que projet Eclipse, puis sélectionnez **Terminer**.

Une fois l’importation du projet terminée, ouvrez **AzureApp.java** (situé dans **blobQuickstart.blobAzureApp** à l’intérieur de **src/main/java**) et remplacez `accountname` et `accountkey` dans la chaîne `storageConnectionString`. Puis exécutez l’application. Les instructions spécifiques pour effectuer ces tâches sont décrites dans les sections suivantes.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
    
Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Ouvrez le fichier **AzureApp.Java**. Trouvez la variable `storageConnectionString` et collez la valeur de la chaîne de connexion copiée dans la section précédente. Votre variable `storageConnectionString` doit être semblable à l’exemple de code suivant :

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple d’application crée un fichier de test dans votre répertoire par défaut (*C:\Users\<user>\AppData\Local\Temp*, pour les utilisateurs Windows), le charge dans le stockage d’objets blob, liste les objets blob dans le conteneur, puis télécharge le fichier avec un nouveau nom pour vous permettre de comparer les fichiers anciens et nouveaux. 

Exécutez l’exemple à l’aide de Maven au niveau de la ligne de commande. Ouvrez un interpréteur de commandes et accédez à **blobAzureApp** à l’intérieur de votre répertoire cloné. Puis saisissez `mvn compile exec:java`. 

L’exemple suivant illustre le résultat obtenu si vous deviez exécuter l’application sur Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Avant de continuer, vérifiez votre répertoire par défaut (*C:\Users\<user>\AppData\Local\Temp*, pour les utilisateurs Windows) pour l’exemple de fichier. Copiez l’URL de l’objet blob en dehors de la fenêtre de console et collez-la dans un navigateur pour afficher le contenu du fichier dans Stockage Blob. Si vous comparez l’exemple de fichier dans votre répertoire avec le contenu stocké dans le stockage d’objets blob, vous verrez qu’ils sont identiques. 

  >[!NOTE]
  >Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage.

Une fois que vous avez vérifié les fichiers, appuyez sur la touche **Entrée** pour terminer la démonstration et supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier**AzureApp.java** pour examiner le code. 

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account) pointant vers le compte de stockage.

    L’objet **CloudStorageAccount** est une représentation de votre compte de stockage. Il vous permet de définir et lire les propriétés du compte de stockage par programmation. Vous pouvez utiliser l’objet **CloudStorageAccount** pour créer une instance de l’objet **CloudBlobClient**, nécessaire pour accéder au service BLOB.

* Créez une instance de l’objet **CloudBlobClient** pointant vers le [service Blob](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) de votre compte de stockage.

    L’objet **CloudBlobClient** fournit un point d’accès au service BLOB, ce qui vous permet de définir et lire les propriétés de stockage d’objets blob par programmation. Vous pouvez utiliser l’objet **CloudBlobClient** pour créer une instance de l’objet **CloudBlobContainer**, nécessaire pour créer des conteneurs.

* Créez une instance de l’objet [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) qui représente le conteneur auquel vous accédez. Utilisez les conteneurs pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.    

    Une fois que vous avez le **CloudBlobContainer**, vous pouvez créer une instance de l’objet [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) qui pointe vers l’objet blob spécifique qui vous intéresse, et effectuer une opération de chargement, téléchargement, copie, ou autre.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les conteneurs, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Créez un conteneur.

Dans cette section, vous créez une instance des objets, un conteneur, puis définissez des autorisations sur le conteneur pour les objets blob soient publics et accessibles par une simple URL. Le conteneur est appelé **quickstartcontainer**. 

Cet exemple utilise [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists), car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Dans un environnement de production où vous utilisez le même conteneur pour toute l’application, il est préférable d’appeler une seule fois **CreateIfNotExists**. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Pour charger un fichier dans un objet blob de blocs, obtenez une référence à l’objet blob dans le conteneur cible. Une fois que vous avez la référence d’objet blob, vous pouvez y charger des données à l’aide de [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement, en stockant le fichier à charger comme **source** et le nom de l’objet blob dans **blob**. L’exemple suivant charge le fichier sur votre conteneur nommé **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Il existe plusieurs méthodes `upload`, y compris [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) et [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext) que vous pouvez utiliser avec le stockage d’objets blob. Par exemple, avec une chaîne, vous pouvez utiliser la méthode `UploadText` au lieu de la méthode `Upload`. 

Les objets blob de blocs peuvent être n’importe quel type de fichier texte ou binaire. Les objets blob de pages sont principalement utilisés pour les fichiers VHD qui soutiennent les machines virtuelles IaaS. Utilisez des objets blob d’ajout pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers pour montrer les URI des objets blob rencontrés. Vous pouvez copier l’URI à partir de la fenêtre Commande et la coller dans un navigateur pour afficher le fichier.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez des objets blob sur votre disque local à l’aide de [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Le code suivant télécharge l’objet blob chargé dans la section précédente et ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob pour pouvoir voir les deux fichiers sur le disque local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des objets blob que vous avez chargés, vous pouvez supprimer le conteneur entier à l’aide de [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Cette méthode permet de supprimer les fichiers du conteneur également.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à transférer des fichiers entre un disque local et le Stockage Blob Azure avec Java. Pour en savoir plus sur l’utilisation de Java, accédez à notre dépôt de code source GitHub.

> [!div class="nextstepaction"]
> [Kit de développement logiciel Stockage Azure pour Java](https://github.com/azure/azure-storage-java) 
> [Référence API](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
> [Exemples de code pour Java](../common/storage-samples-java.md)

* Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).