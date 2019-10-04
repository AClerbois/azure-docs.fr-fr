---
title: Création visuelle dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser la création visuelle dans Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: e8b9aa3af6b15272e19589eb422016f4abd9380f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883673"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Création visuelle dans Azure Data Factory

L’expérience utilisateur Azure Data Factory vous permet de créer et de déployer visuellement des ressources dans votre fabrique de données sans avoir à écrire du code. Vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline.

## <a name="authoring-canvas"></a>Zone de travail de création

Pour ouvrir la **zone de travail de création**, cliquez sur l’icône de crayon. 

![Zone de travail de création](media/author-visually/authoring-canvas.png)

Ici, vous allez créer les pipelines, les activités, les jeux de données, les services liés, les flux de données, les déclencheurs et les runtimes d’intégration qui composent votre fabrique. Pour commencer à créer un pipeline à l’aide de la zone de travail de création, consultez [Copier des données à l’aide de l’activité de copie](tutorial-copy-data-portal.md). 

L’expérience de création visuelle par défaut fonctionne directement avec le service Data Factory. L’intégration GitHub ou Azure Repos Git est également prise en charge afin d’autoriser le contrôle du code source et la collaboration pour les utiliser sur vos pipelines Data Factory. Pour en savoir plus sur les différences entre ces expériences de création, consultez [Contrôle de code source dans Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Expressions et fonctions

Les expressions et les fonctions peuvent être utilisées à la place des valeurs statiques pour spécifier de nombreuses propriétés dans Azure Data Factory.

Pour spécifier une expression pour une valeur de propriété, sélectionnez **Ajouter du contenu dynamique** ou cliquez sur **Alt+P** tout en vous concentrant sur le champ.

![Ajouter du contenu dynamique](media/author-visually/dynamic-content-1.png)

Vous ouvrez ainsi le **Générateur d’expressions Data Factory**, où vous pouvez générer des expressions à partir des variables système prises en charge, de la sortie de l’activité, des fonctions et des variables ou paramètres spécifiés par l’utilisateur. 

![Générateur d’expressions](media/author-visually/dynamic-content-2.png)

Pour plus d’informations sur le langage d’expression, consultez [Expressions et fonctions dans Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fournir des commentaires

Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

![Commentaires](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
