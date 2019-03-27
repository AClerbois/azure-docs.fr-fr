---
title: 'Tutoriel : Générer une application hautement disponible avec le stockage Blob - Stockage Azure'
description: Utiliser le stockage géoredondant avec accès en lecture pour rendre vos données d’application hautement disponibles
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: f00f11e66d33c37ff2107cbb8282b49994fe0d3b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013579"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutoriel : Générer une application hautement disponible avec le stockage Blob

Ce tutoriel est la première partie d’une série d’étapes. Il vous apprend à rendre vos données d’application hautement disponibles dans Azure.

À l’issue de ce tutoriel, vous disposez d’une application console qui charge et récupère un objet blob à partir d’un compte de stockage [géographiquement redondant avec accès en lecture](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS).

Le stockage géographiquement redondant avec accès en lecture (RA-GRS) réplique des transactions d’une région primaire vers une région secondaire. Ce processus de réplication garantit que les données de la région secondaire sont cohérentes. L’application utilise le modèle [Disjoncteur](/azure/architecture/patterns/circuit-breaker) pour déterminer à quel point de terminaison se connecter, en basculant automatiquement d’un point de terminaison à l’autre au fur et à mesure des simulations d’échecs et de récupérations.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Définir la chaîne de connexion
> * Exécuter l’application console

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
  - **Développement Azure**

  ![Développement Azure (sous Web & Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Installez [Python](https://www.python.org/downloads/)
* Téléchargez et installez le [SDK Stockage Azure pour Python](https://github.com/Azure/azure-storage-python).

# <a name="java-v10-sdktabjava-v10"></a>[SDK Java V10](#tab/java-v10)

* Installer et configurer [Maven](https://maven.apache.org/download.cgi) pour qu’il fonctionne à partir de la ligne de commande
* Installer et configurer [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

* Installez [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Un compte de stockage fournit un espace de noms unique pour stocker les objets de données de Stockage Azure et y accéder.

Suivez ces étapes pour créer un compte de stockage géographiquement redondant avec accès en lecture :

1. Sélectionnez le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Stockage** dans la page **Nouveau**.
3. Sélectionnez **Compte de stockage - blob, fichier, table, file d’attente** sous **Recommandés**.
4. Remplissez le formulaire de compte de stockage avec les informations suivantes, comme indiqué dans l’image ci-après, et sélectionnez **Créer** :

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Abonnement** | Votre abonnement |Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup |Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Nom du compte de stockage** | mystorageaccount | Valeur unique pour votre compte de stockage |
   | **Lieu** | USA Est | Choisissez un emplacement. |
   | **Performances** | standard | Le type Standard est suffisant pour l’exemple de scénario. |
   | **Type de compte** | StorageV2 | Pour plus d’informations sur les types de compte, consultez [Types de compte de stockage](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Réplication**| Stockage géo-redondant avec accès en lecture (RA-GRS) | Ce paramètre est nécessaire pour que l’exemple fonctionne. |
   | **Niveau d’accès** | À chaud | Le type À chaud est suffisant pour l’exemple de scénario. |

![créer un compte de stockage](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Télécharger l’exemple

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Téléchargez l’exemple de projet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) et extrayez (décompressez) le fichier storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Vous pouvez aussi utiliser [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. L’exemple de projet contient une application console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Téléchargez l’exemple de projet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) et extrayez (décompressez) le fichier storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Vous pouvez aussi utiliser [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. L’exemple de projet contient une application Python de base.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK Java V10](#tab/java-v10)

[Téléchargez l’exemple de projet](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) et extrayez le fichier storage-java-ragrs.zip. Vous pouvez aussi utiliser [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. L’exemple de projet contient une application Java de base.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

[Téléchargez l’exemple de projet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) et décompressez le fichier. Vous pouvez aussi utiliser [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. L’exemple de projet contient une application Node.js de base.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configurer l'exemple

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Vous pouvez stocker cette chaîne de connexion dans une variable d’environnement sur l’ordinateur local exécutant l’application. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer la variable d’environnement.

Dans le portail Azure, accédez à votre compte de stockage. Sélectionnez **Clés d’accès** sous **Paramètres** dans votre compte de stockage. Copiez la **chaîne de connexion** de la clé primaire ou secondaire. Exécutez l’une des commandes suivantes en fonction de votre système d’exploitation, en remplaçant \<yourconnectionstring\> par la chaîne de connexion réelle. Cette commande enregistre une variable d’environnement sur la machine locale. Dans Windows, la variable d’environnement n’est pas disponible tant que vous n’avez pas rechargé l’**invite de commandes** ou l’interpréteur de commandes que vous utilisez.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans l’application, vous devez fournir les informations d’identification de votre compte de stockage. Vous pouvez stocker ces informations dans des variables d’environnement sur la machine locale exécutant l’application. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer les variables d’environnement.

Dans le portail Azure, accédez à votre compte de stockage. Sélectionnez **Clés d’accès** sous **Paramètres** dans votre compte de stockage. Collez les valeurs **Nom du compte de stockage** et **Clé** dans les commandes suivantes, en remplaçant les espaces réservés \<youraccountname\> et \<youraccountkey\>. Cette commande enregistre les variables d’environnement sur la machine locale. Dans Windows, la variable d’environnement n’est pas disponible tant que vous n’avez pas rechargé l’**invite de commandes** ou l’interpréteur de commandes que vous utilisez.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```
### <a name="windows"></a> Windows

```PowerShell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK Java V10](#tab/java-v10)

Cet exemple nécessite que vous conserviez en toute sécurité le nom et la clé de votre compte de stockage. Conservez-les dans des variables d’environnement en local sur l’ordinateur qui exécute l’exemple. Utilisez l’exemple Linux ou Windows, en fonction de votre système d’exploitation, pour créer les variables d’environnement. 

### <a name="linux-example"></a>Exemple Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemple Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

Pour trouver ces informations dans le portail Azure, accédez à votre compte de stockage et sélectionnez **Clés d’accès** dans la section **Paramètres**. 

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

Pour exécuter cet exemple, vous devez ajouter les informations d’identification de votre compte de stockage au fichier `.env.example` et le renommer `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Pour trouver ces informations dans le portail Azure, accédez à votre compte de stockage et sélectionnez **Clés d’accès** dans la section **Paramètres**. 

Vous devez également installer les dépendances demandées. Pour cela, ouvrez une invite de commandes, accédez à l’exemple de dossier et entrez `npm install`.

---

## <a name="run-the-console-application"></a>Exécuter l’application console

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Dans Visual Studio, appuyez sur **F5** ou sélectionnez **Démarrer** pour commencer le débogage de l’application. Visual Studio restaure automatiquement les packages NuGet manquants si cette option est configurée. Pour en savoir plus, consultez [Installation et réinstallation de packages avec la restauration de packages](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Une fenêtre de console apparaît et l’application commence à s’exécuter. L’application charge l’image **HelloWorld.png** de la solution dans le compte de stockage. L’application vérifie que l’image s’est répliquée sur le point de terminaison RA-GRS secondaire. Elle commence ensuite à télécharger l’image jusqu’à 999 fois. Chaque lecture est représentée par un **P** ou un **S**. **P** représente le point de terminaison principal et **S** le point de terminaison secondaire.

![Exécution de l’application console](media/storage-create-geo-redundant-storage/figure3.png)

Dans l’exemple de code, la tâche `RunCircuitBreakerAsync` dans le fichier `Program.cs` est utilisée pour télécharger une image du compte de stockage à l’aide de la méthode [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Avant le téléchargement, une classe [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) est définie. Le contexte d’opération définit les gestionnaires d’événements, qui se déclenchent quand un téléchargement se termine correctement ou si un téléchargement échoue et effectue une nouvelle tentative.

# <a name="pythontabpython"></a>[Python](#tab/python)

Pour exécuter l’application sur un terminal ou une invite de commandes, accédez au répertoire **circuitbreaker.py**, puis entrez `python circuitbreaker.py`. L’application charge l’image **HelloWorld.png** de la solution dans le compte de stockage. L’application vérifie que l’image s’est répliquée sur le point de terminaison RA-GRS secondaire. Elle commence ensuite à télécharger l’image jusqu’à 999 fois. Chaque lecture est représentée par un **P** ou un **S**. **P** représente le point de terminaison principal et **S** le point de terminaison secondaire.

![Exécution de l’application console](media/storage-create-geo-redundant-storage/figure3.png)

Dans l’exemple de code, la méthode `run_circuit_breaker` dans le fichier `circuitbreaker.py` est utilisée pour télécharger une image à partir du compte de stockage à l’aide de la méthode [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

La fonction Nouvelle tentative de l’objet de stockage est définie sur une stratégie linéaire de nouvelles tentatives. La fonction Nouvelle tentative indique s’il faut renouveler une requête et spécifie le nombre de secondes à attendre avant de renouveler la requête. Indiquez la valeur true pour **retry\_to\_secondary** si la requête doit être renvoyée à la base de données secondaire en cas d’échec de la requête à la base de données principale. Dans l’exemple d’application, une stratégie personnalisée de nouvelles tentatives est définie dans la fonction `retry_callback` de l’objet de stockage.

Avant le téléchargement, l’objet du service [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) et la fonction [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) sont définis. Ces fonctions définissent les gestionnaires d’événements qui se déclenchent quand un téléchargement se termine correctement ou si un téléchargement échoue et effectue une nouvelle tentative.  

# <a name="java-v10-sdktabjava-v10"></a>[SDK Java V10](#tab/java-v10)

Pour exécuter l’exemple, utilisez Maven au niveau de la ligne de commande.

1. Ouvrez un interpréteur de commandes et accédez à **storage-blobs-java-v10-tutorial-master** à l’intérieur de votre répertoire cloné.
2. Entrez `mvn compile exec:java`.

Cet exemple crée un fichier de test dans votre répertoire par défaut. Pour les utilisateurs Windows, il s’agit de **AppData\Local\Temp**. L’exemple présente ensuite les options suivantes des commandes que vous pouvez entrer :

- Entrez **P** pour exécuter une opération put blob, qui permet de charger un fichier temporaire dans votre compte de stockage.
- Entrez **L** pour effectuer une opération list blob, qui permet de lister les objets blob actuellement dans votre conteneur.
- Entrez **G** pour effectuer une opération get blob, qui permet de télécharger un fichier depuis votre compte de stockage vers votre ordinateur local.
- Entrez **D** pour exécuter une opération delete blob, qui permet de supprimer l’objet blob de votre compte de stockage.
- Entrez **E** pour fermer l’exemple, ce qui permet également de supprimer toutes les ressources que l’exemple a créées.

Cet exemple montre la sortie si vous exécutez l’application sous Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Comme vous contrôlez l’exemple, entrez les commandes nécessaires pour qu’il exécute le code. Les entrées respectent la casse.

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

Pour exécuter l’exemple, ouvrez une invite de commandes, accédez à l’exemple de dossier et entrez `node index.js`.

L’exemple crée un conteneur dans votre compte de stockage d’objets blob, il charge **HelloWorld.png** dans le conteneur, puis à plusieurs reprises vérifie si le conteneur et l’image ont été répliqués dans la région secondaire. Après la réplication, vous êtes invité à entrer **D** ou **Q** (suivi de Entrée) pour télécharger ou quitter. Vous devez obtenir un résultat semblable à l’exemple qui suit :

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Gestionnaire d’événements de nouvelle tentative

Le Gestionnaire d’événements `OperationContextRetrying` est appelé quand le téléchargement de l’image échoue et qu’une nouvelle tentative est définie. Si le nombre maximal de tentatives définies dans l’application est atteint, le paramètre [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de la requête passe à `SecondaryOnly`. Ce paramètre oblige l’application à essayer de télécharger l’image à partir du point de terminaison secondaire. Cette configuration réduit le temps nécessaire pour demander l’image puisque les nouvelles tentatives ne sont pas indéfiniment effectuées sur le point de terminaison principal.
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Gestionnaire d’événements de demande terminée

Le Gestionnaire d’événements `OperationContextRequestCompleted` est appelé quand le téléchargement de l’image est réussi. Si l’application utilise le point de terminaison secondaire, elle continue à utiliser ce point de terminaison jusqu’à 20 fois. Au bout de 20 fois, l’application redéfinit le paramètre [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) sur `PrimaryThenSecondary` et réessaie le point de terminaison principal. Si une requête réussit, l’application poursuit la lecture à partir du point de terminaison principal.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="pythontabpython"></a>[Python](#tab/python) 

### <a name="retry-event-handler"></a>Gestionnaire d’événements de nouvelle tentative

Le Gestionnaire d’événements `retry_callback` est appelé quand le téléchargement de l’image échoue et qu’une nouvelle tentative est définie. Si le nombre maximal de tentatives définies dans l’application est atteint, le paramètre [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) de la requête passe à `SECONDARY`. Ce paramètre oblige l’application à essayer de télécharger l’image à partir du point de terminaison secondaire. Cette configuration réduit le temps nécessaire pour demander l’image puisque les nouvelles tentatives ne sont pas indéfiniment effectuées sur le point de terminaison principal.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Gestionnaire d’événements de demande terminée

Le Gestionnaire d’événements `response_callback` est appelé quand le téléchargement de l’image est réussi. Si l’application utilise le point de terminaison secondaire, elle continue à utiliser ce point de terminaison jusqu’à 20 fois. Au bout de 20 fois, l’application redéfinit le paramètre [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) sur `PRIMARY` et réessaie le point de terminaison principal. Si une requête réussit, l’application poursuit la lecture à partir du point de terminaison principal.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK Java V10](#tab/java-v10)

Avec le kit SDK Java V10, la définition des gestionnaires de rappels est inutile, le kit SDK présentant désormais des différences fondamentales par rapport au kit SDK V7. Au lieu de LocationMode, nous avons un **pipeline** secondaire. Vous pouvez définir un pipeline secondaire par le biais de **RequestRetryOptions** et, le cas échéant, vous autorisez l’application à basculer automatiquement vers ce pipeline secondaire si elle ne parvient pas à accéder à vos données par le biais du pipeline principal.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

Avec le kit SDK Node.js V10, les gestionnaires de rappel sont inutiles. À la place, l’exemple crée un pipeline configuré avec des options de nouvelle tentative et un point de terminaison secondaire. L’application peut ainsi basculer automatiquement vers le pipeline secondaire si elle ne parvient pas à atteindre vos données via le pipeline principal. 

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3, 
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500, 
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de la série, vous avez appris à rendre une application hautement disponible avec des comptes de stockage RA-GRS.

Passez à la deuxième partie de la série pour apprendre à simuler un échec et à forcer votre application à utiliser le point de terminaison RA-GRS secondaire.

> [!div class="nextstepaction"]
> [Simuler un échec de connexion à votre point de terminaison de stockage principal](storage-simulate-failure-ragrs-account-app.md)
