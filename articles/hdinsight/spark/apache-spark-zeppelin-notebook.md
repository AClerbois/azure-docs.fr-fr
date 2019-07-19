---
title: Utiliser des blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight
description: Instructions détaillées sur l’utilisation des blocs-notes Zeppelin avec les clusters Apache Spark sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 219cdeea228ae3e334213a0f0654f904592cb09e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448741"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight

Les clusters HDInsight Spark incluent des blocs-notes [Apache Zeppelin](https://zeppelin.apache.org/) que vous pouvez utiliser pour exécuter des tâches [Apache Spark](https://spark.apache.org/). Dans cet article, vous allez apprendre à utiliser le bloc-notes Zeppelin sur un cluster HDInsight.

**Configuration requise :**

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Le schéma d'URI de votre principal espace de stockage de clusters. Il s'agirait de `wasb://` pour Stockage Blob Azure, de `abfs://` pour Azure Data Lake Storage Gen2 ou de `adl://` pour Azure Data Lake Storage Gen1. Si l'option de transfert sécurisé était activée pour Stockage Blob ou Data Lake Storage Gen2, l'URI serait `wasbs://` ou `abfss://`, respectivement.  Pour plus d'informations, consultez également [Exiger un transfert sécurisé dans Stockage Azure](../../storage/common/storage-require-secure-transfer.md).

## <a name="launch-an-apache-zeppelin-notebook"></a>Lancer un bloc-notes Apache Zeppelin

1. Dans la section **Vue d'ensemble** du cluster Spark, accédez à **Tableaux de bord du cluster** et sélectionnez **Bloc-notes Zeppelin**. Entrez les informations d'identification de l'administrateur du cluster.  

   > [!NOTE]  
   > Vous pouvez également atteindre le bloc-notes Zeppelin pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Créer un nouveau bloc-notes. Dans le volet d'en-tête, accédez à **Bloc-notes** > **Créer une note**.

    ![Créer un nouveau bloc-notes Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Créer un nouveau bloc-notes Zeppelin")

    Entrez un nom pour le bloc-notes, puis sélectionnez **Créer une note**.

3. Vérifiez que l'en-tête du bloc-notes indique un état connecté. Il est indiqué par un point vert dans le coin supérieur droit.

    ![État du bloc-notes Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "État du bloc-notes Zeppelin")

4. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l'exemple de fichier de données `hvac.csv` est copié sur le compte de stockage associé, sous `\HdiSamples\SensorSampleData\hvac`.

    Collez l’extrait suivant dans le paragraphe vide créé par défaut dans le nouveau bloc-notes.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **Lire** pour que le paragraphe exécute l'extrait de code. L’état indiqué dans le coin supérieur droit du paragraphe doit progresser de READY, PENDING, RUNNING à FINISHED. Le résultat s’affiche au bas du même paragraphe. La capture d’écran ressemble à ceci :

    ![Créer une table temporaire à partir de données brutes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Créer une table temporaire à partir de données brutes")

    Vous pouvez également indiquer un titre pour chaque paragraphe. Dans le coin droit du paragraphe, sélectionnez l'icône **Paramètres** (pignon), puis sélectionnez **Afficher le titre**.  

    > [!NOTE]  
    > L’interpréteur %spark2 n’est pas pris en charge dans les bloc-notes Zeppelin sur toutes les versions HDInsight et l’interpréteur %sh ne sera pas pris en charge à partir de HDInsight 4.0 et versions ultérieures.

5. Vous pouvez maintenant exécuter des instructions Spark SQL dans la table `hvac`. Collez la requête suivante dans un nouveau paragraphe. La requête récupère l’ID de bâtiment et la différence entre les températures cibles et réelles pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    L’instruction **%sql** du début demande au bloc-notes d’utiliser l’interpréteur Livy Scala.

6. Sélectionnez l'icône **Graphique à barres** pour modifier l'affichage.  **paramètres**, qui apparaît une fois que vous avez sélectionné **Graphique à barres**, vous permet de choisir les **Clés** et les **Valeurs**.  La capture d’écran qui suit présente le résultat.

    ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

7. Vous pouvez également exécuter des instructions Spark SQL à l’aide de variables dans la requête. L'extrait suivant montre comment définir la variable `Temp` dans la requête avec les valeurs d'interrogation possibles. Lors de la première exécution de la requête, une liste déroulante est automatiquement renseignée avec les valeurs que vous avez spécifiées pour la variable.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Collez cet extrait dans un nouveau paragraphe, puis appuyez sur **MAJ + ENTRÉE**. Sélectionnez ensuite **65** dans la liste déroulante **Temp**. 

8. Sélectionnez l'icône **Graphique à barres** pour modifier l'affichage.  Sélectionnez ensuite **paramètres** et apportez les modifications suivantes :

   * **Groupes :**  Ajoutez **targettemp**.  
   * **Valeurs :** 1. Supprimez **date**.  2. Ajoutez **temp_diff**.  3.  Remplacez l'agrégateur **SUM** par **AVG**.  

     La capture d’écran qui suit présente le résultat.

     ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

9. Redémarrez l’interpréteur Livy pour quitter l’application. Pour ce faire, ouvrez les paramètres de l'interpréteur en sélectionnant le nom de l'utilisateur connecté dans le coin supérieur droit, puis sélectionnez **Interpréteur**.  

    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")

10. Accédez à **livy**, puis sélectionnez **redémarrer**.  À l'invite, sélectionnez **OK**.

    ![Redémarrer l’interpréteur Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Redémarrer l’interpréteur Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Comment utiliser des packages externes avec le bloc-notes ?
Vous pouvez configurer le bloc-notes Zeppelin dans un cluster Apache Spark sur HDInsight pour utiliser des packages externes bénéficiant de la contribution de la communauté, qui ne sont pas inclus dans le cluster. Vous pouvez rechercher le [référentiel Maven](https://search.maven.org/) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles à partir d’autres sources. Par exemple, une liste complète des packages bénéficiant de la contribution de la communauté est disponible sur le site [Spark Packages](https://spark-packages.org/)(Packages Spark).

Dans cet article, vous allez apprendre à utiliser le package [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le bloc-notes Jupyter.

1. Ouvrez les paramètres de l’interpréteur. Dans le coin supérieur droit, sélectionnez le nom de l'utilisateur connecté, puis **Interpréteur**.

    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")

2. Accédez à **livy**, puis sélectionnez **modifier**.

    ![Modifier les paramètres de l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modifier les paramètres de l’interpréteur")

3. Ajoutez une nouvelle clé appelée `livy.spark.jars.packages` et définissez sa valeur au format `group:id:version`. Par conséquent, si vous souhaitez utiliser le package [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), vous devez définir la valeur de la clé sur `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modifier les paramètres de l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modifier les paramètres de l’interpréteur")

    Sélectionnez **Enregistrer**, puis redémarrez l'interpréteur Livy.

4. si vous souhaitez savoir comment atteindre la valeur de la clé entrée ci-dessus, lisez ce qui suit.
   
    a. Recherchez le package dans le référentiel Maven. Dans cet article, nous avons utilisé [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. À partir du référentiel, rassemblez les valeurs pour **GroupId**, **ArtifactId** et **Version**.
   
    ![Utiliser des packages externes avec le bloc-notes Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Utiliser des packages externes avec le bloc-notes Jupyter")
   
    c. Concaténez les trois valeurs séparées par deux-points ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Où les blocs-notes Zeppelin sont-ils enregistrés ?
Les blocs-notes Zeppelin sont enregistrés dans les nœuds principaux du cluster. Par conséquent, si vous supprimez le cluster, les blocs-notes seront aussi supprimés. Si vous souhaitez conserver vos blocs-notes pour une utilisation ultérieure sur les autres clusters, vous devez les exporter après avoir terminé l’exécution des tâches. Pour exporter un bloc-notes, sélectionnez l'icône **Exporter** comme illustré sur l'image ci-dessous.

![Télécharger le bloc-notes](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Télécharger le bloc-notes")

Cela permet d’enregistrer le bloc-notes en tant que fichier JSON dans l’emplacement de téléchargement.

## <a name="livy-session-management"></a>Gestion des sessions Livy
Lorsque vous exécutez le premier paragraphe de code dans votre bloc-notes Zeppelin, une nouvelle session Livy est créée dans votre cluster HDInsight Spark. Cette session est partagée par tous les blocs-notes Zeppelin que vous créerez par la suite. Si, pour une raison quelconque, la session Livy est arrêtée (redémarrage de cluster, etc.), vous ne serez pas en mesure d’exécuter de tâches à partir du bloc-notes Zeppelin.

Dans ce cas, vous devez effectuer les étapes suivantes avant de commencer à exécuter des tâches à partir d’un bloc-notes Zeppelin.  

1. Redémarrez l’interpréteur Livy à partir du bloc-notes Zeppelin. Pour ce faire, ouvrez les paramètres de l'interpréteur en sélectionnant le nom de l'utilisateur connecté dans le coin supérieur droit, puis sélectionnez **Interpréteur**.

    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")

2. Accédez à **livy**, puis sélectionnez **redémarrer**.

    ![Redémarrer l’interpréteur Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Redémarrer l’interpréteur Zeppelin")

3. Exécutez une cellule de code à partir d’un bloc-notes Zeppelin existant. Cela crée une session Livy dans le cluster HDInsight.

## <a name="seealso"></a>Voir aussi
* [Présentation : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser le plug-in Azure HDInsight Tools pour IntelliJ IDEA afin de déboguer des applications Apache Spark à distance](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
