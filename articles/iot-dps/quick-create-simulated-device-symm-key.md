---
title: Ce guide de démarrage rapide montre comment utiliser une clé symétrique pour provisionner un appareil simulé auprès d’Azure IoT Hub à l’aide de C | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez utiliser le SDK device pour C pour créer un appareil simulé qui utilise une clé symétrique avec le service Azure IoT Hub Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5899b2b667df4800bf98aa6ed7b70f2f8ba4f931
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337102"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Démarrage rapide : Provisionner un appareil simulé avec des clés symétriques

Dans ce guide de démarrage rapide, vous allez apprendre à créer et à exécuter un simulateur d’appareil sur une machine de développement Windows. Vous allez configurer cet appareil simulé à affecter à un hub IoT de manière à ce qu’il utilise une clé symétrique pour s’authentifier auprès d’une instance du service Device Provisioning. Un exemple de code du [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) est utilisé pour simuler une séquence de démarrage pour l’appareil qui lance le provisionnement. L’appareil est reconnu en fonction d’une inscription individuelle auprès de l’instance du service de provisionnement et affecté à un hub IoT.

Bien que cet article illustre le provisionnement avec une inscription individuelle, vous pouvez utiliser les mêmes procédures avec des groupes d’inscription. La seule différence réside dans le fait que vous devez utiliser une clé d’appareil dérivée avec un ID d’inscription unique pour l’appareil. Avec les groupes d’inscription, la clé symétrique sur l’inscription n’est pas utilisée directement. Bien que les groupes d’inscription avec des clés symétriques ne soient pas limités aux appareils d’ancienne génération, le [Guide pratique pour provisionner des appareils d’ancienne génération à l’aide d’une attestation de clé symétrique](how-to-legacy-device-symm-key.md) fournit un exemple de groupe d’inscription. Pour plus d’informations, consultez [Inscriptions de groupe pour l’attestation de clé symétrique](concepts-symmetric-key-attestation.md#group-enrollments).

Si vous ne connaissez pas le processus d’approvisionnement automatique, consultez [Concepts de provisionnement automatique](concepts-auto-provisioning.md). 

Vérifiez également que vous avez suivi la procédure décrite dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md) avant de poursuivre ce démarrage rapide. Ce guide de démarrage rapide nécessite au préalable la création d’une instance du service Device Provisioning.

Cet article traite d’une station de travail Windows. Toutefois, vous pouvez effectuer les procédures sur Linux. Pour obtenir un exemple sur Linux, consultez [Guide pratique du provisionnement pour la multilocation](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

* Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Dernière version de [Git](https://git-scm.com/download/) installée.


<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Préparer un environnement de développement pour le SDK Azure IoT pour C

Dans cette section, vous allez préparer un environnement de développement pour générer le [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c). 

Le SDK inclut l’exemple de code pour un appareil simulé. Cet appareil simulé tente le provisionnement durant la séquence de démarrage de l’appareil.

1. Téléchargez la version 3.11.4 du [système de génération de CMake](https://cmake.org/download/). Vérifiez le binaire téléchargé à l’aide de la valeur de hachage de chiffrement correspondante. L’exemple suivant utilise Windows PowerShell pour vérifier le hachage de chiffrement pour la version 3.11.4 de la distribution MSI x64 :

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Les valeurs de hachage suivantes pour la version 3.11.4 étaient celles indiquées sur le site de CMake au moment de la rédaction de cet article :

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Il est important que les composants requis Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») soient installés sur votre machine, **avant** de commencer l’installation de l’élément `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake.

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le dépôt GitHub du SDK Azure IoT pour C :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Pour le moment, ce référentiel a une taille d’environ 220 Mo. Attendez-vous à ce que cette opération prenne plusieurs minutes.


3. Créez un sous-répertoire `cmake` dans le répertoire racine du référentiel Git et accédez à ce dossier. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Exécutez la commande suivante qui génère une version du SDK propre à votre plateforme cliente de développement. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Si `cmake` ne trouve pas votre compilateur C++, vous obtiendrez peut-être des erreurs de build lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Une fois la génération terminée, les dernières lignes de sortie doivent ressembler à la sortie suivante :

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```



## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Créer une entrée d’inscription d’appareil dans le portail

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre instance Device Provisioning Service.

2. Sélectionnez l’onglet **Gérer les inscriptions**, puis cliquez sur le bouton **Ajouter une inscription individuelle** dans la partie supérieure. 

3. Dans **Ajouter une inscription**, entrez les informations suivantes, puis cliquez sur le bouton **Enregistrer**.

    - **Mécanisme** : sélectionnez **Clé symétrique** comme *Mécanisme* d'attestation d'identité.

    - **Générer automatiquement les clés** : cochez cette case.

    - **ID d'inscription** : entrez un ID pour identifier l'inscription. Utilisez uniquement des caractères alphanumériques en minuscules et des tirets (« - »). Par exemple : `symm-key-device-007`.

    - **ID de l'appareil IoT Hub :** entrez un identificateur d'appareil. Par exemple : **device-007**.

    ![Ajouter une inscription individuelle pour l’attestation de clé symétrique dans le portail](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Une fois que vous avez enregistré votre inscription, la **Clé primaire** et la **Clé secondaire** sont générées et ajoutées à l’entrée d’inscription. Votre inscription d’appareil à clé symétrique apparaît sous la forme **symm-key-device-007** sous la colonne *ID d’inscription* de l’onglet *Inscriptions individuelles*. 

    Ouvrez l’inscription et copiez la valeur de votre **Clé primaire** générée.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simuler la première séquence de démarrage de l’appareil

Dans cette section, mettez à jour l’exemple de code pour envoyer la séquence de démarrage de l’appareil vers votre instance de service Device Provisioning. Cette séquence de démarrage entraîne la reconnaissance et l’affectation de l’appareil à un hub IoT lié à l’instance de service Device Provisioning.



1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning et notez les valeurs de **_Étendue de l’ID_**.

    ![Extraction des informations de point de terminaison du service Device Provisioning à partir du panneau du Portail](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Dans Visual Studio, ouvrez le fichier de solution **azure_iot_sdks.sln** généré en exécutant CMake. Le fichier solution doit être à l’emplacement suivant :

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Samples**. Développez l’exemple de projet nommé **prov\_dev\_client\_sample**. Développez **Fichiers sources**, puis ouvrez **prov\_dev\_client\_sample.c**.

4. Recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Recherchez la définition de la fonction `main()` dans le même fichier. Vérifiez que la variable `hsm_type` a la valeur `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, comme indiqué ci-dessous :

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Recherchez l'appel vers `prov_dev_set_symmetric_key_info()` dans **prov\_dev\_client\_sample.c** qui est commenté.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Supprimez les marques de commentaire de l'appel de fonction et remplacez les valeurs d'espace réservé (y compris les crochets) par votre ID d'inscription et les valeurs de clé primaire.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Enregistrez le fichier .

7. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**. 

8. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite pour régénérer le projet, cliquez sur **Oui** pour régénérer le projet avant l’exécution.

    La sortie suivante est un exemple illustrant le démarrage réussi de l’appareil simulé et sa connexion à une instance de service Device Provisioning pour l’affecter à un hub IoT :

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. Dans le portail, accédez au hub IoT auquel votre appareil simulé a été affecté, puis cliquez sur l’onglet **Appareils IoT**. En cas de réussite du provisionnement de l’appareil simulé sur le hub, son ID d’appareil s’affiche sur le panneau **Appareils IoT**, avec un *ÉTAT* **activé**. Vous devrez peut-être cliquer sur le bouton **Actualiser** dans la partie supérieure. 

    ![L’appareil est inscrit avec le hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez **Appareils IoT** pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil simulé sur un ordinateur Windows. Vous l’avez également provisionné sur votre hub IoT à l’aide d’une clé symétrique avec le service Azure IoT Hub Device Provisioning sur le portail. Pour apprendre à inscrire un appareil par programmation, passez au guide de démarrage rapide sur l’inscription par programmation des appareils X.509. 

> [!div class="nextstepaction"]
> [Démarrage rapide d’Azure : Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
