---
title: Générer des recommandations à l’aide de Apache Mahout et HDInsight (SSH) - Azure
description: Apprenez à utiliser la bibliothèque à apprentissage automatique Apache Mahout pour générer des recommandations de films avec HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709085"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Génération de recommandations de films à l’aide d’Apache Mahout avec Apache Hadoop sous Linux dans HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout](https://mahout.apache.org) avec Azure HDInsight pour générer des recommandations de films

Mahout est une [apprentissage](https://en.wikipedia.org/wiki/Machine_learning) bibliothèque pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, tel que le filtrage, la classification et le clustering. Dans cet article, vous utilisez un moteur de recommandation pour générer des recommandations en fonction des films vus par vos amis.

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Apache Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Contrôle de version Apache Mahout

Pour plus d'informations sur la version de Mahout dans HDInsight, consultez [Versions HDInsight et composants Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Présentation des recommandations

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données au format `userID`, `itemId` et `prefValue` (les préférences pour l’élément). Mahout peut alors effectuer une analyse des co-occurrences afin de déterminer que *les utilisateurs ayant une préférence pour un élément ont également une préférence pour ces autres éléments*. Mahout détermine ensuite des utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

Voici un exemple simplifié de cette méthode pour les données de films :

* **Co-occurrence** : Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L’Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l’un de ces films aiment également les deux autres.

* **Co-occurrence** : Robert et Alice ont aussi aimé *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces trois films.

* **Recommandation par similarité** : Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d’autres personnes ayant les mêmes goûts que lui ont aimés aussi, mais que Jean n’a pas encore vus (aimés/notés). Dans ce cas, Mahout recommande *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*.

### <a name="understanding-the-data"></a>Vue d’ensemble des données

Heureusement, [GroupLens Research](https://grouplens.org/datasets/movielens/) fournit des données d’évaluation de films dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster à `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` et `user-ratings.txt`. Le fichier `user-ratings.txt` est utilisé pendant l’analyse. Le fichier `moviedb.txt` est utilisé pour fournir des informations de texte conviviales au moment de l’affichage des résultats.

Les données contenues dans le fichier user-ratings.txt respectent la structure suivante : `userID`, `movieID`, `userRating` et `timestamp`, ce qui indique la note attribuée par chaque utilisateur à un film. Voici un exemple de ces données :

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Exécution de l'analyse

À partir d’une connexion SSH avec le cluster, utilisez la commande suivante pour exécuter la tâche de recommandation :

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> L’exécution de la tâche peut durer quelques minutes et entraîner l’exécution de plusieurs tâches MapReduce.

## <a name="view-the-output"></a>Affichage du résultat

1. Une fois la tâche terminée, utilisez la commande suivante pour afficher le résultat généré :

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    La sortie se présente comme suit :

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La première colonne est `userID`. Les valeurs contenues entre  « [ » et « ] » sont `movieId`:`recommendationScore`.

2. Vous pouvez utiliser la sortie ainsi que le fichier moviedb.txt pour fournir plus d’informations sur les recommandations. Tout d’abord, copiez les fichiers localement en utilisant les commandes suivantes :

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Cette commande copie les données de sortie dans un fichier nommé **recommendations.txt** dans le répertoire actif, avec les fichiers de données de film.

3. Utilisez la commande suivante pour créer un script Python afin de rechercher les noms de film présents dans les données de sortie des recommandations :

    ```bash
    nano show_recommendations.py
    ```

    Lorsque l’éditeur s’ouvre, utilisez le texte suivant comme contenu du fichier :

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Appuyez sur **Ctrl-X**, **O**, et enfin **Entrée** pour enregistrer les données.

4. Exécutez le script Python. La commande suivante suppose que vous avez ouvert le répertoire dans lequel tous les fichiers ont été téléchargés :

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    La commande examinera les recommandations générées pour l’utilisateur ID 4.

   * Le fichier **user-ratings.txt** est utilisé pour récupérer les films évalués.

   * Le fichier **moviedb.txt** est utilisé pour récupérer les noms des films.

   * Le fichier **recommendations.txt** est utilisé pour récupérer les recommandations de films pour cet utilisateur.

     Le résultat de cette commande doit ressembler au texte suivant :

       Seven Years in Tibet (1997), score=5.0   Indiana Jones and the Last Crusade (1989), score=5.0   Jaws (1975), score=5.0   Sense and Sensibility (1995), score=5.0   Independence Day (ID4) (1996), score=5.0   My Best Friend’s Wedding (1997), score=5.0   Jerry Maguire (1996), score=5.0   Scream 2 (1997), score=5.0   Time to Kill, A (1996), score=5.0

## <a name="delete-temporary-data"></a>Suppression des données temporaires

Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C’est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un chemin spécifique afin de pouvoir les effacer facilement. Pour supprimer les fichiers temporaires, utilisez la commande suivante :

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Si vous souhaitez réexécuter la commande, vous devez également supprimer le répertoire de sortie. Utilisez la commande suivante pour supprimer ce répertoire :
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Mahout, découvrez d’autres façons d’utiliser les données dans HDInsight :

* [Apache Hive avec HDInsight](hdinsight-use-hive.md)
* [Apache Pig avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)