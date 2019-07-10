---
title: 'Démarrage rapide : Créer une application HoloLens Unity avec Azure Spatial Anchors | Microsoft Docs'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application HoloLens avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c29819d817138f2512420584947763247837a9ea
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "67135203"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Démarrage rapide : Créer une application HoloLens Unity qui utilise Azure Spatial Anchors

Ce guide de démarrage rapide explique comment créer une application HoloLens Unity qui utilise [Azure Spatial Anchors](../overview.md). Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte avec des objets qui conservent leur emplacement sur les appareils. Quand vous aurez terminé, vous aurez une application HoloLens générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

- Créer un compte Spatial Anchors.
- Préparer les paramètres de build Unity.
- Configurer l’identificateur et la clé du compte Spatial Anchors.
- Exporter le projet HoloLens Visual Studio.
- Déployer l’application et l’exécuter sur un appareil HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :


- Vous avez besoin d’un ordinateur Windows sur lequel <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> ou version ultérieure, et <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ou version ultérieure sont installés. Votre installation de Visual Studio doit comprendre la charge de travail **Développement pour la plateforme Windows universelle**. Installez <a href="https://git-scm.com/download/win" target="_blank">Git pour Windows</a>.
- Vous avez besoin d’un appareil HoloLens avec le [mode développeur](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) activé. [La mise à jour de Windows 10 d’octobre 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (également appelée RS5) doit être installée sur l’appareil. Pour mettre à jour votre appareil HoloLens avec la dernière version, ouvrez l’application **Paramètres**, accédez à **Mise à jour et sécurité**, puis sélectionnez **Rechercher les mises à jour**.
- Dans votre application, vous devez activer la fonctionnalité **SpatialPerception**. Ce paramètre se trouve dans **Build Settings (Paramètres de build)**  > **Player Settings (Paramètres du lecteur)**  > **Publishing Settings (Paramètres de publication)**  > **Capabilities (Fonctionnalités)** .
- Dans votre application, vous devez activer **Virtual Reality Supported** (Réalité virtuelle prise en charge) avec le **SDK Windows Mixed Reality**. Ce paramètre se trouve dans **Build Settings (Paramètres de build)**  > **Player Settings (Paramètres du lecteur)**  > **XR Settings (Paramètres XR)** .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Télécharger et ouvrir l’exemple de projet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Dans la section **Platform** (Plateforme), sélectionnez **Universal Windows Platform** (Plateforme Windows universelle). Remplacez **Target Device** (Appareil cible) par **HoloLens**.

Sélectionnez **Switch Platform** (Changer de plateforme) pour passer à la plateforme **Universal Windows Platform** (Plateforme Windows universelle). Unity peut vous demander d’installer des composants de prise en charge UWP s’ils sont manquants.

![Fenêtre des paramètres de build dans Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Fermez la fenêtre **Build Settings**.

## <a name="configure-the-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Enregistrez la scène en sélectionnant **File** > **Save**.

## <a name="export-the-hololens-visual-studio-project"></a>Exporter le projet HoloLens Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Sélectionnez **Build**. Dans la boîte de dialogue, sélectionnez un dossier où exporter le projet HoloLens Visual Studio.

À l’issue de l’exportation, un dossier avec le projet HoloLens exporté s’affiche.

## <a name="deploy-the-hololens-application"></a>Déployer l’application HoloLens

Dans le dossier, double-cliquez sur **HelloAR U3D.sln** pour ouvrir le projet dans Visual Studio.

Remplacez **Configuration de la solution** par **Mise en production**, **Plateforme de solution** par **x86**, puis sélectionnez **Appareil** dans les options de cible de déploiement.

Si vous utilisez HoloLens 2, utilisez **ARM** comme **plateforme de solution** au lieu de **x86**.

   ![Configuration de Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Allumez l’appareil HoloLens, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB.

Sélectionnez **Déboguer** > **Démarrer le débogage** pour déployer votre application et commencer le débogage.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

Dans Visual Studio, arrêtez l’application en sélectionnant **Arrêter le débogage** ou en appuyant sur Maj+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager des ancres spatiales sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
