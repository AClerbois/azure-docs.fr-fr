---
title: Installer et utiliser Giraph sur des clusters Hadoop dans HDInsight - Azure
description: Découvrez comment personnaliser un cluster HDInsight avec Giraph et comment utiliser Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 37e8b0a5cc89eded1890eebbeb93cc82d54c9f05
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360892"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Installer et utiliser Apache Giraph sur les clusters HDInsight Windows

Découvrez comment personnaliser un cluster HDInsight Windows avec Apache Giraph en utilisant une action de script, et comment utiliser Giraph pour traiter des graphes à grande échelle. Pour plus d’informations sur l’utilisation de Giraph avec un cluster Linux, consultez [Installer Apache Giraph sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight Windows. HDInsight est uniquement disponible sur Windows pour les versions antérieures à HDInsight 3.4. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Pour plus d’informations sur l’installation de Giraph sur un cluster HDInsight Linux, consultez [Installer Apache Giraph sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Vous pouvez installer Giraph sur n’importe quel type de cluster (Hadoop, Storm, HBase, Spark) sur Azure HDInsight à l’aide d’une *action de script*. Un exemple de script d’installation de Giraph sur un cluster HDInsight est disponible depuis un objet blob en lecture seule du stockage Azure à l’adresse [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Articles connexes**

* [Installer Apache Giraph sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-provision-clusters.md) : informations générales sur la création de clusters HDInsight.
* [Personnaliser le cluster HDInsight à l’aide d’Action de Script] [hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l’aide d’Action de Script.
* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Présentation de Giraph
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a> permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight. Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

* identifier les amis potentiels en fonction de vos relations actuelles ;
* identifier le chemin le plus court entre deux ordinateurs d'un réseau ;
* calculer le classement de pages web.

## <a name="install-giraph-using-portal"></a>Installation de Giraph à l'aide du portail
1. Démarrez la création d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE** , comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

    ![Utiliser Action de script pour personnaliser un cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Utiliser Action de script pour personnaliser un cluster")

    |Propriété|Valeur|  
    |---|---|  
    |Nom|Indiquez un nom pour l’action de script. Par exemple, **Installation Giraph**|
    |URI du script|Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, *https :\//hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*|
    |Type de nœud|Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre **Tous les nœuds**, **Nœuds principaux uniquement** et **Nœuds de travail uniquement**.
    |parameters|Spécifiez les paramètres, si le script le demande. Le script d'installation de Giraph ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.|  

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer la création du cluster.

## <a name="use-giraph"></a>Utiliser Giraph
L’exemple SimpleShortestPathsComputation indique l’implémentation basique de <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> pour trouver le chemin le plus court entre des objets d’un graphique. Procédez comme suit pour télécharger les exemples de données et l'exemple de fichier JAR, exécuter une tâche avec l'exemple SimpleShortestPathsComputation, puis afficher les résultats.

1. Téléchargez un exemple de fichier de données dans le stockage d'objets blob Azure. Sur votre poste de travail local, créez un nouveau fichier nommé **tiny_graph.txt**. Il doit contenir les lignes suivantes :

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Téléchargez le fichier tiny_graph.txt dans le stockage principal pour votre cluster HDInsight. Pour plus d’informations sur le chargement de données, consultez [Charger des données pour les tâches Apache Hadoop dans HDInsight](hdinsight-upload-data.md).

    Ces données décrivent une relation entre les objets d’un graphique dirigé, en utilisant le format [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Chaque ligne représente une relation entre un objet **source\_id** et un ou plusieurs objets **dest\_id**. La valeur **edge\_value** (ou pondération) peut être considérée comme l’intensité ou la distance de la connexion entre **source_id** et **dest\_id**.

    Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela :

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. Exécutez l'exemple SimpleShortestPathsComputation. Utilisez les applets de commande Azure PowerShell suivantes pour exécuter l'exemple en utilisant le fichier tiny_graph.txt comme entrée.

    > [!IMPORTANT]  
    > La prise en charge de la gestion des ressources HDInsight par Azure PowerShell à l’aide d’Azure Service Manager est **déconseillée** ; elle a été supprimée le 1er janvier 2017. Dans ce document, la procédure repose sur les nouvelles applets de commande HDInsight qui fonctionnent avec Azure Resource Manager.
    >
    > Suivez les étapes indiquées dans [Installation et de configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) pour installer la dernière version d’Azure PowerShell. Si vous devez modifier certains scripts pour utiliser les nouvelles applets de commande fonctionnant avec Azure Resource Manager, consultez [Migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) pour plus d’informations.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    Dans l’exemple ci-dessus, remplacez **clustername** par le nom de votre cluster HDInsight sur lequel Giraph est installé.
3. Affichez les résultats. Une fois la tâche terminée, les résultats sont stockés dans deux fichiers de sortie, dans le dossier **wasb:///example/out/shotestpaths**. Les fichiers se nomment **part-m-00001** et **part-m-00002**. Procédez comme suit pour télécharger et afficher le résultat :

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Cette opération crée la structure de répertoires **example/output/shortestpaths** dans le répertoire actuel de votre poste de travail et télécharge les deux fichiers de sortie à cet emplacement.

    Utilisez l’applet de commande **Cat** pour afficher le contenu des fichiers :

        Cat example/output/shortestpaths/part*

    Le résultat doit ressembler à ce qui suit :

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme `destination_id distance`, où distance est la valeur (ou la pondération) des bords parcourus entre l’ID d’objet 1 et l’ID cible.

    En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et ID 4 est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis entre <span style="color:red">ID 3 et 4</span>.

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Installer Giraph à l'aide d’Azure PowerShell
Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).  L’exemple montre comment installer Apache Spark avec Azure PowerShell. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installation de Giraph à l'aide de .NET SDK
Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md). L'exemple montre comment installer Spark à l'aide du Kit de développement logiciel (SDK) .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Voir aussi
* [Installer Apache Giraph sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-provision-clusters.md) : informations générales sur la création de clusters HDInsight.
* [Personnaliser un cluster HDInsight à l’aide d’Action de Script](hdinsight-hadoop-customize-cluster-linux.md): informations générales sur la personnalisation de clusters HDInsight à l’aide d’Action de Script.
* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md).
* [Installer et utiliser Apache Spark sur les clusters HDInsight][hdinsight-install-spark] : Exemple d’action de script sur l’installation de Spark.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
