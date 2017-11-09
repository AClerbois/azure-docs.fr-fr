---
title: "Azure Data Lake Tools : Utiliser Azure Data Lake Tools pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour créer, tester et exécuter des scripts U-SQL. "
Keywords: "VScode,Azure Data Lake Tools,exécution locale,débogage local,Débogage local,aperçu du fichier de stockage,charger vers le chemin de stockage,télécharger,charger"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/10/2017
ms.author: jejiang
ms.openlocfilehash: e724a8db4424a1e608ae7ee5625cd4cc16f6078f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utiliser Azure Data Lake Tools pour Visual Studio Code

Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code (VS Code) pour créer, tester et exécuter des scripts U-SQL. Ces informations sont également décrites dans la vidéo suivante :

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Composants requis

Azure Data Lake Tools pour VSCode prend en charge Windows, Linux et MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Pour MacOS et Linux :
- [SDK .NET Core 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Installer Data Lake Tools

Après avoir installé les prérequis, vous pouvez installer Data Lake Tools pour VS Code.

**Pour installer Data Lake Tools**

1. Ouvrez Visual Studio Code.
2. Cliquez sur **Extensions** dans le volet gauche. Entrez **Azure Data Lake** dans la zone de recherche.
3. Cliquez sur **Installer** en regard de **Azure Data Lake Tools**. Après quelques secondes, le bouton **Installer** change en **Recharger**.
4. Cliquez sur **Recharger** pour activer l’extension **Azure Data Lake Tools**.
5. Cliquez sur **Recharger la fenêtre** pour confirmer. **Azure Data Lake Tools** s’affiche dans le volet Extensions.

    ![Volet Extensions de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Activer Azure Data Lake Tools
Créez un fichier .usql ou ouvrez un fichier .usql existant pour activer l’extension. 

## <a name="open-the-sample-script"></a>Ouvrir l’exemple de script
Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **ADL: Open Sample Script**. Une autre instance de cet exemple s’ouvre. Vous pouvez également modifier, configurer et envoyer un script sur cette instance.

## <a name="work-with-u-sql"></a>Utilisation de U-SQL

Vous devez ouvrir un dossier ou un fichier U-SQL pour utiliser U-SQL.

**Pour ouvrir un dossier pour votre projet U-SQL**

1. À partir de Visual Studio Code, sélectionnez le menu **Fichier**, puis **Ouvrir un dossier**.
2. Spécifiez un dossier, puis sélectionnez **Sélectionner le dossier**.
3. Sélectionnez le menu **Fichier**, puis **Nouveau**. Un fichier Untitled-1 est ajouté au projet.
4. Entrez le code suivant dans le fichier Untitled-1 :

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Le script crée un fichier departments.csv avec des données dans le dossier /output.

5. Enregistrez le fichier sous **myUSQL.usql** dans le dossier ouvert. Un fichier de configuration adltools_settings.json est également ajouté au projet.
4. Ouvrez et configurez le fichier adltools_settings.json avec les propriétés suivantes :

    - Compte : un compte Data Lake Analytics dans votre abonnement Azure.
    - Base de données : une base de données sous votre compte. La valeur par défaut est **master**.
    - Schéma : un schéma sous votre base de données. La valeur par défaut est **dbo**.
    - Paramètres facultatifs :
        - Priorité : la plage de priorité est comprise entre 1 et 1000. 1 correspond à la priorité la plus élevée. La valeur par défaut est **1000**.
        - Parallélisme : la plage de parallélisme est comprise entre 1 et 150. La valeur par défaut est le parallélisme maximal autorisé dans votre compte Azure Data Lake Analytics. 
        
        > [!NOTE] 
        > Si les paramètres ne sont pas valides, les valeurs par défaut sont utilisées.

        ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

        Un compte Data Lake Analytics de calcul est nécessaire pour compiler et exécuter des travaux U-SQL. Vous devez configurer le compte d’ordinateur avant de compiler et d’exécuter des travaux U-SQL.
    
        Une fois la configuration enregistrée, les informations de compte, de base de données et de schéma s’affichent dans la barre d’état dans l’angle inférieur gauche du fichier .usql correspondant. 
 
 
Par rapport à l’ouverture d’un fichier, quand vous ouvrez un dossier, vous pouvez :

- Utiliser un fichier code-behind. En mode de fichier unique, code-behind n’est pas pris en charge.
- Utiliser un fichier de configuration. Quand vous ouvrez un dossier, les scripts dans le dossier de travail partagent un même fichier de configuration.


Le script U-SQL est compilé à distance par le biais du service Data Lake Analytics. Quand vous émettez la commande **compile**, le script U-SQL est envoyé à votre compte Data Lake Analytics. Plus tard, Visual Studio Code reçoit le résultat de la compilation. En raison de la compilation à distance, Visual Studio Code impose que vous mentionniez les informations nécessaires à la connexion à votre compte Data Lake Analytics dans le fichier de configuration.

**Pour compiler un script U-SQL**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Compile Script**. Les résultats de la compilation s’affichent dans la fenêtre **Output**. Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionnez **ADL: Compile Script** pour compiler un travail U-SQL. Le résultat de la compilation s’affiche dans le volet **Output**.
 

**Pour envoyer un script U-SQL**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Submit Job**.  Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionner **ADL: Submit Job**. 

Une fois que vous avez envoyé un travail U-SQL, les journaux d’envoi apparaissent dans la fenêtre **Output** dans VS Code. Si l’envoi a réussi, l’URL du travail est également affichée. Vous pouvez ouvrir l’URL du travail dans un navigateur web pour suivre l’état du travail en temps réel.

Pour activer la sortie des informations sur le travail, définissez **jobInformationOutputPath** dans le fichier **vs code for the u-sql_settings.json**.
 
## <a name="use-a-code-behind-file"></a>Utiliser un fichier code-behind

Un fichier code-behind est un fichier C# associé à un script U-SQL. Vous pouvez définir un script dédié à UDO, UDA, UDT et UDF dans le fichier code-behind. UDO, UDA, UDT et UDF peuvent être utilisés directement dans le script sans inscription préalable de l’assembly. Le fichier code-behind est placé dans le même dossier que le fichier de script U-SQL correspondant. Si le script est nommé xxx.usql, le fichier code-behind est nommé xxx.usql.cs. Si vous supprimez manuellement le fichier code-behind, la fonctionnalité code-behind est désactivée pour le script U-SQL associé. Pour plus d’informations sur l’écriture de code client pour le script U-SQL, consultez [Écriture et utilisation de code personnalisé dans U-SQL - Fonctions définies par l’utilisateur]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

Pour la prise en charge du code-behind, vous devez ouvrir un dossier de travail. 

**Pour générer un fichier code-behind**

1. Ouvrez un fichier source. 
2. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
3. Entrez **ADL: Generate Code Behind**. Un fichier code-behind est créé dans le même dossier. 

Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionner **ADL: Generate Code Behind**. 

La compilation et l’envoi d’un script U-SQL avec un fichier code-behind s’effectuent exactement comme avec la version autonome du fichier de script U-SQL.

Les deux captures d’écran suivantes illustrent un fichier code-behind et le fichier de script U-SQL associé :
 
![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools pour Visual Studio Code - fichier de script code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

Nous prenons en charge l’exécution locale et le débogage local. Pour obtenir des instructions, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="use-assemblies"></a>Utiliser les assemblys

Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Vous pouvez utiliser Data Lake Tools pour inscrire des assemblys de code personnalisé dans le catalogue Data Lake Analytics.

**Pour inscrire un assembly**

Vous pouvez inscrire l’assembly par le biais de la commande **ADL: Register Assembly** ou **ADL: Register Assembly through Configuration**.

**Pour inscrire par le biais de la commande ADL: Register Assembly command**
1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Sélectionnez un compte Data Lake Analytics.
5.  Sélectionnez une base de données.

Résultats : le portail s’ouvre dans le navigateur et affiche le processus d’inscription de l’assembly.  

Une autre méthode pratique pour déclencher la commande **ADL: Register Assembly** consiste à cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers. 

**Pour inscrire par le biais de la commande ADL: Register Assembly through Configuration**
1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly through Configuration**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Le fichier JSON s’affiche. Examinez et modifiez, si nécessaire, les paramètres des ressources et les dépendances de l’assembly. Les instructions s’affichent dans la fenêtre **Output**. Pour procéder à l’inscription de l’assembly, enregistrez (Ctrl+S) le fichier JSON.

![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dépendances de l’assembly : Azure Data Lake Tools détecte automatiquement si la DLL a des dépendances. Une fois détectées, les dépendances s’affichent dans le fichier JSON. 
>- Ressources : vous pouvez charger vos ressources DLL (par exemple .txt, .png et .csv) dans le cadre de l’inscription de l’assembly. 

Une autre méthode pour déclencher la commande **ADL: Register Assembly through Configuration** consiste à cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers. 

Le code U-SQL suivant montre comment appeler un assembly. Dans l’exemple, le nom de l’assembly est *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>Connexion à Azure

Avant de pouvoir compiler et exécuter des scripts U-SQL dans Data Lake Analytics, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2.  Entrez **ADL: Login**. Les informations de connexion s’affichent dans le volet **Output**.

    ![Palette de commandes de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informations de connexion d’appareil Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Sélectionnez Ctrl+clic sur l’URL de connexion : https://aka.ms/devicelogin pour ouvrir la page web de connexion. Entrez le code **G567LX42V** dans la zone de texte, puis sélectionnez **Continuer**.

   ![Data Lake Tools pour Visual Studio Code - Coller le code de connexion](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Suivez les instructions pour vous connecter à partir de la page web. Une fois connecté, le nom de votre compte Azure s’affiche dans la barre d’état dans le coin inférieur gauche de la fenêtre **VS Code**. 

    > [!NOTE] 
    > Si votre compte a deux facteurs activés, nous vous recommandons d’utiliser l’authentification par téléphone plutôt qu’un code PIN.

Pour vous déconnecter, entrez la commande **ADL: Logout**.

## <a name="list-your-data-lake-analytics-accounts"></a>Répertorier vos comptes Data Lake Analytics

Pour tester la connexion, obtenez une liste de vos comptes Data Lake Analytics.

**Pour répertorier les comptes Data Lake Analytics dans votre abonnement Azure**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: List Accounts**. Les comptes s’affichent dans le panneau **Sortie**.


## <a name="access-the-data-lake-analytics-catalog"></a>Accès au catalogue Data Lake Analytics

Une fois connecté à Azure, vous pouvez utiliser les étapes suivantes pour accéder au catalogue U-SQL.

**Pour accéder aux métadonnées Azure Data Lake Analytics**

1.  Sélectionnez Ctrl+Maj+P et entrez **ADL: List Tables**.
2.  Sélectionnez l’un des comptes Data Lake Analytics.
3.  Sélectionnez l’une des bases de données Data Lake Analytics.
4.  Sélectionnez l’un des schémas. La liste des tables s’affiche.

## <a name="view-data-lake-analytics-jobs"></a>Afficher les travaux Data Lake Analytics

**Pour afficher les travaux Data Lake Analytics**
1.  Ouvrez la palette de commandes (Ctrl+Maj+P) et sélectionnez **ADL: Show Job**. 
2.  Sélectionnez un compte local ou Data Lake Analytics. 
3.  Attendez que la liste des travaux pour le compte apparaisse.
4.  Sélectionnez un travail dans la liste, Data Lake Tools ouvre les détails du travail dans le portail et affiche le fichier JobInfo dans VS Code.

    ![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Intégration d’Azure Data Lake Storage

Vous pouvez utiliser les commandes Azure Data Lake Storage pour :
 - Parcourir les ressources de stockage Azure Data Lake Storage [Répertorier le chemin de stockage](#list-the-storage-path) 
 - Afficher un aperçu du fichier de stockage Azure Data Lake Storage [Afficher un aperçu du fichier de stockage](#preview-the-storage-file) 
 - Charger le fichier directement sur Azure Data Lake Storage dans VS Code [Charger le fichier](#upload-file)
 - Télécharger le fichier directement à partir d’Azure Data Lake Storage dans VS Code [Télécharger le fichier](#download-file)

## <a name="list-the-storage-path"></a>Répertorier le chemin de stockage 

**Pour répertorier le chemin de stockage par le biais de la palette de commandes**

Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **ADL: List Storage Path** (Répertorier le chemin de stockage).

Choisissez le dossier dans la liste ou cliquez sur **Enter Path** (Entrer le chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé en guise d’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/). -> La palette de commandes répertorie les informations sur le chemin en fonction des informations que vous avez entrées.

![Data Lake Tools pour Visual Studio Code - Répertorier le chemin de stockage, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Il est plus pratique d’utiliser le menu contextuel accessible par un clic droit pour répertorier le chemin relatif.

**Pour répertorier le chemin de stockage par le biais d’un clic droit**

Cliquez avec le bouton droit sur la chaîne du chemin pour sélectionner **List Storage Path** (Répertorier le chemin de stockage) pour continuer.

![Data Lake Tools pour Visual Studio Code - Menu contextuel accessible par un clic droit](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Afficher un aperçu du fichier de stockage

Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **ADL: Preview Storage File** (Afficher un aperçu du fichier de stockage).

Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez un chemin de fichier de stockage Azure (par exemple, /output/SearchLog.txt). -> Résultat : le fichier s’ouvre dans VSCode.

   ![Data Lake Tools pour Visual Studio Code - Aperçu du fichier, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Vous pouvez également afficher un aperçu du fichier de stockage à l’aide du menu accessible par un clic droit sur le chemin complet ou le chemin relatif du fichier dans l’éditeur de script. 

## <a name="upload-file"></a>Charger le fichier 

Vous pouvez charger des fichiers en entrant la commande **ADL: Upload File** ou **ADL: Upload File through Configuration**.

**Pour charger des fichiers par le biais de la commande ADL: Upload File through Configuration**
1.  Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Upload File through Configuration** (Charger le fichier par le biais de la configuration).
2.  VS Code affiche un fichier JSON. Vous pouvez entrer des chemins de fichiers et charger plusieurs fichiers en même temps. Les instructions s’affichent dans la fenêtre **Output**. Pour continuer à charger le fichier, enregistrez (Ctrl+S) le fichier JSON.

       ![Chemin de fichier Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Résultats : la fenêtre **Output** affiche l’état de chargement du fichier.

       ![Data Lake Tools pour Visual Studio Code - État du chargement](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

En même temps, vous pouvez surveiller [l’état du chargement](#check-storage-tasks-status).

**Pour charger des fichiers par le biais de la commande ADL: Upload File**

Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **Upload File** (Charger le fichier).

Entrez votre chemin local (**Local File Path**). -> Choisissez le dossier dans la liste ou cliquez sur **Enter Path** (Entrer le chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé en guise d’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/). -> Cliquez sur **Choose Current Folder** (Choisir le dossier actuel) pour spécifier la destination du chargement.

![Data Lake Tools pour Visual Studio Code - État du chargement](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


Vous pouvez également charger un fichier dans le stockage à l’aide du menu accessible par un clic droit sur le chemin complet du fichier ou le chemin relatif du fichier dans l’éditeur de script.

En même temps, vous pouvez surveiller [l’état du chargement](#check-storage-tasks-status).

## <a name="download-file"></a>Télécharger un fichier 
Vous pouvez télécharger des fichiers en entrant les commandes **ADL: Download Storage File** (Télécharger un fichier de stockage) ou **ADL: Download Storage File through Configuration** (Télécharger un fichier de stockage par le biais de la configuration).

**Pour télécharger des fichiers par le biais de la commande ADL: Download File through Configuration**
1. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Download Storage File through Configuration** (Télécharger un fichier de stockage par le biais de la configuration).
2. VS Code affiche un fichier JSON. Vous pouvez entrer des chemins de fichiers et télécharger plusieurs fichiers en même temps. Les instructions s’affichent dans la fenêtre **Output**. Pour continuer à télécharger le fichier, enregistrez (Ctrl+S) le fichier JSON.

    ![Téléchargement de fichiers Data Lake Tools pour Visual Studio Code avec la configuration](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Résultats : la fenêtre **Output** affiche l’état de chargement du fichier.

    ![Téléchargement de plusieurs fichiers Data Lake Tools pour Visual Studio Code - Résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

En même temps, vous pouvez surveiller [l’état du téléchargement](#check-storage-tasks-status).

**Pour télécharger des fichiers par le biais de la commande ADL: Download Storage File**

Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Download Storage File** (Télécharger un fichier de stockage).

Choisissez le dossier dans la liste ou cliquez sur **Enter Path** (Entrer le chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé en guise d’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/) -> choisissez un fichier à télécharger.

   ![Data Lake Tools pour Visual Studio Code - État du téléchargement](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   Dans l’image de résultat, le fichier est enregistré en tant que dossier temporaire. Vous pouvez définir vous-même un chemin de téléchargement par défaut pour le paramètre **usql.defaultLocalFolderForDownload**, par le biais du menu VSCode **Fichier** -> **Préférences** -> **Paramètre**.

Vous pouvez également télécharger des fichiers de stockage à l’aide du menu accessible par un clic droit sur le chemin complet ou le chemin relatif du fichier dans l’éditeur de script.

En même temps, vous pouvez surveiller [l’état du téléchargement](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Vérifier l’état des tâches de stockage
L’état s’affiche en bas de la barre d’état à l’issue du téléchargement et du chargement.
1. Cliquez sur la barre d’état ci-dessous pour afficher l’état du téléchargement et du chargement dans le panneau **OUTPUT**.

   ![Data Lake Tools pour Visual Studio Code - Vérifier l’état du stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="open-azure-storage-explorer"></a>Ouvrir l’Explorateur de stockage Azure
Vous pouvez ouvrir l’**Explorateur de stockage Azure** en entrant la commande **ADL: Open Web Azure Storage Explorer** ou en le sélectionnant dans le menu contextuel accessible par un clic droit.

**Pour ouvrir l’Explorateur de stockage Azure**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **Open Web Azure Storage Explorer** ou cliquez avec le bouton droit sur un chemin relatif ou le chemin complet dans l’éditeur de script, puis sélectionnez **Open Web Azure Storage Explorer**.
3. Sélectionnez un compte Data Lake Analytics.

Data Lake Tools ouvre le chemin de stockage Azure dans le portail Azure. Vous pouvez rechercher le chemin et afficher un aperçu du fichier à partir du web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Exécution locale et débogage local pour les utilisateurs de Windows
L’exécution locale de U-SQL teste vos données locales et valide votre script localement, avant que votre code soit publié dans Data Lake Analytics. La fonctionnalité de débogage local vous permet d’effectuer les tâches suivantes avant que votre code soit soumis à Data Lake Analytics : 
- Déboguer votre code-behind C# 
- Examiner le code 
- Valider votre script localement

Pour obtenir des instructions sur l’exécution locale et le débogage local, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Fonctionnalités supplémentaires

Data Lake Tools pour VS Code prend en charge les fonctionnalités suivantes :

-   Saisie semi-automatique IntelliSense : des suggestions s’affichent dans des fenêtres indépendantes autour des éléments tels que les mots clés, les méthodes et les variables. Des icônes différentes représentent des types d’objets différents :

    - Type de données Scala
    - Type de données complexe
    - Types définis par l’utilisateur (UDT) intégrés
    - Classes et collection .NET
    - Expressions C#
    - UDF, UDO et UDAAG C# intégrés 
    - Fonctions U-SQL
    - Fonction de fenêtrage U-SQL
 
    ![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Saisie semi-automatique IntelliSense sur les métadonnées Data Lake Analytics : Data Lake Tools télécharge les informations de métadonnées Data Lake Analytics localement. La fonctionnalité IntelliSense remplit automatiquement les objets, notamment les bases de données, les schémas, les tables, les vues, les fonctions table, les procédures et les assemblys C# à partir des métadonnées Data Lake Analytics.
 
    ![Data Lake Tools pour Visual Studio Code - Métadonnées IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marqueur d’erreur IntelliSense : Data Lake Tools souligne les erreurs d’édition pour U-SQL et C#. 
-   Coloration syntaxique : Data Lake Tools utilise différentes couleurs pour différencier les éléments tels que les variables, les mots clés, les types de données et les fonctions. 

    ![Data Lake Tools pour Visual Studio Code - Points clés de la syntaxe](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour l’exécution locale et le débogage local U-SQL avec Visual Studio Code, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Pour obtenir des informations sur la prise en main de Data Lake Analytics, consultez [Didacticiel : bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Pour obtenir des informations sur l’utilisation de Data Lake Tools pour Visual Studio, consultez [Didacticiel : développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).



