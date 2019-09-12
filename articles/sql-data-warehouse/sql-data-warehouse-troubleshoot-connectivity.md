---
title: Résolution des problèmes d’Azure SQL Data Warehouse | Microsoft Docs
description: Résolution des problèmes d’Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859233"
---
# <a name="troubleshooting-connectivity-issues"></a>Résolution des problèmes de connectivité

Cet article répertorie les techniques de dépannage courantes relatives à la connexion à SQL Data Warehouse.
- [Vérifier la disponibilité du service](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Vérifier les opérations de mise à l’échelle ou interrompues](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Vérifier les paramètres de pare-feu](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Vérifiez les paramètres de point de terminaison de service/du réseau virtuel](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Vérifier les derniers pilotes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Vérifier la chaîne de connexion](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problèmes de connexion intermittente](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Messages d’erreur courants](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Vérifier la disponibilité du service

Vérifiez si le service est disponible. Dans le portail Azure, accédez à l’entrepôt SQL Data Warehouse auquel vous souhaitez vous connecter. Dans le panneau Table des matières à gauche, cliquez sur **Diagnostiquer et résoudre les problèmes**.

![Sélectionner l’intégrité des ressources](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

L’état de votre entrepôt SQL Data Warehouse s’affiche ici. Si le service ne s’affiche pas comme étant **disponible**, vérifiez d’autres étapes.

![Service disponible](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Si l’intégrité des ressources indique que votre entrepôt de données est interrompu ou mis à l’échelle, suivez les instructions pour reprendre l’exécution de l’entrepôt de données.

![Service suspendu](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Des informations supplémentaires relatives à l’intégrité des ressources sont disponibles ici.

## <a name="check-for-paused-or-scaling-operation"></a>Vérifier les opérations de mise à l’échelle ou interrompues

Dans le portail, vérifiez si votre entrepôt SQL Data Warehouse est interrompu ou mis à l’échelle.

![Service suspendu](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Si vous constatez que votre service est interrompu ou mis à l’échelle, vérifiez que ce n’est pas durant le programme de maintenance. Dans le portail, dans la *Vue d’ensemble* de votre entrepôt SQL Data Warehouse, vous verrez le programme de maintenance sélectionné.

![Vue d’ensemble du programme de maintenance](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Sinon, contactez votre administrateur informatique pour vérifier que cette opération de maintenance n’est pas un événement planifié. Pour reprendre l’exécution de l’entrepôt SQL Data Warehouse, suivez les étapes décrites [ici](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Vérifier les paramètres de pare-feu

SQL Data Warehouse communique sur le port 1433.   Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433. Vous trouverez plus d’informations sur les configurations de pare-feu [ici](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Vérifiez les paramètres de point de terminaison de service/du réseau virtuel

Si vous recevez des erreurs 40914 et 40615, consultez la documentation [Résolution et description de l’erreur ici](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vérifier les derniers pilotes

### <a name="software"></a>Logiciel

Vérifiez que vous utilisez bien les outils les plus récents pour vous connecter à votre entrepôt SQL Data Warehouse :

* SSMS
* Studio de données Azure
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Pilotes

Vérifiez que vous disposez des dernières versions des pilotes.  L’utilisation d’une version antérieure des pilotes peut entraîner des comportements inattendus car les anciens pilotes peuvent ne pas en charge les nouvelles fonctionnalités.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Vérifier la chaîne de connexion

Vérifiez que vos chaînes de connexion sont correctement définies.  Vous trouverez quelques exemples ci-dessous.  Vous trouverez des informations supplémentaires concernant les [chaînes de connexion ici](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Chaîne de connexion ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Chaîne de connexion ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Chaîne de connexion PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Chaîne de connexion JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problèmes de connexion intermittente

Vérifiez si vous constatez une charge importante sur le serveur avec un grand nombre de requêtes en file d’attente. Vous devrez peut-être mettre à l’échelle l’entrepôt de données pour obtenir des ressources supplémentaires.

## <a name="common-error-messages"></a>Messages d’erreur courants

Erreurs 40914 et 40615, consultez la documentation [Résolution et description de l’erreur ici](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Vous rencontrez toujours des problèmes de connectivité ?
Créez un [ticket de support](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) afin d’être assisté par l’équipe d’ingénierie.
