---
title: 'Portail Azure : Créer une base de données SQL | Microsoft Docs'
description: Créez un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et une base de données dans le Portail Azure, puis interrogez cette dernière.
keywords: tutoriel sur la base de données sql, créer une base de données sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 07/16/2018
ms.author: sachinp
ms.openlocfilehash: 172ee6c2200334a57ebaa073d7ff530d19b2f07d
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090528"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Création d’une base de données SQL Azure dans le portail Azure

Ce démarrage rapide décrit comment créer une base de données SQL dans Azure à l’aide du [modèle d’achat DTU](sql-database-service-tiers-dtu.md). Azure SQL Database est une offre de type « base de données en tant que service » qui vous permet d’exécuter et de mettre à l’échelle des bases de données SQL Server hautement disponibles dans le cloud. Ce démarrage rapide vous indique comment commencer en créant une base de données SQL à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

  >[!NOTE]
  >Ce didacticiel utilise le modèle d’achat DTU, mais le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) est également disponible.

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers-dtu.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md).

Suivez ces étapes pour créer une base de données SQL contenant les exemples de données Adventure Works LT.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Dans la page **Nouveau**, sélectionnez **Bases de données**, puis **Créer** sous **SQL Database** dans **cette même** page.

   ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources**  | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Exemple (AdventureWorksLT) | Charge le schéma et les données AdventureWorksLT schéma dans votre nouvelle base de données |

   > [!IMPORTANT]
   > Vous devez sélectionner l’exemple de base de données sur ce formulaire, car il est utilisé dans le reste de ce démarrage rapide.
   >

4. Sous **Serveur**, cliquez sur **Configurer les paramètres requis** et remplissez le formulaire de serveur SQL (serveur logique) avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
   | **Abonnement** | Votre abonnement | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Une fois le formulaire rempli, cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU et la quantité de stockage. Explorez les options concernant la quantité de DTU et de stockage disponible pour chaque niveau de service.

   > [!IMPORTANT]
   > Le niveau Premium offre actuellement plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : Royaume-Uni Nord, USA Centre-Ouest, Royaume-Uni Sud 2, Chine Est, USDoDCentral, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, US Gov Centre-Sud, Allemagne - Nord-Est, Chine Nord, US Gov Est. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Pour ce démarrage rapide, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **10 DTU (S0)** et **1** Go de stockage.

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Acceptez les conditions d’utilisation de la préversion pour pouvoir utiliser l’option **Stockage de composants additionnels**.

   > [!IMPORTANT]
   > Le niveau Premium offre actuellement plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : USA Centre-Ouest, Chine Est, USDoDCentral, US Gov Iowa, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, Allemagne - Nord-Est, Chine Nord. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Après avoir sélectionné le niveau du serveur, le nombre de DTU et la quantité de stockage, cliquez sur **Appliquer**.  

10. Maintenant que vous avez rempli le formulaire SQL Database, cliquez sur **Créer** pour provisionner la base de données. Le provisionnement prend quelques minutes.

11. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

     ![notification](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, elle affiche le nom de serveur complet (tel que **mynewserver-20170824.database.windows.net**) et fournit des options pour poursuivre la configuration.

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les démarrages rapides suivants.

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png)

3. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.
>

## <a name="query-the-sql-database"></a>Interroger la base de données SQL

Maintenant que vous avez créé un exemple de base de données dans Azure, nous allons utiliser l’outil de requête intégré au portail Azure pour vérifier que vous pouvez vous connecter à la base de données et demander des données.

1. Sur la page SQL Database de votre base de données, cliquez sur **Éditeur de requêtes (version préliminaire)** dans le menu de gauche, puis sur **Connexion**.

   ![se connecter](./media/sql-database-get-started-portal/query-editor-login.png)

2. Sélectionnez l’authentification SQL Server, fournissez les informations de connexion requises, puis cliquez sur **OK** pour vous connecter.

3. Après vous être authentifié en tant qu’**Administrateur du serveur**, saisissez la requête suivante dans le volet de l’éditeur de requête.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

   ![query editor results](./media/sql-database-get-started-portal/query-editor-results.png)

5. Fermez la page **Éditeur de requêtes**, puis cliquez sur **OK** pour ignorer vos modifications non enregistrées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Enregistrez les ressources si vous souhaitez passer aux [Étapes suivantes](#next-steps) et découvrir comment vous connecter à votre base de données et les différentes méthodes à votre disposition pour l’interroger. Toutefois, si vous souhaitez supprimer les ressources créées dans ce démarrage rapide, procédez comme suit.


1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous disposez d’une base de données, vous pouvez vous [connecter et interroger](sql-database-connect-query.md) à l’aide d’un de vos outils ou langages préférés. 
- Pour apprendre à concevoir votre première base de données, créer des tables et insérer des données, consultez un de ces didacticiels :
 - [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
  - [Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)
