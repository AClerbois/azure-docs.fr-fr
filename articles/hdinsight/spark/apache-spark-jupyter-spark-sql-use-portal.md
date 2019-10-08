---
title: 'Démarrage rapide : créer un cluster Spark dans HDInsight à l’aide du portail Azure'
description: Ce guide de démarrage rapide montre comment utiliser le portail Azure pour créer un cluster Apache Spark dans Azure HDInsight et exécuter Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: a4c7fe0d01bc9e5045cfe585c3f235636aa3dd22
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676985"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Démarrage rapide : créer un cluster Apache Spark dans Azure HDInsight à l’aide du portail Azure

Découvrez comment créer un cluster Apache Spark dans Azure HDInsight et comment exécuter des requêtes Spark SQL sur des tables Apache Hive. Apache Spark permet une analytique des données et des calculs sur cluster rapides à l’aide du traitement en mémoire. Pour en savoir plus sur le service Spark sur HDInsight, consultez [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md).

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer un cluster HDInsight Spark. Le cluster utilise des objets blob Stockage Azure comme stockage du cluster. Pour plus d’informations sur l’utilisation de Data Lake Storage Gen2, consultez [Démarrage rapide : Configurer des clusters dans HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> La facturation des clusters HDInsight est calculée au prorata des minutes écoulées, que vous les utilisiez ou non. Veillez à supprimer votre cluster une fois que vous avez fini de l’utiliser. Pour plus d’informations, consultez la section [Nettoyer les ressources](#clean-up-resources) de cet article.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-hdinsight-spark-cluster"></a>Créer un cluster HDInsight Spark

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Analytics** > **HDInsight**.

    ![Portail Azure : Créer une ressource HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight sur le portail Azure")

1. Sous **Informations de base**, fournissez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |Subscription  | Dans la liste déroulante, sélectionnez un abonnement Azure utilisé pour ce cluster. L’abonnement utilisé dans ce guide de démarrage rapide est un abonnement **Azure**. |
    |Resource group | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le nom du groupe de ressources utilisé dans ce guide de démarrage rapide est **myResourceGroup**. |
    |Nom du cluster | Attribuez un nom à votre cluster HDInsight Spark. Le nom du cluster utilisé dans ce guide de démarrage rapide est **myspark2019**.|
    |Location   | Sélectionnez l’emplacement du groupe de ressources. Le modèle utilise cet emplacement pour créer le cluster, ainsi que pour stocker le cluster par défaut. L’emplacement utilisé dans ce guide de démarrage rapide est **USA Est**. |
    |Type de cluster| Sélectionnez **Spark** comme type de cluster.|
    |Version du cluster|Lorsque le type de cluster est sélectionné, ce champ est automatiquement renseigné avec la version par défaut.|
    |Nom d’utilisateur de connexion au cluster| Entrez le nom d’utilisateur de connexion au cluster.  Le nom par défaut est *admin*. Vous utiliserez ce compte pour vous connecter au notebook Jupyter plus loin dans ce démarrage rapide. |
    |Mot de passe de connexion au cluster| Entrez le mot de passe de connexion au cluster. |
    |Nom d’utilisateur SSH (Secure Shell)| Entrez le nom d’utilisateur SSH. Le nom d’utilisateur SSH utilisé pour ce démarrage rapide est **sshuser**. Par défaut, ce compte a le même mot de passe que le compte *Nom d’utilisateur de connexion au cluster*. |

    ![Créer des configurations de base de cluster HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Créer des configurations de base de cluster HDInsight Spark")

    Sélectionnez **Suivant : Stockage >>** pour accéder à la page **Stockage**.

1. Sous **Stockage**, fournissez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |Type de stockage principal|Utilisez la valeur par défaut : **Stockage Azure**.|
    |Méthode de sélection|Utilisez la valeur par défaut : **Sélectionner dans la liste**.|
    |Compte de stockage principal|Utilisez la valeur renseignée automatiquement.|
    |Conteneur|Utilisez la valeur renseignée automatiquement.|

    ![Créer des configurations de base de cluster HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage-spark.png "Créer des configurations de base de cluster HDInsight Spark")

    Sélectionnez **Vérifier + créer** pour continuer.

1. Sous **Vérifier + créer**, sélectionnez **Créer**. La création du cluster prend environ 20 minutes. Il faut que le cluster soit créé pour pouvoir passer à la prochaine session.

Si vous rencontrez un problème avec la création de clusters HDInsight, c’est que vous n’avez peut-être pas les autorisations requises pour le faire. Pour plus d’informations, consultez [Exigences de contrôle d’accès](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Créer un bloc-notes Jupyter

Jupyter Notebook est un environnement de Notebook interactif qui prend en charge plusieurs langages de programmation. Le Notebook vous permet d’interagir avec vos données, de combiner du code avec le texte Markdown et d’effectuer des visualisations simples.

1. Ouvrez le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Clusters HDInsight**, puis le cluster que vous avez créé.

    ![Ouvrir le cluster HDInsight sur le Portail Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. Sur le Portail, sélectionnez **Tableaux de bord de cluster**, puis **Jupyter Notebook**. À l’invite (le cas échéant), entrez les informations d’identification du cluster.

   ![Ouvrir Jupyter Notebook pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Ouvrir Jupyter Notebook pour exécuter une requête interactive Spark SQL")

1. Sélectionnez **Nouveau** > **PySpark** pour créer un Notebook.

   ![Créer un bloc-notes Jupyter Notebook pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Créer un bloc-notes Jupyter Notebook pour exécuter une requête interactive Spark SQL")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb).

## <a name="run-spark-sql-statements"></a>Exécuter des instructions Spark SQL

SQL (Structured Query Language) est le langage le plus courant et le plus largement utilisé pour interroger et définir des données. Spark SQL fonctionne en tant qu’extension d’Apache Spark pour le traitement des données structurées, à l’aide de la syntaxe SQL classique.

1. Vérifiez que le noyau est prêt. Le noyau est prêt lorsque vous voyez un cercle vide à côté du nom du noyau dans le bloc-notes. Un cercle plein indique que le noyau est occupé.

    ![Requête Apache Hive dans HDInsight Spark1](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Requête Hive dans HDInsight Spark1")

    Lorsque vous démarrez le bloc-notes pour la première fois, le noyau effectue certaines tâches en arrière-plan. Attendez que le noyau soit prêt.

1. Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. La commande répertorie les tables Hive sur le cluster :

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Lorsque vous utilisez un bloc-notes Jupyter avec votre cluster HDInsight Spark, vous obtenez une présélection `sqlContext` que vous pouvez utiliser pour exécuter des requêtes Hive à l’aide de Spark SQL. `%%sql` demande à Jupyter Notebook d’utiliser la présélection `sqlContext` pour exécuter la requête Hive. La requête extrait les 10 premières lignes d’une table Hive (**hivesampletable**) qui est disponible par défaut sur tous les clusters HDInsight. Il faut environ 30 secondes pour obtenir les résultats. Le résultat se présente ainsi :

    ![Requête Apache Hive dans HDInsight Spark2](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Requête Hive dans HDInsight Spark2")

    À chaque exécution d’une requête dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit.

1. Exécutez une autre requête pour afficher les données dans `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    L’écran doit s’actualiser pour afficher la sortie de requête.

    ![Sortie de requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Sortie de requête Hive dans HDInsight Spark")

1. Dans le menu **Fichier** du Notebook, sélectionnez **Fermer et interrompre**. L’arrêt du bloc-notes libère les ressources de cluster.

## <a name="clean-up-resources"></a>Supprimer des ressources

HDInsight enregistre vos données dans le Stockage Azure ou Azure Data Lake Storage, ce qui vous permet de supprimer un cluster sans risque s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Si vous prévoyez d’utiliser tout de suite le tutoriel mentionné sous [Étapes suivantes](#next-steps), il peut être intéressant de conserver le cluster.

Revenez au Portail Azure, puis sélectionnez **Supprimer**.

![Portail Azure : supprimer un cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Supprimer un cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster HDInsight Spark et à exécuter une requête Spark SQL de base. Passez au tutoriel suivant pour apprendre à utiliser un cluster HDInsight Spark pour exécuter des requêtes interactives sur des exemples de données.

> [!div class="nextstepaction"]
> [Exécuter des requêtes interactives sur Apache Spark](./apache-spark-load-data-run-query.md)
