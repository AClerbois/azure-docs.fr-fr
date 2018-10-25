---
title: Provisionner un appareil Raspberry Pi pour la surveillance à distance à l’aide de C - Azure| Microsoft Docs
description: Explique comment connecter un appareil Raspberry Pi à l’accélérateur de solution de surveillance à distance avec une application écrite en C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: c20b1d5f3a84e950e37a3236272256db620a5985
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831098"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Connecter votre appareil Raspberry Pi à l’accélérateur de solution Monitoring à distance (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce tutoriel vous montre comment connecter un appareil physique à l’accélérateur de solution Surveillance à distance. Comme avec la plupart des applications embarquées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil Raspberry Pi est écrit en C. Dans ce didacticiel, vous générez l’application sur un appareil Raspberry Pi exécutant le système d’exploitation Raspbian.

### <a name="required-hardware"></a>Matériel requis

Un ordinateur de bureau permettant de vous connecter à distance à la ligne de commande sur le Raspberry Pi.

[Starter Kit Microsoft IoT pour Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou composants équivalents. Ce didacticiel utilise les éléments suivants du kit :

- Raspberry Pi 3
- Carte MicroSD (avec NOOBS)
- Un câble mini USB
- Un câble Ethernet

### <a name="required-desktop-software"></a>Logiciels de bureau requis

Vous devez installer le client SSH sur votre ordinateur de bureau afin de pouvoir accéder à distance à la ligne de commande sur le Raspberry Pi.

- Windows n’inclut pas de client SSH. Nous vous recommandons d’utiliser [PuTTY](http://www.putty.org/).
- La plupart des distributions Linux et Mac OS incluent l’utilitaire de ligne de commande SSH. Pour plus d’informations, consultez [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Utilisation de SSH avec Linux ou Mac OS).

### <a name="required-raspberry-pi-software"></a>Logiciels Raspberry Pi requis

Cet article suppose que vous avez installé la dernière version du [système d’exploitation Raspbian sur votre appareil Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Les étapes suivantes vous montrent comment préparer votre appareil Raspberry Pi pour générer une application C qui se connecte à l’accélérateur de solution :

1. Connectez-vous à votre appareil Raspberry Pi avec **ssh**. Pour plus d’informations, consultez [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) sur le [site web de Raspberry Pi](https://www.raspberrypi.org/).

1. Pour mettre à jour votre Raspberry Pi, utilisez la commande suivante :

    ```sh
    sudo apt-get update
    ```

1. Pour effectuer les étapes décrites dans ce guide pratique, suivez les étapes de [Configurer votre environnement de développement Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pour ajouter les outils et bibliothèques de développement requis pour votre Raspberry Pi.

## <a name="view-the-code"></a>Afficher le code

L’[exemple de code](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilisé dans ce guide est disponible dans le référentiel GitHub des Kits de développement logiciel (SDK) Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Télécharger le code source et préparer le projet

Pour préparer le projet, clonez ou téléchargez le [référentiel de Kits de développement logiciel (SDK) Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) à partir de GitHub.

L’exemple se trouve dans le dossier **samples/solutions/remote_monitoring_client**.

Ouvrez le fichier **remote_monitoring.c** dans le dossier **samples/solutions/remote_monitoring_client** dans un éditeur de texte.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Les étapes suivantes décrivent comment utiliser *CMake* pour générer votre application cliente. L’application cliente de supervision à distance est générée dans le cadre du processus de génération du Kit de développement logiciel (SDK).

1. Modifiez le fichier **remote_monitoring.c** pour remplacer `<connectionstring>` par la chaîne de connexion d’appareil que vous avez notée au début de ce guide pratique lorsque vous avez ajouté un appareil à l’accélérateur de solution.

1. Accédez à la racine de votre copie clonée du [référentiel de Kits de développement logiciel (SDK) Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) et exécutez les commandes suivantes pour générer l’application cliente :

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Exécutez l’application cliente et envoyez les données de télémétrie à IoT Hub :

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    La console affiche des messages quand l’application :

    - L’application envoie un exemple de données de télémétrie à l’accélérateur de solution.
    - Répond aux méthodes appelées à partir du tableau de bord des solutions.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
