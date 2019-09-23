---
title: Créer un pipeline CI/CD pour Node.js avec Azure DevOps Projects
description: DevOps Projects facilite la prise en main d’Azure. Ce composant vous aide à lancer une application sur le service Azure de votre choix en quelques étapes rapides.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-javascript-september2019
monikerRange: vsts
ms.openlocfilehash: c59df73e7fa1d19db4c83a1c9a317f9b69ec8a37
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091181"
---
#  <a name="create-a-cicd-pipeline-for--nodejs-with-azure-devops-projects"></a>Créer un pipeline CI/CD pour Node.js avec Azure DevOps Projects 

Azure DevOps Projects présente une expérience simplifiée qui crée des ressources Azure et configure un pipeline d’intégration continue (CI) et de livraison continue (CD) pour votre application Node.js dans Azure Pipelines.  

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

DevOps Projects crée un pipeline CI/CD dans Azure Pipelines. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Dans le volet gauche, sélectionnez **Créer une ressource**, puis recherchez **DevOps Projects**. 

    ![Démarrage de la configuration de la livraison continue](_img/azure-devops-project-nodejs/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez l’exemple d’application Node.js.  
    Les exemples Node.js offrent le choix entre plusieurs infrastructures d’application.

1. L’exemple de framework par défaut est Express.js. Laissez le paramètre par défaut, puis sélectionnez **Suivant**.  
    La cible de déploiement par défaut est **Web App sur Windows**.  Le framework d’application, que vous avez choisi précédemment, détermine le type de cible de déploiement de service Azure disponible ici.  

2. Laissez le service par défaut, puis sélectionnez **Suivant**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurer Azure DevOps et un abonnement Azure 

1. Créez une organisation Azure DevOps ou choisissez une organisation existante. 

    a. Entrez un nom pour votre projet.

    b. Sélectionnez votre abonnement et un emplacement Azure, entrez un nom pour votre application, puis sélectionnez **Terminé**.  
    Après quelques minutes, le tableau de bord DevOps Projects s’affiche dans le portail Azure. Un exemple d’application est configuré dans un dépôt de votre organisation Azure DevOps, une build est exécutée et votre application est déployée sur Azure. Ce tableau de bord donne une visibilité sur votre dépôt de code, le pipeline CI/CD et votre application dans Azure.
     
3. Sélectionnez **Parcourir** pour voir votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-nodejs/dashboardnopreview.png) 
    
DevOps Projects a configuré automatiquement un déclencheur de build et de mise en production CI.  Vous êtes maintenant prêt à collaborer avec une équipe sur une application Node.js avec un processus CI/CD qui déploie automatiquement votre travail le plus récent sur votre site web.

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

DevOps Projects crée un dépôt Git dans Azure Repos ou dans GitHub. Suivez les étapes ci-dessous pour voir le dépôt et modifier le code dans votre application.

1. À gauche du tableau de bord DevOps Projects, sélectionnez le lien correspondant à votre branche master.  
Ce lien ouvre une vue sur le référentiel Git récemment créé.

1. Pour afficher l’URL du clone du dépôt, sélectionnez **Cloner** en haut à droite du navigateur.   
    Vous pouvez cloner votre référentiel Git dans votre environnement de développement intégré favori. Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications de code directement dans la branche maîtresse et les valider.

1. Sur la gauche du navigateur, accédez au fichier **views/index.pug**.

1. Sélectionnez **Modifier**, puis modifiez le titre h2.  
    Par exemple, entrez **Commencer sur le champs avec Azure DevOps Projects** ou faites une autre modification.

1. Sélectionnez **Valider**, puis enregistrez vos modifications.

1. Dans votre navigateur, accédez au tableau de bord DevOps Projects.   
Vous devez maintenant voir une build en cours. Les modifications que vous venez d’effectuer font automatiquement l’objet d’une build et d’un déploiement via un pipeline CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examiner le pipeline CI/CD Azure

Dans l’étape précédente, DevOps Projects a automatiquement configuré un pipeline CI/CD complet. Explorez et personnalisez le pipeline selon vos besoins. Effectuez les étapes suivantes pour vous familiariser avec les pipelines de build et de mise en production.

1. En haut du tableau de bord DevOps Projects, sélectionnez **Pipelines de build**.  
Ce lien ouvre un onglet du navigateur et le pipeline de build de votre nouveau projet.

1. Pointez sur le champ **État**, puis sélectionnez les points de suspension (...).  
    Cette action ouvre un menu permettant de lancer plusieurs activités, comme mettre une nouvelle build en file d’attente, mettre en pause une build et modifier le pipeline de build.

1. Sélectionnez **Modifier**.

1. Dans ce volet, vous pouvez examiner les différentes tâches de votre pipeline de build.  
La build effectue différentes tâches, comme la récupération (fetch) de sources dans le dépôt Git, la restauration de dépendances et la publication des sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le nom du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, sélectionnez **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.   
Dans le volet **Historique**, vous pouvez voir une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure Pipelines gère les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.   
 DevOps Projects a créé automatiquement un déclencheur CI, et chaque validation dans le dépôt lance une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.   
En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Mises en production**.  
 DevOps Projects crée un pipeline de mise en production pour gérer les déploiements sur Azure.

1. Sur la gauche, sélectionnez les points de suspension (...) en regard de votre pipeline de mise en production, puis sélectionnez **Modifier**.  
Le pipeline de mise en production définit le processus de mise en production.

12. Sous **Artefacts**, sélectionnez **Déposer**.  
    Le pipeline de build que vous avez examiné aux étapes précédentes génère la sortie utilisée pour l’artefact. 

1. En regard de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  
Ce pipeline de mise en production a un déclencheur CD activé, qui effectue un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur, vos déploiements nécessitant alors une exécution manuelle. 


1. Sur la gauche, sélectionnez **Tâches**.   
Les tâches sont les activités effectuées par votre processus de déploiement. Dans cet exemple, une tâche a été créée pour déployer sur Azure App Service.


1. Sur la droite, sélectionnez **Afficher les mises en production**.  
Cette vue affiche un historique des mises en production.

1. Sélectionnez les points de suspension (...) en regard d’une de vos mises en production, puis sélectionnez **Ouvrir**.  
Il y a plusieurs menus à explorer, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.   
Cette vue montre les validations de code associées au déploiement spécifique.

1. Sélectionnez **Journaux d’activité**.  
Les journaux d’activité contiennent des informations utiles sur le processus de déploiement. Ils peuvent être affichés pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer Azure App Service et les autres ressources associées que vous avez créées. Utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.


## <a name="next-steps"></a>Étapes suivantes

Quand vous avez configuré votre processus CI/CD, les pipelines de build et de mise en production ont été créés automatiquement. Vous pouvez le modifier afin qu’il réponde aux besoins de votre équipe. Pour en savoir plus sur le pipeline CI/CD, consultez :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videos

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
