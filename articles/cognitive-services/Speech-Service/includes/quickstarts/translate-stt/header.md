---
title: 'Démarrage rapide : Traduction de la parole en texte – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504738"
---
Dans ce guide de démarrage rapide, vous utiliserez le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire interactivement la parole d’une langue en texte dans une autre langue. Après avoir satisfait à quelques conditions préalables, la traduction de la parole en texte ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechConfig```` pour spécifier les langues source et cible.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
