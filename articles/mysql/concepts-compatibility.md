---
title: "Compatibilité des pilotes MySQL et des outils de gestion |Microsoft Docs"
description: Pilotes MySQL et outils de gestion compatibles avec Azure Database pour MySQL
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 4df0dcc7d0f2bde24c1a7e12eea6fa142612a0e7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
## <a name="mysql-drivers"></a>Pilotes MySQL
En termes de base de données MySQL, Azure Database pour MySQL utilise la version community edition la plus populaire au monde. Par conséquent, elle est compatible avec un large éventail de langages de programmation et de pilotes. L’objectif est de prendre en charge les trois versions les plus récentes de pilotes MySQL, et de poursuivre les efforts avec les auteurs de la Communauté open source, afin d’améliorer en permanence les fonctionnalités et la facilité d’utilisation des pilotes MySQL. Une liste de pilotes testés et détectés compatibles avec Azure Database pour MySQL 5.6 et 5.7 est fournie dans le tableau suivant :

| **Pilote** | **Liens** | **Versions compatibles** | **Versions non compatibles** | **Remarques** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://PHP.NET/downloads.PHP | 5.5 5.6 7.x | 5.3 | Pour la connexion PHP 7.0 à SSL MySQLi, ajoutez MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT dans la chaîne de connexion. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Ensemble PDO : option ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` définie sur false.|
| .Net | [MySqlConnector sur Github] : https://github.com/mysql-net/MySqlConnector/releases <br> [Package d’installation à partir de Nuget] :<br> https://www.NuGet.org/packages/MySqlConnector/ | 0.27 et versions ultérieures | 0.26.5 et versions antérieures | |
| Nodejs |  [MySQLjs sur Github] :<br> https://github.com/mysqljs/mysql/releases <br> [Package d’installation à partir de NPM] :<br> Exécutez « npm install mysql » à partir de NPM | 2.15 | 2.14.1 et versions antérieures | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 et versions antérieures | Utiliser allowNativePasswords=true dans la chaîne de connexion |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 et versions antérieures | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 et versions antérieures | |

## <a name="management-tools"></a>Outils de gestion
L’avantage de compatibilité s’étend également aux outils de gestion de base de données. Vos outils existants doivent continuer de fonctionner avec Azure Database pour MySQL, tant que la manipulation de base de données s’effectue dans les limites des autorisations utilisateur. Trois outils de gestion de base de données courants, qui ont été testés et détectés compatibles avec Azure Database pour MySQL 5.6 et 5.7, sont répertoriés dans le tableau suivant :

|                                     | **MySQL Workbench 6.x et supérieur** | **Navicat 12** | **PHPMyAdmin 4.x et supérieur** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Création, mise à jour, lecture, écriture, suppression | X | X | X |
| Connexion SSL | X | X | X |
| Saisie semi-automatique de la requête SQL | X | X |  |
| Importation et exportation de données | X | X | X |
| Exportation dans plusieurs formats | X | X | X |
| Sauvegarde et restauration |  | X |  |
| Affichage des paramètres du serveur | X | X | X |
| Affichage des connexions client | X | X | X |