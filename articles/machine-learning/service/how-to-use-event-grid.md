---
title: Créer des flux de travail Machine Learning basés sur les événements
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Event Grid avec Azure Machine Learning pour activer des solutions basées sur les événements.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622360"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Créer des flux de travail Machine Learning basés sur les événements (préversion)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) prend en charge les événements de service Azure Machine Learning. Par exemple, vous pouvez utiliser les événements de l’achèvement de l’exécution, de l’inscription de modèle, du déploiement de modèle et de la détection de dérive des données étendus à un espace de travail.

Pour plus d’informations, consultez [Intégration d’Azure Machine Learning avec Event Grid](concept-event-grid-integration.md) et [Schéma Event Grid Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Utilisez Event Grid pour activer des scénarios courants tels que :

* Déclenchement de pipelines pour la reformation
* Événements de diffusion en continu d’Azure Machine Learning sur divers points de terminaison

## <a name="prerequisites"></a>Prérequis

* Accès du contributeur ou du propriétaire à l’espace de travail du service Azure Machine Learning pour lequel vous allez créer des événements.
* Sélectionnez un point de terminaison de gestionnaire d’événements, tel qu’un webhook ou un hub d’événements. Pour plus d’informations, consultez la section relative aux [gestionnaires d’événements](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Si vous avez utilisé Azure Event Grid ou Machine Learning avant le 1er novembre 2019, vous devrez peut-être réinscrire les fournisseurs de ressources avant de pouvoir suivre les étapes décrites dans ce document. Pour réinscrire les fournisseurs, procédez comme suit :

1. Accédez au Portail Azure et sélectionnez __Abonnements__. Sélectionnez l’abonnement que vous souhaitez utiliser.
1. Sélectionnez __Fournisseurs de ressources__, puis recherchez __EventGrid__.
1. Sélectionnez l’entrée __Microsoft.EventGrid__, puis __Réinscrire__.

    ![re-register-resource-provider](media/how-to-use-event-grid/re-register-resource-provider.png)

1. Recherchez __MachineLearningServices__, sélectionnez l’entrée, puis __Réinscrire__.

> [!TIP]
> Si vous ne disposez pas des autorisations nécessaires pour effectuer ces étapes, demandez à votre administrateur d’abonnement de les exécuter.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Configurer des événements Machine Learning à l’aide du Portail Azure

1. Ouvrez le [Portail Azure](https://portal.azure.com) et accédez à votre espace de travail Azure Machine Learning.

1. Dans la barre de gauche, sélectionnez __Événements__ puis sélectionnez **Abonnements aux événements**. 

    ![select-events-in-workspace.png](media/how-to-use-event-grid/select-event.png)

1. Sélectionnez le type d’événement à consommer. Par exemple, la capture d’écran suivante a sélectionné __Modèle inscrit__, __Modèle déployé__, __Exécution terminée__ et __Dérive de jeu de données détectée__ :

    ![add-event-type](media/how-to-use-event-grid/add-event-type.png)

1. Sélectionnez le point de terminaison sur lequel publier l’événement. Dans la capture d’écran suivante, __Event Hub__ est le point de terminaison sélectionné :

    ![select-event-handler](media/how-to-use-event-grid/select-event-handler.png)

Une fois que vous avez confirmé votre sélection, cliquez sur __Créer__. Après la configuration, ces événements seront envoyés à votre point de terminaison.

## <a name="set-up-azure-event-grid-using-cli"></a>Configurer Azure Event Grid à l’aide de l’interface CLI

Vous pouvez installer la dernière version d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou utiliser Azure Cloud Shell fourni dans le cadre de votre abonnement Azure.

Pour installer l’extension Event Grid, utilisez la commande suivante à partir de l’interface CLI :

```azurecli-interactive
az add extension --name eventgrid
```

L’exemple suivant montre comment sélectionner un abonnement Azure, puis créer un nouvel abonnement aux événements pour Azure Machine Learning :

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>Exemples de scénarios

### <a name="use-a-logic-app-to-send-email-alerts"></a>Utiliser une application logique pour envoyer des alertes par e-mail

Tirez parti de [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) pour configurer des e-mails pour tous vos événements. Personnalisez avec des conditions et spécifiez des destinataires pour permettre la collaboration et la sensibilisation entre les équipes travaillant ensemble.

1. Dans la Portail Azure, accédez à votre espace de travail Azure Machine Learning et sélectionnez l’onglet Événements dans la barre de gauche. À partir de là, sélectionnez __Applications logiques__. 

    ![select-logic-ap](media/how-to-use-event-grid/select-logic-ap.png)

1. Connectez-vous à l’interface utilisateur de l’application logique et sélectionnez le service Machine Learning comme type de rubrique. 

    ![select-topic-type](media/how-to-use-event-grid/select-topic-type.png)

1. Sélectionnez le ou les événements pour lesquels vous souhaitez recevoir des alertes. Par exemple, la capture d’écran suivante __RunCompleted__.

    ![select-event-runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. Vous pouvez également ajouter des filtres pour déclencher uniquement l’application logique sur un sous-ensemble de types d’événements. Dans la capture d’écran suivante, un __filtre de préfixe__ de __/datadriftID/runs/__ est utilisé.

    ![filter-events](media/how-to-use-event-grid/filtering-events.png)

1. Ensuite, ajoutez une étape pour utiliser cet événement et rechercher un e-mail. Il existe plusieurs comptes de messagerie différents que vous pouvez utiliser pour recevoir des événements. Vous pouvez également configurer des conditions sur le moment où envoyer une alerte par e-mail.

    ![select-email-action](media/how-to-use-event-grid/select-email-action.png)

1. Sélectionnez __Envoyer un e-mail__ et renseignez les paramètres. Dans l’objet, vous pouvez inclure le __Type d’événement__ et la __Rubrique__ pour aider à filtrer les événements. Vous pouvez également inclure un lien vers la page de l’espace de travail pour les exécutions dans le corps du message. 

    ![configure-email-body](media/how-to-use-event-grid/configure-email-body.png)

1. Pour enregistrer cette action, sélectionnez **Enregistrer sous** dans le coin gauche de la page. Dans la barre de droite qui s’affiche, confirmez la création de cette action.

    ![confirm-logic-app-create](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Utiliser une application logique pour déclencher des flux de travail de reformation lorsque la dérive de données se produit

Les modèles deviennent obsolètes au fil du temps et ne restent pas fonctionnels dans le contexte dans lequel ils s’exécutent. Pour savoir s’il est temps de reformer le modèle, vous pouvez détecter la dérive des données. 

Cet exemple montre comment utiliser Event Grid avec une application logique Azure pour déclencher la reformation. L’exemple déclenche un pipeline Azure Data Factory lorsqu’une dérive de données se produit entre les jeux de données de formation et de service d’un modèle.

Avant de commencer, effectuez les étapes suivantes :

* Configurer une analyse de jeu de données pour [détecter la dérive de données]( https://aka.ms/datadrift) dans un espace de travail
* Créer un [pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) publié.

Dans cet exemple, un pipeline Data Factory simple est utilisé pour copier des fichiers dans un magasin d’objets blob et exécuter un pipeline Machine Learning publié. Pour plus d’informations sur ce scénario, découvrez comment configurer une [étape de Machine Learning dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Commencez par créer l’application logique. Accédez au [Portail Azure](https://portal.azure.com), recherchez Applications logiques, puis sélectionnez Créer.

    ![search-logic-app](media/how-to-use-event-grid/search-for-logic-app.png)

1. Entrez les informations demandées. Pour simplifier l’expérience, utilisez le même abonnement et le même groupe de ressources que votre pipeline Azure Data Factory et votre espace de travail Azure Machine Learning.

    ![set-up-logic-app-for-adf](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Une fois que vous avez créé l’application logique, sélectionnez __Quand un événement lié à une ressource Event Grid se produit__. 

    ![select-event-grid-trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Connectez-vous et renseignez les détails de l’événement. Définissez le __Nom de la ressource__ sur le nom de l’espace de travail. Définissez le __type d’événement__ sur __DatasetDriftDetected__.

    ![login-and-add-event](media/how-to-use-event-grid/login-and-add-event.png)

1. Ajoutez une nouvelle étape, et recherchez __Azure Data Factory__. Sélectionnez __Créer une exécution du pipeline__. 

    ![create-adfpipeline-run](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Connectez-vous et spécifiez le pipeline Azure Data Factory publié à exécuter.

    ![specify-adf-pipeline](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Enregistrez et créez l’application logique à l’aide du bouton **Enregistrer** en haut à gauche de la page. Pour afficher votre application, accédez à votre espace de travail dans le [Portail Azure](https://portal.azure.com) et cliquez sur **Événements**.

    ![show-logic-app-webhook](media/how-to-use-event-grid/show-logic-app-webhook.png)

Désormais, le pipeline Data Factory est déclenché lorsque la dérive se produit. Affichez les détails de l’exécution de la dérive de données et du pipeline d’apprentissage automatique sur le [nouveau portail d’espace de travail](https://ml.azure.com). 

![view-in-workspace](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Utiliser Azure Functions pour déployer un modèle basé sur des balises

Un objet de modèle Azure Machine Learning contient des paramètres sur lesquels vous pouvez baser les déploiements, tels que le nom du modèle, la version, la balise et la propriété. L’événement d’inscription du modèle peut déclencher un point de terminaison et vous pouvez utiliser une fonction Azure pour déployer un modèle en fonction de la valeur de ces paramètres.

Pour obtenir un exemple, consultez le référentiel [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) et suivez les étapes dans le fichier **readme**.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les événements disponibles, consultez le [schéma d’événement Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)