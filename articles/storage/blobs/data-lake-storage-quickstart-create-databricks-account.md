---
title: 'Démarrage rapide : Analyser des données dans Azure Data Lake Storage Gen2 à l’aide d’Azure Databricks | Microsoft Docs'
description: Apprenez à exécuter un travail Spark sur Azure Databricks à l’aide du portail Azure et d’un compte de stockage Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: 1b5f05f3cddea986230327165399b0fe530e361b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588073"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Démarrage rapide : Analyser des données dans Azure Data Lake Storage Gen2 à l’aide d’Azure Databricks

Ce guide de démarrage rapide montre comment exécuter un travail Apache Spark à l’aide d’Azure Databricks pour effectuer une analyse des données stockées dans un compte de stockage pour lequel Azure Data Lake Storage Gen2 est activé.

Dans le cadre du travail Spark, vous allez analyser des données d’abonnement à une station de radio pour obtenir des insights sur l’utilisation gratuite/payante en fonction de données démographiques.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Créez un compte de stockage Data Lake Gen2. Consultez [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

  Collez le nom du compte de stockage dans un fichier texte. Vous en aurez besoin dans quelques instants.

*  Créer un principal de service. Consultez [Procédure : Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Vous devrez faire certaines choses spécifiques pendant que vous suivrez les étapes décrites dans cet article.

   :heavy_check_mark: Au cours des étapes décrites dans la section [Attribuer un rôle à l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) de l’article, veillez à affecter le rôle **Contributeur aux données Blob du stockage** au principal de service.

   > [!IMPORTANT]
   > Veillez à attribuer le rôle dans l’étendue du compte de stockage Data Lake Storage Gen2. Vous pouvez attribuer un rôle à l’abonnement ou au groupe de ressources parent, mais des erreurs d’autorisation sont générées tant que ces attributions de rôles ne sont pas propagées au compte de stockage.

   :heavy_check_mark: Au cours des étapes indiquées dans la section [Obtenir les valeurs de connexion](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de l’article, collez les valeurs de l’ID de locataire, de l’ID d’application et de la clé d’authentification dans un fichier texte. Vous en aurez besoin bientôt.

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure.

1. Dans le portail Azure, sélectionnez **Créer une ressource** >  **Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks sur le portail Azure")

2. Sous **Service Azure Databricks**, renseignez les valeurs pour créer un espace de travail Databricks.

    ![Créer un espace de travail Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

    Renseignez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **USA Ouest 2**. N’hésitez pas à sélectionner une autre région publique si vous préférez.        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.

3. La création de l’espace de travail prend un peu de temps. Durant la création de l’espace de travail, la vignette **Envoi du déploiement pour Azure Databricks** apparaît sur le côté droit. Pour voir la vignette, vous devrez peut-être faire défiler votre tableau de bord vers la droite. Une barre de progression s’affiche également vers le haut de l’écran. Vous pouvez surveiller la progression de la zone souhaitée.

    ![Vignette de déploiement Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Nouveau** > **Cluster**.

    ![Databricks sur Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

    * Entrez un nom pour le cluster.
    * Créez un cluster avec le runtime **5.1**.
    * Veillez à cocher la case **Arrêter après 120 minutes d’inactivité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.

4. Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

Pour plus d’informations sur la création de clusters, consultez [Créer un cluster Spark dans Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-file-system"></a>Créer un système de fichiers de compte de stockage

Dans cette section, vous créez un bloc-notes dans l’espace de travail Azure Databricks, puis exécutez les extraits de code pour configurer le compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’espace de travail Azure Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Dans le volet gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Créer un notebook dans Databricks")

3. Dans la boîte de dialogue **Créer un bloc-notes**, entrez un nom pour le bloc-notes. Sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Sélectionnez **Créer**.

4. Copiez et collez le bloc de code suivant dans la première cellule, mais n’exécutez pas ce code pour l’instant.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Ce bloc de code accède directement au point de terminaison Data Lake Gen2 via OAuth. Toutefois, il existe d’autres moyens de connecter l’espace de travail Databricks à votre compte Data Lake Storage Gen2. Par exemple, vous pouvez monter le système de fichiers à l’aide d’OAuth ou utiliser un accès direct par clé partagée. <br>Pour voir des exemples de ces approches, consultez l’article [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) sur le site web Azure Databricks.

5. Dans ce bloc de code, remplacez les valeurs d’espace réservé `storage-account-name`, `application-id`, `authentication-id` et `tenant-id` par les valeurs que vous avez collectées au moment de la création du principal de service. Affectez le nom de système de fichiers de votre choix à la valeur d’espace réservé `file-system-name`.

    > [!NOTE]
    > Dans un environnement de production, songez à stocker votre clé d’authentification dans Azure Databricks. Ensuite, ajoutez une clé de recherche à votre bloc de code au lieu de la clé d’authentification. Après avoir suivi ce guide de démarrage rapide, consultez l’article [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) sur le site web Azure Databricks pour voir des exemples de cette approche.

6. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

## <a name="ingest-sample-data"></a>Ingérer des exemples de données

Avant de commencer cette section, vous devez effectuer les prérequis suivants :

Entrez le code suivant dans une cellule du bloc-notes :

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter le code.

À présent, dans une nouvelle cellule en dessous de celle-ci, entrez le code suivant et remplacez les valeurs entre crochets par les valeurs que vous avez utilisées plus tôt :

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter le code.

## <a name="run-a-spark-sql-job"></a>Exécuter un travail SQL Spark

Effectuez les tâches suivantes pour exécuter une tâche SQL Spark sur les données.

1. Exécutez une instruction SQL pour créer une table temporaire en utilisant les données de l’exemple de fichier de données JSON, **small_radio_json.json**. Dans l’extrait de code suivant, remplacez les valeurs d’espace réservé par le nom de votre système de fichiers et le nom de votre compte de stockage. À l’aide du bloc-notes que vous avez créé précédemment, collez l’extrait de code dans une nouvelle cellule de code du bloc-notes, puis appuyez sur Maj+Entrée.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
    )
    ```

    Une fois la commande terminée, vous avez toutes les données du fichier JSON sous forme de table dans le cluster Databricks.

    La commande magic `%sql` vous permet d’exécuter du code SQL à partir du notebook, même si celui-ci est d’un autre type. Pour plus d’informations, consultez [Mélange de langages dans un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

2. Examinons un instantané des données JSON pour mieux comprendre la requête que vous exécutez. Collez l’extrait de code suivant dans une cellule de code, puis appuyez sur **Maj+Entrée**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Vous voyez une sortie tabulaire, comme celle qui est montrée dans la capture d’écran suivante (seules certaines colonnes apparaissent) :

    ![Exemples de données JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Exemples de données JSON")

    Parmi d’autres informations, les exemples de données indiquent quel est le sexe des auditeurs d’une chaîne de radio (le nom de la colonne est **gender**), et si leur abonnement est gratuit ou payant (le nom de colonne est **level**).

4. Vous créez maintenant une représentation visuelle de ces données de façon à montrer, pour chaque sexe, le nombre d’utilisateurs avec un abonnement gratuit et avec un abonnement payant. Dans le bas de la sortie tabulaire, cliquez sur l’icône **Graphique à barres**, puis cliquez sur **Options de traçage**.

    ![Créer un graphique à barres](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Créer un graphique à barres")

5. Dans **Personnaliser le traçage**, faites un glisser-déplacer des valeurs comme indiqué dans la capture d’écran.

    ![Personnaliser le graphique à barres](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Personnaliser le graphique à barres")

    - Définissez **Clés** sur **gender**.
    - Définissez **Regroupements de séries** sur **level**.
    - Définissez **Valeurs** sur **level**.
    - Définissez **Agrégation** sur **COUNT**.

6. Cliquez sur **Appliquer**.

7. La sortie montre la représentation visuelle, comme illustré dans la capture d’écran suivante :

     ![Personnaliser le graphique à barres](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Personnaliser le graphique à barres")

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez fini la lecture de cet article, vous pouvez arrêter le cluster. Dans l’espace de travail Azure Databricks, sélectionnez **Clusters** et recherchez le cluster que vous voulez arrêter. Déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Terminer**.

![Arrêter un cluster Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous n’arrêtez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** lors de la création du cluster. Si vous définissez cette option, le cluster s’arrête après le délai d’inactivité spécifié.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un cluster Spark dans Azure Databricks et exécuté un travail Spark à l’aide des données dans un compte de stockage dans Data Lake Storage Gen2 activé. Vous pouvez également consulter [Sources de données Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) pour découvrir comment importer des données à partir d’autres sources de données dans Azure Databricks. Passez à l’article suivant pour savoir comment effectuer une opération ETL (extraction, transformation et chargement de données) à l’aide d’Azure Databricks.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
