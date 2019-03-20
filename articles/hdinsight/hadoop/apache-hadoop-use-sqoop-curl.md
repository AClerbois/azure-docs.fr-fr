---
title: Utiliser Apache Hadoop Sqoop avec Curl dans HDInsight - Azure
description: Découvrez comment transmettre à distance des tâches Apache Sqoop vers HDInsight à l’aide de Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ad716e2ef5e597424c860378e7a63d5c2de53f54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834555"
---
# <a name="run-apache-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Exécuter des travaux Apache Sqoop avec Hadoop dans HDInsight via Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser Curl pour exécuter des tâches Apache Sqoop sur un cluster Apache Hadoop dans HDInsight.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des demandes HTTP brutes pour exécuter, analyser et récupérer des travaux Sqoop. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :


* Complète [Use Apache Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) pour configurer un environnement avec un cluster HDInsight et une base de données SQL Azure.
* [Curl](https://curl.haxx.se/). Curl est un outil pour transférer des données depuis ou vers un cluster HDInsight.
* [jq](https://stedolan.github.io/jq/). L’utilitaire jq est utilisé pour traiter les données JSON renvoyées à partir de demandes REST.


## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Envoyer les travaux Apache Sqoop avec Curl
> [!NOTE]  
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
> 
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L’API REST est sécurisée à l’aide de l’ [authentification de base](https://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.
> 
> 

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Vous devez recevoir une réponse ayant l'aspect suivant :

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G** : indique qu’il s’agit d’une demande GET.
     
     Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les requêtes. Le chemin d’accès, **/status**, indique que la demande doit renvoyer le statut de WebHCat (également appelé Templeton) au serveur. 
2. Pour envoyer un travail Sqoop, utilisez la commande suivante :

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Les paramètres utilisés dans cette commande sont les suivants :

   * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

       * **user.name** : l’utilisateur qui exécute la commande.

       * **command** : commande Sqoop à exécuter.

       * **statusdir** : le répertoire où seront enregistrés les statuts de cette tâche.

     Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Si le travail est terminé, l’état est **TERMINÉ**.
   
   > [!NOTE]  
   > Cette demande Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche ; jq est utilisé pour récupérer uniquement la valeur de statut.

4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasb:///exemple/data/sqoop/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/data/sqoop/curl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.
   
    Vous pouvez utiliser le portail Azure pour accéder aux objets BLOB stderr et stdout.  Vous pouvez également utiliser Microsoft SQL Server Management Studio pour vérifier les données chargées vers la table log4jlogs.

## <a name="limitations"></a>Limites
* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.
* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="summary"></a>Résumé
Comme illustré dans ce document, vous pouvez utiliser une demande HTTP brute pour exécuter, surveiller et afficher les résultats des travaux Sqoop sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez le <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guide de l’API REST Apache Sqoop</a>.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utiliser Apache Sqoop avec Apache Hadoop sur HDInsight](hdinsight-use-sqoop.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour d’autres articles HDInsight impliquant curl :
 
* [Créer des clusters Apache Hadoop à l’aide de l’API REST Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Exécuter des requêtes Apache Hive avec Apache Hadoop dans HDInsight à l’aide de REST](apache-hadoop-use-hive-curl.md)
* [Exécuter des tâches MapReduce avec Apache Hadoop sur HDInsight à l’aide de REST](apache-hadoop-use-mapreduce-curl.md)
* [Exécuter des tâches Apache Pig avec Apache Hadoop sur HDInsight à l’aide de cURL](apache-hadoop-use-pig-curl.md)



