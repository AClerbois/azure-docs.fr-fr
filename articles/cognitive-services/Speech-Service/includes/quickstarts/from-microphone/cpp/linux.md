---
title: 'Démarrage rapide : Reconnaître la parole provenant d’un micro, C++ (Linux) – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en C++ sur Linux à l’aide du SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 5236536abbe8ad7a415f0ea94036edf49b892052
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505634"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Créez un fichier source C++ nommé `helloworld.cpp`, et collez le code suivant dedans.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement aux services Voix.

1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Sur un système **x64** (64 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Sur un système **x86** (32 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Sur un système **ARM64** (64 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Exécution de l'application

1. Configurez le chemin de la bibliothèque du chargeur pour qu’il pointe vers la bibliothèque du kit SDK Speech.

   * Sur un système **x64** (64 bits), entrez la commande suivante.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Sur un système **x86** (32 bits), entrez cette commande.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * Sur un système **ARM64** (64 bits), entrez la commande suivante.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Exécutez l'application.

   ```sh
   ./helloworld
   ```

1. Dans la fenêtre de la console, une invite s’affiche, demandant que vous disiez quelque chose. Prononcez une phrase ou quelques mots en anglais. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la même fenêtre.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
