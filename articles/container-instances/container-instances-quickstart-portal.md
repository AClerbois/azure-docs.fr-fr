---
title: "Guide de démarrage rapide : créer votre premier conteneur Azure Container Instances avec le portail Azure"
description: "Déploiement et prise en main d’Azure Container Instances"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7007fa8989f6d87028906918dbbc9c2998cd3bf4
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Créer son premier conteneur dans Azure Container Instances

Azure Container Instances simplifie la création et la gestion des conteneurs dans Azure. Dans ce guide de démarrage rapide, vous créez un conteneur dans Azure et l’exposez sur internet avec une adresse IP publique. Cette opération est accomplie à l’aide du portail Azure. Quelques clics suffisent pour afficher ceci dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-app-browser]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-container-instance"></a>Créer une instance de conteneur

Sélectionnez **Nouveau** > **Conteneurs** > **Azure Container Instances (préversion)**.

![Commencer à créer une instance de conteneur dans le portail Azure][aci-portal-01]

Entrez les valeurs suivantes dans les zones de texte **Nom du conteneur**, **Image de conteneur** et **Groupe de ressources**. Conservez les autres valeurs par défaut, puis cliquez sur **OK**.

* Nom du conteneur : `mycontainer`
* Image du conteneur : `microsoft/aci-helloworld`
* Groupe de ressources : `myResourceGroup`

![Configuration des paramètres de base pour une nouvelle instance de conteneur dans le portail Azure][aci-portal-03]

Vous pouvez créer des conteneurs Windows et Linux dans Azure Container Instances. Dans ce guide de démarrage rapide, nous conservons le paramètre par défaut **Linux**, car nous avons spécifié un conteneur basé sur Linux (`microsoft/aci-helloworld`) à l’étape précédente.

Conservez les valeurs par défaut des autres paramètres dans **Configuration**, puis cliquez sur **OK** pour valider la configuration.

![Configuration d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-04]

Une fois la validation terminée, un résumé des paramètres de votre conteneur s’affiche. Sélectionnez **OK** pour envoyer votre demande de déploiement de conteneur.

![Résumé des paramètres d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-05]

Lorsque le déploiement commence, une vignette apparaît sur le tableau de bord de votre portail, indiquant la progression du déploiement. Une fois le déploiement terminé, la vignette est mise à jour pour afficher votre nouveau groupe de conteneurs **mycontainer-myc1**.

![Progression de la création d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-08]

Sélectionnez le groupe de conteneurs **mycontainer-myc1** pour afficher ses propriétés. Prenez note de l’**Adresse IP** du groupe de conteneurs, ainsi que de l’**ÉTAT** de votre conteneur.

![Vue d’ensemble du groupe de conteneurs dans le portail Azure][aci-portal-06]

Lorsque le conteneur passe à l’état **En cours d’exécution**, accédez à l’adresse IP que vous avez notée à l’étape précédente pour afficher l’application hébergée dans votre nouveau conteneur.

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image dans un référentiel Hub Docker public. Si vous voulez essayer de créer un conteneur par vous-même et le déployer dans Azure Container Instances à l’aide d’Azure Container Registry, passez au didacticiel relatif à Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiels Azure Container Instances](./container-instances-tutorial-prepare-app.md)