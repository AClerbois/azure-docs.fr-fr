---
title: Streaming Apache Spark avec Apache Kafka - Azure HDInsight
description: Découvrez comment utiliser Apache Spark pour diffuser des données vers ou depuis Apache Kafka à l’aide de DStreams. Dans cet exemple, vous diffusez des données à l’aide d’un bloc-notes Jupyter à partir de Spark sur HDInsight.
keywords: exemple kafka, zookeeper kafka, kafka de diffusion spark, exemple kafka de diffusion spark
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 110357a7a84aa8cc6bd7dcb5bbef9ca02879a9cb
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494284"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Exemple Apache Spark Streaming (DStream) avec Apache Kafka sur HDInsight

Découvrez comment utiliser [Apache Spark](https://spark.apache.org/) pour diffuser des données vers ou à partir d’[Apache Kafka](https://kafka.apache.org/) sur HDInsight à l’aide de [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Cet exemple utilise un bloc-notes [Jupyter Notebook](https://jupyter.org/) qui s’exécute sur le cluster Spark.

> [!NOTE]
> Les étapes décrites dans ce document créent un groupe de ressources Azure qui contient à la fois un Spark sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Spark de communiquer directement avec le cluster Kafka.
>
> Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

> [!IMPORTANT]
> Cet exemple utilise DStreams, une technologie de diffusion en continu Spark plus ancienne. Pour obtenir un exemple utilisant des fonctionnalités de diffusion en continu Spark plus récentes, consultez le document [Spark Structured Streaming avec Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui communique avec Kafka doit se trouver sur le même réseau virtuel Azure que les nœuds du cluster Kafka. Pour cet exemple, les clusters Kafka et Spark sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme des clusters Spark et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Bien que Kafka soit lui-même limité à la communication au sein du réseau virtuel, d’autres services sur le cluster comme SSH et Ambari sont accessibles sur Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Même si vous pouvez créer un réseau virtuel Azure, et des clusters Kafka et Spark manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka et Spark sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle Azure Resource Manager se trouve dans **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Pour garantir la disponibilité de Kafka sur HDInsight, votre cluster doit contenir au moins trois nœuds Worker. Ce modèle crée un cluster Kafka qui contient trois nœuds de travail.

    Ce modèle crée un cluster HDInsight 3.6 pour Kafka et Spark.

2. Utilisez les informations suivantes pour remplir les entrées dans la section **Déploiement personnalisé** :
   
    ![Déploiement HDInsight personnalisé](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.

    * **Emplacement** : choisissez un emplacement proche de vous géographiquement.

    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Spark et Kafka. Par exemple, si vous entrez **hdistreaming**, un cluster Spark nommé __spark-hdistreaming__ et un cluster Kafka nommé **kafka-hdistreaming** sont créés.

    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour les clusters Spark et Kafka.

    * **Mot de passe du cluster** : mot de passe utilisateur Admin pour les clusters Spark et Kafka.

    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Spark et Kafka.

    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Spark et Kafka.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Enfin, sélectionnez **Achat**. La création des clusters prend environ 20 minutes.

Dès que les ressources sont créées, une page de résumé s’affiche.

![Résumé du groupe de ressources pour le réseau virtuel et les clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **spark-BASENAME** et **kafka-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="use-the-notebooks"></a>Obtenir les blocs-notes

Le code de l’exemple décrit dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Pour terminer cet exemple, suivez les étapes du fichier `README.md`.

## <a name="delete-the-cluster"></a>Supprimer le cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. La suppression de ce groupe supprime toutes les ressources créées par la suite dans ce document, le réseau virtuel Azure et le compte de stockage utilisé par les clusters.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser Spark pour lire et écrire dans Kafka. Utilisez les liens suivants pour découvrir d’autres façons de travailler avec Kafka :

* [Prise en main d’Apache Kafka sur HDInsight](kafka/apache-kafka-get-started.md)
* [Utiliser MirrorMaker pour créer un réplica d’Apache Kafka sur HDInsight](kafka/apache-kafka-mirroring.md)
* [Utiliser Apache Storm avec Apache Kafka sur HDInsight](hdinsight-apache-storm-with-kafka.md)

