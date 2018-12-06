---
title: Charges de travail de conteneur sur Azure Batch | Microsoft Docs
description: Découvrez comment exécuter des applications à partir d’images conteneur sur Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: danlep
ms.openlocfilehash: 1d915482a3a8b1f6416b50ab52de997a9d33294f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262429"
---
# <a name="run-container-applications-on-azure-batch"></a>Exécuter des applications de conteneur sur Azure Batch

Azure Batch vous permet d’exécuter et de mettre à l’échelle de nombreux travaux informatiques par lots sur Azure. Les tâches Batch peuvent être exécutées directement sur des machines virtuelles (nœuds) dans un pool Batch, mais vous pouvez également configurer un pool Batch pour exécuter des tâches dans des conteneurs compatibles Docker sur les nœuds. Cet article vous montre comment créer un pool de nœuds de calcul prenant en charge les tâches en cours d’exécution du conteneur, puis exécuter des tâches du conteneur dans le pool. 

Vous devez être familiarisé avec les concepts de conteneur et savoir comment créer un pool et une tâche Batch. Les exemples de code utilisent les Kits de développement logiciel (SDK) .NET Batch et Python. Vous pouvez également utiliser d’autres kits de développement logiciel (SDK) et outils Batch, notamment le portail Azure, pour créer des pools Batch pour conteneur et pour exécuter des tâches du conteneur.

## <a name="why-use-containers"></a>Pourquoi utiliser des conteneurs ?

L’utilisation de conteneurs permet de lancer simplement des tâches par lot sans avoir à gérer un environnement et des dépendances pour exécuter des applications. Les conteneurs déploient les applications sous la forme d’unités légères, portables et autonomes pouvant s’exécuter dans différents environnements. Par exemple, générez et testez localement un conteneur, puis chargez l’image conteneur dans un registre dans Azure ou ailleurs. Le modèle de déploiement de conteneur permet de s’assurer que l’environnement d’exécution de votre application est toujours correctement installé et configuré, où que votre application soit hébergée. Les tâches basées sur le conteneur dans Batch peuvent également tirer parti des fonctionnalités des tâches non basées sur le conteneur, notamment les packages d’applications et la gestion des fichiers de ressources et des fichiers de sortie. 

## <a name="prerequisites"></a>Prérequis

* **Versions du SDK** : le SDK prend en charge des images conteneur dans les versions suivantes :
    * API REST (version : 6.0 du 01/09/2017)
    * Kit de développement logiciel Batch .NET SDK (version 8.0.0)
    * Kit de développement logiciel Batch Python (version 4.0)
    * Kit de développement logiciel Batch Java (version 3.0)
    * Kit de développement logiciel Batch Node.js (version 3.0)

* **Comptes** : sur votre abonnement Azure, vous devez créer un compte Batch et, éventuellement, un compte de stockage Azure.

* **Une image de machine virtuelle prise en charge** : les conteneurs sont uniquement pris en charge dans les pools créés lors de la configuration des machines virtuelles à partir d’images détaillées dans la section « Images de machines virtuelles prises en charge ». Si vous fournissez une image personnalisée, consultez les considérations présentées dans la section suivante et la configuration requise dans [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md). 

### <a name="limitations"></a>Limites

* Batch prend en charge RDMA uniquement pour les conteneurs exécutés sur les pools Linux.

* Pour les charges de travail de conteneur Windows, il est recommandé de choisir une taille de machine virtuelle multicœur pour votre pool

## <a name="supported-virtual-machine-images"></a>Images de machines virtuelles prises en charge

Utilisez une des images Windows ou Linux prises en charge suivantes afin de créer un pool de nœuds de calcul de machines virtuelles pour les charges de travail du conteneur. Pour plus d’informations sur les images de la Place de marché qui sont compatibles avec Batch, consultez la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Images Windows

Pour les charges de travail de conteneur Windows, Batch prend actuellement en charge l’image **Windows Server 2016 Datacenter avec des conteneurs** dans la Place de marché Azure. Seules les images de conteneur Docker sont prises en charge sur Windows.

Vous pouvez également créer des images personnalisées à partir de machines virtuelles exécutant Docker sur Windows.

### <a name="linux-images"></a>Images Linux

Pour les charges de travail de conteneur Linux, Batch prend actuellement en charge les images Linux suivantes publiées par Microsoft Azure Batch dans la Place de marché Azure :

* **CentOS pour les pools de conteneur Azure Batch**

* **CentOS (avec pilotes RDMA) pour les pools de conteneur Azure Batch**

* **Serveur Ubuntu pour les pools de conteneur Azure Batch**

* **Serveur Ubuntu (avec pilotes RDMA) pour les pools de conteneur Azure Batch**

Ces images sont uniquement prises en charge pour une utilisation dans des pools Azure Batch. Elles offrent :

* Une version d’exécution de conteneur [Moby](https://github.com/moby/moby) préinstallée 

* Des pilotes NVIDIA GPU préinstallés pour simplifier le déploiement sur des machines virtuelles Azure N-series

* Votre choix d’images avec ou sans pilotes RDMA préinstallés. Ces pilotes permettent aux nœuds de pool d’accéder au réseau RDMA Azure quand ils sont déployés sur des tailles de machine virtuelle prenant en charge RDMA. 

Vous pouvez également créer des images personnalisées à partir de machines virtuelles exécutant Docker sur l’une des distributions Linux compatibles avec Batch. Si vous souhaitez fournir votre propre image Linux personnalisée, consultez les instructions dans [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).

Pour la prise en charge de Docker sur une image personnalisée, installez [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Remarques supplémentaires relatives à l’utilisation d’une image personnalisée Linux :

* Pour tirer parti des performances du GPU de tailles Azure N-series lors de l’utilisation d’une image personnalisée, préinstallez des pilotes NVIDIA. En outre, vous devez installer l’utilitaire moteur Docker pour GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Pour accéder au réseau RDMA Azure, utilisez une taille de machine virtuelle prenant en charge RDMA. Les pilotes RDMA nécessaires sont installés dans les images CentOS HPC et Ubuntu prises en charge par Batch. Une configuration supplémentaire peut être nécessaire pour exécuter des charges de travail MPI. Voir [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuration du conteneur pour le pool Batch

Pour permettre à un pool Batch d’exécuter des charges de travail sur le conteneur, vous devez spécifier les paramètres [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) dans l’objet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) du pool. (Cet article fournit des liens vers la référence sur l’API .NET Batch. Les paramètres correspondants sont dans l’API [Python Batch](/python/api/azure.batch).)

Vous pouvez créer un pool pour conteneurs avec ou sans images conteneur prérécupérées, comme indiqué dans les exemples suivants. Le processus d’extraction (ou prérécupération) vous permet de précharger les images conteneur à partir du Hub Docker ou d’un autre registre de conteneurs sur Internet. Pour de meilleures performances, utilisez un [Registre de conteneurs Azure](../container-registry/container-registry-intro.md) dans la même région que le compte Batch.

L’avantage de la prérécupération d’images conteneur est que, lors de la première exécution des tâches, ces dernières ne doivent pas attendre que l’image conteneur soit téléchargée. La configuration du conteneur extrait des images conteneur vers les machines virtuelles une fois le pool créé. Les tâches exécutées sur le pool peuvent ensuite référencer la liste des images et des options d’exécution du conteneur.


### <a name="pool-without-prefetched-container-images"></a>Pool sans images conteneur prérécupérées

Pour configurer un pool pour conteneurs sans images conteneur prérécupérées, définissez des objets `ContainerConfiguration` et `VirtualMachineConfiguration`, comme indiqué dans l’exemple Python suivant. Cet exemple utilise le serveur Ubuntu pour l’image de pools de conteneur Azure Batch dans la Place de marché.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Prérécupérer des images pour la configuration du conteneur

Pour prérécupérer des images conteneur sur le pool, ajoutez la liste d’images conteneur (`container_image_names` dans Python) à `ContainerConfiguration`. 

L’exemple de base Python suivant montre comment prérécupérer une image conteneur Ubuntu standard à partir du [Hub Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


L’exemple C# suivant suppose que vous souhaitez prérécupérer une image TensorFlow sur le [Hub Docker](https://hub.docker.com). Cet exemple inclut une tâche de démarrage qui s’exécute dans l’hôte de la machine virtuelle sur les nœuds du pool. Vous pouvez lancer une tâche de démarrage dans l’hôte, par exemple, pour monter un serveur de fichiers accessible à partir des conteneurs.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prérécupérer des images à partir d’un registre de conteneurs privé

Vous pouvez également prérécupérer des images conteneur en vous authentifiant sur un serveur de registres de conteneurs privé. Dans l’exemple suivant, les objets `ContainerConfiguration` et `VirtualMachineConfiguration` prérécupèrent une image TensorFlow privée dans un registre de conteneurs Azure privé. La référence d’image est la même que dans l’exemple précédent.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Paramètres de conteneur pour la tâche

Pour exécuter une tâche de conteneur sur un pool prenant en charge les conteneurs, spécifiez des paramètres propres aux conteneurs. Il s’agit notamment de l’image à utiliser, du registre et des options d’exécution des conteneurs.

* Utilisez la propriété `ContainerSettings` des classes de tâches pour configurer les paramètres spécifiques au conteneur. Ces paramètres sont définis par la classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

* Si vous exécutez des tâches sur des images conteneur, la [tâche de cloud](/dotnet/api/microsoft.azure.batch.cloudtask) et la [tâche du gestionnaire de travaux](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) nécessitent des paramètres de conteneur. Toutefois, la [tâche de démarrage](/dotnet/api/microsoft.azure.batch.starttask), la [tâche de préparation du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) et la [tâche de mise en production du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) ne nécessitent pas de paramètres de conteneur (autrement dit, elles peuvent s’exécuter dans un contexte de conteneur ou directement sur le nœud).

### <a name="container-task-command-line"></a>Ligne de commande de la tâche de conteneur

Quand vous exécutez une tâche de conteneur, Batch utilise automatiquement la commande [docker create](https://docs.docker.com/engine/reference/commandline/create/) pour créer un conteneur à l’aide de l’image spécifiée dans la tâche. Batch contrôle ensuite l’exécution de la tâche dans le conteneur. 

Comme pour les autres tâches Batch, vous définissez une ligne de commande pour une tâche de conteneur. Batch crée automatiquement le conteneur, la ligne de commande spécifie donc uniquement la ou les commandes à exécuter dans le conteneur.

Si l’image conteneur d’une tâche Batch est configurée avec un script [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example), vous pouvez définir votre ligne de commande pour qu’elle utilise la valeur par défaut ENTRYPOINT ou la remplacer : 

* Pour utiliser la valeur par défaut ENTRYPOINT de l’image conteneur, définissez la ligne de commande de la tâche sur la chaîne vide `""`.

* Pour remplacer la valeur par défaut ENTRYPOINT ou si l’image n’a pas de point d’entrée, définissez une ligne de commande appropriée pour le conteneur, par exemple, `/app/myapp` ou `/bin/sh -c python myscript.py`.

Les options [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) facultatives sont des arguments supplémentaires que vous fournissez à la commande `docker create` que Batch utilise pour créer et exécuter le conteneur. Par exemple, afin de définir un répertoire de travail pour le conteneur, définissez l’option `--workdir <directory>`. Consultez les informations de référence de [docker create](https://docs.docker.com/engine/reference/commandline/create/) pour connaître les autres options.

### <a name="container-task-working-directory"></a>Répertoire de travail de la tâche de conteneur

Une tâche de conteneur Batch s’exécute dans un répertoire de travail du conteneur qui est très similaire au répertoire configuré par Batch pour une tâche normale (non-conteneur). Notez que ce répertoire de travail n’est pas [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) s’il est configuré dans l’image, ni le répertoire de travail du conteneur par défaut (`C:\` sur un conteneur Windows ou `/` sur un conteneur Linux). 

Pour une tâche de conteneur Batch :

* Tous les répertoires sous `AZ_BATCH_NODE_ROOT_DIR` sur le nœud hôte (la racine des répertoires Azure Batch) sont mappés de façon récursive dans le conteneur
* Toutes les variables d’environnement de la tâche sont mappées dans le conteneur.
* Le répertoire de travail de tâche `AZ_BATCH_TASK_WORKING_DIR` sur le nœud est défini de la même façon que pour une tâche normale et mappé dans le conteneur. 

Ces mappages vous permettent d’utiliser les tâches de conteneur quasiment comme des tâches non-conteneur. Par exemple, vous pouvez installer des applications à l’aide de packages d’application, accéder aux fichiers de ressources du Stockage Azure, utiliser les paramètres d’environnement de tâche et conserver les fichiers de sortie de tâche une fois le conteneur arrêté.

### <a name="troubleshoot-container-tasks"></a>Résoudre les problèmes des tâches de conteneur

Si votre tâche de conteneur ne s’exécute pas comme prévu, vous pouvez avoir besoin d’obtenir des informations sur la configuration de WORKDIR ou d’ENTRYPOINT pour l’image conteneur. Pour voir la configuration, exécutez la commande [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/). 

Si nécessaire, ajustez les paramètres de la tâche de conteneur en fonction de l’image :

* Spécifiez un chemin absolu dans la ligne de commande de tâche. Si la valeur par défaut ENTRYPOINT de l’image est utilisée pour la ligne de commande de tâche, vérifiez qu’un chemin absolu est défini.

* Dans les options d’exécution du conteneur de la tâche, changez le répertoire de travail pour qu’il corresponde à WORKDIR dans l’image. Par exemple, définissez `--workdir /app`.

## <a name="container-task-examples"></a>Exemples de tâche de conteneur

L’extrait de code Python suivant montre une ligne de commande de base exécutée dans un conteneur créé à partir d’une image fictive tirée du Hub Docker. Ici, l’option de conteneur `--rm` supprime le conteneur une fois la tâche terminée et l’option `--workdir` définit un répertoire de travail. La ligne de commande remplace la valeur ENTRYPOINT du conteneur par une commande shell simple qui écrit un petit fichier dans le répertoire de travail de la tâche sur l’hôte. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage', 
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)

```

L’exemple C# suivant montre les paramètres de conteneur de base d’une tâche de cloud :

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Étapes suivantes

* Consultez également la boîte à outils [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour faciliter le déploiement de charges de travail de conteneur sur Azure Batch par le biais de [recettes Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Pour plus d’informations sur l’installation et l’utilisation de Docker CE sur Linux, consultez la documentation [Docker](https://docs.docker.com/engine/installation/).

* Pour en savoir plus sur l’utilisation d’images personnalisées, consultez [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).

* En savoir plus sur le [projet Moby](https://mobyproject.org/), une infrastructure pour la création de systèmes basés sur le conteneur.