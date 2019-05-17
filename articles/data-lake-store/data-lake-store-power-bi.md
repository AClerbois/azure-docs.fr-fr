---
title: Analyser des données dans Azure Data Lake Storage Gen1 à l’aide de Power BI | Microsoft Docs
description: Utilisez Power BI pour analyser des données stockées dans Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603212"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analyser des données dans Azure Data Lake Storage Gen1 à l’aide de Power BI
Dans cet article, vous allez apprendre à utiliser Power BI Desktop pour analyser et visualiser les données stockées dans Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Data Lake Storage Gen1**. Suivez les instructions de [prise en main d’Azure Data Lake Storage Gen1 avec le portail Azure](data-lake-store-get-started-portal.md). Cet article suppose que vous avez déjà créé un compte Data Lake Storage Gen1 appelé **myadlsg1** et chargé un exemple de fichier de données (**Drivers.txt**) sur le compte. Cet exemple de fichier est disponible au téléchargement à partir du [référentiel Git d’Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Création d’un rapport dans Power BI Desktop
1. Lancez Power BI Desktop sur votre ordinateur.
2. À partir du ruban **Accueil**, cliquez sur **Obtenir les données**, puis cliquez sur Plus. Dans la boîte de dialogue **Obtenir les données**, cliquez sur **Azure**, cliquez sur **Azure Data Lake Store**, puis cliquez sur **Connexion**.
   
    ![Se connecter à Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Se connecter à Data Lake Storage Gen1")
3. Si une boîte de dialogue s’affiche indiquant que le connecteur est dans une phase de développement, choisissez de continuer.
4. Dans la boîte de dialogue **Azure Data Lake Store**, entrez l’URL de votre compte Data Lake Storage Gen1, puis cliquez sur **OK**.
   
    ![URL de Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL de Data Lake Storage Gen1")
5. Dans la boîte de dialogue suivante, cliquez sur **Se connecter** pour vous connecter au compte Data Lake Storage Gen1. Vous êtes redirigé vers la page de connexion de votre organisation. Suivez les instructions de l’invite pour vous connecter au compte.
   
    ![Se connecter à Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Se connecter à Data Lake Storage Gen1")
6. Une fois que vous vous êtes connecté, cliquez sur **Connexion**.
   
    ![Se connecter à Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Se connecter à Data Lake Storage Gen1")
7. La boîte de dialogue suivante montre le fichier que vous avez téléchargé dans votre compte Data Lake Storage Gen1. Vérifiez les informations, puis cliquez sur **Charger**.
   
    ![Charger des données à partir de Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Charger des données à partir de Data Lake Storage Gen1")
8. Une fois que les données ont été chargées correctement dans Power BI, les champs suivants s’affichent dans l’onglet **Champs** .
   
    ![Champs importés](./media/data-lake-store-power-bi/imported-fields.png "Champs importés")
   
    Toutefois, pour visualiser et analyser les données, nous préférons que les données soient disponibles dans les champs suivants
   
    ![Champs souhaités](./media/data-lake-store-power-bi/desired-fields.png "Champs souhaités")
   
    Dans les étapes suivantes, nous mettrons à jour la requête pour convertir les données importées dans le format souhaité.
9. À partir du ruban **Accueil**, cliquez sur **Modifier les requêtes**.
   
    ![Modifier des requêtes](./media/data-lake-store-power-bi/edit-queries.png "Modifier des requêtes")
10. Dans l’éditeur de requête, sous la colonne **Contenu**, cliquez sur **Binaire**.
    
    ![Modifier des requêtes](./media/data-lake-store-power-bi/convert-query1.png "Modifier des requêtes")
11. Une icône de fichier s’affiche. Elle représente le fichier **Drivers.txt** que vous avez chargé. Cliquez avec le bouton droit sur le fichier, puis cliquez sur **CSV**.    
    
    ![Modifier des requêtes](./media/data-lake-store-power-bi/convert-query2.png "Modifier des requêtes")
12. Une sortie comme celle illustrée ci-dessous doit s’afficher. Vos données sont désormais disponibles dans un format que vous pouvez utiliser pour créer des visualisations.
    
    ![Modifier des requêtes](./media/data-lake-store-power-bi/convert-query3.png "Modifier des requêtes")
13. À partir du ruban **Accueil**, cliquez sur **Fermer et appliquer**, puis cliquez sur **Fermer et appliquer**.
    
    ![Modifier des requêtes](./media/data-lake-store-power-bi/load-edited-query.png "Modifier des requêtes")
14. Une fois que la requête est mise à jour, l’onglet **Champs** affiche les nouveaux champs disponibles pour la visualisation.
    
    ![Champs mis à jour](./media/data-lake-store-power-bi/updated-query-fields.png "Champs mis à jour")
15. Créons un graphique à secteurs pour représenter les pilotes dans chaque ville d’un pays/une région donnée. Pour ce faire, effectuez les sélections suivantes.
    
    1. Dans l’onglet Visualisations, cliquez sur le symbole du graphique à secteurs.
       
        ![Créer un graphique à secteurs](./media/data-lake-store-power-bi/create-pie-chart.png "Créer un graphique à secteurs")
    2. Les colonnes que nous allons utiliser sont **colonne 4** (nom de la ville) et **colonne 7** (nom de pays/région). Faites glisser ces colonnes de l’onglet **Champs** vers l’onglet **Visualisations** comme indiqué ci-dessous.
       
        ![Créer des visualisations](./media/data-lake-store-power-bi/create-visualizations.png "Créer des visualisations")
    3. Le graphique à secteurs doit maintenant ressembler à celui illustré ci-dessous.
       
        ![Graphique à secteurs](./media/data-lake-store-power-bi/pie-chart.png "Créer des visualisations")
16. En sélectionnant un pays/une région spécifique dans les filtres de niveau page, vous pouvez maintenant voir le nombre de pilotes dans chaque ville du pays/région sélectionnée. Par exemple, sous l’onglet **Visualisations**, sous **Filtres au niveau de la page**, sélectionnez **Brésil**.
    
    ![Sélectionnez un pays](./media/data-lake-store-power-bi/select-country.png "sélectionnez une pays/région")
17. Le graphique à secteurs est automatiquement mis à jour pour afficher les pilotes dans les villes du Brésil.
    
    ![Pilotes dans un pays](./media/data-lake-store-power-bi/driver-per-country.png "pilotes par pays/région")
18. Dans le menu **Fichier**, cliquez sur **Enregistrer** pour enregistrer la visualisation sous forme de fichier Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publication d’un rapport dans le service Power BI
Une fois que vous avez créé les visualisations dans Power BI Desktop, vous pouvez les partager avec d’autres utilisateurs en les publiant sur le service Power BI. Pour obtenir des instructions, consultez la page [Publier à partir de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Voir aussi
* [Analyser des données dans Data Lake Storage Gen1 à l’aide de Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

