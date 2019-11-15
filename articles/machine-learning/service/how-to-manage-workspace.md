---
title: Créer des espaces de travail Azure ML dans le portail
titleSuffix: Azure Machine Learning
description: Découvrez comment créer, afficher et supprimer des espaces de travail Azure Machine Learning dans le portail Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 9e3915a2f55680fc2e24ac63d5e7170ada026c2e
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615157"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Créer et gérer des espaces de travail Azure Machine Learning dans le Portail Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez créer, afficher et supprimer des [**espaces de travail Azure Machine Learning**](concept-workspace.md) dans le portail Azure pour [Azure Machine Learning](overview-what-is-azure-ml.md).  Le portail est le moyen le plus simple de prendre en main les espaces de travail, mais à mesure que vos besoins évoluent ou que les besoins de l’automatisation augmentent, vous pouvez également créer et supprimer des espaces de travail [à l’aide de l’interface CLI](reference-azure-machine-learning-cli.md), [à l’aide de code Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [via l’extension VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Créer un espace de travail

Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de votre abonnement Azure. 

1. En haut à gauche du portail Azure, sélectionnez **+ Créer une ressource**.

      ![Créer une nouvelle ressource](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. Utilisez la barre de recherche pour rechercher **Machine Learning**.

1. Sélectionnez **Machine Learning**.

1. Dans le volet **Machine Learning**, sélectionnez **Créer** pour commencer.

1. Fournissez les informations suivantes pour configurer votre nouvel espace de travail :

   Champ|Description 
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.
   Édition de l’espace de travail | Sélectionnez **De base** ou **Entreprise**.  Cette édition de l’espace de travail détermine les fonctionnalités auxquelles vous aurez accès et la tarification. En savoir plus sur les [offres De base et Édition Entreprise](overview-what-is-azure-ml.md#sku). 

    ![Configurer votre espace de travail](media/how-to-manage-workspace/select-edition.png)

1. Une fois que vous avez terminé de configurer l’espace de travail, sélectionnez **Créer**. 

   > [!Warning] 
   > La création de votre espace de travail dans le cloud peut prendre plusieurs minutes.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. 
 
 1. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

### <a name="download-a-configuration-file"></a>Télécharger un fichier de configuration

1. Si vous prévoyez de créer une [machine virtuelle Notebook](tutorial-1st-experiment-sdk-setup.md#azure), ignorez cette étape.

1. Si vous prévoyez d’utiliser du code sur votre environnement local qui référence cet espace de travail, sélectionnez **Télécharger config.json** dans la section **Vue d’ensemble** de l’espace de travail.  

   ![Télécharger config.json](./media/how-to-manage-workspace/configure.png)
   
   Placez le fichier dans la structure de répertoires avec vos scripts Python ou vos notebooks Jupyter. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent. Quand vous créez une machine virtuelle Notebook, ce fichier est automatiquement ajouté au répertoire approprié sur la machine virtuelle.

## <a name="upgrade"></a>Mise à jour vers l’édition Enterprise

Vous pouvez mettre à jour votre espace de travail de l’édition De base vers l’édition Entreprise pour tirer parti des fonctionnalités améliorées, telles que les expériences à faible niveau de code et les fonctionnalités de sécurité améliorées.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez l’espace de travail que vous souhaitez mettre à jour.

1. Sélectionnez **Mettre à niveau** en haut ou dans le message de mise à niveau.

    ![Mettre à niveau un espace de travail](media/how-to-manage-workspace/upgrade.png)

1. Sélectionnez **Confirmer la mise à jour**.


> [!IMPORTANT]
> Vous ne pouvez pas rétrograder un espace de travail Édition Entreprise vers une édition De base. 

## <a name="view"></a>Trouver un espace de travail

1. Dans le champ de recherche du haut, tapez **Machine Learning**.  

1. Sélectionnez **Machine Learning**.

   ![Rechercher l’espace de travail Azure Machine Learning](media/how-to-manage-workspace/find-workspaces.png)

1. Examinez la liste des espaces de travail trouvés. Vous pouvez filtrer en fonction d’abonnements, de groupes de ressources et de localisations.  

1. Sélectionnez un espace de travail pour afficher ses propriétés.

## <a name="delete-a-workspace"></a>Supprimer un espace de travail

Utilisez le bouton Supprimer en haut de l’espace de travail que vous souhaitez supprimer.

  ![Bouton Supprimer](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Suivez le didacticiel complet pour apprendre à utiliser un espace de travail et créer, faire l’apprentissage et déployer des modèles avec Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
