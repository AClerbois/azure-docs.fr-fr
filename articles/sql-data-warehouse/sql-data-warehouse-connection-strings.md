---
title: Chaînes de connexion pour Azure SQL Data Warehouse | Microsoft Docs
description: Chaînes de connexion pour SQL Data Warehouse
services: sql-data-warehouse
author: rockboyfor
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 057e412857feb39069c55bb9905c83dcea7cbd4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402647"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Chaînes de connexion pour Azure SQL Data Warehouse
Vous pouvez vous connecter à SQL Data Warehouse avec plusieurs protocoles d’application différents, comme [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] et [JDBC][JDBC]. Voici quelques exemples de chaînes de connexion pour chaque protocole.  Vous pouvez également utiliser le portail Azure pour créer votre chaîne de connexion.  Pour créer votre chaîne de connexion avec le portail Azure, accédez au panneau de votre base de données, sous *Éléments principaux*, cliquez sur *Afficher les chaînes de connexion de la base de données*.

## <a name="sample-adonet-connection-string"></a>Exemple de chaîne de connexion ADO.NET
```csharp
Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Exemple de chaîne de connexion ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Exemple de chaîne de connexion PHP
```PHP
Server: {your_server}.database.chinacloudapi.cn,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.chinacloudapi.cn,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.chinacloudapi.cn,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Exemple de chaîne de connexion JDBC
```Java
jdbc:sqlserver://yourserver.database.chinacloudapi.cn:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;
```

> [!NOTE]
> Vous pouvez définir le délai de connexion à 300 secondes pour permettre à la connexion de perdurer en cas de courtes périodes d’indisponibilité.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio][Query with Visual Studio].

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
<!-- Update_Description: update meta properties, wording update -->