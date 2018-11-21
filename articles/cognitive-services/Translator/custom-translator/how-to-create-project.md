---
title: Comment créer un projet ? - Custom Translator
titleSuffix: Azure Cognitive Services
description: Comment créer un projet dans Custom Translator ?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 4e5ac4386af55855c5240f89557feafd4a93adfb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626771"
---
# <a name="create-a-project"></a>Création d’un projet

Un projet est un conteneur pour les modèles, documents et tests. Chaque projet inclut automatiquement tous les documents téléchargés dans cet espace de travail dotés de la paire de langues qui convient. 

La création d'un projet constitue la première étape de création d’un modèle. 

## <a name="create-a-project"></a>Créer un projet :

1.  Dans le portail [Custom Translator](https://portal.customtranslator.azure.ai), cliquez sur Créer un projet.

    ![Créer un projet](media/how-to/how-to-create-project.png)

2.  Dans la boîte de dialogue, entrez les détails suivants relatifs à votre projet :

    a.  Nom du projet (obligatoire) : donnez un nom unique et explicite à votre projet. Il n’est pas nécessaire de mentionner les langues dans le titre.

    b.  Description : résumé succinct du projet. Cette description n'influe en rien sur le comportement de Custom Manager ou du système personnalisé qui en résulte, mais peut vous aider à différencier les projets.

    c.  Paire de langues (obligatoire) : sélectionnez les langues source et cible.

    d.  Catégorie (obligatoire) : sélectionnez la catégorie la plus adaptée à votre projet. La catégorie décrit la terminologie et le style des documents que vous envisagez de traduire.

    e.  Description de la catégorie : utilisez ce champ pour mieux décrire le domaine ou secteur dans lequel vous travaillez. Par exemple, si votre catégorie est médecine, vous pouvez ajouter un document spécifique (chirurgie ou pédiatrie, par exemple). Cette description n'influe en rien sur le comportement de Custom Manager ou du système personnalisé qui en résulte.

    f.  Étiquette du projet : l'[étiquette du projet](workspace-and-project.md#project-labels) permet de différencier les projets présentant la même paire de langues et la même catégorie. En guise de bonne pratique, utilisez une étiquette *uniquement* si vous envisagez de générer plusieurs projets pour la même paire de langues et la même catégorie, et souhaitez accéder à ces projets avec un ID de catégorie différent. N’utilisez pas ce champ si vous créez des systèmes pour une seule catégorie. Une étiquette de projet n’est pas obligatoire, et inutile pour différentier les paires de langues. Vous pouvez utiliser la même étiquette pour plusieurs projets.

    ![Créer une boîte de dialogue de projet](media/how-to/how-to-create-project-dialog.png)

3.  Cliquez sur Create.

## <a name="view-project-details"></a>Afficher les détails du projet

La page d’accueil Custom Translator affiche les 10 premiers projets de votre espace de travail. Elle affiche le nom du projet, la paire de langues, la catégorie, l'état ainsi que le score BLEU.

Après avoir sélectionné un projet, la page de projet affiche ce qui suit :

- CategoryID : un CategoryID est créé en concaténant l'ID d'espace de travail, l'étiquette de projet et le code de catégorie. Vous utilisez le CategoryID avec l’API Text Translator pour obtenir des traductions personnalisées.

- Bouton Effectuer l'apprentissage : utilisez ce bouton pour démarrer l'[apprentissage d’un modèle](how-to-train-model.md).

- Bouton Ajouter des documents : utilisez ce bouton pour [charger des documents](how-to-upload-document.md).

- Bouton Filtrer des documents : utilisez ce bouton pour filtrer et rechercher des documents spécifiques.

    ![Afficher les détails du projet](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment effectuer une recherche, modifier ou supprimer un projet](how-to-search-edit-delete-projects.md).
- Découvrez [comment télécharger un document](how-to-upload-document.md) pour créer des modèles de traduction.
