---
title: Utiliser MapReduce et PowerShell avec Hadoop - Azure HDInsight
description: Apprenez à utiliser PowerShell pour exécuter des tâches MapReduce à distance avec Hadoop sur HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.openlocfilehash: cab6fc652fa11db7dd1e9e9ae7f0a1a634dca3b0
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591818"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Exécution à distance des travaux MapReduce avec Hadoop sur HDInsight à l’aide de PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour exécuter une tâche MapReduce sur un Hadoop sur un cluster HDInsight.

## <a id="prereq"></a>Configuration requise

* **Un cluster Azure HDInsight (Hadoop sur HDInsight)**

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un poste de travail sur lequel est installé Azure PowerShell**.

## <a id="powershell"></a>Exécuter une tâche MapReduce

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches MapReduce sur HDInsight. En interne, PowerShell effectue des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches MapReduce sur un cluster HDInsight à distance.

* **Connect-AzureRmAccount** : authentifie Azure PowerShell auprès de votre abonnement Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition** : crée une *définition de tâche* avec les informations MapReduce spécifiées.

* **Start-AzureRmHDInsightJob** : envoie la définition du travail à HDInsight et démarre la tâche. Un objet *job* est retourné.

* **Wait-AzureRmHDInsightJob**: utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.

* **Get-AzureRmHDInsightJobOutput** : permet de récupérer la sortie de la tâche.

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
Get-AzureRmHDInsightJobOutput `
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

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
