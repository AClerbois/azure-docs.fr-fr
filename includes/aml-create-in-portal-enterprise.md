---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/04/2019
ms.openlocfilehash: c1f4b3d60c0b8af96d851ce54c2aac57c76335a7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633658"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de votre abonnement Azure. 

1. En haut à gauche du portail Azure, sélectionnez **+ Créer une ressource**.

      ![Créer une nouvelle ressource](media/aml-create-in-portal/create-workspace.gif)

1. Utilisez la barre de recherche pour rechercher **Machine Learning**.

1. Sélectionnez **Machine Learning**.

1. Dans le volet **Machine Learning**, sélectionnez **Créer** pour commencer.

1. Fournissez les informations suivantes pour configurer votre nouvel espace de travail :

   Champ|Description 
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.
   Édition de l’espace de travail | Sélectionnez **Enterprise**.  Ce tutoriel nécessite l’utilisation de l’édition Enterprise.  L’édition Enterprise est en préversion et n’implique pas de frais supplémentaires pour le moment.  

1. Une fois que vous avez terminé de configurer l’espace de travail, sélectionnez **Créer**. 

   > [!Warning] 
   > La création de votre espace de travail dans le cloud peut prendre plusieurs minutes.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. 
 
 1. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

