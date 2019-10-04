---
title: Azure HDInsight pour Visual Studio Code
description: Découvrez comment utiliser les outils Spark et Hive (Azure HDInsight) pour Visual Studio Code pour créer et envoyer des requêtes et des scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 2b4d588799d72be85030e70aed58b2c8d6b0a9a3
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091747"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Utilisez les outils Spark et Hive pour Visual Studio Code

Découvrez comment utiliser les outils Spark et Hive pour Visual Studio Code pour créer et envoyer des tâches Apache Hive de traitement par lots, des requêtes Hive interactives et des scripts PySpark pour Apache Spark. Nous décrirons d’abord comment installer les outils Spark et Hive dans Visual Studio Code, puis nous expliquerons comment envoyer des travaux à ces outils.  

Vous pouvez installer les outils Spark et Hive sur les plateformes prises en charge par Visual Studio Code, Windows, Linux et MacOS y compris. Les différentes plateformes ont les prérequis suivants.

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous avez les éléments nécessaires suivants :

- Un cluster Azure HDInsight. Pour créer un cluster, consultez [Prise en main de HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Vous pouvez également utiliser un cluster Spark et Hive qui prend en charge un point de terminaison Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono est nécessaire uniquement pour les plateformes Linux et MacOS.
- [Un environnement interactif PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Un répertoire local nommé **HDexample**.  Cet article utilise **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installer les outils Spark et Hive

Une fois que vous disposez de tous les éléments prérequis, vous pouvez installer les outils Spark et Hive pour Visual Studio Code en procédant comme suit :

1. Ouvrez Visual Studio Code.

2. À partir de la barre de menus, accédez à **Afficher** > **Extensions**.

3. Dans la zone de recherche, entrez **Spark et Hive**.

4. Sélectionnez **Outils Spark et Hive** dans les résultats de la recherche, puis sélectionnez **Installer** :

   ![Installation de Python avec Spark et Hive pour Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Sélectionnez **Recharger** si nécessaire.

## <a name="open-a-work-folder"></a>Ouvrir un dossier de travail

Pour ouvrir un dossier de travail et créer un fichier dans Visual Studio Code, procédez comme suit :

1. À partir de la barre de menus, accédez à **Fichier** > **Ouvrir le dossier** > **C:\HD\HDexample**, puis cliquez sur le bouton **Sélectionner le dossier**. Le dossier s’affiche dans la vue **Explorer** sur la gauche.

2. Dans la vue **Explorateur**, sélectionnez le dossier **HDexample**, puis l’icône **Nouveau fichier** à côté du dossier de travail :

   ![icône nouveau fichier visual studio code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nommez le nouveau fichier avec l’extension de fichier `.hql` (requêtes Hive) ou `.py` (script Spark). Cet exemple utilise **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurer l’environnement Azure

Pour un utilisateur de cloud national, suivez ces étapes pour configurer l’environnement Azure, puis utilisez la commande **Azure: Sign In** pour vous connecter à Azure :

1. Sélectionnez **File\Preferences\Settings**.
2. Recherchez la chaîne suivante : **Azure : Cloud**
3. Sélectionnez le cloud national dans la liste :

   ![Définir la configuration de l’entrée de connexion par défaut](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Se connecter à un compte Azure

Avant de pouvoir envoyer des scripts à vos clusters à partir de Visual Studio Code, vous devez vous connecter à votre compte Azure ou lier un cluster (à l’aide du nom d’utilisateur et du mot de passe Apache Ambari ou d’un compte joint au domaine). Suivez ces étapes pour vous connecter à Azure :

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Azure: Sign In** :

    ![Outils Spark et Hive pour la connexion à Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Suivez les instructions de connexion pour vous connecter à Azure. Une fois que vous êtes connecté, le nom de votre compte Azure s’affiche dans la barre d’état, en bas de la fenêtre Visual Studio Code.  

## <a name="link-a-cluster"></a>Lier un cluster

### <a name="link-azure-hdinsight"></a>Lien : Azure HDInsight

Vous pouvez lier un cluster normal à l’aide d’un nom d’utilisateur [Apache Ambari](https://ambari.apache.org/) managé ou lier un cluster de sécurité Hadoop de Pack Sécurité Entreprise à l’aide d’un nom d’utilisateur de domaine (par exemple : `user1@contoso.com`).

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Spark/Hive: Lier un cluster**.

   ![Commande de cluster Lien Palette de commandes](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Sélectionnez le type de cluster lié **Azure HDInsight**.

3. Entrez l’URL du cluster HDInsight.

4. Entrez votre nom d’utilisateur Ambari (**admin** par défaut).

5. Entrez votre mot de passe Ambari.

6. Sélectionnez le type de cluster.

7. Définissez le nom d’affichage du cluster (facultatif).

8. Passez en revue la vue **OUTPUT** à des fins de vérification.

   > [!NOTE]  
   > Le nom d’utilisateur lié et le mot de passe sont utilisés si le cluster est à la fois connecté à l’abonnement Azure et lié à un cluster.  

### <a name="link-generic-livy-endpoint"></a>Lien : point de terminaison Livy générique

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Spark/Hive: Lier un cluster**.

2. Sélectionnez le type de cluster lié **Generic Livy Endpoint**.

3. Entrez le point de terminaison Livy générique. Par exemple, http\://10.172.41.42:18080.

4. Sélectionnez le type d’autorisation **De base** ou **Aucune**.  Si vous sélectionnez **De base** :  
    &emsp;a. Entrez votre nom d’utilisateur Ambari (**admin** par défaut).  
    &emsp;b. Entrez votre mot de passe Ambari.

5. Passez en revue la vue **OUTPUT** à des fins de vérification.

## <a name="list-clusters"></a>Énumérer les clusters

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Spark/Hive: Répertorier un cluster**.

2. Sélectionnez l’abonnement souhaité.

3. Passez en revue la vue **OUTPUT**. Cet affichage montre le ou les clusters liés et tous les clusters de votre abonnement Azure :

    ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Définir le cluster par défaut

1. Rouvrez le dossier **HDexample** évoqué [précédemment](#open-a-work-folder) s’il est fermé.  

2. Sélectionnez le fichier **HelloWorld.hql** créé [précédemment](#open-a-work-folder). Il s’ouvre dans l’éditeur de script.

3. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Spark / Hive: Définir le cluster par défaut**.  

4. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure ou liez un cluster si ce n’est déjà fait.

5. Sélectionnez un cluster à utiliser comme cluster par défaut pour le fichier de script actuel. Les outils mettent automatiquement à jour le fichier de configuration **.VSCode\settings.json** :

   ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Envoyer des requêtes Hive interactives et des scripts de commandes par lot Hive

Avec les outils Spark et Hive pour Visual Studio Code, vous pouvez envoyer des requêtes Hive interactives et des scripts de commandes par lot Hive à vos clusters.

1. Rouvrez le dossier **HDexample** évoqué [précédemment](#open-a-work-folder) s’il est fermé.  

2. Sélectionnez le fichier **HelloWorld.hql** créé [précédemment](#open-a-work-folder). Il s’ouvre dans l’éditeur de script.

3. Copiez le code suivant et collez-le dans votre fichier Hive, puis enregistrez-le :

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure ou liez un cluster si ce n’est déjà fait.

5. Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **Hive: Interactive** pour envoyer la requête, ou utilisez le raccourci clavier Ctrl+Alt+I.  Sélectionnez **Hive : Batch** pour envoyer le script, ou utilisez le raccourci clavier Ctrl+Alt+H.  

6. Si vous n’avez pas spécifié de cluster par défaut, sélectionnez un cluster. Les outils vous permettent également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, les résultats de la requête s’affichent dans un nouvel onglet :

   ![Résultat de requête interactive Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Volet **RÉSULTATS** : Vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou Excel dans un chemin local ou enregistrer seulement certaines lignes du résultat.

    - Volet **MESSAGES** : Cliquez sur un numéro de **ligne** pour accéder à la première ligne du script en cours d’exécution.

## <a name="submit-interactive-pyspark-queries"></a>Envoyer des requêtes PySpark interactives

Pour envoyer des requêtes PySpark interactives, procédez comme suit :

1. Rouvrez le dossier **HDexample** évoqué [précédemment](#open-a-work-folder) s’il est fermé.  

2. Créez un fichier **HelloWorld.py** en suivant les étapes [précédentes](#open-a-work-folder).

3. Copiez et collez le code suivant dans le fichier de script :

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure ou liez un cluster si ce n’est déjà fait.

5. Sélectionnez l’ensemble du code, cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Spark: PySpark Interactive** pour envoyer la requête. Vous pouvez également utiliser le raccourci Ctrl+Alt+I.

   ![menu contextuel interactif pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Si vous n’avez pas spécifié de cluster par défaut, sélectionnez le cluster. Peu après, les **résultats interactifs de Python** s’affichent dans un nouvel onglet. Les outils vous permettent également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel :

   ![Fenêtre Python interactive dans PySpark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Entrez **%%info**, puis appuyez sur Maj+Entrée pour afficher les informations sur le travail (facultatif) :

   ![informations travail d’affichage interactif pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. L’outil prend également en charge la requête **Spark SQL** :

   ![Affichage des résultats dans PySpark Interactive](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   L’état de l’envoi s’affiche à gauche de la barre d’état inférieure lors de l’exécution des requêtes. N’envoyez pas d’autres requêtes lorsque l’état est **PySpark Kernel (busy)** .  

   > [!NOTE]
   >
   > Si la case **Python Extension Enabled** n’est pas activée dans les paramètres (elle est activée par défaut), les résultats d’interaction PySpark envoyés utilisent l’ancienne fenêtre :
   >
   > ![Extension Python dans Pyspark Interactive désactivée](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Envoi de la tâche de traitement par lots PySpark

1. Rouvrez le dossier **HDexample** évoqué [précédemment](#open-a-work-folder) s’il est fermé.  

2. Créez un fichier **BatchFile.py** en suivant les étapes [précédentes](#open-a-work-folder).

3. Copiez et collez le code suivant dans le fichier de script :

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure ou liez un cluster si ce n’est déjà fait.

5. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **Spark : PySpark Batch**, ou utilisez le raccourci clavier Ctrl+Alt+H.

6. Sélectionnez un cluster auquel envoyer le travail PySpark :

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Une fois que vous avez envoyé un travail Python, les journaux d’activité d’envoi apparaissent dans la fenêtre **OUTPUT** (Sortie) dans Visual Studio Code. Les URL des interfaces utilisateur Spark et Yarn s’affichent également. Vous pouvez ouvrir l’URL dans un navigateur web pour suivre l’état du travail.

## <a name="apache-livy-configuration"></a>Configuration d’Apache Livy

La configuration [Apache Livy](https://livy.incubator.apache.org/) est prise en charge. Vous pouvez effectuer la configuration dans le fichier **.VSCode\settings.json** situé dans le dossier de l’espace de travail. Actuellement, la configuration Livy prend uniquement en charge le script Python. Pour plus d’informations, consultez le document [README](https://github.com/cloudera/livy/blob/master/README.rst ) sur Livy.

<a id="triggerlivyconf"></a>**Comment déclencher une configuration Livy**

Méthode 1  
1. À partir de la barre de menus, accédez à **Fichier** > **Préférences** > **Paramètres**.
2. Dans la zone **Paramètres de recherche**, entrez **Envoi de travail HDInsight : Livy Conf**.  
3. Sélectionnez **Modifier dans settings.json** pour le résultat de recherche pertinent.

Méthode 2 - Envoyez un fichier. Vous constatez que le dossier .vscode est ajouté automatiquement au dossier de travail. Vous pouvez voir la configuration Livy en sélectionnant **.vscode\settings.json**.

+ Paramètres du projet :

    ![Configuration d’Apache Livy HDInsight](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Pour les paramètres **driverMemory** et **executorMemory**, définissez la valeur et l’unité. Par exemple :  1 g ou 1 024 m.

+ Configurations Livy prises en charge :

    Corps de la requête **POST /batches**

    | Nom | description | Type |
    | :- | :- | :- |
    | file | Fichier contenant l’application à exécuter | Chemin (obligatoire) |
    | proxyUser | Utilisateur auquel emprunter l’identité lors de l’exécution de la tâche | Chaîne |
    | className | Classe principale Java/Spark de l’application | Chaîne |
    | args | Arguments de ligne de commande pour l’application | Liste de chaînes |
    | jars | Fichiers JAR à utiliser dans cette session | Liste de chaînes | 
    | pyFiles | Fichiers Python à utiliser dans cette session | Liste de chaînes |
    | fichiers d'entrée | Fichiers à utiliser dans cette session | Liste de chaînes |
    | driverMemory | Quantité de mémoire à utiliser pour le processus de pilote | Chaîne |
    | driverCores | Nombre de cœurs à utiliser pour le processus de pilote | Int |
    | executorMemory | Quantité de mémoire à utiliser par processus de l’exécuteur | Chaîne |
    | executorCores | Nombre de cœurs à utiliser pour chaque exécuteur | Int |
    | numExecutors | Nombre d’exécuteurs à lancer pour cette session | Int |
    | archives | Archives à utiliser dans cette session | Liste de chaînes |
    | queue | Nom de la file d’attente YARN vers laquelle effectuer l’envoi| Chaîne |
    | name | Nom de cette session | Chaîne |
    | conf | Propriétés de configuration Spark. | Map of key=val |

    Response body   
    Objet Batch créé

    | name | description | Type |
    | :- | :- | :- | 
    | id | ID de session | Int | 
    | appId | ID d’application de cette session | Chaîne |
    | appInfo | Informations détaillées sur l’application | Map of key=val |
    | log | Lignes du journal | Liste de chaînes |
    | state |État du lot | Chaîne |

    >[!NOTE]
    >La configuration Livy attribuée s’affiche dans le volet de sortie quand vous envoyez le script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Intégrer avec Azure HDInsight à partir de l’Explorateur

**Azure HDInsight** a été ajouté à la vue Explorateur. Vous pouvez parcourir et gérer vos clusters directement à l’aide d’**Azure HDInsight**.

1. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure ou liez un cluster si ce n’est déjà fait.

2. À partir de la barre de menus, accédez à **Afficher** > **Explorateur**.

3. Dans le volet gauche, développez **AZURE HDINSIGHT**.  Les clusters et abonnements disponibles (Spark, Hadoop et HBase sont pris en charge) s’affichent :

   ![Affichage de l’abonnement Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Développez le cluster pour voir le schéma de table et la base de données de métadonnées Hive :

   ![Supprimer un cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="preview-hive-table"></a>Aperçu de la table Hive
Vous pouvez voir un aperçu de la table Hive directement dans vos clusters à l’aide de l’explorateur **Azure HDInsight** :
1. [Connectez-vous](#connect-to-an-azure-account) à votre compte Azure, si ce n’est déjà fait.

2. Cliquez sur l’icône **Azure** dans la colonne la plus à gauche.

3. Dans le volet gauche, développez **AZURE HDINSIGHT**. Les clusters et les abonnements disponibles y sont listés.

4. Développez le cluster pour afficher le schéma de table et la base de données de métadonnées Hive.

5. Cliquez avec le bouton droit sur la table Hive. Par exemple : **hivesampletable**. Sélectionnez **Aperçu**. 

   ![Aperçu de la table Hive avec Spark et Hive pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. La fenêtre **Aperçu des résultats** s’ouvre :

   ![Fenêtre Aperçu des résultats avec Spark et Hive pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Volet RÉSULTATS

   Vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou Excel dans un chemin local ou enregistrer seulement certaines lignes du résultat.

- Volet MESSAGES
   1. Quand le nombre de lignes dans la table est supérieur à 100, le message suivant s’affiche : « Les 100 premières lignes sont affichées dans la table Hive ».
   2. Quand le nombre de lignes dans la table est inférieur ou égal à 100, un message de ce type s’affiche : « 60 lignes sont affichées dans la table Hive ».
   3. Quand la table est vide, le message suivant s’affiche : « 0 lignes sont affichées dans la table Hive ».

        >[!NOTE]
        >
        >Sous Linux, installez xclip pour autoriser la copie des données de table.
        >
        >![Spark et Hive pour Visual Studio Code dans Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>Fonctionnalités supplémentaires

Les fonctionnalités suivantes sont également prises en charge par Spark et Hive pour Visual Studio Code :

- **Saisie semi-automatique IntelliSense**. Des suggestions de mots clés, de méthodes, de variables et d’autres éléments de programmation s’affichent. Les différentes icônes représentent les différents types d’objets :

    ![Outils Spark et Hive pour Visual Studio Code – Objets IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marqueur d’erreurs IntelliSense**. Le service de langage souligne les erreurs de saisie dans le script Hive.     
- **Coloration syntaxique**. Le service de langage utilise plusieurs couleurs pour différencier les variables, les mots clés, le type des données, les fonctions et les autres éléments de programmation :

    ![Outils Spark et Hive pour Visual Studio Code – Points clés de la syntaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rôle Lecteur seul

Les utilisateurs auxquels le rôle de lecteur uniquement a été attribué pour le cluster ne peuvent plus envoyer de travaux au cluster HDInsight ni visualiser la base de données Hive. Vous devez contacter l’administrateur du cluster afin de mettre à niveau votre rôle pour être [**opérateur de cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) dans le [portail Azure](https://ms.portal.azure.com/). Si vous disposez d’informations d’identification Ambari valides, vous pouvez lier manuellement le cluster en suivant les instructions ci-après.

### <a name="browse-the-hdinsight-cluster"></a>Parcourir le cluster HDInsight  

Quand vous sélectionnez l’explorateur Azure HDInsight pour développer un cluster HDInsight, vous êtes invité à lier le cluster si vous disposez du rôle de lecteur uniquement sur le cluster. Utilisez la méthode suivante pour établir une liaison au cluster à l’aide de vos informations d’identification Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Envoyer le travail au cluster HDInsight

Quand vous envoyez un travail à un cluster HDInsight, vous êtes invité à lier le cluster si vous disposez du rôle de lecteur uniquement sur le cluster. Utilisez la procédure suivante pour établir une liaison au cluster à l’aide d’informations d’identification Ambari.

### <a name="link-to-the-cluster"></a>Établir une liaison au cluster

1. Entrez un nom d’utilisateur Ambari valide.
2. Entrez un mot de passe valide.

   ![Nom d’utilisateur pour les outils Spark et Hive pour Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Outils Spark et Hive pour le mot de passe Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Vous pouvez utiliser `Spark / Hive: List Cluster` pour vérifier le cluster lié :
  >
  >![Outils Spark et Hive pour lecteur lié Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Parcourir un compte Data Lake Storage Gen2

Quand vous sélectionnez l’explorateur Azure HDInsight pour développer un compte ADLS Gen2, vous êtes invité à entrer la clé d’accès de stockage si votre compte Azure ne dispose d’aucun accès au stockage Gen2. Une fois la clé d’accès validée, le compte Data Lake Storage Gen2 est développé automatiquement.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Envoyer des travaux à un cluster HDInsight avec Data Lake Storage Gen2

Quand vous envoyez un travail à un cluster HDInsight avec ADLS Gen2, vous êtes invité à entrer la clé d’accès de stockage si votre compte Azure ne dispose pas d’un accès en écriture au stockage Gen2. Le travail est envoyé avec succès une fois que la clé d’accès est validée.

![Outils Spark et Hive pour clé d’accès Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Vous pouvez obtenir la clé d’accès du compte de stockage sur le portail Azure. Pour plus d’informations, consultez [Afficher et copier des clés d’accès](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Supprimer le lien du cluster

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Spark / Hive: Supprimer le lien du cluster**.  

2. Sélectionnez le cluster pour lequel supprimer le lien.  

3. Passez en revue la vue **SORTIE** à des fins de vérification.  

## <a name="sign-out"></a>Se déconnecter  

À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Azure: Se déconnecter**.

## <a name="next-steps"></a>Étapes suivantes

Pour voir une vidéo de démonstration sur l’utilisation de Spark et Hive pour Visual Studio Code, consultez [Spark et Hive pour Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
