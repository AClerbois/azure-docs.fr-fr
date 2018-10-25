---
title: Utiliser le portail Azure pour déployer l’application de catalogue de services | Microsoft Docs
description: Explique aux consommateurs d’applications managées comment déployer une application de catalogue de services via le portail Microsoft Azure.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806154"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Déployer une application du catalogue de services à l’aide du portail Microsoft Azure

Dans le [démarrage rapide précédent](publish-managed-app-definition-quickstart.md), vous avez publié une définition d’application managée. Dans ce démarrage rapide, vous allez créer une application de catalogue de services à partir de cette définition.

## <a name="create-service-catalog-app"></a>Créer l’application de catalogue de services

Dans le portail Microsoft Azure, procédez comme suit :

1. Sélectionnez **Créer une ressource**.

   ![Créer une ressource](./media/deploy-service-catalog-quickstart/create-new.png)

1. Recherchez **Application managée du catalogue de services** et sélectionnez-la parmi les options disponibles.

   ![Rechercher l’application de catalogue de services](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Une description du service Application managée s’affiche. Sélectionnez **Créer**.

   ![Sélectionner Créer](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Le portail affiche les définitions d’application managée auxquelles vous avez accès. Dans les définitions disponibles, sélectionnez celle que vous souhaitez déployer. Dans ce démarrage rapide, utilisez la définition **Managed Storage Account (Compte de stockage managé)** que vous avez créée dans le démarrage rapide précédent. Sélectionnez **Créer**.

   ![Sélectionner la définition à déployer](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Renseignez les valeurs dans l’onglet **De base**. Sélectionnez l’abonnement Azure dans lequel déployer votre application de catalogue de services. Créez un groupe de ressources nommé **applicationGroup**. Sélectionnez l’emplacement de votre application. Lorsque vous avez terminé, sélectionnez **OK**.

   ![Renseigner les valeurs dans l’onglet De base](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Indiquez le préfixe du nom du compte de stockage. Sélectionnez le type de compte de stockage à créer. Lorsque vous avez terminé, sélectionnez **OK**.

   ![Renseignez les valeurs pour le stockage](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Passez en revue le résumé. Si la validation aboutit, sélectionnez **OK** pour commencer le déploiement.

   ![Afficher le résumé](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Afficher les résultats

Une fois l’application de catalogue de services déployée, vous avez deux nouveaux groupes de ressources. Un groupe de ressources contient l’application de catalogue de services. L’autre groupe contient les ressources de l’application de catalogue de services.

1. Affichez le groupe de ressources **applicationGroup** pour voir l’application de catalogue de services.

   ![Afficher l’application](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Affichez le groupe de ressources **applicationGroup{caractères-hachage}** pour voir les ressources de l’application de catalogue de services.

   ![Afficher les ressources](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment créer les fichiers de définition d’une application managée, consultez [Créer et publier une définition d’application gérée](publish-service-catalog-app.md).
* Pour Azure CLI, consultez [Déployer une application managée pour le catalogue de services avec Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Pour PowerShell, consultez [Déployer une application managée pour un catalogue de services avec PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).