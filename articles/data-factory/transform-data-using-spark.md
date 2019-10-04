---
title: Transformer des données à l’aide d’une activité Spark dans Azure Data Factory | Microsoft Docs
description: Découvrez comment transformer des données en exécutant des programmes Spark à partir d’un pipeline de fabrique de données Azure et à l’aide de l’activité de Spark.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c493dbc99edc794dd5a261dfc004c2c8c1cb6d52
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312085"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformer des données à l’aide d’une activité Spark dans Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-spark.md)
> * [Version actuelle](transform-data-using-spark.md)

L’activité Spark d’un [pipeline](concepts-pipelines-activities.md) Data Factory exécute des programmes Spark sur [votre propre](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou sur un [à la demande](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge. Quand vous utilisez un service lié Spark à la demande, Data Factory crée automatiquement un cluster Spark pour vous, juste le temps nécessaire pour traiter les données, puis il le supprime une fois le traitement terminé. 


## <a name="spark-activity-properties"></a>Propriétés de l'activité Spark
Voici l’exemple de définition JSON d’une activité Spark :    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON :

| Propriété              | Description                              | Obligatoire |
| --------------------- | ---------------------------------------- | -------- |
| Nom                  | Nom de l'activité dans le pipeline.    | OUI      |
| description           | Texte décrivant l’activité.  | Non       |
| Type                  | Pour l’activité Spark, le type d’activité est HDinsightSpark. | OUI      |
| linkedServiceName     | Nom du service lié HDInsight Spark sur lequel s’exécute le programme Spark. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | OUI      |
| SparkJobLinkedService | Service lié de stockage Azure qui contient le fichier de travail, les dépendances et les journaux d’activité Spark.  Si vous ne spécifiez pas de valeur pour cette propriété, le stockage associé au cluster HDInsight est utilisé. La valeur de cette propriété ne peut être qu’un service lié de Stockage Azure. | Non       |
| rootPath              | Conteneur d’objets blob Azure et dossier contenant le fichier Spark. Le nom de fichier respecte la casse. Reportez-vous à la section décrivant la structure des dossiers (section suivante) pour obtenir plus d’informations sur la structure de ce dossier. | OUI      |
| entryFilePath         | Chemin d’accès relatif au dossier racine du code/package Spark. Le fichier d’entrée doit être un fichier Python ou un fichier .jar. | OUI      |
| className             | Classe principale Java/Spark de l’application.      | Non       |
| arguments             | Liste d’arguments de ligne de commande du programme Spark. | Non       |
| proxyUser             | Identité du compte d’utilisateur à emprunter pour exécuter le programme Spark. | Non       |
| sparkConfig           | Spécifiez les valeurs des propriétés de configuration de Spark listées dans la rubrique : [Configuration Spark - Propriétés de l’application](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Non       |
| getDebugInfo          | Spécifie quand les fichiers journaux de Spark sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par sparkJobLinkedService. Valeurs autorisées : None, Always ou Failure. Valeur par défaut : Aucune. | Non       |

## <a name="folder-structure"></a>Structure de dossiers
Les travaux Spark sont plus extensibles que les travaux Pig/Hive. Pour les travaux Spark, vous pouvez fournir plusieurs dépendances, telles que des packages jar (placés dans le CLASSPATH Java), des fichiers Python (placés dans le PYTHONPATH) et tout autre fichier.

Créez la structure de dossiers suivante dans le stockage Blob Azure référencé par le service lié HDInsight. Ensuite, téléchargez les fichiers dépendants dans les sous-dossiers appropriés dans le dossier racine représenté par **entryFilePath**. Par exemple, téléchargez les fichiers Python dans le sous-dossier pyFiles et les fichiers jar dans le sous-dossier jars du dossier racine. Lors de l’exécution, le service Data Factory attend la structure de dossiers suivante dans le stockage Blob Azure :     

| Path                  | Description                              | Obligatoire | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (racine)            | Chemin d’accès racine du travail Spark dans le service lié de stockage | OUI      | Dossier |
| &lt;défini par l’utilisateur &gt; | Chemin d’accès pointant vers le fichier d’entrée du travail Spark | OUI      | Fichier   |
| ./jars                | Tous les fichiers dans ce dossier sont téléchargés et placés dans le classpath Java du cluster | Non       | Dossier |
| ./pyFiles             | Tous les fichiers dans ce dossier sont téléchargés et placés dans le PYTHONPATH du cluster | Non       | Dossier |
| ./files               | Tous les fichiers dans ce dossier sont téléchargés et placés dans le répertoire de travail de l’exécuteur | Non       | Dossier |
| ./archives            | Tous les fichiers dans ce dossier ne sont pas compressés | Non       | Dossier |
| ./logs                | Dossier qui contient les journaux d’activité à partir du cluster Spark. | Non       | Dossier |

Voici un exemple de stockage qui contient deux fichiers de travail Spark dans le stockage Blob Azure référencé par le service lié HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité d’exécution du lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
