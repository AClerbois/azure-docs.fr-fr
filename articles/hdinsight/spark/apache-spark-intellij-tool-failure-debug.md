---
title: 'Échec du débogage du travail Spark avec le kit de ressources Azure pour IntelliJ (préversion) '
description: Obtenez des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur des clusters HDInsight via SSH
keywords: déboguer à distance intellij, débogage à distance intellij, ssh, intellij, hdinsight, déboguer intellij, débogage
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: aff9f0f70377ebc6e741618b22ff82bc06251521
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295913"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Échec du débogage du travail Spark avec le kit de ressources Azure pour IntelliJ (préversion)

Cet article fournit des instructions pas à pas sur l’utilisation des outils HDInsight dans le [kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) pour exécuter des applications de **débogage d’échec Spark**. 

## <a name="prerequisites"></a>Prérequis
* Le [SDK Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Ce tutoriel utilise Java version 8.0.202.
  
* IntelliJ IDEA. Cet article utilise [IntelliJ IDEA Community 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Consultez [Installation d’Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Se connecter au cluster HDInsight. Consultez [Se connecter au cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Explorateur Stockage Microsoft Azure. Consultez [Téléchargez l’Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Créez un projet avec le modèle de débogage 

Créer un projet Spark 2.3.2 pour continuer l’échec de débogage, prenez l’exemple de fichier d’échec de débogage de travail dans ce document.

1. Ouvrez IntelliJ IDEA. Ouvrez la fenêtre **Nouveau projet**.

   a. Sélectionnez **Azure Spark/HDInsight** dans le volet gauche. 

   b. Dans la fenêtre principale, sélectionnez **Projet Spark avec échec de débogage de travail (préversion) (Scala)** .

     ![Créer un projet de débogage](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Sélectionnez **Suivant**.     
 
2. Dans la fenêtre **Nouveau projet**, faites les étapes suivantes :

   ![Sélectionner le SDK Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Entrez un nom de projet et un emplacement de projet.

   b. Dans la liste déroulante **Kit de développement logiciel de projet**, sélectionnez **Java 1.8** pour le cluster **Spark 2.3.2**.

   c. Dans la liste déroulante **Version Spark**, sélectionnez **Spark 2.3.2 (Scala 2.11.8)** .

   d. Sélectionnez **Terminer**.

3. Sélectionnez **src** > **principal** > **scala** pour ouvrir votre code dans le projet. Cet exemple utilise le script **AgeMean_Div()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Exécutez une application Spark Scala/Java sur un cluster HDInsight

Créez une application Spark Scala/Java, puis exécutez l’application sur un cluster Spark en procédant comme suit :

1. Cliquez sur **Ajouter une configuration** pour ouvrir la fenêtre **Configurations d’exécution/de débogage**.

   ![Modifier les configurations](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. Dans la boîte de dialogue **Run/Debug Configurations** (Exécuter/Déboguer les configurations) sélectionnez le signe plus ( **+** ). Sélectionnez ensuite l’option **Apache Spark on HDInsight**.

   ![Ajouter une nouvelle configuration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Basculez vers l’onglet **Remotely Run in Cluster (Exécuter à distance dans le cluster)** . Entrez les informations sur le **Nom**, le **Cluster Spark** et le **Nom principal de la classe**. Nos outils prennent en charge le débogage avec **Exécuteurs**. Pour **numExectors**, la valeur par défaut est 5, et vous ne pouvez pas définir une valeur supérieure à 3. Pour réduire le temps d’exécution, vous pouvez ajouter **spark.yarn.maxAppAttempts** dans les **configurations de travail** et définir la valeur sur 1. Cliquez sur le bouton **OK** pour enregistrer la configuration.

   ![Exécuter le débogage des configurations](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. La configuration est maintenant enregistrée avec le nom fourni. Pour afficher les détails de configuration, sélectionnez le nom de configuration. Pour apporter des modifications, sélectionnez **Modifier les configurations**. 

5. Une fois le paramétrage des configurations terminé, vous pouvez exécuter le projet sur le cluster à distance.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Vous pouvez vérifier l’ID de l’application dans la fenêtre de sortie.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Téléchargez le profil de travail ayant échoué

En cas d’échec de l’envoi du travail, vous pouvez télécharger le profil de travail ayant échoué sur la machine locale pour poursuivre le débogage.

1. Ouvrez **Explorateur Stockage Microsoft Azure**, recherchez le compte HDInsight du cluster pour le travail ayant échoué, téléchargez les ressources de travail ayant échoué à partir de l’emplacement correspondant : **\hdp\spark2-events\\.spark-failures\\\<ID d’application>** dans un dossier local. La fenêtre **Activités** indique la progression du téléchargement.

   ![échec du téléchargement du fichier](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![échec du téléchargement du fichier](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configurez l’environnement de débogage local et déboguer en cas d’échec

1. Ouvrez le projet d’origine ou créez un nouveau projet et associez-le au code source d’origine. Seule la version de Spark 2.3.2 est prise en charge pour le débogage d’échec actuellement.

2. Dans IntelliJ IDEA, créez un fichier de configuration de **débogage d’échec Spark**, puis sélectionnez le fichier DFT dans les ressources de travail ayant échoué précédemment téléchargées pour le champ **Emplacement du contexte de l’échec du travail Spark**.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Cliquez sur le bouton d’exécution local dans la barre d’outils, l’erreur s’affiche dans la fenêtre Exécuter.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Définissez le point d’arrêt comme le journal l’indique, puis cliquez sur le bouton de débogage local pour effectuer le débogage local tout comme vos projets Scala/Java normaux dans IntelliJ.

5. Après le débogage, si le projet se termine correctement, vous pouvez renvoyer le travail ayant échoué à votre cluster Spark sur HDInsight.

## <a name="seealso"></a>Étapes suivantes
* [Présentation : Déboguer les applications Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Démonstration
* Créer un projet Scala (vidéo) : [Créer des applications Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : Effectuer une analyse interactive des données à l’aide de Spark dans HDInsight avec les outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour analyser la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure Toolkit for IntelliJ afin de créer des applications Apache Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
