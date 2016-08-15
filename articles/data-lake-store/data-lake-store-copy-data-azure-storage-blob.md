<properties
   pageTitle="Copier des données d’objets blob Azure Storage vers Data Lake Store | Microsoft Azure"
   description="Utiliser l’outil AdlCopy pour copier les données d’objets blob Azure Storage vers Data Lake Store"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Copier des données d’objets blob Azure Storage vers Data Lake Store

> [AZURE.SELECTOR]
- [Utilisation de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilisation d’AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure Data Lake Store fournit un outil en ligne de commande nommé [AdlCopy](http://aka.ms/downloadadlcopy) qui permet de copier les données depuis les sources suivantes :

* Depuis des objets blob Azure dans Data Lake Store. Vous ne pouvez pas utiliser AdlCopy pour copier des données de Data Lake Store vers des objets blob Azure Storage.

* Depuis deux comptes Azure Data Lake Store.

Vous pouvez également utiliser l’outil AdlCopy dans deux modes différents :

* **Autonome** : l’outil utilise des ressources Data Lake Store pour effectuer la tâche.
* **À l’aide d’un compte Data Lake Analytics** : les unités affectées à votre compte Data Lake Analytics permettent d’effectuer l’opération de copie. Vous pouvez utiliser cette option lorsque vous souhaitez que les tâches de copie s’effectuent de manière prévisible.

##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- Conteneur d’**objets blob Azure Storage** avec quelques données.
- **Compte Azure Data Lake Analytics (facultatif)** - Consultez [Prise en main d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la création d’un compte Data Lake Store.
- **Outil AdlCopy**. Installez l’outil AdlCopy à partir de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Syntaxe de l’outil AdlCopy

L’outil AdlCopy utilise la syntaxe suivante.

	AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Les paramètres de la syntaxe sont décrits ci-après :

| Option | Description |
|-----------|------------|
| Source | Spécifie l’emplacement de la source de données dans l’objet blob Azure Storage. La source peut être un conteneur d’objets blob, un objet blob ou un autre compte Data Lake Store. |
| Dest | Spécifie la destination Data Lake Store vers laquelle effectuer la copie. |
| SourceKey | Spécifie la clé d’accès de stockage pour la source d’objet blob Azure Storage. Cela est nécessaire uniquement si la source est un conteneur d’objets blob ou un objet blob. |
| Compte | **Facultatif**. Utilisez ce paramètre si vous souhaitez utiliser un compte Azure Data Lake Analytics pour exécuter la tâche de copie. Si vous utilisez l’option /Account dans la syntaxe, mais sans spécifier de compte Data Lake Analytics, AdlCopy utilise un compte par défaut pour exécuter la tâche. En outre, si vous utilisez cette option, vous devez ajouter la source (objet blob Azure Storage) et la destination (Azure Data Lake Store) comme sources de données de votre compte Data Lake Analytics. |
| Units | Spécifie le nombre d’unités Data Lake Analytics utilisées pour la tâche de copie. Cette option est obligatoire si vous utilisez l’option **/Account** pour spécifier le compte Data Lake Analytics.
| Modèle | Spécifie un modèle d’expression régulière qui indique les objets blob ou les fichiers à copier. AdlCopy respecte la casse pour la mise en correspondance. Le modèle par défaut utilisé lorsqu’aucun modèle n’est spécifié consiste à copier tous les éléments. La spécification de plusieurs modèles de fichiers n’est pas prise en charge.                                                                                                                                                                                                                                                                                                                                               



## Utilisation d’AdlCopy (en mode autonome) pour copier des données à partir d’un objet blob Azure Storage

1. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un objet blob spécifique du conteneur source vers Data Lake Store :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Par exemple :

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	
	>[AZURE.NOTE] La syntaxe ci-dessus spécifie le fichier à copier vers un dossier dans le compte Data Lake Store. L’outil de AdlCopy crée un dossier si le nom de dossier spécifié n’existe pas.

	Vous êtes invité à entrer les informations d’identification de l’abonnement Azure sous lequel vous disposez d’un compte Data Lake Store. Vous devez voir une sortie similaire à ce qui suit :

		Initializing Copy.
		Copy Started.
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. Vous pouvez également copier tous les objets blob d’un conteneur donné vers le compte Data Lake Store à l’aide de la commande suivante :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Par exemple :

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## Utilisation d’AdlCopy (en mode autonome) pour copier des données à partir d’un autre compte Data Lake Store

Vous pouvez également utiliser AdlCopy pour copier des données entre deux comptes Data Lake Store.

1. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un fichier spécifique depuis un compte Data Lake Store vers un autre.

		AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

	Par exemple :

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

	>[AZURE.NOTE] La syntaxe ci-dessus spécifie le fichier à copier vers un dossier dans le compte Data Lake Store de destination. L’outil de AdlCopy crée un dossier si le nom de dossier spécifié n’existe pas.

	Vous êtes invité à entrer les informations d’identification de l’abonnement Azure sous lequel vous disposez d’un compte Data Lake Store. Vous devez voir une sortie similaire à ce qui suit :

		Initializing Copy.
		Copy Started.|
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. La commande suivante copie tous les fichiers depuis un dossier spécifique dans le compte Data Lake Store source vers un dossier dans le compte Data Lake Store de destination.

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## Utilisation d’AdlCopy (avec un compte Data Lake Analytics) pour copier des données

Vous pouvez également utiliser votre compte Data Lake Analytics pour exécuter la tâche AdlCopy afin de copier les données d’objets blob Azure Storage vers Data Lake Store. Vous utilisez généralement cette option lorsque les données à déplacer sont situées dans une plage de plusieurs gigaoctets et téraoctets et que vous souhaitez que le débit des performances soit amélioré et prévisible.

Pour utiliser votre compte Data Lake Analytics avec AdlCopy pour copier des données depuis un objet blob Azure Storage, vous devez ajouter la source (objet blob Azure Storage) comme source de données de votre compte Data Lake Analytics. Pour obtenir des instructions sur l’ajout de sources de données à votre compte Data Lake Analytics, consultez [Gérer les sources de donnés d’un compte Data Lake Analytics](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Si vous copiez à partir d’un compte Azure Data Lake Store (comme source) à l’aide d’un compte Data Lake Analytics, il est inutile d’associer le compte Data Lake Store au compte Data Lake Analytics. Il n’est nécessaire d’associer le magasin (store) source au compte Data Lake Analytics que lorsque la source est un compte Azure Storage.

Exécutez la commande suivante pour copier à partir d’un objet blob Azure Storage vers un compte Data Lake Store l’aide du compte Data Lake Analytics :

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Par exemple :

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


De la même manière, exécutez la commande suivante pour copier à partir d’un objet blob Azure Storage vers un compte Data Lake Store l’aide du compte Data Lake Analytics :

	AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## Utilisation d’AdlCopy pour copier des données à l’aide de critères spéciaux

Dans cette section, vous apprenez à utiliser AdlCopy pour copier des données depuis une source (dans notre exemple ci-dessous, nous utilisons un objet Blob Azure Storage) vers un compte Data Lake Store de destination à l’aide de critères spéciaux. Par exemple, vous pouvez utiliser la procédure ci-dessous pour copier tous les fichiers portant l’extension .csv à partir de l’objet blob source vers la destination.

1. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier tous les fichiers portant l’extension .csv à partir d’un objet blob spécifique du conteneur source vers un Data Lake Store :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

	Par exemple :

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

	
## Facturation

* Si vous utilisez l’outil AdlCopy de manière autonome, vous êtes facturé en fonction des coûts de sortie correspondant au déplacement des données, si le compte Azure Storage source n’est pas situé dans la même région que Data Lake Store.

* Si vous utilisez l’outil AdlCopy avec votre compte Data Lake Analytics, les [tarifs de facturation Data Lake Analytics standard](https://azure.microsoft.com/pricing/details/data-lake-analytics/) s’appliquent.

## Considérations sur l’utilisation d’AdlCopy

* AdlCopy (pour la version 1.0.5) prend en charge la copie de données à partir de sources qui contiennent collectivement plusieurs milliers de fichiers et dossiers. Cependant, si vous rencontrez des problèmes lors de la copie d’un jeu de données volumineux, vous pouvez distribuer les fichiers/dossiers dans différents sous-dossiers et utiliser comme source le chemin d'accès à ces sous-dossiers.

## Étapes suivantes

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0803_2016-->