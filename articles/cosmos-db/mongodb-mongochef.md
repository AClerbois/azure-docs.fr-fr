---
title: Se connecter à l’API Azure Cosmos DB pour MongoDB avec Studio 3T
description: Découvrez comment vous connecter à une API Azure Cosmos DB pour MongoDB à l’aide de Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: c9be9893d28439fdcff9f33f0303a816bd94c7b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584421"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Se connecter à un compte Azure Cosmos DB à l’aide de Studio 3T

Pour vous connecter à l’API Azure Cosmos DB pour MongoDB avec Studio 3T, vous devez procéder comme suit :

* Téléchargez et installez [Studio 3T](https://studio3t.com/).
* Obtenez les informations de la [chaîne de connexion](connect-mongodb-account.md) de votre compte Azure Cosmos.

> [!NOTE]
> Actuellement, les versions v1.2 et antérieures de Robo 3T sont prises en charge par l’API Cosmos DB pour MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Créer la connexion dans Studio 3T

Pour ajouter votre compte Azure Cosmos DB au gestionnaire de connexions Studio 3T, procédez comme suit :

1. Récupérez les informations de connexion de votre API Azure Cosmos DB pour votre compte MongoDB en suivant les instructions figurant dans l’article [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).

    ![Capture d’écran de la page Chaîne de connexion](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Cliquez sur **Connexion** pour ouvrir le gestionnaire de connexions, puis cliquez sur **Nouvelle connexion**.

    ![Capture d’écran du gestionnaire de connexions Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Sous l’onglet **Serveur** de la fenêtre **Nouvelle connexion**, saisissez l’HÔTE (FQDN) du compte Azure Cosmos DB et le PORT.

    ![Capture d’écran de l’onglet Serveur du gestionnaire de connexions Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **Authentification**, choisissez le mode d’authentification **De base (MONGODB-CR ou SCARM-SHA-1)** et entrez les NOM D’UTILISATEUR et MOT DE PASSE.  Acceptez la base de données d’authentification par défaut (admin) ou indiquez votre propre valeur.

    ![Capture d’écran de l’onglet Authentification du gestionnaire de connexions Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **SSL**, cochez la case **Utiliser le protocole SSL pour se connecter** et sélectionnez **Accepter les certificats SSL auto-signés**.

    ![Capture d’écran de l’onglet SSL du gestionnaire de connexions Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Cliquez sur le bouton **Tester la connexion** pour valider les informations de connexion, cliquez sur **OK** pour revenir à la fenêtre Nouvelle connexion, puis cliquez sur **Enregistrer**.

    ![Capture d’écran de la fenêtre Tester la connexion de Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Utiliser Studio 3T pour créer une base de données, une collection et des documents
Pour créer une base de données, une collection et des documents à l’aide de Studio 3T, procédez comme suit :

1. Dans le **Gestionnaire de connexions**, sélectionnez la connexion et cliquez sur **Connexion**.

    ![Capture d’écran du gestionnaire de connexions Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Cliquez avec le bouton droit sur l’hôte et choisissez **Ajouter une base de données**.  Spécifiez un nom de base de données, puis cliquez sur **OK**.

    ![Capture d’écran de l’option Ajouter une base de données de Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Cliquez avec le bouton droit sur la base de données et choisissez **Ajouter une collection**.  Spécifiez un nom de collection et cliquez sur **Créer**.

    ![Capture d’écran de l’option Ajouter une collection de Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Cliquez sur l’élément de menu **Collection**, puis cliquez sur **Ajouter un document**.

    ![Capture d’écran de l’option Ajouter un document de Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Dans la boîte de dialogue Ajouter un document, collez les éléments suivants, puis cliquez sur **Ajouter un document**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Ajoutez un autre document, cette fois avec le contenu suivant :

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Exécutez un exemple de requête. Par exemple, recherchez des familles portant le nom « Andersen » en retournant les champs parents et état.

    ![Capture d’écran des résultats de requête Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
