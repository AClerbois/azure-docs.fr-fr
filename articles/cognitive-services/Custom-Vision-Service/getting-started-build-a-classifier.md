---
title: 'Démarrage rapide : Créer un classifieur - Service Custom Vision'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser le site web Custom Vision pour créer un modèle de classification d’images.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: anroth
ms.openlocfilehash: dfc137375e35d0d13a34ff45b0c6639bcf6784df
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561060"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Démarrage rapide : Comment créer un classifieur avec Custom Vision

Dans ce guide de démarrage rapide, vous découvrez comment créer un classifieur via le site web Custom Vision. Une fois que vous avez créé un modèle de classifieur, vous pouvez utiliser le service Custom Vision pour la classification d’images.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un ensemble d’images avec lequel entraîner votre classifieur. Reportez-vous aux conseils ci-dessous pour le choix des images.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Créer des ressources Custom Vision dans le portail Azure

Pour utiliser le service Custom Vison, vous devez créer des ressources d’entraînement et de prédiction Custom Vision dans le portail Azure. Remplissez la boîte de dialogue de la page de [création Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) pour créer une ressource d’entraînement et une ressource de prédiction. 

## <a name="create-a-new-project"></a>Création d'un projet

Dans votre navigateur web, accédez à la [page web Custom Vision](https://customvision.ai) et sélectionnez __Sign in__ (Se connecter). Connectez-vous avec le même compte que celui utilisé pour vous connecter au portail Azure.

![Image de la page de connexion](./media/browser-home.png)


1. Pour créer votre premier projet, sélectionnez **New Project** (Nouveau projet). La boîte de dialogue **Create new project** (Créer un projet) s’affiche.

    ![La boîte de dialogue du nouveau projet comporte des champs permettant de renseigner le nom, la description et les domaines.](./media/getting-started-build-a-classifier/new-project.png)

1. Entrez un nom et une description pour le projet. Sélectionnez ensuite un groupe de ressources. Si votre compte de connexion est associé à un compte Azure, la liste déroulante Resource Group (Groupe de ressources) affiche tous vos groupes de ressources Azure qui incluent une ressource du service Vision personnalisée. 

   > [!NOTE]
   > Si aucun groupe de ressources n’est disponible, confirmez vous être connecté à [customvision.ai](https://customvision.ai) avec le même compte que celui utilisé pour vous connecter au [portail Azure](https://portal.azure.com/). En outre, assurez-vous d'avoir sélectionné le même « Répertoire » dans le portail de Custom Vision que celui du portail Azure où se trouvent vos ressources Custom Vision. Dans les deux sites, vous pouvez sélectionner votre répertoire à partir du menu déroulant de compte situé en haut à droite de l’écran. 

1. Sélectionnez __Classification__ sous __Project Types__ (Types de projets). Ensuite, sous __Classification Types__ (Types de classifications), choisissez **Multilabel** (Multi-étiquette) ou **Multiclass** (Multiclasse), en fonction de votre cas d’utilisation. La classification multi-étiquette applique un nombre quelconque de vos étiquettes à une image (zéro ou plus), tandis que la classification multiclasse trie les images et les classe dans une seule catégorie (chaque image que vous soumettez se voit appliquer l’étiquette la plus probable). Vous pourrez changer ultérieurement le type de classification si vous le souhaitez.

1. Sélectionnez ensuite un des domaines disponibles. Chaque domaine optimise le classifieur pour des types spécifiques d’images, comme décrit dans le tableau suivant. Vous pourrez changer ultérieurement le domaine si vous le souhaitez.

    |Domaine|Objectif|
    |---|---|
    |__Générique__| Optimisé pour un large éventail de tâches de classification d’images. Si aucun autre domaine n’est approprié, ou si vous hésitez sur le choix du domaine, sélectionnez le domaine Generic (Général). |
    |__Food__ (Nourriture)|Optimisé pour les photographies de plats, tels que vous pouvez les voir dans un menu de restaurant. Si vous souhaitez classer des photographies de fruits ou de légumes distincts, utilisez le domaine Food (Nourriture).|
    |__Landmarks__ (Monuments et sites)|Optimisé pour les monuments et sites reconnaissables, naturels et artificiels. Ce domaine fonctionne mieux lorsque le monument ou le site est clairement visible dans la photographie. Ce domaine fonctionne même si le monument ou le site est légèrement masqué par des personnes placées devant lui.|
    |__Retail__ (Commerce)|Optimisé pour les images qui se trouvent dans des catalogues de vente ou sur des site Web commerciaux. Si vous souhaitez un classement de grande précision pour des robes, des pantalons et des chemises, utilisez ce domaine.|
    |__Compact Domains__ (Domaines compacts)| Optimisés en fonction des contraintes de la classification en temps réel sur les appareils mobiles. Les modèles générés par les domaines compacts sont exportables pour s’exécuter localement.|

1. Enfin, sélectionnez __Create project__ (Créer le projet).

## <a name="choose-training-images"></a>Choisir les images d’entraînement

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Dans cette section, vous chargez et vous étiquetez manuellement des images pour faciliter l’entraînement du classifieur. 

1. Pour ajouter des images, cliquez sur le bouton __Add images__ (Ajouter des images), puis sélectionnez __Browse local files__ (Parcourir les fichiers locaux). Sélectionnez __Open__ (Ouvrir) pour passer à l’étiquetage. Votre sélection d’étiquettes s’applique à l’ensemble du groupe d’images que vous avez choisi de charger : il est donc plus facile de charger des images dans des groupes distincts selon les étiquettes souhaitées. Vous pouvez également changer les étiquettes pour des images individuelles après leur chargement.

    ![Le contrôle d’ajout d’images est affiché dans le coin supérieur gauche, et sous la forme d’un bouton dans le bas de la page, au centre.](./media/getting-started-build-a-classifier/add-images01.png)


1. Pour créer une étiquette, entrez du texte dans le champ __My Tags__ (Mes étiquettes) et appuyez sur Entrée. Si l’étiquette existe déjà, elle apparaît dans un menu déroulant. Dans un projet multi-étiquette, vous pouvez ajouter plusieurs étiquettes à vos images, mais dans un projet multiclasse, vous ne pouvez en ajouter qu’une seule. Pour terminer le chargement des images, utilisez le bouton __Upload [nombre] files__ (Charger [nombre] fichiers). 

    ![Image de la page d’attribution des mots clés et du chargement](./media/getting-started-build-a-classifier/add-images03.png)

1. Sélectionnez __Terminé__ lorsque les images ont été chargés.

    ![La barre de progression indique que toutes les tâches sont terminées.](./media/getting-started-build-a-classifier/add-images04.png)

Pour charger un autre ensemble d’images, revenez en haut de cette section et répétez les étapes.

## <a name="train-the-classifier"></a>Former le classifieur

Pour former le classifieur, sélectionnez le bouton **Train** (Entraîner). Le classifieur utilise toutes les images pour créer un modèle qui identifie les qualités visuelles de chaque étiquette.

![Le bouton Train (Entraîner) en haut à droite de la barre d’outils de l’en-tête de la page web](./media/getting-started-build-a-classifier/train01.png)

Le processus d’entraînement ne doit durer que quelques minutes. Pendant ce temps, des informations sur le processus d’entraînement s’affichent dans l’onglet **Performance**.

![Fenêtre du navigateur avec une boîte de dialogue d’entraînement dans la section principale](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Évaluer le classifieur

Une fois l’entraînement terminé, les performances du modèle sont estimées et affichées. Le service Vision personnalisée utilise les images que vous avez soumises pour l’entraînement et calcule la précision et le rappel en utilisant un processus appelé [validation croisée par échantillons (« k-fold »)](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). La précision et le rappel sont deux mesures différentes de l’efficacité d’un classifieur :

- La **précision** indique la proportion des classifications identifiées qui étaient correctes. Par exemple, si le modèle a identifié 100 images comme étant des chiens et que 99 d’entre elles étaient effectivement des chiens, la précision est de 99 %.
- Le **rappel** indique la proportion des classifications réelles qui ont été correctement identifiées. Par exemple, s’il y avait 100 images de pommes et que le modèle en a identifié 80 comme étant des pommes, le rappel est de 80 %.

![Les résultats de l’entraînement affichent les taux de précision et de rappel globaux, ainsi que ceux de chaque mot clé dans le classifieur.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Seuil de probabilité

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gérer les itérations d’entraînement

Chaque fois vous entraînez votre classifieur, vous créez une nouvelle _itération_ avec ses propres métriques de performances mises à jour. Vous pouvez afficher toutes vos itérations dans le volet gauche de l’onglet **Performance**. Dans le volet gauche, vous trouvez également le bouton **Delete** (Supprimer), que vous pouvez utiliser pour supprimer une itération si elle est obsolète. Quand vous supprimez une itération, vous supprimez également toutes les images qui lui sont spécifiquement associées.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment créer et entraîner un modèle de classification d’images avec le site web Custom Vision. Vous obtenez ensuite plus d’informations sur le processus itératif d’amélioration de votre modèle.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)

