---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 88751efdd5aaceddeed490c95d15d82a263fa81e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
   
   ![Volet « Bases de données » du portail Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Dans la page **Nouveau compte**, entrez les paramètres pour le nouveau compte Azure Cosmos DB. 

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Entrez un nom unique*|Entrez un nom unique pour identifier ce compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI, utilisez un ID unique mais identifiable.<br><br>L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|Gremlin (graphique)|L’API détermine le type de compte à créer. Azure Cosmos DB fournit cinq API pour répondre aux besoins de votre application : SQL (base de données Document), Gremlin (base de données de graphiques), MongoDB (base de données Document), Table Azure et Cassandra, qui nécessitent toutes un compte séparé. <br><br>Sélectionnez **Gremlin (graphe)**, car dans ce démarrage rapide, vous créez un graphe qui peut être interrogé avec la syntaxe Gremlin.<br><br>[En savoir plus sur l’API Graph](../articles/cosmos-db/graph-introduction.md)
    Abonnement|*Votre abonnement*|Sélectionnez l’abonnement Azure que vous voulez utiliser pour ce compte Azure Cosmos DB. 
    Groupe de ressources|Création<br><br>*Entrez ensuite le même nom unique que celui fourni plus haut dans ID*|Sélectionnez **Créer**, puis entrez le nom du nouveau groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID.
    Lieu|*Sélectionner la région la plus proche de vos utilisateurs*|Sélectionnez l’emplacement géographique où héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs, pour leur donner l’accès le plus rapide possible aux données.
    Activer la géoredondance| Laisser vide | Ceci crée une version répliquée de votre base de données dans une seconde région (appairée). Laissez ce champ vide.  
    Épingler au tableau de bord | Sélectionnez | Cochez cette case pour que votre nouveau compte de base de données soit ajouté à votre tableau de bord du portail pour un accès facilité.

    Cliquez ensuite sur **Créer**.

    ![Panneau de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. La création du compte prend quelques minutes. Attendez que le portail affiche la page **Félicitations ! Votre compte Azure Cosmos DB a été créé**.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)