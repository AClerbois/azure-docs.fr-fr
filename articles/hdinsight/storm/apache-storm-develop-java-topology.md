---
title: Exemple de topologie Java Apache Storm - Azure HDInsight
description: Découvrez comment créer des topologies Apache Storm en Java en créant un exemple de topologie de comptage de mots.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm,exemple apache storm,storm java,exemple de topologie storm
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2c1c144899189e2320d1388fca848fa3d7ec2257
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122078"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Créer une topologie Apache Storm en Java

Découvrez comment créer une topologie basée sur Java pour [Apache Storm](https://storm.apache.org/). Ici, vous créez une topologie Storm qui implémente une application de comptage de mots. Vous utilisez [Apache Maven](https://maven.apache.org/) pour générer et empaqueter le projet. Ensuite, vous allez apprendre à définir la topologie à l’aide de la [Apache Storm Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) framework.

Après avoir suivi les étapes décrites dans ce document, vous pourrez déployer la topologie sur Apache Storm sur HDInsight.

> [!NOTE]  
> Une version complète des exemples de topologie Storm créés dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Conditions préalables

* [Kit de développeur Java (JDK) version 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) correctement [installé](https://maven.apache.org/install.html) en fonction d’Apache.  Maven est un système de génération de projet pour les projets Java.

## <a name="test-environment"></a>Environnement de test
L’environnement utilisé pour cet article était un ordinateur exécutant Windows 10.  Les commandes ont été exécutées dans une invite de commandes, et les différents fichiers ont été modifiés avec le bloc-notes.

À partir d’une invite de commandes, entrez les commandes ci-dessous pour créer un environnement de travail :

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Création d’un projet Maven

Entrez la commande suivante pour créer un projet Maven nommé **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Cette commande crée un répertoire nommé `WordCount` à l’emplacement actuel, qui contient un projet Maven de base. La deuxième commande modifie le répertoire de travail actuel pour `WordCount`. La troisième commande crée un nouveau répertoire, `resources`, qui sera utilisé ultérieurement.  Le répertoire `WordCount` contient les éléments suivants :

* `pom.xml`: contient les paramètres du projet Maven.
* `src\main\java\com\microsoft\example`: contient votre code d’application.
* `src\test\java\com\microsoft\example`: contient des tests pour votre application.  

### <a name="remove-the-generated-example-code"></a>Supprimer l’exemple de code généré

Supprimer les fichiers de l’application et le test généré `AppTest.java`, et `App.java` en entrant les commandes ci-dessous :

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Ajouter des référentiels Maven

HDInsight étant basé sur Hortonworks Data Platform (HDP), nous recommandons d’utiliser le référentiel Hortonworks pour télécharger les dépendances pour vos projets Apache Storm.  

Ouvrez `pom.xml` en entrant la commande ci-dessous :

```cmd
notepad pom.xml
```

Puis ajoutez le code XML suivant après le `<url> https://maven.apache.org</url>` ligne :

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Ajout de propriétés

Maven vous permet de définir des valeurs au niveau du projet appelées propriétés. Dans `pom.xml`, ajoutez le texte suivant après le `</repositories>` ligne :

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Vous pouvez maintenant utiliser cette valeur dans d’autres sections de `pom.xml`. Par exemple, lorsque vous spécifiez la version des composants Storm, vous pouvez utiliser `${storm.version}` plutôt que de coder en dur une valeur.

## <a name="add-dependencies"></a>Ajout de dépendances

Ajoutez une dépendance pour les composants Storm. Dans `pom.xml`, ajoutez le texte suivant dans la `<dependencies>` section :

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Au moment de la compilation, Maven utilise ces informations pour rechercher `storm-core` dans le référentiel Maven. Il recherche d’abord dans le référentiel sur votre ordinateur local. Si les fichiers ne s’y trouvent pas, Maven les télécharge à partir du référentiel Maven public et les stocke dans le référentiel local.

> [!NOTE]  
> Notez la ligne `<scope>provided</scope>` dans cette section. Ce paramètre indique à Maven d’exclure **storm-core** de tous les fichiers JAR créés, étant donné que ce dernier est fourni par le système.

## <a name="build-configuration"></a>Configuration de build

Les plug-ins Maven permettent de personnaliser les étapes de génération du projet, telles que la manière dont le projet est compilé ou empaqueté dans un fichier jar. Dans `pom.xml`, ajoutez le texte suivant directement au-dessus du `</project>` ligne.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Cette section est utilisée pour ajouter des plug-ins, des ressources et d’autres options de configuration de build. Pour des informations complètes de la `pom.xml` de fichiers, consultez [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Ajout de plug-ins

* **Le plug-in de EXEC Maven**

    Pour les topologies Apache Storm implémentées en Java, le [plug-in Exec Maven ](https://www.mojohaus.org/exec-maven-plugin/) est utile, car il permet d’exécuter facilement la topologie localement dans votre environnement de développement. Ajoutez le code suivant à la section `<plugins>` du fichier `pom.xml` pour inclure le plug-in Exec Maven :
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
            <goals>
                <goal>exec</goal>
            </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler Plugin**

    Le [plug-in du compilateur Maven Apache](https://maven.apache.org/plugins/maven-compiler-plugin/) est un autre plug-in utile, car il sert à modifier les options de compilation. Modifier la version de Java que Maven utilise pour la source et la cible pour votre application.
    
  * Pour HDInsight __3.4 ou antérieure__, définissez la source et la cible de la version Java sur __1.7__.
    
  * Pour HDInsight __3.5__, définissez la source et la cible de la version Java sur __1.8__.
    
    Ajoutez le texte ci-après à la section `<plugins>` du fichier `pom.xml` pour inclure le plug-in du compilateur Maven Apache. Étant donné que cet exemple spécifie la valeur 1.8, la version cible de HDInsight est 3.5.
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>Configuration des ressources

La section des ressources vous permet d’inclure des ressources autres que du code comme les fichiers de configuration requis par les composants de la topologie. Pour cet exemple, ajoutez le texte suivant dans le `<resources>` section de la `pom.xml` fichier.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Cet exemple ajoute le répertoire des ressources à la racine du projet (`${basedir}`) en tant qu’emplacement contenant des ressources, et inclut le fichier nommé `log4j2.xml`. Ce fichier est utilisé pour configurer les informations qui sont enregistrées par la topologie.

## <a name="create-the-topology"></a>Création de la topologie

Une topologie Apache Storm basée sur Java comprend trois composants que vous devez créer (ou référencer) en tant que dépendance.

* **Spouts** : ils lisent les données provenant de sources externes et émettent des flux de données dans la topologie.

* **Bolts** : ils effectuent le traitement des flux de données émis par les spouts ou les autres bolts et émettent un ou plusieurs flux.

* **Topologie** : elle définit l’organisation des spouts et des bolts, et fournit le point d’entrée pour la topologie.

### <a name="create-the-spout"></a>Création du spout

Afin de réduire les besoins de configuration de sources de données externes, le spout suivant émet des phrases aléatoires. Il s’agit d’une version modifiée d’un spout fourni dans les [exemples Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Bien que cette topologie utilise un seul spout, d’autres peuvent en avoir plusieurs, qui alimentent la topologie avec des données provenant de sources différentes.

Entrez la commande ci-dessous pour créer et ouvrir un nouveau fichier `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Puis copiez et collez le code java ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Pour obtenir un exemple de spout effectuant des lectures à partir d’une source de données externe, consultez un des exemples suivants :
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java) : exemple de spout qui lit à partir de Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka) : spout qui lit à partir de Kafka.


### <a name="create-the-bolts"></a>Création des bolts

Les bolts gèrent le traitement des données. Les bolts peuvent tout faire : calculs, persistance, communication avec des composants externes, etc. Cette topologie utilise deux bolts :

* **SplitSentence** : fractionne les phrases émises par **RandomSentenceSpout** en mots.

* **WordCount** : compte le nombre d’occurrences de chaque mot.


#### <a name="splitsentence"></a>SplitSentence

Entrez la commande ci-dessous pour créer et ouvrir un nouveau fichier `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Puis copiez et collez le code java ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Entrez la commande ci-dessous pour créer et ouvrir un nouveau fichier `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Puis copiez et collez le code java ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Définition de la topologie

La topologie lie les spouts et les bolts dans un graphique, qui définit la circulation des données entre les composants. Elle fournit également des indicateurs de parallélisme que Storm utilise lors de la création des instances de composants au sein du cluster.

L’image ci-dessous illustre un diagramme de base des composants de cette topologie.

![schéma montrant la disposition des spouts et bolts](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Pour implémenter la topologie, entrez la commande suivante pour créer et ouvrir un nouveau fichier `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Puis copiez et collez le code java ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Configuration de la journalisation

Storm utilise [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) pour journaliser les informations. Si vous ne configurez pas la journalisation, la topologie émet des informations de diagnostic. Pour contrôler ce qui est enregistré, créez un fichier nommé `log4j2.xml` dans le `resources` répertoire en entrant la commande ci-dessous :

```cmd
notepad resources\log4j2.xml
```

Puis copiez et collez le texte XML ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Ce code XML configure un nouvel enregistreur pour la classe `com.microsoft.example`, qui inclut les composants de cet exemple de topologie. Le niveau est défini pour effectuer le suivi de cet enregistreur d’événements, ce qui capture les informations de journalisation émises par les composants dans cette topologie.

La section `<Root level="error">` configure le niveau racine de journalisation (tout ce qui ne figure pas dans `com.microsoft.example`) pour enregistrer uniquement les informations d’erreur.

Pour plus d’informations sur la configuration de la journalisation pour Log4j 2, consultez [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Storm version 0.10.0 et ultérieure utilisent Log4j 2.x. Les versions antérieures de Storm utilisaient Log4j 1.x, qui utilisait un autre format pour la configuration du journal. Pour plus d’informations sur la configuration antérieure, accédez à l’adresse [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Test local de la topologie

Après avoir enregistré les fichiers, utilisez la commande suivante pour tester la topologie localement.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Pendant son exécution, la topologie affiche les informations de démarrage. Le texte ci-après est un exemple de sortie de statistiques :

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Cet exemple de journal indique que le mot « and » a été utilisé 113 fois. Le décompte continue d’augmenter tant que la topologie s’exécute, car le Spout émet continuellement les mêmes phrases.

Il existe un intervalle de 5 secondes entre l’émission des mots et les décomptes. Le composant **WordCount** est configuré pour émettre des informations uniquement lors de la réception d’un tuple de graduation. Il demande que tuples de graduation soient remis uniquement toutes les cinq secondes.

## <a name="convert-the-topology-to-flux"></a>Convertir la topologie vers Flux

[Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) est une nouvelle infrastructure disponible avec Storm 0.10.0 et versions supérieures qui vous permet de séparer la configuration de la mise en œuvre. Vos composants sont toujours définis dans Java, mais la topologie est définie à l’aide d’un fichier YAML. Vous pouvez empaqueter une définition de la topologie par défaut avec votre projet, ou utiliser un fichier autonome lors de l’envoi de la topologie. Lors de l’envoi de la topologie à Storm, vous pouvez utiliser des variables d’environnement ou des fichiers de configuration pour remplir les valeurs dans la définition de la topologie YAML.

Le fichier YAML définit les composants à utiliser pour la topologie et le flux de données entre eux. Vous pouvez inclure un fichier YAML dans le fichier jar ou utiliser un fichier YAML externe.

Pour plus d’informations sur Flux, voir [Flux framework (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html) (Framework Flux).

> [!WARNING]  
> En raison d’un [bogue (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) lié à Storm 1.0.1, vous devrez peut-être installer un [environnement de développement Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html) pour exécuter localement les topologies Flux.

1. Auparavant, `WordCountTopology.java` définissait la topologie, mais il n’est pas nécessaire avec Flux. Supprimez le fichier avec la commande suivante :

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. Entrez la commande ci-dessous pour créer et ouvrir un nouveau fichier `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    Puis copiez et collez le texte ci-dessous dans le nouveau fichier.  Puis fermez le fichier.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Entrez la commande ci-dessous pour ouvrir `pom.xml` pour rendre les modifications décrites ci-dessous :

    ```cmd
    notepad pom.xml
    ```

   * Ajoutez la nouvelle dépendance suivante dans la section `<dependencies>` :

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * Ajoutez le plug-in suivant à la section `<plugins>` . Ce plug-in gère la création d’un package (fichier jar) pour le projet et applique certaines transformations spécifiques à Flux lors de la création du package.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * Dans le **exec-maven-plugin** `<configuration>` section, remplacez la valeur de `<mainClass>` de `${storm.topology}` à `org.apache.storm.flux.Flux`. Ce paramètre permet à Flux de gérer l’exécution de la topologie localement dans l’environnement de développement.

   * Dans le `<resources>` , ajoutez le code suivant à `<includes>`. Cela inclut le fichier YAML définissant la topologie en tant que partie du projet.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Tester la topologie Flux localement

1. Entrez la commande suivante pour compiler et exécuter la topologie Flux à l’aide de Maven :

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Si votre topologie utilise des bits Storm 1.0.1, cette commande échoue. Cette défaillance est provoquée par [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055). Au lieu de cela, [installez Storm dans votre environnement de développement](https://storm.apache.org/releases/current/Setting-up-development-environment.html) et utilisez les étapes suivantes :
    >
    > Si vous avez [installé Storm dans votre environnement de développement](https://storm.apache.org/releases/current/Setting-up-development-environment.html), vous pouvez utiliser les commandes suivantes à la place :
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Le paramètre `--local` exécute la topologie en mode local dans votre environnement de développement. Le paramètre `-R /topology.yaml` utilise la ressource de fichier `topology.yaml` à partir du fichier jar pour définir la topologie.

    Pendant son exécution, la topologie affiche les informations de démarrage. Le texte ci-après constitue un exemple de sortie :

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Il existe un délai de 10 secondes entre chaque lot d’informations enregistrées.

2. Créer un nouveau yaml de topologie à partir du projet.
 
    a. Entrez la commande ci-dessous pour ouvrir `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. Recherchez la section suivante et remplacez la valeur de `10` à `5`. Cela a pour effet de modifier l’intervalle entre les émissions de lots de comptes de mots de 10 à 5 secondes.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. Enregistrer le fichier sous `newtopology.yaml`.

3. Pour exécuter la topologie, entrez la commande suivante :

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Ou, si vous avez Storm dans votre environnement de développement :

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     Cette commande utilise le `newtopology.yaml` en tant que définition de la topologie. Étant donné que nous n’avons pas inclus le paramètre `compile`, Maven utilise la version du projet créée aux étapes précédentes.

    Une fois que la topologie démarre, vous devriez remarquer que l’heure entre les lots émis a changé pour refléter la valeur dans `newtopology.yaml`. Par conséquent, vous pouvez voir que vous pouvez modifier votre configuration via un fichier YAML sans avoir à recompiler la topologie.

Pour plus d’informations sur ces fonctionnalités et d’autres fonctionnalités du framework Flux, voir [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) est une abstraction de haut niveau fournie par Storm. Il prend en charge le traitement avec état. Le principal avantage de Trident est qu’il peut garantir que chaque message qui entre dans la topologie n’est traité qu’une seule fois, Sans utilisation de Trident, votre topologie peut uniquement garantir que les messages sont traités au moins une fois. Il existe aussi d'autres différences, comme les composants intégrés pouvant être utilisés, plutôt que de créer des bolts. Les Bolts sont remplacés par des composants moins génériques, tels que des filtres, des projections et des fonctions.

Les applications Trident peuvent être créées à l’aide de projets Maven. Les étapes de base sont les mêmes que celles présentées plus haut dans cet article, seul le code est différent. Trident est également inutilisable (actuellement) avec l’infrastructure Flux.

Pour plus d’informations sur Trident, consultez la [Présentation de l’API Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à créer une topologie Apache Storm à l’aide de Java. Apprenez maintenant à effectuer les actions suivantes :

* [Déploiement et gestion des topologies Apache Storm sur HDInsight](apache-storm-deploy-monitor-topology.md)

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Vous trouverez davantage d’exemples de topologies Apache Storm en vous rendant sur [Exemples de topologies Apache Storm sur HDInsight](apache-storm-example-topology.md).
