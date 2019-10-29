---
title: Déployer un modèle Azure Resource Manager - IntelliJ IDEA
description: Découvrez comment créer votre premier modèle Azure Resource Manager à l’aide d’IntelliJ IDEA et comment le déployer.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: 137a87ad0314fad08f9af0eb567e1d31032ddcb9
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533650"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Démarrage rapide : Créer et déployer des modèles Azure Resource Manager avec IntelliJ IDEA

Découvrez comment déployer un modèle Resource Manager sur Azure avec IntelliJ IDEA et comment le modifier et le mettre à jour directement à partir de l’environnement de développement intégré. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Présentation d’Azure Resource Manager](resource-group-overview.md).

![Schéma du portail de démarrage rapide du modèle Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Après avoir suivi le tutoriel, vous déployez un compte de Stockage Azure. Le même processus peut être utilisé pour déployer d’autres ressources Azure.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition
* Une fois que [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) est installé, consultez le [guide de gestion des plug-ins IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html) pour plus d’informations.
* [Être](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) connecté à votre compte Azure pour Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Déployer un modèle de démarrage rapide

Au lieu de créer un modèle à partir de zéro, ouvrez un modèle à partir de [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/). Le référentiel Modèles de démarrage rapide Azure contient les modèles Resource Manager. Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Il définit une ressource de compte de stockage Azure.

1. Cliquez avec le bouton droit et enregistrez [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) et [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) sur votre ordinateur local.

1. Si Azure Toolkit est correctement installé et connecté, vous devriez voir Azure Explorer dans la barre latérale d’IntelliJ IDEA. Cliquez avec le bouton droit sur **Gestion des ressources** , puis sélectionnez **Créer un déploiement**.

    ![Modèle Resource Manager : cliquer avec le bouton droit pour créer un déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Configurez les éléments **Nom du déploiement**, **Abonnement**, **Groupe de ressources** et **Région**. Dans le cas présent, nous déployons le modèle dans un nouveau groupe de ressources `testRG`. Ensuite, sous **Modèle de ressource**, sélectionnez le chemin vers `azuredeploy.json` et, sous **Paramètres de ressource**, le chemin vers le fichier `azuredeploy.parameters.json` que vous avez téléchargé.

    ![Modèle Resource Manager : sélectionner les fichiers pour créer un déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Une fois que vous avez cliqué sur OK, le déploiement démarre. La progression est indiquée en bas, dans la **barre d’état** d’IntelliJ IDEA, jusqu’à l’achèvement du déploiement.

    ![Modèle Resource Manager : état du déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Parcourir un déploiement existant

1. Une fois le déploiement terminé, vous pouvez voir le nouveau groupe de ressources `testRG` et un nouveau déploiement créé. Cliquez avec le bouton droit sur le déploiement pour voir la liste des actions possibles. À présent, sélectionnez **Afficher les propriétés**.

    ![Modèle Resource Manager : parcourir un déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Un onglet s’ouvre et présente des propriétés utiles telles que l’état du déploiement et la structure du modèle.

    ![Modèle Resource Manager : afficher les propriétés du déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Modifier et mettre à jour un déploiement existant

1. Sélectionnez **Modifier le déploiement** dans le menu contextuel ou la vue Afficher les propriétés ouverte précédemment. Un autre onglet s’ouvre, présentant les fichiers de modèles et de paramètres pour le déploiement sur Azure. Pour enregistrer ces fichiers sur l’ordinateur local, vous pouvez cliquer sur **Exporter le fichier de modèle** ou **Exporter le fichier de paramètres**.

    ![Modèle Resource Manager : modifier le déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Vous pouvez modifier les deux fichiers sur cette page et déployer les modifications dans Azure. Dans le cas présent, nous modifions la valeur de **storageAccountType** dans les fichiers de paramètres en remplaçant `Standard_LRS` par `Standard_GRS`. Ensuite, cliquez sur **Mettre à jour un déploiement** en bas de l’écran, puis confirmez la mise à jour.

    ![Modèle Resource Manager : modifier le déploiement](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Une fois le déploiement mis à jour, vous pouvez accéder au portail pour vérifier que le compte de stockage créé est remplacé par `Standard_GRS`.

## <a name="clean-up-resources"></a>Supprimer des ressources

1. Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources. Vous pouvez le faire par le biais du portail Azure ou de l’interface de ligne de commande Azure. Dans Azure Explorer, à partir d’IntelliJ IDEA, cliquez avec le bouton droit sur le **groupe de ressources** que vous avez créé, puis sélectionnez Supprimer.

    ![Supprimer un groupe de ressources dans Azure Explorer à partir d’IntelliJ IDEA](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Notez que la suppression d’un déploiement ne supprime pas les ressources créées par le déploiement. Supprimez le groupe de ressources correspondant ou des ressources spécifiques quand vous n’en avez plus besoin.

## <a name="next-steps"></a>Étapes suivantes

Le principal objectif de ce guide de démarrage rapide est d’utiliser IntelliJ IDEA pour déployer un modèle existant à partir de modèles de démarrage rapide Azure. Vous avez également appris à consulter et à mettre à jour un déploiement existant sur Azure. Il est possible que les modèles tirés des modèles de démarrage ne vous apportent pas tout ce dont vous avez besoin. Pour en savoir plus sur le développement de modèles, consultez notre nouvelle série de tutoriels pour les débutants :

> [!div class="nextstepaction"]
> [Tutoriels pour les débutants](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Visiter le centre de développement Java sur Azure](https://docs.microsoft.com/azure/java)
