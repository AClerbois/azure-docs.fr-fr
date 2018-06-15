---
title: Utiliser Robomongo pour Azure Cosmos DB | Microsoft Docs
description: 'Découvrez comment utiliser Robomongo avec un compte Azure Cosmos DB : API pour MongoDB'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: b6d64d7d7b30d4175fb8c8bf6c98127465427d4e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795020"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Utiliser Robomongo avec un compte Azure Cosmos DB : API pour MongoDB
Pour vous connecter à un compte Azure Cosmos DB : API pour MongoDB avec Robomongo, vous devez :

* Télécharger et installer [Robomongo](https://robomongo.org/)
* Disposer des informations de [chaîne de connexion](connect-mongodb-account.md) de votre compte Azure Cosmos DB : API pour MongoDB

## <a name="connect-using-robomongo"></a>Connexion à l’aide de Robomongo
Pour ajouter votre compte Azure Cosmos DB : API pour MongoDB aux connexions Robomongo MongoDB, procédez comme suit.

1. Récupérez les informations de connexion de votre compte Azure Cosmos DB : API pour MongoDB à l’aide des instructions [ici](connect-mongodb-account.md).

    ![Capture d’écran du panneau Chaîne de connexion](./media/mongodb-robomongo/connectionstringblade.png)
2. Exécutez *Robomongo.exe*

3. Cliquez sur le bouton de connexion sous **Fichier** pour gérer vos connexions. Ensuite, cliquez sur **Créer** dans la fenêtre **Connexions MongoDB**, ce qui ouvre la fenêtre **Paramètres de connexion**.

4. Dans la fenêtre **Paramètres de connexion**, choisissez un nom. Ensuite, recherchez **Hôte** et **Port** dans vos informations de connexion à l’étape 1 et entrez-les dans les champs **Adresse** et **Port**, respectivement.

    ![Capture d’écran de gestion des connexions avec Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Dans l’onglet **Authentification**, cliquez sur **Effectuer l’authentification**. Entrez ensuite votre base de données (la valeur par défaut est *Admin*), le **Nom d’utilisateur** et le **Mot de passe**.
Le **Nom d’utilisateur** et le **Mot de passe** figurent tous deux dans vos informations de connexion à l’étape 1.

    ![Capture d’écran de l’onglet authentification de Robomongo](./media/mongodb-robomongo/authentication.png)
6. Dans l’onglet **SSL**, cochez **Utiliser le protocole SSL**, puis modifiez la **Méthode d’authentification** sur **Certificat auto-signé**.

    ![Capture d’écran de l’onglet SSL de Robomongo](./media/mongodb-robomongo/SSL.png)
7. Enfin, cliquez sur **Test** pour vérifier que vous êtes en mesure de vous connecter, puis sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* Explorez les [exemples](mongodb-samples.md) d’Azure Cosmos DB : API pour MongoDB.
