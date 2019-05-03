---
title: Implémenter une solution de base de données Azure SQL géodistribuée | Microsoft Docs
description: Découvrez comment configurer votre application et votre base de données Azure SQL pour le basculement sur une base de données répliquée et comment tester le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6022c016b83ffe1362db4d826a5ee4397afd4128
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338965"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Didacticiel : Implémenter une base de données géo-distribuée

Configurez une application et une base de données Azure SQL pour le basculement sur une région distante, puis testez un plan de basculement. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md)
> - Exécuter une application Java pour interroger une base de données Azure SQL
> - Test de basculement

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

Pour suivre le tutoriel, vérifiez que les éléments suivants sont installés :

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- base de données Azure SQL. Pour en créer une :
  - [Portal](sql-database-single-database-get-started.md)
  - [INTERFACE DE LIGNE DE COMMANDE](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Ce tutoriel utilise l’exemple de base de données *AdventureWorksLT*.

- Java et Maven. Consultez [Générer une application à l’aide de SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), mettez en surbrillance **Java** et sélectionnez votre environnement, puis suivez les étapes.

> [!IMPORTANT]
> Veillez à configurer des règles de pare-feu pour pouvoir utiliser l’adresse IP publique de l’ordinateur sur lequel vous effectuez les étapes de ce tutoriel. Les règles de pare-feu au niveau de la base de données sont répliquées automatiquement sur le serveur secondaire.
>
> Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou, pour déterminer l’adresse IP utilisée pour la règle de pare-feu au niveau du serveur pour votre ordinateur, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Créer un groupe de basculement

À l’aide d’Azure PowerShell, créez des [groupes de basculement](sql-database-auto-failover-group.md) entre un serveur SQL Azure existant et un nouveau serveur SQL Azure dans une autre région. Ajoutez ensuite l’exemple de base de données au groupe de basculement.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Pour créer un groupe de basculement, exécutez le script suivant :

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Vous pouvez également changer les paramètres de géoréplication dans le portail Azure. Pour cela, sélectionnez votre base de données, puis **Paramètres** > **Géoréplication**.

![Paramètres de géoréplication](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Exécuter l’exemple de projet

1. Dans la console, créez un projet Maven avec la commande suivante :

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Tapez **Y**, puis appuyez sur **Entrée**.

1. Passez au répertoire du nouveau projet.

   ```bash
   cd SqlDbSample
   ```

1. À l’aide de votre éditeur habituel, ouvrez le fichier *pom.xml* dans le dossier de votre projet.

1. Ajoutez la dépendance Pilote Microsoft JDBC pour SQL Server en ajoutant la section `dependency` suivante. La dépendance doit être collée dans la section `dependencies` plus grande.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Spécifiez la version de Java en ajoutant la section `properties` après la section `dependencies` :

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Assurez la prise en charge des fichiers manifeste en ajoutant la section `build` après la section `properties` :

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Enregistrez et fermez le fichier *pom.xml*.

1. Ouvrez le fichier *App.java* situé à l’emplacement ..\SqlDbSample\src\main\java\com\sqldbsamples et remplacez son contenu par le code suivant :

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Enregistrez et fermez le fichier *App.java*.

1. Dans la console de commandes, exécutez la commande suivante :

   ```bash
   mvn package
   ```

1. Démarrez l’application. Celle-ci s’exécute pendant environ 1 heure jusqu’à ce que vous l’arrêtiez manuellement, ce qui vous donne le temps d’exécuter le test de basculement.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Test de basculement

Exécutez les scripts suivants pour simuler un basculement et observer les résultats de l’application. Notez l’échec de certaines insertions et sélections durant la migration de base de données.

Vous pouvez également vérifier le rôle du serveur de reprise d’activité pendant le test avec la commande suivante :

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Pour tester un basculement :

1. Démarrer un basculement manuel du groupe de basculement :

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Rétablir le groupe de basculement sur le serveur principal :

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré une application et une base de données Azure SQL pour le basculement sur une région distante, puis testé votre plan de basculement. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer un groupe de basculement de géoréplication
> - Exécuter une application Java pour interroger une base de données Azure SQL
> - Test de basculement

Passez au tutoriel suivant sur la migration à l’aide de DMS.

> [!div class="nextstepaction"]
> [Migrer SQL Server vers Azure SQL Database Managed Instance à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md)
