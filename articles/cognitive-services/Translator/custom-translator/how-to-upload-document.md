---
title: Pour télécharger un document - Custom Translator
titleSuffix: Azure Cognitive Services
description: À l’aide de la fonctionnalité de chargement de documents, vous pouvez télécharger le document parallèle pour vos formations. Les documents parallèles sont des paires de documents dont un document est la traduction de l'autre. Un document de la paire contient des phrases dans la langue source et l’autre document contient ces phrases traduites dans la langue cible.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 3a0adb4b761121334e93f2b8d2d764ff3ab495e2
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633246"
---
# <a name="upload-a-document"></a>Charger un document

Dans [Custom Translator](https://portal.customtranslator.azure.ai), vous pouvez charger des documents parallèles pour former vos modèles de traduction. Les [documents parallèles](what-are-parallel-documents.md) sont des paires de documents dont un document est la traduction de l'autre. Un document de la paire contient des phrases dans la langue source et l’autre document contient ces phrases traduites dans la langue cible.

Avant de charger vos documents, consultez [l’aide relatives à la convention d’affectation de noms et aux formats des documents](document-formats-naming-convention.md) pour vérifier que votre format de fichier est pris en charge dans Custom Translator.

## <a name="how-to-upload-document"></a>Comment charger un document ?

À partir du portail [Custom Translator](https://portal.customtranslator.azure.ai), cliquez sur l’onglet de « Documents » pour accéder à la page des documents.

![Lien de chargement de documents](media/how-to/how-to-upload-1.png)


1.  Cliquez sur le bouton Charger des fichiers sur la page des documents.

    ![Charger la page des documents](media/how-to/how-to-upload-2.png)

2.  Dans la boîte de dialogue, renseignez les informations suivantes :

    a.  Type de document :

    -  Formation : ces documents seront utilisés pour un ensemble de formation.
    -  Réglage : ces documents seront utilisés pour un ensemble de réglage.
    -  Tests : ces documents seront utilisés pour un ensemble de tests.
    -  Dictionnaire d’expressions : ces documents seront utilisés pour un dictionnaire d’expressions.
    -  Dictionnaire de phrases : ces documents seront utilisés pour un dictionnaire de phrases.

    b.  Paire de langues

    c.  Remplacer un document existant : cochez cette case si vous souhaitez remplacer des documents existants portant le même nom.

    d.  Remplissez la section appropriée pour les données en parallèle ou les données de liste déroulante.

    -  Copie parallèle :
        -  Fichier source : sélectionnez un fichier de langue source sur votre ordinateur local.
        -  Fichier cible : sélectionnez un fichier de langue cible sur votre ordinateur local.
        -  Nom du document : utilisé uniquement si vous chargez des fichiers parallèles.

    - Données de liste déroulante :
        -  Fichier de liste déroulante : sélectionnez le fichier de liste déroulante à partir de votre ordinateur local. Votre fichier de liste déroulante contient à la fois les phrases des langues source et cible. La [convention d’affectation de noms](document-formats-naming-convention.md) est importante pour les fichiers de liste déroulante.

    e.  Cliquez sur Charger

    ![Chargez la boîte de dialogue des documents](media/how-to/how-to-upload-dialog.png)

3.  À ce stade, nous traitons vos documents et tentons d’extraire les phrases. Vous pouvez cliquer sur « Afficher la progression du chargement » pour vérifier l’état de vos documents pendant le traitement.

    ![Charger la boîte de dialogue de traitement de documents](media/how-to/how-to-upload-processing-dialog.png)

4.  Cette page affiche l’état et les erreurs pour chaque fichier dans votre chargement. Vous pouvez afficher l’état après le chargement à tout moment en cliquant sur l’onglet « Charger l’historique ».

    ![Charger la boîte de dialogue de l’historique des documents](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Afficher l’historique du chargement

À la page de l’historique du chargement, vous pouvez afficher l’historique de tous les chargements des documents, tels que le type de document, la paire de langues, l’état du chargement, etc.

1. À partir du portail [Custom Translator](https://portal.customtranslator.azure.ai), cliquez sur l’onglet Historique de chargement pour afficher l’historique.

    ![Onglet Historique du chargement](media/how-to/how-to-upload-history-1.png)

2. Cette page affiche l’état de tous vos chargements passés. Elle affiche les chargements du plus récent au moins récent. Pour chaque chargement, il affiche le nom du document, l’état du chargement, la date du chargement, le nombre de fichiers chargés, le type de fichier chargé et la paire de langues du fichier.

    ![Afficher la page Historique](media/how-to/how-to-document-history-2.png)

3. Cliquez sur n’importe quel enregistrement de l’historique de chargement. À la page des détails de l’historique de chargement, vous pouvez afficher les fichiers chargés dans le cadre du chargement, l’état de chargement du fichier, la langue du fichier et le message d’erreur (en cas d’erreur de chargement).

## <a name="next-steps"></a>Étapes suivantes

- Utilisez la [page de détails du document](how-to-view-document-details.md) pour passer en revue la liste des phrases extraites.
- [Pour effectuer l'apprentissage d'un modèle](how-to-train-model.md).
