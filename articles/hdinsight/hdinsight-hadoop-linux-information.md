---
title: Conseils sur l’utilisation de Hadoop sur un cluster HDInsight basé sur Linux - Azure
description: Obtenez des conseils d’implémentation concernant l’utilisation de clusters HDInsight (Hadoop) basés sur Linux dans un environnement Linux familier, exécuté dans le cloud Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: c149c6466f7d86f5cb22c840d4353c3939768768
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518981"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation de HDInsight sous Linux

Les clusters Azure HDInsight fournissent Apache Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Conditions préalables

La plupart des étapes décrites dans ce document utilisent les utilitaires ci-après, que vous pouvez avoir besoin d’installer sur votre système.

* [cURL](https://curl.haxx.se/) : permet de communiquer avec des services web.
* **jq**, un processeur JSON en ligne de commande.  Voir [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) : permet de gérer à distance des services Azure.
* **Un client SSH**. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Utilisateurs

Sauf s’il est [joint à un domaine](./domain-joined/apache-domain-joined-introduction.md), HDInsight doit être considéré comme un système **mono-utilisateur**. Un compte d’utilisateur SSH unique est créé avec le cluster, avec les autorisations de niveau administrateur. Des comptes SSH supplémentaires peuvent être créés, mais ils auront également l’accès administrateur au cluster.

HDInsight joint à un domaine prend en charge la présence de plusieurs utilisateurs et des paramètres d’autorisation et de rôles plus précis. Pour plus d’informations, consultez la section [Gestion des clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Noms de domaine

Le nom de domaine complet (FQDN) à utiliser lors de la connexion au cluster à partir d’internet est `CLUSTERNAME.azurehdinsight.net` ou `CLUSTERNAME-ssh.azurehdinsight.net` (pour SSH).

En interne, chaque nœud du cluster porte un nom qui est attribué pendant la configuration du cluster. Pour rechercher les noms de cluster, consultez la page **Hôtes** sur l’interface Web Ambari. Vous pouvez également utiliser les éléments suivants pour renvoyer la liste des hôtes à partir de l’API REST Ambari :

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez `CLUSTERNAME` par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe du compte Administrateur. Cette commande renvoie un document JSON qui contient une liste des hôtes du cluster. [jq](https://stedolan.github.io/jq/) est utilisé pour extraire le `host_name` valeur de l’élément pour chaque hôte.

Si vous avez besoin de trouver le nom du nœud d’un service spécifique, vous pouvez interroger Ambari pour obtenir ce composant. Par exemple, pour trouver les hôtes du nœud de nom HDFS, utilisez la commande suivante :

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Cette commande renvoie un document JSON qui décrit le service, puis [jq](https://stedolan.github.io/jq/) extrait uniquement la `host_name` valeur pour les ordinateurs hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services

* **Ambari (web)** - https://CLUSTERNAME.azurehdinsight.net

    S’authentifier à l’aide de l’utilisateur d’administrateur de cluster et le mot de passe, puis connectez-vous à Ambari.

    L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

    > [!IMPORTANT]  
    > Certaines interfaces utilisateur web disponibles via Ambari accèdent aux nœuds à l’aide d’un nom de domaine interne. Les noms de domaine internes ne sont pas accessibles publiquement sur internet. Des erreurs vous indiquant que le serveur est introuvable sont susceptibles d’apparaître lorsque vous essayerez d’accéder à certaines fonctionnalités sur Internet.
    >
    > Pour bénéficier de toutes les fonctionnalités de l’interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH pour assurer l’acheminement proxy vers le nœud principal cluster. Consultez [Utiliser le tunneling SSH pour accéder à l’interface web d’Apache Ambari, à ResourceManager, à JobHistory, à NameNode, à Oozie et à d’autres interfaces utilisateur web](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.NET sur le port 22 ou 23. Le port 22 est utilisé pour se connecter au nœud principal primaire tandis que le port 23 est utilisé pour se connecter au nœud principal secondaire. Pour plus d’informations sur les nœuds principaux, consultez [Disponibilité et fiabilité des clusters Apache Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Vous pouvez accéder aux nœuds principaux du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds Worker à l’aide de SSH depuis un nœud principal.

Pour plus d’informations, consultez le document [Ports utilisés par les services Apache Hadoop sur HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="file-locations"></a>Emplacements des fichiers

Les fichiers relatifs à Hadoop se trouvent sur les nœuds du cluster dans `/usr/hdp`. Le répertoire contient les sous-répertoires suivants :

* **2.6.5.3006-29**: le nom du répertoire correspond à la version de la plateforme Hortonworks Data utilisée par HDInsight. Le numéro qui figure sur votre cluster peut être différent de celui indiqué ici.
* **current** : Ce répertoire contient des liens vers des sous-répertoires sous le **2.6.5.3006-29** directory. Ce répertoire vous évite d’avoir à mémoriser le numéro de version.

Vous trouverez des exemples de données et de fichiers JAR sur le système HDSF (Hadoop Distributed File System) dans `/example` et `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Stockage Azure et Data Lake Storage

Dans la plupart des distributions Hadoop, les données sont stockées dans HDFS, qui est sauvegardé par un stockage local sur les machines dans le cluster. L’utilisation du stockage peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l’heure ou à la minute pour les ressources de calcul.

Quand vous utilisez HDInsight, les fichiers de données sont stockés de manière évolutive et résiliente dans le cloud, à l’aide du Stockage Blob Azure et, éventuellement, d’Azure Data Lake Storage. Ces services offrent les avantages suivants :

* Un stockage à long terme peu coûteux.
* Un accès à partir de services externes comme des sites web, des utilitaires de chargement/téléchargement de fichier, des SDK en différentes langues et des navigateurs web.
* Capacité de prise en charge des grands fichiers et grand stockage évolutif.

Pour plus d’informations, consultez [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Présentation des objets blob) et [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Quand vous utilisez Stockage Azure ou Data Lake Storage, vous n’avez aucune opération particulière à effectuer à partir de HDInsight pour accéder aux données. Par exemple, la commande suivante liste les fichiers dans le dossier `/example/data`, qu’il soit stocké sur Stockage Azure ou sur Data Lake Storage :

    hdfs dfs -ls /example/data

Dans HDInsight, les ressources de stockage de données (Stockage Blob Azure et Azure Data Lake Storage) sont dissociées des ressources de calcul. Par conséquent, vous pouvez créer des clusters HDInsight pour effectuer les calculs dont vous avez besoin et, par la suite, supprimer le cluster lorsque le travail est terminé, tout en conservant vos fichiers de données en toute sécurité dans le stockage cloud aussi longtemps que nécessaire.


### <a name="URI-and-scheme"></a>URI et schéma

Certaines commandes peuvent vous imposer de spécifier le schéma dans l’URI lorsque vous accédez à un fichier. Par exemple, le composant Storm-HDFS requiert la spécification du schéma. Lorsque vous n’utilisez pas le stockage par défaut (stockage ajouté en tant que stockage « supplémentaire » au cluster), vous devez toujours indiquer le schéma dans l’URI.

Lorsque vous utilisez __Stockage Azure__, utilisez l’un des schémas d’URI suivants :

* `wasb:///`: accès au stockage par défaut avec une communication non chiffrée.

* `wasbs:///`: accès au stockage par défaut avec une communication chiffrée.  Le schéma wasbs est pris en charge uniquement à partir de HDInsight version 3.6 et versions ultérieures.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: utilisé pour communiquer avec un compte de stockage autre que celui par défaut. Par exemple, si vous avez un compte de stockage supplémentaire ou si vous accédez à des données stockées dans un compte de stockage accessible au public.

Lorsque vous utilisez __Azure Data Lake Storage Gen2__, utilisez l’un des schémas d’URI suivants :

* `abfs:///`: accès au stockage par défaut avec une communication non chiffrée.

* `abfss:///`: Accédez au stockage par défaut à l’aide d’une communication chiffrée.  Le schéma abfss est pris en charge uniquement à partir de HDInsight version 3.6 et versions ultérieures.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: utilisé pour communiquer avec un compte de stockage autre que celui par défaut. Par exemple, si vous avez un compte de stockage supplémentaire ou si vous accédez à des données stockées dans un compte de stockage accessible au public.

Lorsque vous utilisez __Azure Data Lake Storage Gen1__, utilisez l’un des schémas d’URI suivants :

* `adl:///`: Accédez au stockage Data Lake par défaut pour le cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilisé pour communiquer avec un stockage Data Lake autre que celui par défaut. Également utilisé pour accéder aux données en dehors du répertoire racine de votre cluster HDInsight.

> [!IMPORTANT]  
> Quand vous utilisez Data Lake Storage comme banque par défaut pour HDInsight, vous devez spécifier un chemin dans la banque à utiliser comme racine de stockage HDInsight. Le chemin d’accès par défaut est : `/clusters/<cluster-name>/`.
>
> Lorsque vous utilisez `/` ou `adl:///` pour accéder aux données, vous pouvez uniquement accéder à des données stockées à la racine (par exemple, `/clusters/<cluster-name>/`) du cluster. Pour accéder à des données n’importe où dans le magasin, utilisez le format `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Quel stockage le cluster utilise-t-il ?

Vous pouvez utiliser Ambari pour récupérer la configuration de stockage par défaut du cluster. Utilisez la commande suivante pour récupérer les informations de configuration HDFS à l’aide de curl, puis filtrez ces informations avec [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Cette commande renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Vous devrez peut-être répertorier toutes les versions de configuration pour rechercher la version la plus récente.

Cette commande retourne une valeur semblable aux URI suivants :

* `wasb://<container-name>@<account-name>.blob.core.windows.net` si vous utilisez un compte de stockage Azure.

    Le nom du compte correspond au nom du compte Stockage Azure. Le nom du conteneur est le conteneur d’objets blob qui constitue la racine de l’espace de stockage en cluster.

* `adl://home` en cas d’utilisation d’Azure Data Lake Storage. Pour obtenir le nom du Data Lake Storage, utilisez l’appel REST suivant :

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Cette commande renvoie le nom d’hôte suivant : `<data-lake-store-account-name>.azuredatalakestore.net`.

    Pour obtenir le répertoire du magasin qui correspond à la racine de HDInsight, utilisez l’appel REST suivant :

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Cette commande renvoie un chemin d’accès semblable à ce qui suit : `/clusters/<hdinsight-cluster-name>/`.

Vous pouvez également rechercher les informations de stockage à l’aide du portail Azure en suivant les étapes ci-dessous :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre cluster HDInsight.

2. Dans la section **Propriétés**, sélectionnez **Comptes de stockage**. Les informations de stockage du cluster s’affichent.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Comment accéder à des fichiers situés en dehors de HDInsight ?

Il existe plusieurs façons d’accéder à des données à l’extérieur du cluster HDInsight. Voici quelques liens vers des utilitaires et Kits de développement logiciel (SDK) qui peuvent être utilisés pour exploiter vos données :

Si vous utilisez le __stockage Azure__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) : commandes de l’interface de ligne de commande fonctionnant avec Azure. Après l’installation, utilisez la commande `az storage` pour obtenir de l’aide sur l’utilisation du stockage ou la commande `az storage blob` pour obtenir les commandes spécifiques aux objets blob.
* [blobxfer.py](https://github.com/Azure/blobxfer) : script python pour travailler avec des objets blob dans Stockage Azure.
* Divers Kits de développement logiciel (SDK) :

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Si vous utilisez __Azure Data Lake Storage__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Navigateur Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interface de ligne de commande Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Mise à l’échelle de votre cluster

La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier de manière dynamique le nombre de nœuds de données utilisés par un cluster. Vous pouvez effectuer des opérations de mise à l’échelle pendant que d’autres travaux ou processus s’exécutent sur un cluster.  Voir aussi [HDInsight de mise à l’échelle des clusters](./hdinsight-scaling-best-practices.md)

Les différents types de cluster sont affectés par la mise à l’échelle comme suit :

* **Hadoop** : quand vous réduisez le nombre de nœuds dans un cluster, certains services du cluster sont redémarrés. Les opérations de mise à l’échelle peuvent provoquer l’échec des tâches en cours d’exécution ou en attente à la fin de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l’opération terminée.
* **HBase** : les serveurs régionaux sont automatiquement équilibrés en l’espace de quelques minutes, une fois l’opération de mise à l’échelle terminée. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

    1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Pour démarrer le shell HBAse, utilisez la commande suivante :

            hbase shell

    3. Une fois le shell HBase chargé, utilisez ce qui suit pour équilibrer manuellement les serveurs régionaux :

            balancer

* **Storm** : vous devez rééquilibrer les topologies Storm en cours d’exécution après l’exécution d’une opération de mise à l’échelle. Le rééquilibrage permet à la topologie de réajuster les paramètres de parallélisme basés sur le nouveau nombre de nœuds du cluster. Pour rééquilibrer les topologies en cours d’exécution, utilisez l’une des options suivantes :

    * **SSH** : connectez-vous au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

            storm rebalance TOPOLOGYNAME

        Vous pouvez également spécifier des paramètres pour remplacer les indicateurs de parallélisme initialement fournis par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` permet de reconfigurer la topologie sur 5 processus Worker, 3 exécuteurs pour le composant blue-spout et 10 exécuteurs pour le composant yellow-bolt.

    * **Interface utilisateur de Storm** : utilisez les étapes suivantes pour rééquilibrer une topologie avec l’interface utilisateur de Storm.

        1. Ouvrez `https://CLUSTERNAME.azurehdinsight.net/stormui` dans votre navigateur web, où `CLUSTERNAME` est le nom de votre cluster Storm. Si vous y êtes invité, entrez le nom et le mot de passe de l’administrateur (admin) du cluster HDInsight spécifiés lors de la création du cluster.
        2. Sélectionnez la topologie que vous souhaitez rééquilibrer, puis le bouton **Rééquilibrer** . Saisissez le délai avant l’opération de rééquilibrage.

* **Kafka** : vous devez rééquilibrer les réplicas de partition après les opérations de mise à l’échelle. Pour plus d’informations, consultez le document [Haute disponibilité des données avec Apache Kafka sur HDInsight](./kafka/apache-kafka-high-availability.md).

Pour obtenir des informations spécifiques sur la mise à l’échelle de votre cluster HDInsight, consultez :

* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestion des clusters Apache Hadoop dans HDInsight avec Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Comment installer Hue (ou un autre composant Hadoop) ?

HDInsight est un service géré. Si Azure détecte un problème avec le cluster, il peut supprimer le nœud défaillant et créer un nœud pour le remplacer. Si vous installez manuellement des éléments sur le cluster, ils ne sont pas conservés lorsque cette opération se produit. Utilisez plutôt [Actions de script HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Une action de script peut être utilisée pour effectuer les modifications suivantes :

* Installer et configurer un service ou un site web.
* Installer et configurer un composant qui nécessite des modifications de configuration sur plusieurs nœuds du cluster.

Les actions de script sont des scripts Bash. Les scripts s’exécutent pendant la création du cluster et sont utilisés pour installer et configurer des composants supplémentaires. Des exemples de scripts sont fournis pour l’installation des composants suivants :

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Pour plus d’informations sur le développement de vos propres actions de script, consultez [Développement d’actions de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Fichiers Jar

Certaines technologies Hadoop sont fournies dans des fichiers jar autonomes, qui contiennent des fonctions utilisées dans le cadre d’un travail MapReduce ou à partir de Pig ou Hive. Il ne nécessitent généralement aucune configuration et peuvent être chargés dans le cluster après la création et être utilisés directement. Si vous souhaitez que le composant survive à la réinitialisation du cluster, vous pouvez stocker le fichier jar dans le stockage par défaut de votre cluster (WASB ou ADL).

Par exemple, si vous souhaitez utiliser la dernière version d’[Apache DataFu](https://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet, puis le charger vers le cluster HDInsight. Suivez ensuite la documentation DataFu pour savoir comment l’utiliser à partir de Pig ou Hive.

> [!IMPORTANT]  
> Certains composants qui sont des fichiers jar autonomes sont fournis avec HDInsight, mais ne se trouvent pas dans le chemin d’accès. Pour rechercher un composant spécifique sur votre cluster, vous pouvez utiliser la commande suivante :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Cette commande renvoie le chemin d’accès de tous les fichiers jar correspondants.

Pour utiliser une version différente d’un composant, chargez la version dont vous avez besoin et utilisez-la dans vos tâches.

> [!IMPORTANT]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aide à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Il existe par exemple de nombreux sites communautaires, comme : [Forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [https://apache.org](https://apache.org), par exemple : [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer la migration de HDInsight Windows vers HDInsight Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Gérer les clusters HDInsight à l’aide de l’API Apache Ambari REST](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)
