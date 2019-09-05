---
title: Démarrage rapide Azure - Créer un objet blob dans un stockage d’objets avec Python | Microsoft Docs
description: Dans le cadre de ce guide de démarrage rapide, vous allez créer un compte de stockage et un conteneur dans un stockage d’objets (blob). Ensuite, vous utilisez la bibliothèque de client de stockage pour Python, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/29/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 059e41b744ad92cdec9057e4a17f470b5b769c62
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142965"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Démarrage rapide : charger, télécharger et répertorier des objets blob avec Python

Dans ce guide de démarrage rapide, vous apprenez à utiliser Python pour charger, télécharger et lister des objets blob de blocs dans un conteneur de stockage Blob Azure. Les objets blob sont simplement des objets qui peuvent contenir n’importe quelle quantité de données texte ou binaires (par exemple, des images, des documents, du contenu multimédia en streaming ou des données d’archive) et sont distincts dans Stockage Azure des partages de fichiers, des tables sans schéma et des files d’attente de messages. (Pour plus d’informations, consultez [Présentation de Stockage Azure](/azure/storage/common/storage-introduction).)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Vérifiez que les prérequis suivants sont installés :

* [Python](https://www.python.org/downloads/)
* [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application
[L’exemple d’application](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) dans ce démarrage rapide est une application Python de base.  

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Cette commande clone le référentiel *Azure-Samples/storage-blobs-python-quickstart* dans votre dossier git local. Pour exécuter le programme Python, ouvrez le fichier *example.py* à la racine du référentiel.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
Dans l’application, fournissez votre nom de compte de stockage et votre clé de compte pour créer un objet `BlockBlobService`. Ouvrez le fichier *example.py* dans l’Explorateur de solutions de votre IDE. Remplacez les valeurs `accountname` et `accountkey` par le nom et la clé de votre compte. 

```python
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Cet exemple permet de créer un fichier de test dans le dossier *Documents*. L’exemple de programme charge le fichier de test sur Stockage Blob, liste les objets blob du conteneur, puis télécharge le fichier sous un nouveau nom. 

Commencez par installer les dépendances en exécutant `pip install` :

```python
    pip install azure-storage-blob
```

Ensuite, exécutez l’exemple. Vous pouvez voir des messages similaires à ce qui suit :
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Avant de continuer, recherchez les deux fichiers dans votre dossier *Documents*. Vous pouvez les ouvrir et constater qu’ils sont identiques.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](https://storageexplorer.com) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier *example.py* pour examiner le code. 

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Parcourons l’exemple de code pour comprendre son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage
Commencez par créer les références aux objets utilisés pour accéder au stockage Blob et le gérer. Ces objets reposent les uns sur les autres, chacun est utilisé par le suivant dans la liste.

* Instanciez l’objet **BlockBlobService**, qui pointe vers le service blob de votre compte de stockage. 

* Instanciez l’objet **CloudBlobContainer** qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le conteneur d’objets blob cloud, instanciez l’objet **CloudBlockBlob** qui pointe vers l’objet blob qui vous intéresse. Vous pouvez ensuite charger, télécharger et copier l’objet blob si nécessaire.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Affectation de noms et références aux conteneurs, objets blob et métadonnées).

Dans cette section, vous instanciez les objets, créez un conteneur, puis définissez des autorisations sur le conteneur pour que les objets blob soient publics. Le conteneur est appelé **quickstartblobs**. 

```python
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés et nous les utilisons donc dans ce guide de démarrage rapide.  

Pour charger un fichier dans un objet blob, récupérez le chemin d’accès complet du fichier en joignant le nom de répertoire au nom de fichier sur votre disque local. Vous pouvez ensuite charger le fichier vers le chemin d’accès spécifié à l’aide de la méthode `create_blob_from_path`. 

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement. Il stocke le fichier à charger en tant que *full_path_to_file* et le nom de l’objet blob en tant que *local_file_name*. L'exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, avec un flux de données en mémoire, vous pouvez utiliser la méthode `create_blob_from_stream` au lieu de la méthode `create_blob_from_path`. 

Les objets blob de blocs peuvent atteindre une taille maximale de 4.7 To et peuvent représenter toutes sortes d’éléments allant des feuilles de calcul Excel aux fichiers vidéo volumineux. Les objets blob de pages sont principalement utilisés pour les fichiers VHD qui soutiennent les machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de la méthode `list_blobs`. Cette méthode retourne un générateur. Le code suivant récupère la liste des objets blob&mdash;, puis effectue une boucle sur ces derniers&mdash; en affichant les noms des objets blob trouvés dans un conteneur.  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Télécharger les objets blob

Téléchargez les objets blob sur votre disque local à l’aide de la méthode `get_blob_to_path`. Le code suivant télécharge l’objet blob chargé dans une section précédente. *_DOWNLOADED* est ajouté en tant que suffixe au nom de l’objet blob, ce qui vous permet de voir les deux fichiers sur le disque local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’avez plus besoin des objets blob chargés dans ce démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de la méthode `delete_container`. Si vous préférez supprimer les fichiers individuels, utilisez la méthode `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Ressources sur le développement d’applications Python avec des objets blob

Pour plus d’informations sur le développement Python avec le stockage Blob, consultez ces ressources supplémentaires :

### <a name="binaries-and-source-code"></a>Fichiers binaires et code source

- Affichez, téléchargez et installez le [code source de la bibliothèque de client Python](https://github.com/Azure/azure-storage-python) pour Stockage Azure sur GitHub.

### <a name="client-library-reference-and-samples"></a>Référence et exemples de la bibliothèque de client

- Pour plus d’informations sur la bibliothèque de client Python, consultez la [référence API Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Explorez les [exemples de Stockage Blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) écrits avec la bibliothèque de client Python.

## <a name="next-steps"></a>Étapes suivantes
 
Dans ce démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et un stockage Blob Azure avec Python. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](./storage-python-how-to-use-blob-storage.md)
 
Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
