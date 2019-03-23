---
title: Utiliser MapReduce et PowerShell avec Apache Hadoop - Azure HDInsight
description: Apprenez à utiliser PowerShell pour exécuter des tâches MapReduce à distance avec Apache Hadoop sur HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.openlocfilehash: 015728a43e091e36dcf02b5cc17f0135a64428ca
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361946"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Exécuter des travaux MapReduce avec Apache Hadoop sur HDInsight à l’aide de PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour exécuter une tâche MapReduce sur un Hadoop sur un cluster HDInsight.

## <a id="prereq"></a>Configuration requise

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Un cluster Azure HDInsight (Hadoop sur HDInsight)**

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un poste de travail sur lequel est installé Azure PowerShell**.

## <a id="powershell"></a>Exécuter une tâche MapReduce

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches MapReduce sur HDInsight. En interne, PowerShell effectue des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches MapReduce sur un cluster HDInsight à distance.

* **Se connecter-AzAccount**: Authentifie Azure PowerShell dans votre abonnement Azure.

* **New-AzHDInsightMapReduceJobDefinition**: Crée une *définition de travail* avec les informations MapReduce spécifiées.

* **Start-AzHDInsightJob**: Envoie la définition de travail à HDInsight et démarre le travail. Un objet *job* est retourné.

* **Wait-AzHDInsightJob**: Utilise l’objet de travail pour vérifier l’état du travail. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.

* **Get-AzHDInsightJobOutput**: Utilisé pour récupérer la sortie du travail.

Les étapes suivantes montrent comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Ouvrez une invite de commandes **Azure PowerShell** . Accédez à l’emplacement du fichier **mapreducejob.ps1** , puis utilisez les éléments suivants pour exécuter le script :

        .\mapreducejob.ps1

    Lorsque vous exécutez le script, vous êtes invité à entrer le nom du cluster HDInsight et le nom de connexion du cluster. Vous pouvez également être invité à vous authentifier sur votre abonnement Azure.

3. Une fois la tâche terminée, vous obtenez un résultat similaire au texte suivant :

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Cela indique que la tâche a été effectuée avec succès.

    > [!NOTE]  
    > Si **ExitCode** correspond à une valeur différente de 0, consultez [Dépannage](#troubleshooting).

    Cet exemple stocke également les fichiers téléchargés dans un dossier **output.txt** dans le répertoire à partir duquel vous avez exécuté le script.

### <a name="view-output"></a>Affichage de la sortie

Pour afficher les mots et les décomptes générés par la tâche, ouvrez le fichier **output.txt** dans un éditeur de texte.

> [!NOTE]  
> Les fichiers de résultat d’une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devez modifier le nom du fichier de sortie.

## <a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est retournée lorsque la tâche est terminée, affichez les erreurs liées à cette tâche. Pour afficher les informations d’erreur pour ce projet, ajoutez la commande suivante à la fin du fichier **mapreducejob.ps1** , enregistrez-le et exécutez-le à nouveau.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Cette applet de commande retourne les informations écrites sur STDERR lors de l’exécution de la tâche.

## <a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches MapReduce sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
