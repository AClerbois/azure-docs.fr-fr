---
title: Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight | Microsoft Docs
description: Découvrez comment utiliser Zeppelin pour exécuter des requêtes Hive.
keywords: hdinsight,hadoop,hive,interactive query,LLAP
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: c8fe65d2eadaede1d99befbf76c4d06fab9598fc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight 

Les clusters Interactive Query HDInsight incluent les notebooks Zeppelin que vous pouvez utiliser pour exécuter des requêtes Hive interactives. Dans cet article, vous allez apprendre à utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight. 

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous avez les éléments suivants :

* **Cluster Interactive Query HDInsight**. Consultez [Créer un cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) pour créer un cluster HDInsight.  Vous devez sélectionner le type Interactive Query. 

## <a name="create-a-zeppelin-note"></a>Créer une note Zeppelin

1. Accédez à l’URL suivante :

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Remplacez **CLUSTERNAME** par le nom de votre cluster.

2. Entrez votre nom d’utilisateur Hadoop et votre mot de passe. Dans la page Zeppelin, choisissez d’ouvrir une note existante ou d’en créer une. HiveSample contient plusieurs exemples de requêtes Hive.  

    ![Zeppelin avec Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Cliquez sur **Create new Note**.
4. Tapez ou sélectionnez les valeurs suivantes :

    - Note name : entrez un nom pour la note.
    - Default interpreter : sélectionnez **JDBC**.

5. Cliquez sur **Create Note**.
6. Exécutez la requête Hive suivante :

        %jdbc(hive)
        show tables

    ![exécution de la requête Interactive Query HDInsight avec Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    L’instruction **%jdbc(hive)** dans la première ligne indique au notebook d’utiliser l’interpréteur Hive JDBC.

    La requête retourne une table Hive appelée *hivesampletable*.

    Vous pouvez exécuter les deux autres requêtes Hive suivantes sur la table hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Les résultats de la requête sont retournés plus rapidement qu’avec une requête Hive standard.


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à visualiser des données de HDInsight à l’aide de Power BI.  Pour en savoir plus, consultez les articles suivants :

* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connexion d’Excel à Hadoop à l’aide de Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./hdinsight-upload-data.md).
