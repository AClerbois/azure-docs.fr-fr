---
title: Fichier Include
description: Fichier Include
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111701"
---
## <a name="download-the-source-code"></a>Télécharger le code source

Les dépôts de code source de supervision à distance incluent le code source et les fichiers config Docker nécessaires à l’exécution des images Docker des microservices.

Pour cloner le référentiel et en créer une version locale, utilisez votre environnement de ligne de commande afin d'accéder à un dossier approprié sur votre ordinateur local. Exécutez ensuite l’un des ensembles de commandes suivants pour cloner le dépôt .NET :

Pour télécharger la dernière version des implémentations de microservices .NET, exécutez :

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Ces commandes téléchargent le code source de tous les microservices en plus des scripts que vous utilisez pour exécuter les microservices localement. Même si vous n’avez pas besoin du code source pour exécuter les microservices dans Docker, vous pouvez l’utiliser si vous envisagez de modifier ultérieurement l’accélérateur de solution et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ceux-ci dépendent de l’exécution des services Azure exécutés dans le cloud. Utilisez le script suivant pour déployer les services Azure. Les exemples de script suivants supposent que vous utilisez le référentiel .NET sur un ordinateur Windows. Si vous travaillez dans un autre environnement, adaptez les chemins, les extensions de fichier et les séparateurs de chemin en conséquence.

### <a name="create-new-azure-resources"></a>Créer des ressources Azure

Si vous n’avez pas encore créé les ressources Azure nécessaires, procédez comme suit :

1. Dans votre environnement de ligne de commande, accédez au dossier **\services\scripts\local\launch** de votre copie clonée du référentiel.

1. Exécutez les commandes suivantes pour installer l'outil CLI **pcs** et vous connecter à votre compte Azure :

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Exécutez le script **start.cmd**. Le script vous invite à entrer les informations suivantes :
   * Le nom de la solution.
   * Sélectionnez l’abonnement Azure à utiliser.
   * L’emplacement du centre de données Azure à utiliser.

     Le script crée un groupe de ressources dans Azure avec le nom de votre solution. Ce groupe de ressources contient les ressources Azure qu’utilise l’accélérateur de solution. Vous pouvez supprimer ce groupe de ressources dès que vous n’avez plus besoin des ressources correspondantes.

     Le script ajoute également un ensemble de variables d'environnement dotées du préfixe **PCS** sur votre ordinateur local. Ces variables d’environnement fournissent les détails de surveillance à distance être en mesure de lire à partir d’une ressource Azure Key Vault. Cette ressource Key Vault est où la surveillance à distance lira à partir de ses valeurs de configuration.

     > [!TIP]
     > Quand le script s’exécute, il enregistre également les variables d’environnement dans un fichier appelé **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env**. Vous pouvez les utiliser pour de futurs déploiements d’accélérateurs de solutions. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

1. Quittez votre environnement de ligne de commande.

### <a name="use-existing-azure-resources"></a>Utiliser les ressources Azure existantes

Si vous avez déjà créé les ressources Azure requises, créez les variables d'environnement correspondantes sur votre ordinateur local.
Définissez les variables d’environnement pour les éléments suivants :
* **PCS_KEYVAULT_NAME** -nom de la ressource Azure Key Vault
* **PCS_AAD_APPID** -AAD de l’ID d’application
* **PCS_AAD_APPSECRET** -clé secrète d’application le AAD

Valeurs de configuration seront lue à partir de cette ressource Azure Key Vault. Ces variables d’environnement peuvent être enregistrées dans le  **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env** fichier à partir du déploiement. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

Certaines configurations requises par le microservice est stocké dans une instance de **Key Vault** qui a été créé sur le déploiement initial. Les variables correspondants dans le coffre de clés doivent être modifiés en fonction des besoins.