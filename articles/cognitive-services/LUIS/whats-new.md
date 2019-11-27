---
title: 'Nouveauté : service Language Understanding (LUIS)'
titleSuffix: Azure Cognitive Services
description: Cet article est régulièrement mis à jour avec des informations nouvelles sur l’API Language Understanding Azure Cognitive Services.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 62ce0ac36223098d013cc75b7f62fc2c68e358ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953378"
---
# <a name="whats-new-in-language-understanding"></a>Nouveauté du service Language Understanding

Découvrir les nouveautés du service. Ces éléments incluent des notes de publication, des vidéos, des billets de blog et d’autres types d’informations. Marquez cette page pour rester aux faits des nouveautés du service.  

## <a name="release-notes"></a>Notes de publication 

### <a name="november-4-2019---ignite"></a>4 novembre 2019 – Ignite

* Amélioration de la productivité des développeurs
    * Disponibilité générale de notre [point de terminaison de prédiction V3](luis-migration-api-v3.md). 
    * Possibilité d’importer et d’exporter des applications avec le format .lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Cela ouvre la voie à un processus d’intégration/livraison continues (CI/CD) efficace. 
* Extension linguistique
    * [Arabe et hindi](luis-language-support.md) en préversion publique.
* Modèles prédéfinis
    * Des [domaines prédéfinis](luis-reference-prebuilt-domains.md) sont désormais en disponibilité générale (GA)
    * Les [entités prédéfinies](luis-reference-prebuilt-entities.md#japanese-entity-support) japonaises (âge, devise, nombre, pourcentage), ne sont pas prises en charge dans la version V3.
    * Les [entités prédéfinies](luis-reference-prebuilt-entities.md#italian-entity-support) italiennes (âge, devise, dimension, nombre, résolution de pourcentage) ont changé depuis la version V2.
* Amélioration de l’expérience utilisateur dans le [portail preview.luis.ai](https://preview.luis.ai) (expérience d’étiquetage remaniée pour permettre la génération et le débogage de modèles complexes). Essayez les tutoriels du portail en préversion :
    * [Intentions uniquement](tutorial-intents-only.md)
    * [Entité issue du Machine Learning décomposable](tutorial-machine-learned-entity.md) 
* Fonctionnalités avancées de compréhension de la langue ([génération de modèles linguistiques sophistiqués](luis-concept-entity-types.md) avec moins d’efforts). 
* Définition de fonctionnalités de Machine Learning au niveau du modèle et activation de modèles à utiliser en tant que signaux pour d’autres modèles, comme l’utilisation d’entités en tant que fonctionnalités pour des intentions et d’autres entités.
* Nouvelles [limites](luis-boundaries.md) étendues (maximum plus élevé pour les listes d’expressions et le nombre total d’expressions, ainsi que nouveau modèle en tant que limites de fonctionnalité)
* Extraction d’informations à partir de texte au format de structure hiérarchique profonde, qui rend les applications de conversation plus puissantes.

    ![image d’entité issue de l’apprentissage automatique](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 septembre 2019

* Ressource de création Azure : [migrer maintenant](luis-migration-authoring.md).
    * 500 applications par ressource Azure
    * 100 versions par application
* Prise en charge de la langue turque pour les entités prédéfinies
* Prise en charge de la langue italienne pour datetimeV2

### <a name="july-23-2019"></a>23 juillet 2019

* Mise à jour de [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) vers 1.2.3
    * Reconnaissances de l’âge, de la température, de la dimension et des devises en italien.
    * Amélioration de la reconnaissance des jour fériés en anglais pour calculer correctement les dates basées sur la Thanksgiving.
    * Améliorations de DateTime en français pour réduire les faux positifs des entités non-date et non-heure.
    * Prise en charge des années civiles/scolaires/fiscales et des acronymes dans DateRange en anglais.
    * Reconnaissance de PhoneNumber améliorée en chinois et japonais.
    * Prise en charge améliorée de NumberRange en anglais.
    * Amélioration des performances.

### <a name="june-24-2019"></a>24 juin 2019

* [Entité prédéfinie OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) pour la prise en charge de l’ordre, comme « suivant », « précédent » et « dernier ». Anglais uniquement.

### <a name="may-6-2019---build-conference"></a>6 mai 2019 - //Conférence Build

Les fonctionnalités publiées lors de la conférence Build 2019 sont les suivantes :

* [Aperçu du guide de migration API V3](luis-migration-api-v3.md)
* [Tableau de bord d’analyse amélioré](luis-how-to-use-dashboard.md)
* [Domaines prédéfinis améliorés](luis-reference-prebuilt-domains.md) 
* [Entités de liste dynamique](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entités externes](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Videos

### <a name="2019-build-videos"></a>Vidéos Build 2019

[Comment utiliser l’intelligence artificielle conversationnelle Azure pour préparer votre entreprise à la prochaine génération](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Mises à jour de service

[Annonces de mise à jour Azure pour Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
