---
title: Importer une application de fonction Azure en tant qu’API dans Gestion des API Azure | Microsoft Docs
description: Ce didacticiel vous montre comment importer une application de fonction Azure en tant qu’API dans le service Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: apimpm
ms.openlocfilehash: fe6a008a6cbd2ca4e8aedeeca6d96cc00f6b29d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046051"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importer une application de fonction Azure en tant qu’API dans Gestion des API Azure

Le service Gestion des API Azure prend en charge l’importation des applications Azure Function en tant que nouvelles API ou en les ajoutant à des API existantes. Le processus génère automatiquement une clé d’hôte dans l’application de fonction Azure, qui est ensuite assignée à une valeur nommée dans le service Gestion des API Azure.

Cet article décrit la procédure d’importation d’une application de fonction Azure en tant qu’API dans le service Gestion des API Azure. Il décrit également la procédure de test.

Vous apprendrez à :

> [!div class="checklist"]
> * Importer une application de fonction Azure en tant qu’API
> * Ajouter une application de fonction Azure à une API
> * Afficher la clé d’hôte de la nouvelle application de fonction Azure et la valeur nommée du service Gestion des API Azure
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

* Suivez le guide de démarrage rapide [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
* Vérifiez que votre abonnement contient une application Azure Functions. Pour plus d’informations, consultez [Création d’une application Azure Function](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). L’application doit contenir des fonctions avec un déclencheur HTTP et un paramètre de niveau d’autorisation défini sur *Anonyme* ou *Fonction*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Importer une application de fonction Azure en tant que nouvelle API

Suivez les étapes ci-dessous pour créer une nouvelle API à partir d’une application de fonction Azure.

1. Dans votre instance du service **Gestion des API Azure**, sélectionnez **API** dans le menu de gauche.

2. Dans la liste **Ajouter une nouvelle API**, sélectionnez **Application de fonction**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-01.png)

3. Cliquez sur **Parcourir** pour sélectionner des fonctions à importer.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-02.png)

4. Cliquez sur la section **Application de fonction** pour afficher la liste des applications de fonction disponibles.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-03.png)

5. Recherchez l’application de fonction à partir de laquelle vous souhaitez importer des fonctions, cliquez dessus, puis appuyez sur **Sélectionner**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-04.png)

6. Sélectionnez les fonctions que vous souhaitez importer et cliquez sur **Sélectionner**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Vous pouvez importer uniquement des fonctions qui utilisent un déclencheur HTTP et dont le paramètre de niveau d’autorisation est défini sur *Anonyme* ou *Fonction*. À ce stade, les applications de fonction Linux ne sont pas prises en charge.

7. Passez à la vue **Complète** et affectez **Produit** à votre nouvelle API. Si nécessaire, modifiez les autres champs préremplis.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-06.png)

8. Cliquez sur **Créer**.

## <a name="append-azure-function-app-to-api"></a> Ajouter une application de fonction Azure à une API existante

Suivez les étapes ci-dessous pour ajouter une application de fonction Azure à une API existante.

1. Dans votre instance du service **Gestion des API Azure**, sélectionnez **API** dans le menu de gauche.

2. Choisissez l’API dans laquelle vous souhaitez importer une application de fonction Azure. Cliquez sur **...** et sélectionnez **Importer** dans le menu contextuel.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/append-01.png)

3. Cliquez sur la vignette **Application de fonction**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/append-02.png)

4. Dans la fenêtre indépendante qui s’affiche, cliquez sur **Parcourir**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/append-03.png)

5. Cliquez sur la section **Application de fonction** pour afficher la liste des applications de fonction disponibles.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-03.png)

6. Recherchez l’application de fonction à partir de laquelle vous souhaitez importer des fonctions, cliquez dessus, puis appuyez sur **Sélectionner**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-04.png)

7. Sélectionnez les fonctions que vous souhaitez importer et cliquez sur **Sélectionner**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/add-05.png)

8. Cliquez sur **Importer**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a> Autorisation

L’importation d’une application de fonction Azure génère automatiquement :

* Une clé d’hôte au sein de l’application de fonction portant le nom apim-{*nom de votre instance de service Gestion des API Azure*}.
* Une valeur nommée au sein de l’instance de Gestion des API Azure portant le nom {*nom de votre instance d’Azure Function App*}-key, qui contient la clé d’hôte créée.

Pour les API créées après le 4 avril 2019, la clé d’hôte est passée dans les requêtes HTTP de la Gestion des API vers l’application de fonction dans un en-tête. Les API plus anciennes transmettent la clé d’hôte sous la forme d’[un paramètre de requête](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization). Vous pouvez changer ce comportement par le biais de l’[appel à l’API REST](https://docs.microsoft.com/rest/api/apimanagement/backend/update#backendcredentialscontract) `PATCH Backend` sur l’entité *Backend* associée à l’application de fonction.

> [!WARNING]
> En cas de suppression ou de modification de la clé d’hôte Azure Function App ou de la valeur nommée de Gestion des API Azure, la communication entre les services sera interrompue. Les valeurs ne se synchronisent pas automatiquement.
>
> Si vous avez besoin de modifier la clé d’hôte, assurez-vous que la valeur nommée est également modifiée dans le service Gestion des API Azure.

### <a name="access-azure-function-app-host-key"></a>Accéder à la clé d’hôte d’application de fonction Azure

1. Accédez à votre instance d’application de fonction Azure.

2. Sélectionnez **Paramètres Function App** dans la vue d’ensemble.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/keys-02-a.png)

3. La clé s’affiche dans la section **Clés d’hôte**.

    ![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Accéder à la valeur nommée dans le service Gestion des API Azure

Accédez à votre instance de Gestion des API Azure et sélectionnez **Valeurs nommées** dans le menu de gauche. Vous y trouverez la clé Azure Function App.

![Ajouter à partir d’une application de fonction](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Tester la nouvelle API Gestion des API dans le portail Azure

Vous pouvez appeler des opérations directement depuis le portail Azure. Le portail Azure est pratique pour afficher et tester les opérations d’une API.  

1. Sélectionnez l’API que vous avez créée dans la section précédente.

2. Sélectionnez l’onglet **Test**.

3. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est **Ocp-Apim-Subscription-Key**, pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance Gestion des API, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 

4. Sélectionnez **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

## <a name="test-in-developer-portal"></a> Appeler une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations à partir du portail des développeurs pour tester les API. 

1. Sélectionnez l’API que vous avez créée à l’étape Importer et publier une API de serveur principal.

2. Sélectionnez **Portail des développeurs**.

    Le site « Portail des développeurs » s’ouvre.

3. Sélectionnez l’**API** que vous avez créée.

4. Cliquez sur l’opération que vous souhaitez tester.

5. Sélectionnez **Essayer**.

6. Sélectionnez **Envoyer**.
    
    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
