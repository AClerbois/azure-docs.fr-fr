---
title: 'Démarrage rapide : API Cassandra avec Python - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment utiliser l’API Apache Cassandra Azure Cosmos DB pour créer une application de profil avec Python.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.custom: quick start connect, mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 5ffd134bd4e47f92264f8b299f8fd4bdb76f6c9f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870314"
---
# <a name="quickstart-build-a-cassandra-app-with-python-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application web Cassandra avec Python et Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ce guide de démarrage rapide montre comment utiliser Python et [l’API Cassandra](cassandra-introduction.md) Azure Cosmos DB pour créer une application de profil en clonant un exemple de GitHub. Ce guide de démarrage rapide vous montre également comment utiliser le portail Azure web pour créer un compte Azure Cosmos DB.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des bases de données de documents, de tables, de paires clé/valeur et de graphes, lesquelles bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale d’Azure Cosmos DB.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vous pouvez également [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, ni frais ni engagement.

Par ailleurs, vous devez avoir :
* [Python](https://www.python.org/downloads/) version v2.7.14
* [Git](http://git-scm.com/)
* [Pilote Python pour Apache Cassandra](https://github.com/datastax/python-driver)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API Cassandra à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une invite de commandes. Créez un dossier nommé `git-samples`. Ensuite, fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Si vous voulez savoir comment le code crée les ressources de base de données, vous pouvez consulter les extraits de code suivants. Tous les extraits de code sont tirés du fichier pyquickstart.py. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

* Les valeurs de nom d’utilisateur et de mot de passe ont été définies en utilisant la page de chaîne de connexion du portail Azure. `path\to\cert` fournit le chemin d’un certificat X509. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* Le `cluster` est initialisé avec les informations contactPoint. Le contactPoint est récupéré à partir du portail Azure.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* Le `cluster` se connecte à l’API Cassandra Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Un espace de clés est créé.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Une table est créée.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Des entités clé/valeur sont insérées.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Requête pour obtenir toutes les valeurs de clé.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Requête pour obtenir une paire clé-valeur.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. La chaîne de connexion permet à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](http://portal.azure.com/), sélectionnez **Chaîne de connexion**. 

    Utilisez le ![bouton Copier](./media/create-cassandra-python/copy.png) à droite de l’écran pour copier la valeur supérieure, c’est-à-dire le POINT DE CONTACT.

    ![Affichez et copiez un nom d’utilisateur, un mot de passe et un point de contact dans le panneau de chaîne de connexion du portail Azure](./media/create-cassandra-python/keys.png)

2. Ouvrez le fichier `config.py` . 

3. Collez la valeur POINT DE CONTACT à partir du portail sur `<FILLME>` à la ligne 10.

    La ligne 10 doit maintenant ressembler à 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Copiez la valeur NOM D’UTILISATEUR à partir du portail et collez-la sur `<FILLME>` à la ligne 6.

    La ligne 6 doit maintenant ressembler à 

    `'username': 'cosmos-db-quickstart',`
    
5. Copiez la valeur MOT DE PASSE à partir du portail et collez-la sur `<FILLME>` à la ligne 8.

    La ligne 8 doit maintenant ressembler à

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Enregistrez le fichier config.py.
    
## <a name="use-the-x509-certificate"></a>Utiliser le certificat X509

1. Téléchargez le certificat racine Baltimore CyberTrust localement à partir de [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Renommez le fichier avec l’extension de fichier `.cer`.

   Le certificat a le numéro de série `02:00:00:b9` et l’empreinte digitale SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Ouvrez `pyquickstart.py` et changez le `path\to\cert` pour pointer vers votre nouveau certificat.

3. Enregistrez `pyquickstart.py`.

## <a name="run-the-python-app"></a>Exécuter l’application Python

1. Utilisez la commande cd dans le terminal git pour passer au dossier `azure-cosmos-db-cassandra-python-getting-started`. 

2. Exécutez les commandes suivantes pour installer les modules nécessaires :

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Exécutez la commande suivante pour démarrer votre application Node :

    ```
    python pyquickstart.py
    ```

3. Vérifiez que les résultats sont corrects à partir de la ligne de commande.

    Appuyez sur CTRL + C pour arrêter l’exécution du programme et fermer la fenêtre de console. 

    ![Consulter et vérifier la sortie](./media/create-cassandra-python/output.png)
    
4. Dans le portail Azure, ouvrez **l’Explorateur de données** pour interroger, modifier et utiliser ces nouvelles données. 

    ![Afficher les données dans l’Explorateur de données](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer un conteneur à l’aide de l’Explorateur de données, et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)

