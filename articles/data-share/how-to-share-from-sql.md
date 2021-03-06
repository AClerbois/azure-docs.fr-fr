---
title: Partager et recevoir des données d’Azure SQL Database et d’Azure Synapse Analytics
description: Découvrez comment partager et recevoir des données d’Azure SQL Database et d’Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: e813921727ee08bf9a76c0a2dbfe15f45fe4db79
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89490069"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Partager et recevoir des données d’Azure SQL Database et d’Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share prend en charge le partage basé sur des captures instantanées d’Azure SQL Database et d’Azure Synapse Analytics (anciennement Azure SQL DW). Cet article explique comment partager et recevoir des données de ces sources.

Azure Data Share prend en charge le partage de tables ou d’affichages à partir d’Azure SQL Database et d’Azure Synapse Analytics (anciennement Azure SQL DW). Les consommateurs de données peuvent choisir d’accepter les données dans Azure Data Lake Storage Gen2 ou Stockage Blob Azure en tant que fichier csv ou parquet, ainsi que dans Azure SQL Database et Azure Synapse Analytics en tant que tables.

Lors de l’acceptation de données dans Azure Data Lake Store Gen 2 ou Stockage Blob Azure, des captures instantanées complètes remplacent le contenu du fichier cible s’il existe déjà.
Lors de la réception de données dans une table, si la table cible n’existe pas encore, Azure Data Share crée la table SQL avec le schéma source. S’il existe une table cible du même nom, celle-ci est remplacée par la dernière capture instantanée complète. Les captures instantanées incrémentielles ne sont actuellement pas prises en charge.

## <a name="share-data"></a>Partager des données

### <a name="prerequisites-to-share-data"></a>Conditions préalables pour partager des données

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* L’adresse e-mail de connexion Azure de vos destinataires (l’utilisation de leur alias de courrier ne fonctionnera pas).
* Si le magasin de données Azure source se trouve dans un autre abonnement Azure que celui que vous utiliserez pour créer une ressource de partage de données, inscrivez le [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) dans l’abonnement où se trouve le magasin de données Azure. 

### <a name="prerequisites-for-sql-source"></a>Conditions préalables pour une source SQL

* Instance Azure SQL Database ou Azure Synapse Analytics (anciennement SQL Data Warehouse) avec les tables et les vues que vous voulez partager.
* Autorisation d’écrire dans les bases de données sur SQL Server, qui est présente dans *Microsoft.Sql/servers/databases/write*. Cette autorisation existe dans le rôle Contributeur.
* Autorisation pour l’accès du partage de données à l’entrepôt de données. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur SQL Server.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter l’identité managée de la ressource Data Share en tant que db_datareader. Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Notez que *<share_acc_name>* est le nom de votre ressource Data Share. Si vous n’avez pas encore créé de ressource Data Share, vous pourrez le faire plus tard.  

* Un utilisateur Azure SQL Database avec un accès « db_datareader » pour parcourir et sélectionner les tables et/ou les vues que vous voulez partager. 

* Accès au pare-feu SQL Server de l’adresse IP du client. Pour ce faire, procédez comme suit : 
    1. Dans SQL Server, dans le portail Azure, accédez à *Pare-feux et réseaux virtuels*
    1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services.
    1. Cliquez sur **+ Ajouter une adresse IP de client**, puis cliquez sur **Enregistrer**. Cette adresse IP est susceptible d’être modifiée. Il peut être nécessaire de répéter ce processus la prochaine fois que vous partagerez des données SQL à partir du portail Azure. Vous pouvez également ajouter une plage d’adresses IP. 

### <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Créer un compte Data Share

Créez une ressource Azure Data Share dans un groupe de ressources Azure.

1. Sélectionnez le bouton du menu en haut à gauche du portail, puis **Créer une ressource** (+).

1. Recherchez *Data Share*.

1. Sélectionnez Data Share, puis sélectionnez **Créer**.

1. Indiquez les détails de base de votre ressource Azure Data Share à partir des informations suivantes. 

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour votre compte de partage de données.|
    | Groupe de ressources | *test-resource-group* | Utilisez un groupe de ressources existant ou créez-en un. |
    | Emplacement | *USA Est 2* | Sélectionnez une région pour votre compte de partage de données.
    | Nom | *datashareaccount* | Spécifiez un nom pour votre compte de partage de données. |
    | | |

1. Sélectionnez **Vérifier + créer** puis **Créer** pour provisionner votre compte de partage de données. En règle générale, le provisionnement d’un nouveau compte de partage de données prend environ 2 minutes au maximum. 

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

### <a name="create-a-share"></a>Créer un partage

1. Accédez à la page de présentation de votre partage Data Share.

    ![Partager vos données](./media/share-receive-data.png "Partager vos données") 

1. Sélectionnez **Start sharing your data** (Commencer à partager vos données).

1. Sélectionnez **Create** (Créer).   

1. Renseignez les détails de votre partage. Spécifiez un nom, un type de partage, une description du contenu du partage et des conditions d’utilisation (facultatif). 

    ![EnterShareDetails](./media/enter-share-details.png "Entrer les détails de partage") 

1. Sélectionnez **Continuer**.

1. Pour ajouter des jeux de données à votre partage, sélectionnez **Add Datasets** (Ajouter des jeux de données). 

    ![Ajouter des jeux de données à votre partage](./media/datasets.png "Groupes de données")

1. Sélectionnez le type de jeu de données à ajouter. Vous verrez une liste différente de types de jeux de données en fonction du type de partage (instantané ou sur place) que vous avez sélectionné à l’étape précédente. 

    ![AddDatasets](./media/add-datasets.png "Ajouter des jeux de données")    

1. Sélectionnez votre serveur SQL Server, fournissez les informations d’identification et sélectionnez **Suivant** pour accéder à l’objet que vous souhaitez partager, puis sélectionnez « Ajouter des jeux de données ». 

    ![SelectDatasets](./media/select-datasets-sql.png "Sélectionner des jeux de données")    

1. Sous l’onglet Destinataires, entrez les adresses e-mail de votre consommateur de données en sélectionnant + Add Recipient (Ajouter un destinataire). 

    ![AddRecipients](./media/add-recipient.png "Ajouter des destinataires") 

1. Sélectionnez **Continuer**.

1. Si vous avez sélectionné le type de partage d’instantané, vous pouvez configurer la planification d’instantanés pour fournir des mises à jour de vos données à votre consommateur de données. 

    ![EnableSnapshots](./media/enable-snapshots.png "Activer les captures instantanées") 

1. Sélectionnez une heure de début et un intervalle de périodicité. 

1. Sélectionnez **Continuer**.

1. Sous l’onglet Review + Create (Revoir + Créer), passez en revue le contenu du package, les paramètres, les destinataires ainsi que les paramètres de synchronisation. Sélectionnez **Create** (Créer).

Votre partage Azure Data Share est désormais créé. Le destinataire de votre partage Data Share est maintenant prêt à accepter votre invitation. 

## <a name="receive-data"></a>Recevoir des données

### <a name="prerequisites-to-receive-data"></a>Conditions préalables pour la réception de données
Avant de pouvoir accepter une invitation de partage de données, vous devez provisionner un certain nombre de ressources Azure listées ci-dessous. 

Vérifiez que tous les prérequis sont remplis avant d’accepter une invitation de partage de données. 

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Invitation Data Share : Invitation de Microsoft Azure dont l’objet est « Invitation Azure Data Share de **<yourdataprovider@domain.com>**  ».
* Inscrivez le [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) dans l’abonnement Azure où vous allez créer une ressource de partage de données et dans l’abonnement Azure où se trouvent vos magasins de données Azure cibles.

### <a name="prerequisites-for-target-storage-account"></a>Conditions préalables pour le compte de stockage cible
Si vous choisissez de recevoir des données dans le service Stockage Azure, vous trouverez ci-dessous la liste des conditions préalables.

* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Autorisation d’écrire dans le compte de stockage, qui est présent dans *Microsoft.Storage/storageAccounts/write*. Cette autorisation existe dans le rôle Contributeur. 
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans *Microsoft.Authorization/role assignments/write*. Cette autorisation existe dans le rôle Propriétaire.  

### <a name="prerequisites-for-sql-target"></a>Conditions préalables pour une cible SQL
Si vous choisissez de recevoir des données dans Azure SQL Database, Azure Synapse Analytics, vous trouverez ci-dessous la liste des conditions préalables.

* Autorisation d’écrire dans les bases de données sur le serveur SQL Server, qui est présente dans *Microsoft.Sql/servers/databases/write*. Cette autorisation existe dans le rôle Contributeur. 
* Autorisation permettant à l’identité managée de la ressource de partage de données d’accéder à Azure SQL Database ou à Azure Synapse Analytics. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur SQL Server.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter l’identité managée Data Share en tant que « db_datareader, db_datawriter, db_ddladmin ». Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Notez que *<share_acc_name>* est le nom de votre ressource Data Share. Si vous n’avez pas encore créé de ressource Data Share, vous pourrez le faire plus tard.         

* Accès au pare-feu SQL Server de l’adresse IP du client. Pour ce faire, procédez comme suit : 
    1. Dans SQL Server, dans le portail Azure, accédez à *Pare-feux et réseaux virtuels*
    1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services.
    1. Cliquez sur **+ Ajouter une adresse IP de client**, puis cliquez sur **Enregistrer**. Cette adresse IP est susceptible d’être modifiée. Il peut être nécessaire de répéter ce processus la prochaine fois que vous recevrez des données dans une cible SQL à partir du portail Azure. Vous pouvez également ajouter une plage d’adresses IP. 

### <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Ouvrir l’invitation

1. Vous pouvez ouvrir une invitation à partir d’un e-mail, ou directement à partir du portail Azure. 

   Pour ouvrir une invitation à partir d’un e-mail, recherchez dans votre boîte de réception une invitation émanant de votre fournisseur de données. L’invitation provient de Microsoft Azure et s’intitule **Invitation Azure Data Share de <yourdataprovider@domain.com>** . Cliquez sur **Afficher l’invitation** pour voir votre invitation dans Azure. 

   Pour ouvrir l’invitation directement à partir du portail Azure, recherchez **Invitations Azure Data Share** dans le portail Azure. Vous accédez à la liste des invitations Data Share.

   ![Liste des invitations](./media/invitations.png "Liste des invitations") 

1. Sélectionnez le partage que vous souhaitez voir. 

### <a name="accept-invitation"></a>Accepter l’invitation
1. Veillez à passer en revue tous les champs, notamment les **Conditions d’utilisation**. Si vous acceptez les conditions d’utilisation, vous devez cocher la case indiquant que vous donnez votre accord. 

   ![Conditions d’utilisation](./media/terms-of-use.png "Conditions d’utilisation") 

1. Sous *Target Data Share Account* (Compte Data Share cible), sélectionnez l’abonnement et le groupe de ressources à utiliser pour le déploiement de votre partage de données. 

   Pour le champ **Data Share Account** (Compte Data Share), sélectionnez **Create new** (Créer) si vous n’avez pas de compte Data Share existant. Sinon, sélectionnez un compte Data Share existant dans lequel vous souhaitez accepter votre partage de données. 

   Pour le champ **Nom de partage reçu**, vous pouvez conserver la valeur par défaut spécifiée par le fournisseur de données ou spécifier le nouveau nom du partage reçu. 

   Une fois que vous avez accepté les conditions d’utilisation et spécifié un compte Data Share pour gérer votre partage reçu, sélectionnez **Accepter et configurer**. Un abonnement de partage est créé. 

   ![Options d’acceptation](./media/accept-options.png "Options d’acceptation") 

   Vous accédez alors au partage reçu dans votre compte Data Share. 

   Si vous ne souhaitez pas accepter l’invitation, sélectionnez *Reject* (Rejeter). 

### <a name="configure-received-share"></a>Configurer un partage reçu
Suivez les étapes ci-dessous pour configurer l’emplacement où vous souhaitez recevoir les données.

1. Sélectionnez l’onglet **Jeux de données**. Cochez la case en regard du jeu de données auquel vous souhaitez affecter une destination. Sélectionnez **+ Mapper à la cible** pour choisir un magasin de données cible. 

   ![Mapper sur cible](./media/dataset-map-target.png "Mapper sur cible") 

1. Sélectionnez un de magasin de données cible dans lequel vous souhaitez que les données arrivent. Tous les fichiers de données ou les tables dans le magasin de données cible ayant le même chemin et le même nom seront remplacés. 

   ![Compte de stockage cible](./media/dataset-map-target-sql.png "Magasin de données cible") 

1. Pour le partage basé sur un instantané, si le fournisseur de données a créé une planification d’instantané pour fournir une mise à jour régulière des données, vous pouvez également activer la planification des instantanés en sélectionnant l’onglet **Planification d’instantanés**. Cochez la case en regard de la planification d’instantané et sélectionnez **+ Activer**.

   ![Activer la planification d’instantané](./media/enable-snapshot-schedule.png "Activer la planification d’instantané")

### <a name="trigger-a-snapshot"></a>Déclencher une capture instantanée
Ces étapes s’appliquent seulement au partage basé sur des instantanés.

1. Vous pouvez déclencher un instantané en sélectionnant l’onglet **Détails** puis **Déclencher un instantané**. Ici, vous pouvez déclencher une capture instantanée complète ou incrémentielle de vos données. Si vous recevez des données de votre fournisseur de données pour la première fois, sélectionnez l’option de copie complète. Pour les sources SQL, seule la capture instantanée complète est prise en charge.

   ![Déclencher un instantané](./media/trigger-snapshot.png "Déclencher un instantané") 

1. Une fois que le dernier état d’exécution est *Réussi*, accédez au magasin de données cible pour voir les données reçues. Sélectionnez **Jeux de données**, puis cliquez sur le lien dans le chemin cible. 

   ![Jeux de données consommateur](./media/consumer-datasets.png "Mappage de jeu de données consommateur") 

### <a name="view-history"></a>Afficher l’historique
Cette étape s’applique uniquement au partage basé sur un instantané. Pour afficher l’historique de vos instantanés, sélectionnez l’onglet **Historique**. Vous trouverez ici un historique de tous les instantanés générés au cours des 30 derniers jours. 

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à partager et recevoir des données à partir d’un compte de stockage à l’aide du service Azure Data Share. Pour en savoir plus sur le partage à partir d’autres sources de données, passez à [Magasins de données pris en charge](supported-data-stores.md).

