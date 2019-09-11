---
title: Intégration d’Azure Stream Analytics avec Microsoft Azure Machine Learning
description: Cet article explique comment configurer rapidement un travail Azure Stream Analytics simple avec Azure Machine Learning à l’aide d’une fonction définie par l’utilisateur.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: ab510f7ee2f95a2c3373c0514c31554598451b27
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173333"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-preview"></a>Analyse des sentiments avec Azure Stream Analytics et Azure Machine Learning Studio (préversion)
Cet article explique comment configurer rapidement un travail Azure Stream Analytics simple avec Azure Machine Learning Studio. Vous allez utiliser un modèle d’analyse des sentiments Machine Learning de la galerie Cortana Intelligence pour analyser la diffusion des données de texte et déterminer l’évaluation des sentiments en temps réel. Cortana Intelligence Suite vous permet d’accomplir cette tâche sans vous préoccuper de la complexité inhérente à la création d’un modèle d’analyse des sentiments.

Vous pourrez appliquer les instructions de cet article dans les scénarios suivants :

* Analyse des sentiments en temps réel sur la diffusion de données Twitter en continu.
* Analyse des enregistrements de conversations de clients avec le personnel de support.
* Évaluation des commentaires sur les forums, blogs et vidéos. 
* Nombreux autres scénarios de notation prédictive en temps réel.

Dans un scénario réel, vous recevriez les données directement à partir d’un flux de données Twitter. Pour simplifier le didacticiel, il est écrit de telle manière que le travail Stream Analytics reçoit des tweets à partir d’un fichier CSV du stockage Blob Azure. Vous pouvez créer votre propre fichier CSV, ou vous pouvez utiliser un exemple de fichier CSV, comme dans l’image suivante :

![Exemples de tweets dans un fichier CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

La tâche Streaming Analytics créée applique le modèle d’analyse des sentiments en tant que fonction définie par l’utilisateur (UDF) sur l’exemple de données de texte à partir du magasin d’objets blob. La sortie (le résultat de l’analyse des sentiments) est écrite dans le même magasin d’objets blob, mais dans un fichier CSV différent. 

La figure suivante illustre cette configuration. Comme nous l’avons indiqué, pour un scénario plus réaliste, vous pouvez remplacer le stockage d’objets blob par la diffusion de données Twitter en continu à partir d’une entrée Event Hub Azure. Vous pouvez également créer une visualisation [Microsoft Power BI](https://powerbi.microsoft.com/) en temps réel agrégée du sentiment.    

![Vue d’ensemble de l’intégration de Machine Learning dans Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Prérequis
Avant de commencer, veillez à disposer des éléments qui suivent :

* Un abonnement Azure actif.
* Un fichier CSV contenant des données. Vous pouvez télécharger le fichier indiqué précédemment à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), ou créer votre propre fichier. Dans cet article, nous supposons que vous utilisez le fichier de GitHub.

À un niveau général, pour effectuer les tâches décrites dans cet article, vous devez procédez comme suit :

1. Créez un compte de stockage Azure et un conteneur de stockage d’objets blob, et chargez un fichier d’entrée au format CSV dans le conteneur.
3. Ajoutez un modèle d’analyse des sentiments à partir de la galerie Cortana Intelligence à votre espace de travail Azure Machine Learning Studio et déployez-le en tant que service web dans l’espace de travail Machine Learning.
5. Créez un travail Stream Analytics qui appelle ce service web en tant que fonction afin de déterminer le sentiment de saisie de texte.
6. Démarrez le travail Stream Analytics et observez le résultat.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Créer un conteneur de stockage et charger le fichier d’entrée CSV
Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, par exemple celui de GitHub.

1. Dans le portail Azure, cliquez sur **Créer une ressource** > **Stockage** > **Compte de stockage**.

2. Indiquez un nom (`samldemo` dans l’exemple). Le nom, qui doit être unique dans Azure, ne peut contenir que des lettres minuscules et des chiffres. 

3. Spécifiez un groupe de ressources existant et un emplacement. Nous vous recommandons d’utiliser le même emplacement pour toutes les ressources créées lors de ce didacticiel.

    ![indiquer les détails du compte de stockage](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Dans le portail Azure, sélectionnez le compte de stockage. Dans le panneau du compte de stockage, cliquez sur **Conteneurs**, puis sélectionnez **+&nbsp;Conteneur** pour créer le stockage d’objets blob.

    ![Créer un conteneur de stockage d’objets blob pour les entrées](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Indiquez ensuite le nom du conteneur (`azuresamldemoblob` dans cet exemple) et vérifiez que le **type d’accès** est défini sur **Blob**. Quand vous avez terminé, cliquez sur **OK**.

    ![spécifier les détails du conteneur d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Dans le panneau **Conteneurs**, sélectionnez le nouveau conteneur pour ouvrir le panneau correspondant.

7. Cliquez sur **Télécharger**.

    ![Bouton ’Télécharger’ d’un conteneur](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Dans le panneau **Charger l’objet blob**, chargez le fichier **sampleinput.csv** que vous avez préalablement téléchargé. Dans la section **Type d’objet blob**, sélectionnez **objet blob de blocs** et définissez la taille de bloc sur 4 Mo, ce qui est suffisant pour ce didacticiel.

9. Cliquez sur le bouton **Charger** en bas du panneau.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Ajout du modèle d’analyse de sentiments de la galerie Cortana Intelligence

Maintenant que les exemples de données sont dans un objet blob, vous pouvez activer le modèle d’analyse des sentiments dans la galerie Cortana Intelligence.

1. Accédez au [modèle d’analyse prédictive des sentiments](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dans la galerie Cortana Intelligence.  

2. Cliquez sur **Ouvrir dans Studio**.  
   
   ![Stream Analytics Machine Learning, ouvrir Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Connectez-vous pour accéder à l’espace de travail. Sélectionnez un emplacement.

4. Cliquez sur **Exécuter** en bas de la page. Le processus s’exécute. Cela dure environ une minute.

   ![exécuter une expérience dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Après l’exécution du processus, sélectionnez **Déployer un service web** au bas de la page.

   ![déployer l’expérience dans Machine Learning Studio en tant que service web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Pour vérifier que le modèle d’analyse des sentiments est prêt à être utilisé, cliquez sur le bouton **Tester**. Entrez du texte, par exemple « I love Microsoft ». 

   ![tester une expérience dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Si le test fonctionne, un résultat similaire au texte suivant s’affiche :

   ![résultats du test dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Dans la colonne **Applications**, cliquez sur le lien **Excel 2010 or earlier workbook** (Classeur Excel 2010 ou antérieur) pour télécharger un classeur Excel. Le classeur contient la clé de l’API et l’URL dont vous aurez besoin pour configurer le travail Stream Analytics.

    ![Stream Analytics Machine Learning, aperçu rapide](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Créer une tâche d’analyse Stream Analytics qui utilise le modèle Machine Learning

Vous pouvez maintenant créer un travail Stream Analytics lisant les exemples de tweets à partir du fichier CSV dans le stockage d’objets blob. 

### <a name="create-the-job"></a>Créer le travail

1. Accédez au [portail Azure](https://portal.azure.com).  

2. Cliquez sur **Créer une ressource** > **Internet des objets** > **Travail Steam Analytics**. 

3. Nommez le travail `azure-sa-ml-demo`, spécifiez un abonnement, spécifiez un groupe de ressources existant ou créez-en un nouveau et sélectionnez l’emplacement du travail.

   ![spécifier les paramètres du nouveau travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurer les entrées du travail
Le travail reçoit les entrées à partir du fichier CSV chargé précédemment dans le stockage d’objets blob.

1. Une fois le travail créé, accédez à la section **Topologie de la tâche** dans le panneau du travail et cliquez sur l’option **Entrées**.    

2. Dans le panneau **Entrées**, cliquez sur **ajouter une entrée de flux** >**Stockage Blob**

3. Indiquez les valeurs suivantes dans le panneau **Stockage Blob** :

   
   |Champ  |Valeur  |
   |---------|---------|
   |**Alias d’entrée** | Utilisez le nom `datainput` et choisissez **Sélectionner le stockage Blob dans votre abonnement**       |
   |**Compte de stockage**  |  Sélectionnez le compte de stockage que vous avez préalablement créé.  |
   |**Conteneur**  | Sélectionnez le conteneur que vous avez créé précédemment (`azuresamldemoblob`)        |
   |**Format de sérialisation de l’événement**  |  Sélectionnez **CSV**       |

   ![Paramètres d’entrée d’un nouveau travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Cliquez sur **Enregistrer**.

### <a name="configure-the-job-output"></a>Configurer la sortie du travail
Le travail envoie les résultats vers le stockage d’objets blob d’où il a reçu les entrées. 

1. Dans la section **Topologie de la tâche** du panneau de travail, sélectionnez l’option **Sorties**.  

2. Dans le panneau **Sorties**, cliquez sur **Ajouter** >**Stockage Blob**, puis ajoutez une sortie avec l’alias `datamloutput`. 

3. Indiquez les valeurs suivantes dans le panneau **Stockage Blob** :

   |Champ  |Valeur  |
   |---------|---------|
   |**Alias de sortie** | Utilisez le nom `datamloutput` et choisissez **Sélectionner le stockage Blob dans votre abonnement**       |
   |**Compte de stockage**  |  Sélectionnez le compte de stockage que vous avez préalablement créé.  |
   |**Conteneur**  | Sélectionnez le conteneur que vous avez créé précédemment (`azuresamldemoblob`)        |
   |**Format de sérialisation de l’événement**  |  Sélectionnez **CSV**       |

   ![Paramètres de sortie d’un nouveau travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Cliquez sur **Enregistrer**.   


### <a name="add-the-machine-learning-function"></a>Ajouter la fonction Machine Learning 
Précédemment, vous avez publié un modèle Machine Learning en tant que service web. Dans notre scénario, lorsque le travail Stream Analysis s’exécute, il envoie chaque exemple de tweet d’entrée au service web pour l’analyse des sentiments. Le service web Machine Learning renvoie un sentiment (`positive`, `neutral` ou `negative`) et associe au tweet une probabilité qu’il soit positif. 

Dans cette section du didacticiel, vous allez définir une fonction dans le travail Stream Analysis. Cette fonction peut être appelée pour envoyer un tweet au service web et renvoyer la réponse. 

1. Assurez-vous que vous disposez de l’URL du service web et de la clé d’API téléchargée précédemment dans le classeur Excel.

2. Accédez à votre panneau de travail > **Fonctions** >  **+ Ajouter** > **AzureML**

3. Remplissez le panneau **fonction d’Azure Machine Learning** avec ces valeurs :

   |Champ  |Valeur  |
   |---------|---------|
   | **Alias de fonction** | Utilisez le nom `sentiment` et sélectionnez **Fournir les paramètres de fonction Azure Machine Learning manuellement**, ce qui vous donne la possibilité d’entrer l’URL et la clé.      |
   | **URL**| Collez l’URL du service web.|
   |**Clé** | Collez la clé d’API. |
  
   ![Paramètres d’ajout d’une fonction Machine Learning au travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Cliquez sur **Enregistrer**.

### <a name="create-a-query-to-transform-the-data"></a>Créer une requête de transformation des données

Stream Analytics utilise une requête SQL déclarative pour examiner l’entrée et la traiter. Dans cette section, vous allez créer une requête lisant chaque tweet d’entrée, puis appelant la fonction Machine Learning pour effectuer l’analyse des sentiments. La requête envoie ensuite le résultat vers la sortie définie (stockage d’objets blob).

1. Revenez au panneau de vue d’ensemble du travail.

2.  Sous **Topologie de la tâche**, sélectionnez la zone **Requête**.

3. Entrez la requête suivante :

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    La requête appelle la fonction créée précédemment (`sentiment`), qui effectue l’analyse des sentiments pour chaque tweet d’entrée. 

4. Cliquez sur **Enregistrer** pour enregistrer la feuille de calcul.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Démarrer le travail Stream Analytics et observer le résultat

Vous pouvez maintenant démarrer le travail Stream Analytics.

### <a name="start-the-job"></a>Démarrage du travail
1. Revenez au panneau de vue d’ensemble du travail.

2. Cliquez sur **Démarrer** dans la partie supérieure du panneau.

3. Dans le panneau **Démarrer la tâche**, sélectionnez **Personnalisé**, puis sélectionnez un jour antérieur au moment où le fichier CSV a été chargé sur le stockage d’objets blob. Quand vous avez terminé, cliquez sur **Démarrer**.  


### <a name="check-the-output"></a>Consulter la sortie
1. Laissez quelques minutes au travail pour s’exécuter avant de constater une activité dans la zone **Surveillance**. 

2. Si vous utilisez habituellement un outil spécifique pour consulter le contenu du stockage d’objets blob, utilisez-le pour examiner le conteneur `azuresamldemoblob`. Vous pouvez également effectuer les étapes suivantes dans le portail Azure :

    1. Dans le portail, recherchez le compte de stockage `samldemo`, et dans ce compte, recherchez le conteneur `azuresamldemoblob`. Le conteneur présente deux fichiers : le fichier qui contient les exemples de tweets et un fichier CSV généré par le travail Stream Analytics.
    2. Cliquez sur le fichier généré avec le bouton droit de la souris, puis sélectionnez **Télécharger**. 

   ![Télécharger la sortie CSV du travail à partir du stockage d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Ouvrez le fichier CSV généré. Vous verrez quelque chose de similaire à l’exemple suivant :  
   
   ![Stream Analytics Machine Learning, vue CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Afficher les mesures
Vous pouvez également afficher les mesures liées à la fonction Azure Machine Learning. Les mesures relatives à la fonction ci-dessous s’affichent dans la zone **Surveillance** du panneau du travail :

* **Requêtes de fonction** indique le nombre de requêtes adressées au service web Machine Learning.  
* **Événements de fonction** indique le nombre d’événements dans la requête. Par défaut, chaque requête envoyée à un service web Machine Learning contient jusqu'à 1 000 événements.  


## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Intégrer l’API REST et le service Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



