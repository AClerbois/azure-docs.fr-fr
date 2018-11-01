---
title: Configurer un disque Microsoft Azure Data Box | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à configurer un disque Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b3b686815b86e28c0473668825b89736b9af5bf1
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740482"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Didacticiel : Déballer, connecter et déverrouiller un disque Azure Data Box

Ce didacticiel explique comment déballer, connecter et déverrouiller votre disque Azure Data Box.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déballer votre disque Data Box
> * Se connecter aux disques et obtenir la clé d’accès
> * Déverrouiller les disques sur le client Windows
> * Déverrouiller les disques sur le client Linux

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez terminé le [Didacticiel : Commander un disque Azure Data Box](data-box-disk-deploy-ordered.md).
2. Vous avez reçu vos disques et l’état de la commande dans le portail affiche **Delivered (Livrée)**.
3. Vous disposez d’un ordinateur client sur lequel vous pouvez installer l’outil de déverrouillage Data Box Disk. Votre ordinateur client doit :
    - Exécuter un [système d’exploitation pris en charge](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)
    - Être équipé des autres [logiciels requis](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) s’il s’agit d’un client Windows  

## <a name="unpack-your-disks"></a>Déballer vos disques

 Procédez comme suit pour déballer vos disques.

1. Les disques Data Box sont livrés dans une boîte d’envoi de petite taille. Ouvrez la boîte et sortez son contenu. Vérifiez que la boîte contient entre 1 à 5 disques SSD, ainsi qu’un câble de connexion USB par disque. Vérifiez que la boîte n’a pas été endommagée. 

    ![Boîte d’envoi de disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Si la boîte d’envoi est altérée ou gravement endommagée, ne l’ouvrez pas. Contactez le Support Microsoft pour savoir si vous pouvez utiliser les disques ou si Microsoft doit vous envoyer des disques de remplacement.
3. Vérifiez que la boîte contient une pochette transparente avec une étiquette d’expédition (sous l’étiquette actuelle) pour l’expédition de retour. Si cette étiquette a été perdue ou endommagée, vous pouvez en télécharger et en imprimer une nouvelle à partir du portail Azure. 

    ![Étiquette d’expédition de disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Conservez la boîte et le papier d’emballage pour renvoyer les disques ultérieurement.

## <a name="connect-to-disks-and-get-the-passkey"></a>Se connecter aux disques et obtenir la clé d’accès 

1. Utilisez le câble fourni pour connecter le disque à l’ordinateur client exécutant un système d’exploitation pris en charge, comme indiqué dans les prérequis. 

    ![Connexion du disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Dans le portail Azure, accédez à **Général > Détails de l’appareil**. Utilisez l’icône de copie pour copier la clé d’accès. Cette clé d’accès permet de déverrouiller les disques.

    ![Clé d’accès de déverrouillage Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

La procédure de déverrouillage des disques diffère selon que vous êtes connecté à un client Windows ou Linux.

## <a name="unlock-disks-on-windows-client"></a>Déverrouiller les disques sur le client Windows

Procédez comme suit pour connecter et déverrouiller vos disques.
     
1. Dans le portail Azure, accédez à **Général > Détails de l’appareil**. 
2. Téléchargez l’ensemble d’outils Data Box Disk correspondant au client Windows. Cette boîte à outils comporte 3 outils : outil de déverrouillage de disque Data Box, outil de validation de disque Data Box et outil de copie de disque Data Box Split. 

    Dans cette procédure, vous n’utiliserez que l’outil Data Box Disk Unlock. Vous vous servirez des deux autres outils ultérieurement.

    > [!div class="nextstepaction"]
    > [Télécharger l’ensemble d’outils Data Box Disk pour Windows](http://aka.ms/databoxdisktoolswin)         

3. Extrayez la boîte à outils sur l’ordinateur que vous allez utiliser pour copier les données. 
4. Ouvrez une fenêtre d’invite de commandes ou exécutez Windows PowerShell en tant qu’administrateur sur le même ordinateur.
5. (Facultatif) Pour vérifier que l’ordinateur que vous utilisez pour déverrouiller le disque répond à la configuration requise de système d’exploitation, exécutez la commande de vérification système. Voici un exemple de sortie obtenue. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Exécutez `DataBoxDiskUnlock.exe` et fournissez la clé d’accès que vous avez obtenue à l’étape [Se connecter aux disques et obtenir la clé d’accès](#Connect-to-disks-and-get-the-passkey). La lettre de lecteur affectée au disque s’affiche. Voici un exemple de sortie obtenue.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Répétez la procédure de déverrouillage pour toutes les réinsertions de disque ultérieures. Si vous avez besoin d’aide concernant l’outil de déverrouillage Data Box Disk, utilisez la commande `help`.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Une fois que le disque est déverrouillé, vous pouvez afficher son contenu.    

    ![Contenu du disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Déverrouiller les disques sur le client Linux

1. Dans le portail Azure, accédez à **Général > Détails de l’appareil**. 
2. Téléchargez l’ensemble d’outils Data Box Disk correspondant au client Linux.  

    > [!div class="nextstepaction"]
    > [Télécharger l’ensemble d’outils Data Box Disk pour Linux](http://aka.ms/databoxdisktoolslinux) 

3. Sur votre client Linux, ouvrez un terminal. Accédez au dossier dans lequel vous avez téléchargé le logiciel. Modifiez les autorisations de fichier pour être en mesure d’exécuter ces fichiers. Tapez la commande suivante : 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Voici un exemple de sortie obtenue. Une fois que la commande chmod a été exécutée, vous pouvez vous assurer que les autorisations de fichier ont été modifiées en exécutant la commande `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Exécutez le script pour qu’il installe tous les fichiers binaires nécessaires pour le logiciel Data Box Disk Unlock. Utilisez `sudo` pour exécuter la commande en tant que racine. Une fois que les fichiers binaires ont été correctement installés, le terminal vous en informe.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Le script commence par vérifier si votre ordinateur client exécute un système d’exploitation pris en charge. Voici un exemple de sortie obtenue. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Tapez `y` pour poursuivre l’installation. Le script installe les packages suivants : 
    - **epel-release** : référentiel contenant les trois packages suivants. 
    - **dislocker et fuse-dislocker** : cet utilitaire permet de déchiffrer les disques chiffrés BitLocker. 
    - **ntfs-3g** : package permettant de monter les volumes NTFS. 
 
    Une fois que les packages ont été correctement installés, le terminal vous en informe.     
    ```
    Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
    ruby-libs.x86 64 0:1.8.7.374-5.el6 
    Complete! 
    Loaded plugins: fastestmirror, refresh-packagekit, security 
    Setting up Remove Process 
    Resolving Dependencies 
    --> Running transaction check 
    ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
    Dependencies Resolved 
    Package        Architecture        Version        Repository        Size 
    Removing:  epel-release        noarch         6-8        @extras        22 k 
    Transaction Summary                                 
    Remove        1 Package(s) 
    Installed size: 22 k 
    Downloading Packages: 
    Running rpmcheckdebug 
    Running Transaction Test 
    Transaction Test Succeeded 
    Running Transaction 
    Erasing : epel-release-6-8.noarch 
    Verifying : epel-release-6-8.noarch 
    Removed: 
    epel-release.noarch 0:6-8 
    Complete! 
    Dislocker is installed by the script. 
    OpenSSL is already installed.
    ```

6. Exécutez l’outil Data Box Disk Unlock. Fournissez la clé d’accès issue du Portail Azure que vous avez obtenue à l’étape [Se connecter aux disques et obtenir la clé d’accès](#Connect-to-disks-and-get-the-passkey). Si vous le souhaitez, spécifiez une liste de volumes chiffrés BitLocker à déverrouiller. La clé d’accès et la liste de volumes doivent être spécifiées entre guillemets simples. 

    Tapez la commande suivante.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’ /Volumes:’<list of volumes>’`         

    Voici un exemple de sortie obtenue. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’ /Volumes:’/dev/sdbl’ 
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Le point de montage du volume sur lequel vous pouvez copier vos données s’affiche.

7. Répétez la procédure de déverrouillage pour toutes les réinsertions de disque ultérieures. Si vous avez besoin d’aide concernant l’outil de déverrouillage Data Box Disk, utilisez la commande `help`. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Voici un exemple de sortie obtenue. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Une fois le disque déverrouillé, vous pouvez accéder au point de montage et visualiser le contenu du disque. Vous voici prêt à copier les données dans les dossiers *BlockBlob* ou *PageBlob*. 

    ![Contenu du disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Déballer votre disque Data Box
> * Se connecter aux disques et obtenir la clé d’accès
> * Déverrouiller les disques sur le client Windows
> * Déverrouiller les disques sur le client Linux


Passez au didacticiel suivant pour découvrir comment copier des données sur votre disque Data Box.

> [!div class="nextstepaction"]
> [Copier des données sur votre disque Data Box](./data-box-disk-deploy-copy-data.md)

