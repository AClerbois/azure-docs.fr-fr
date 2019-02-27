---
title: 'Résolution de problèmes : création d’un espace de travail Machine Learning Studio et connexion à celui-ci'
titleSuffix: Azure Machine Learning Studio
description: Ce guide propose des solutions pour certains défis qui se posent souvent quand vous configurez des espaces de travail Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7f0485221f0f29d08275f9508ba79e32c3f8924a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455436"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Guide de résolution des problèmes : Créer un espace de travail Azure Machine Learning Studio et s’y connecter
Ce guide propose des solutions pour certains défis qui se posent souvent quand vous configurez des espaces de travail Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Propriétaire de l'espace de travail
Pour ouvrir un espace de travail de Machine Learning Studio, vous devez être connecté au compte Microsoft utilisé pour créer l’espace de travail ou recevoir une invitation du propriétaire à rejoindre l’espace de travail. À partir du portail Azure, vous pouvez gérer l’espace de travail, notamment configurer l’accès.

Pour plus d'informations sur la gestion d'un espace de travail, consultez [Gérer un espace de travail Azure Machine Learning Studio].

[Gérer un espace de travail Azure Machine Learning Studio]: manage-workspace.md

## <a name="allowed-regions"></a>Régions autorisées
Machine Learning est actuellement disponible dans un nombre limité de régions. Si votre abonnement n’inclut une de ces régions, le message d’erreur suivant risque de s’afficher : « Vous ne disposez d’aucun abonnement dans les régions autorisées ».

Pour demander qu’une région soit ajoutée à votre abonnement, créez une nouvelle demande d’assistance depuis le portail Azure, choisissez **Facturation** comme type de problème, puis suivez les invites pour envoyer votre demande.

## <a name="storage-account"></a>Compte de stockage
Le service Machine Learning a besoin d'un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant ou en créer un lors de la création de l’espace de travail Machine Learning Studio (si vous disposez d’un quota pour la création d’un compte de stockage).

Une fois l’espace de travail Machine Learning Studio créé, vous pouvez vous connecter à Machine Learning Studio au moyen du compte Microsoft utilisé pour créer l’espace de travail. Si vous rencontrez le message d'erreur « Espace de travail introuvable » (comme dans la capture d'écran suivante), effectuez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Workspace not found](media/troubleshooting-creating-ml-workspace/screen3.png)

**Pour supprimer les cookies du navigateur**

1. Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** dans le coin supérieur droit et sélectionnez **Options Internet**.  

   ![Options Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Sous l'onglet **Général**, cliquez sur **Supprimer…**

   ![General tab](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Dans la boîte de dialogue **Supprimer l’historique de navigation**, vérifiez que **Cookies et données de sites web** est sélectionné, puis cliquez sur **Supprimer**.

   ![Delete cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Une fois les cookies supprimés, redémarrez le navigateur, puis accédez à la page [Microsoft Azure Machine Learning Studio](https://studio.azureml.net). Lorsque vous êtes invité à fournir un nom d’utilisateur et un mot de passe, entrez le même compte Microsoft que celui utilisé pour créer l’espace de travail.

## <a name="comments"></a>Commentaires

Notre objectif est de vous offrir une expérience de Machine Learning qui soit aussi transparente que possible. Publiez tous vos commentaires et problèmes sur le [forum Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous permettre de vous aider au mieux.
