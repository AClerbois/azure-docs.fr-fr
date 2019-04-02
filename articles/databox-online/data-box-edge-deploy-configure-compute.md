---
title: Transformer des données avec Azure Data Box Edge | Microsoft Docs
description: Découvrez comment configurer le rôle de calcul sur Data Box Edge et l’utiliser pour transformer des données avant de les envoyer à Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 31911c124aeafecb8ee37d14e58d3a0bdc0d4955
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400739"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Didacticiel : Transformer des données avec Azure Data Box Edge

Ce tutoriel explique comment configurer un rôle de calcul sur votre appareil Azure Data Box Edge. Une fois que vous avez configuré le rôle de calcul, Data Box Edge peut transformer des données avant de les envoyer à Azure.

Cette procédure peut prendre environ 10 à 15 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le calcul
> * Ajouter des partages
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

 
## <a name="prerequisites"></a>Prérequis

Avant de configurer un rôle de calcul sur votre appareil Data Box Edge, vérifiez que :

- Vous avez activé votre appareil Data Box Edge comme décrit dans [Connecter, configurer et activer Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurer le calcul

Pour configurer le calcul sur votre appareil Data Box Edge, vous allez créer une ressource IoT Hub.

1. Sur le portail Azure de votre ressource Data Box Edge, accédez à Vue d’ensemble. Dans le volet droit, sur la vignette **Calculer**, sélectionnez **Bien démarrer**.

    ![Bien démarrer avec le calcul](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Sur la vignette **Configurer le computing en périphérie**, sélectionnez **Configurer le calcul**.
3. Sur le panneau **Configurer le computing en périphérie**, entrez ce qui suit :

   
    |Champ  |Valeur  |
    |---------|---------|
    |IoT Hub     | Choisissez **Nouveau** ou **Existant**. <br> Par défaut, un niveau Standard (S1) est utilisé pour créer une ressource IoT. Pour utiliser une ressource IoT de niveau gratuit, créez-en une, puis sélectionnez-la. <br> Dans chaque cas, la ressource IoT Hub utilise les mêmes abonnement et groupe de ressources que la ressource Data Box Edge.     |
    |Nom     |Entrez un nom pour votre ressource IoT Hub.         |

    ![Bien démarrer avec le calcul](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Sélectionnez **Créer**. La création de ressources IoT Hub prend quelques minutes. Une fois la ressource IoT Hub créée, la vignette **Configurer le calcul** est mise à jour pour afficher la configuration du calcul. Pour vérifier que le rôle de computing en périphérie a été configuré, sélectionnez **Voir le computing** sur la vignette **Configurer le calcul**.
    
    ![Bien démarrer avec le calcul](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Quand le rôle de calcul Edge est configuré sur l’appareil Edge, il crée deux appareils : un appareil IoT et un appareil IoT Edge. Ces deux appareils peuvent être visualisés dans la ressource IoT Hub. Un runtime IoT Edge est également exécuté sur cet appareil IoT Edge. À ce stade, seule la plateforme Linux est disponible pour votre appareil IoT Edge.


## <a name="add-shares"></a>Ajouter des partages

Pour le déploiement simple dans ce tutoriel, vous aurez besoin de deux partages : un partage Edge et un autre partage local Edge.

1. Ajouter un partage Edge sur l’appareil en effectuant les étapes suivantes :

    1. Dans votre ressource Data Box Edge, accédez à **Computing en périphérie > Bien démarrer**.
    2. Sur la vignette **Ajouter un ou plusieurs partages**, sélectionnez **Ajouter**.
    3. Sur le panneau **Ajouter un partage**, fournissez le nom du partage et sélectionnez le type de partage.
    4. Pour monter le partage Edge, cochez la case **Utiliser le partage avec le computing en périphérie**.
    5. Sélectionnez le **Compte de stockage**, le **Service de stockage**, un utilisateur existant, puis sélectionnez **Créer**.

        ![Ajouter un partage Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Si vous avez créé un partage NFS local, utilisez l’option de commande rsync (synchronisation à distance) suivante pour copier les fichiers sur le partage :

    `rsync <source file path> < destination file path>`

    Pour plus d’informations sur la commande rsync, consultez la [documentation de Rsync](https://www.computerhope.com/unix/rsync.htm).

    Le partage Edge est créé, et vous recevrez une notification de création réussie. La liste de partages peut être mise à jour, mais vous devez attendre que la création du partage soit terminée.

2. Ajoutez un partage local Edge sur l’appareil Edge en répétant les étapes décrites à l’étape précédente et en cochant la case **Configurer en tant que partage local Edge**. Les données dans le partage local restent sur l’appareil.

    ![Ajouter un partage local Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Sélectionnez **Ajouter un ou plusieurs partages** pour voir la liste des partages mise à jour.

    ![Liste des partages mise à jour](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Ajouter un module

Vous pouvez ajouter un module prédéfini ou personnalisé. Il n’existe aucun module personnalisé sur cet appareil Edge. Pour savoir comment créer un module personnalisé, accédez à [Développer un module C# pour votre appareil Data Box Edge](data-box-edge-create-iot-edge-module.md).

Dans cette section, vous allez ajouter un module personnalisé à l’appareil IoT Edge que vous avez créé dans [Développer un module C# pour votre appareil Data Box Edge](data-box-edge-create-iot-edge-module.md). Ce module personnalisé place des fichiers d’un partage local Edge sur l’appareil de périphérie, puis les déplace vers un partage Edge (cloud) sur l’appareil. Le partage cloud envoie ensuite les fichiers vers le compte de stockage Azure associé au partage cloud.

1. Accédez à **Computing en périphérie > Bien démarrer**. Sur la vignette **Ajouter des modules**, sélectionnez le type de scénario **Simple**. Sélectionnez **Ajouter**.
2. Dans le panneau **Configurer et ajouter un module**, entrez les valeurs suivantes :

    
    |Champ  |Valeur  |
    |---------|---------|
    |Nom     | Nom unique pour le module. Ce module est un conteneur Docker que vous pouvez déployer sur un appareil IoT Edge associé à votre compte Data Box Edge.        |
    |URI d’image     | URI d’image de l’image conteneur associée pour le module.        |
    |Informations d’identification obligatoires     | Si cette case est cochée, le nom d’utilisateur et le mot de passe sont utilisés pour récupérer les modules avec une URL correspondante.        |
    |Partage d’entrée     | Sélectionnez un partage d’entrée. Dans ce cas, le partage local Edge est le partage d’entrée. Le module utilisé ici déplace les fichiers depuis le partage local Edge vers un partage Edge où ils sont chargés sur le cloud.        |
    |Partage de sortie     | Sélectionnez un partage de sortie. Dans ce cas, le partage Edge est le partage de sortie.        |
    |Type de déclencheur     | Sélectionnez **Fichier** ou **Planifier**. Un déclencheur de fichier est activé chaque fois qu’un événement de fichier se produit, tel que l’écriture d’un fichier sur le partage d’entrée. Un déclencheur planifié se déclenche en fonction d’une planification définie par vos soins.         |
    |Nom du déclencheur     | Nom unique de votre déclencheur.         |
    |Variables d’environnement| Informations facultatives qui aident à définir l’environnement dans lequel votre module sera exécuté.   |

    ![Ajouter et configurer un module](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Sélectionnez **Ajouter**. Le module est ajouté. La vignette **Ajouter un module** est mise à jour pour indiquer que le module est déployé. 

    ![Module déployé](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Vérifier la transformation des données et transférer

L’étape finale consiste à vérifier que le module est connecté et qu’il s’exécute comme prévu. L’état du runtime du module doit être en cours d’exécution pour votre appareil IoT Edge dans la ressource IoT Hub.

Pour vérifier que le module est en cours d’exécution, effectuez les étapes suivantes :

1. Sélectionnez la vignette **Ajouter un module**. Le panneau **Modules** apparaît. Dans la liste des modules, identifiez le module que vous avez déployé. L’état du runtime du module que vous avez ajouté doit être *en cours d’exécution*.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Dans l’Explorateur de fichiers, connectez-vous aux partages local Edge et Edge que vous avez créés.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Ajoutez des données au partage local.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Les données sont déplacées vers le partage cloud.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Les données sont ensuite envoyées du partage cloud vers le compte de stockage. Pour afficher les données, accédez à l’Explorateur Stockage.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Vous avez terminé le processus de validation.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le calcul
> * Ajouter des partages
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

Pour découvrir comment gérer votre appareil Data Box Edge, consultez :

> [!div class="nextstepaction"]
> [Utiliser l’interface utilisateur web locale pour administrer un appareil Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
