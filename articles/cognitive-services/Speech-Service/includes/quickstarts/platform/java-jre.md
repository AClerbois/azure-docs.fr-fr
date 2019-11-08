---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Speech pour configuration de la plateforme Java (Windows, Linux, macOS) – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour l’utilisation de Java (Windows, Linux, macOS) avec Speech Services SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504338"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour le Java Runtime Environment (JRE) Java 8 64 bits.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- Le package Java du Kit de développement logiciel (SDK) Speech est disponible pour les systèmes d’exploitation suivants :
  - Windows : 64 bits uniquement
  - Mac : macOS X version 10.13 ou ultérieure
  - Linux : 64 bits uniquement sur Ubuntu 16.04, Ubuntu 18.04 ou Debian 9

## <a name="prerequisites"></a>Prérequis

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (nécessite Java déjà installé)
- Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

  - Sur Ubuntu, exécutez les commandes suivantes pour installer les packages requis :

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Sur Debian 9, exécutez les commandes suivantes pour installer les packages requis :

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour votre plateforme. Notez que la première installation peut nécessiter le redémarrage de Windows avant de continuer à suivre ce guide.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Créer un projet Eclipse et installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
