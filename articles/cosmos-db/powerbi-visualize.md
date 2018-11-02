---
title: Didacticiel Power BI pour le connecteur Azure Cosmos DB | Microsoft Docs
description: Utilisez ce didacticiel Power BI pour importer JSON, créer des rapports et visualiser les données à l’aide du connecteur Power BI et Azure Cosmos DB.
keywords: didacticiel power BI, visualiser les données, connecteur Power BI
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: sngun
ms.openlocfilehash: 90f5caa13bf95fb5298a06bda5a03e9bc17bcb1a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945176"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualiser les données Azure Cosmos DB à l’aide du connecteur Power BI

[Power BI](https://powerbi.microsoft.com/) est un service en ligne où vous pouvez créer et partager des tableaux de bord et des rapports. Power BI Desktop est un outil de création de rapport qui vous permet de récupérer des données à partir de diverses sources. Azure Cosmos DB est l’une des sources de données que vous pouvez utiliser avec Power BI Desktop. Vous pouvez connecter Power BI Desktop à votre compte Azure Cosmos DB via le connecteur Azure Cosmos DB pour Power BI.  Après avoir importé des données Azure Cosmos DB dans Power BI, vous pouvez les transformer, créer des rapports et les publier sur Power BI.   

Cet article décrit les étapes requises pour connecter le compte Azure Cosmos DB à Power BI Desktop. Une fois connecté, vous accédez à une collection, extrayez les données, transformez les données JSON en format tabulaire et publiez un rapport dans Power BI.

> [!NOTE]
> Le connecteur Power BI pour Azure Cosmos DB se connecte à Power BI Desktop. Les rapports créés dans Power BI Desktop peuvent ensuite être publiés sur PowerBI.com. L’extraction directe des données Azure Cosmos DB ne peut pas être effectuée sur PowerBI.com. 

> [!NOTE]
> La connexion à Azure Cosmos DB avec le connecteur Power BI est actuellement prise en charge uniquement pour les comptes d’API SQL Azure Cosmos DB et d’API Gremlin.

## <a name="prerequisites"></a>Prérequis
Avant de suivre les instructions de ce didacticiel Power BI, assurez-vous de disposer des ressources suivantes :

* [Télécharger la dernière version de Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Téléchargez les [exemples de données de volcan](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) à partir de GitHub.

* [Créer un compte de base de données Azure Cosmos DB](https://azure.microsoft.com/documentation/articles/create-account/) et importer les données de volcan à l’aide de l’[outil de migration de données Azure Cosmos DB](import-data.md).

Pour partager vos rapports dans PowerBI.com, vous devez disposer d’un compte sous PowerBI.com.  Pour en savoir plus Power BI et Power BI Pro, visitez [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Prise en main
Dans ce didacticiel, imaginons que vous êtes géologue et que vous devez étudier les volcans du monde entier. Les données relatives aux volcans sont stockées dans un compte Azure Cosmos DB et le format de document JSON est semblable à ce que vous pouvez voir ci-dessous :

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Vous souhaitez récupérer les données relatives aux volcans du compte Azure Cosmos DB et visualiser ces données dans un rapport Power BI similaire à ce qui suit.

1. Démarrez Power BI Desktop.

2. Vous pouvez **obtenir des données**, consulter les **Sources récentes** ou **Ouvrir d’autres rapports** directement à partir de cet écran d’accueil. Cliquez sur le symbole X en haut à droite pour fermer l’écran. La vue **Rapport** de Power BI Desktop s’affiche.
   
   ![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Sélectionnez le ruban **Accueil**, puis cliquez sur **Obtenir des données**.  La fenêtre **Obtenir des données** doit alors s’afficher.

4. Cliquez sur **Azure**, sélectionnez **Azure Cosmos DB (bêta)** puis cliquez sur **Se connecter**. 

    ![Power BI Desktop - Obtenir des données - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Sur la page **Aperçu connecteur**, cliquez sur **Continuer**. La fenêtre **Azure Cosmos DB** s’affiche.

6. Spécifiez l’URL du point de terminaison du compte Azure Cosmos DB dont vous souhaitez récupérer les données, comme indiqué ci-dessous, puis cliquez sur **OK**. Vous pouvez récupérer l’URL dans la zone URI du panneau **[Clés](manage-account.md#keys)** du portail Azure pour utiliser votre propre compte. Vous pouvez, de manière facultative, fournir un nom de base données, un nom de collection ou utiliser le Navigateur pour sélectionner la base de données et la collection afin d’identifier la provenance des données.
   
7. Si vous vous connectez pour la première fois à ce point de terminaison, le système vous demandera la clé du compte. Vous pouvez récupérer la clé dans la zone **Clé primaire** du panneau **[Clés en lecture seule](manage-account.md#keys)** du portail Azure pour votre propre compte. Entrez la clé appropriée, puis cliquez sur **Connecter**.
   
   nous vous recommandons d’utiliser la clé en lecture seule lorsque vous créez des rapports, ce afin de ne pas exposer inutilement la clé principale à des risques de sécurité potentiels. Cela évite toute exposition inutile de la clé principale à des risques de sécurité potentiels. La clé en lecture seule est disponible à partir du panneau [Clés](manage-account.md#keys) du portail Azure. 
    
8. Une fois le compte connecté, le volet **Navigateur** s’affiche. Le **Navigateur** affiche une liste des bases de données attachées au compte.

9. Sélectionnez puis développez la base de données d’où proviennent les données du rapport. Sélectionnez **volcanodb** (le nom de votre base de données peut être différent).   

10. À présent, sélectionnez une collection qui contient les données à récupérer, sélectionnez **volcano1** (le nom de votre collection peut être différent).
    
    le volet d’aperçu affiche une liste des éléments d’ **enregistrement** .  Dans Power BI, un Document est représenté sous la forme d’un type d’ **enregistrement** . De même, un bloc JSON imbriqué à l’intérieur d’un document est également considéré comme un **enregistrement**.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Fenêtre de Navigateur](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Cliquez sur **Modifier** pour lancer l’éditeur de requête dans une nouvelle fenêtre et transformer les données.

## <a name="flattening-and-transforming-json-documents"></a>Mise à plat et transformation de documents JSON
1. Basculez dans la fenêtre de l’éditeur de requête de Power BI, où une colonne **Document** s’affiche dans le volet central.
   ![Power BI Desktop - Éditeur de requête](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Cliquez sur l’icône de développement à droite de l’en-tête de colonne **Document** .  Un menu contextuel s’affiche avec une liste de champs.  Sélectionnez les champs dont vous avez besoin pour votre rapport (par exemple, nom du volcan, pays, région, emplacement, altitude, type, état et dernière éruption connue). Désactivez la case à cocher **Utiliser le nom de la colonne d’origine comme préfixe**, puis cliquez sur **OK**.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Développement des documents](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Le volet central affiche un aperçu du résultat avec les champs sélectionnés.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Résultats aplatis](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Dans notre exemple, la propriété Emplacement est un bloc GeoJSON contenu dans un document.  Comme vous pouvez le constater, l’emplacement est représenté en tant que type d’ **enregistrement** dans Power BI Desktop.  
5. Cliquez sur l’icône de développement à droite de l’en-tête de colonne Document.Location.  Un menu contextuel s’affiche avec le type et les coordonnées.  Nous allons sélectionner le champ des coordonnées, vérifier que la case **Utiliser le nom de la colonne d’origine comme préfixe** n’est pas cochée, puis cliquer sur **OK**.
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Enregistrement emplacement](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Le volet central affiche maintenant une colonne de coordonnées de type **Liste** .  Comme indiqué au début de ce didacticiel, les données GeoJSON utilisées dans ce didacticiel sont de type « Point », avec des valeurs de latitude et longitude enregistrées dans le tableau de coordonnées.
   
    L’élément coordinates[0] représente la longitude et l’élément coordinates[1] la latitude.
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Liste des coordonnées](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Pour mettre à plat le tableau de coordonnées, créez une **colonne personnalisée** appelée LatLong.  Sélectionnez le ruban **Ajouter une colonne**, puis cliquez sur **Colonne personnalisée**.  La fenêtre **Colonne personnalisée** s’affiche.
8. Indiquez un nom pour la nouvelle colonne, par exemple LatLong.
9. Spécifiez ensuite la formule personnalisée à appliquer à la nouvelle colonne.  Dans notre exemple, nous allons concaténer les valeurs de latitude et longitude séparées par une virgule, comme indiqué ci-dessous, à l’aide de la formule suivante : `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Cliquez sur **OK**.
   
    Pour plus d’informations sur le langage DAX (Data Analysis Expressions) et notamment sur les fonctions DAX, consultez la page [DAX Basic in Power BI Desktop (Dax de base dans Power BI Desktop)](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Ajout d’une colonne personnalisée](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Maintenant, le volet central affiche les nouvelles colonnes LatLong remplies avec les valeurs.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Colonne personnalisée LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Si vous recevez une erreur dans la nouvelle colonne, assurez-vous que les étapes appliquées sous les Paramètres de requête correspondent à la figure suivante :
    
    ![Les étapes appliquées doivent être Source, Navigation, Expanded Document, Expanded Document.Location, Added Custom](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Si vos étapes sont différentes, supprimez les étapes supplémentaires et essayez d’ajouter de nouveau la colonne personnalisée. 

11. Cliquez sur **Fermer et appliquer** pour enregistrer le modèle de données.
    
    ![Didacticiel Power BI pour le connecteur Microsoft Azure Cosmos DB et Power BI - Fermer et appliquer](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Création de rapports
La vue Rapport de Power BI Desktop vous permet de créer des rapports pour visualiser des données.  Pour créer des rapports, vous pouvez simplement faire glisser des champs et les déposer dans la zone de dessin **Rapport** .

![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Dans la vue Rapport, vous devriez trouver les éléments suivants :

1. Le volet **Champs**, qui contient une liste de modèles de données avec des champs que vous pouvez utiliser pour vos rapports.
2. Le volet **Visualisations** . Un rapport peut contenir une ou plusieurs visualisations.  Dans le volet **Visualisations** , sélectionnez les types visuels adaptés à vos besoins.
3. La zone de dessin **Rapport**, où vous créez les éléments visuels de votre rapport.
4. La page **Rapport** . Vous pouvez ajouter plusieurs pages de rapport dans Power BI Desktop.

Vous trouverez ci-dessous les principales étapes de création d’un rapport de vue cartographique interactif simple.

1. Dans notre exemple, nous allons créer une vue cartographique indiquant l’emplacement de chaque volcan.  Dans le volet **Visualisations** , cliquez sur le type d’élément visuel « carte », comme illustré dans la capture d’écran ci-dessus.  Le type d’élément visuel « carte » devrait s’afficher dans la zone de dessin **Rapport** .  Le volet **Visualisations** doit également afficher un ensemble de propriétés associées au type d’élément visuel « carte ».
2. Maintenant, faites glissez et déplacez le champ LatLong du volet **Champs** vers la propriété **Emplacement** du volet **Visualisations**.
3. Faites glissez le champ Nom du volcan vers la propriété **Légende** .  
4. Ensuite, faites glisser le champ Altitude sur la propriété **Taille** .  
5. Vous devriez maintenant voir une carte contenant un ensemble de bulles qui indiquent l’emplacement de chaque volcan, la taille de la bulle étant proportionnelle à l’altitude des volcans.
6. Vous avez maintenant créé un rapport de base.  Vous pouvez continuer à personnaliser votre rapport en ajoutant davantage de visualisations.  Dans notre cas, nous avons ajouté un segment Type de volcan pour obtenir un rapport interactif.  
   
7. Dans le menu Fichier, cliquez sur **Enregistrer** et enregistrez le fichier sous PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publier et partager votre rapport
Pour partager votre rapport, vous devez disposer d’un compte dans PowerBI.com.

1. Dans Power BI Desktop, cliquez sur le ruban **Accueil** .
2. Cliquez sur **Publier**.  Vous êtes invité à entrer le nom d’utilisateur et le mot de passe associés à votre compte PowerBI.com.
3. Une fois les informations d’identification authentifiées, le rapport est publié sur la destination sélectionnée.
4. Cliquez sur **Open 'PowerBITutorial.pbix' in Power BI (Ouvrir PowerBITutorial.pbix dans Power BI)** pour consulter et partager votre rapport sur PowerBI.com.
   
    ![Publication réussie sur Power BI ! Ouvrir le didacticiel dans Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Créer un tableau de bord dans PowerBI.com
Maintenant que vous disposez d’un rapport, partageons-le dans PowerBI.com.

Lorsque vous publiez votre rapport à partir de Power BI Desktop sur PowerBI.com, des éléments **Rapport** et **Jeu de données** sont générés dans votre client PowerBI.com. Par exemple, après la publication d’un rapport nommé **PowerBITutorial** sur PowerBI.com, un élément PowerBITutorial s’affiche dans les sections **Rapports** et **Jeux de données** de PowerBI.com.

   ![Capture d’écran des nouveaux rapport et jeu de données dans PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Pour créer un tableau de bord partageable, cliquez sur le bouton **Épingler une page dynamique** de votre rapport PowerBI.com.

   ![Capture d’écran des nouveaux rapport et jeu de données dans PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Ensuite, suivez les instructions de [Pin a tile from a report (Épingler une vignette d’un rapport)](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) afin de créer un tableau de bord. 

Vous pouvez également apporter des modifications ad hoc au rapport avant de créer un tableau de bord. Toutefois, il est recommandé d’utiliser Power BI Desktop pour effectuer les modifications et republier le rapport sur PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur Power BI, consultez la section [Get started with Power BI (Prise en main de Power BI)](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Pour en savoir plus sur Azure Cosmos DB, consultez la [page d’accueil de la documentation relative à Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

