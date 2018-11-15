---
title: Créer un modèle Azure Resource Manager pour le déploiement d’un compte de stockage chiffré | Microsoft Docs
description: Utilisez Visual Studio Code pour créer un modèle pour le déploiement d’un compte de stockage chiffré.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c75c0ce7d12f65878ec8cde0f265022c7beeb29
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613509"
---
# <a name="tutorial-deploy-an-encrypted-azure-storage-account-with-resource-manager-template"></a>Didacticiel : Déployer un compte de stockage Azure chiffré à l’aide d’un modèle Resource Manager

Découvrez comment trouver les informations concernant le schéma de modèle et utiliser ces informations pour créer des modèles Azure Resource Manager.

Dans ce didacticiel, vous utiliserez un modèle de base pris dans les modèles de démarrage rapide Azure. À l’aide des documents de référence du modèle, vous personnalisez le modèle de base pour créer un compte de stockage chiffré.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Comprendre le modèle
> * Trouver la référence du modèle
> * Modifier le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Outils Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

[Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Le modèle définit une ressource de compte de stockage Azure.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="understand-the-schema"></a>Comprendre le schéma

1. Dans VS Code, réduisez le modèle jusqu’au niveau de la racine. Vous avez la structure la plus simple avec les éléments suivants :

    ![Structure la plus simple du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema** : spécifiez l’emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.
    * **contentVersion** : spécifiez n’importe quelle valeur pour que cet élément documente les modifications importantes dans votre modèle.
    * **parameters** : spécifiez les valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources.
    * **variables** : spécifiez les valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle.
    * **ressources** : spécifiez les types de ressource déployés ou mis à jour dans un groupe de ressources.
    * **outputs** : spécifiez les valeurs retournées après le déploiement.

2. Développer des **ressources**. Une ressource `Microsoft.Storage/storageAccounts` est définie. Le modèle crée un compte de stockage non chiffré.

    ![Définition du modèle de compte de stockage Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Trouver la référence du modèle

1. Accédez aux [modèles Azure](https://docs.microsoft.com/azure/templates/).
2. Dans **Filtrer par titre**, entrez **comptes de stockage**.
3. Sélectionnez **Référence/Référence de modèle/Stockage/Comptes de stockage**, comme indiqué dans la capture d’écran suivante :

    ![Resource Manager modèle référence compte de stockage](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Rechercher les informations de définition relatives au chiffrement.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Sur la même page Web, la description qui suit confirme que `encryption`l’objet est utilisé pour créer un compte de stockage chiffré.

    ![Resource Manager modèle référence compte de stockage chiffrage](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Il existe deux façons de gérer la clé de chiffrement. Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec Storage Service Encryption ou utiliser vos propres clés de chiffrement. Pour que ce didacticiel reste simple, utilisez l’option `Microsoft.Storage`, afin de ne pas avoir à créer un Azure Key Vault.

    ![Resource Manager modèle référence compte de stockage chiffrage objet](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Votre objet de chiffrement doit ressembler à :

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Modifier le modèle

À partir de Visual Studio Code, modifiez le modèle afin que l’élément de ressources ressemble à :

![Ressources de compte de stockage chiffré du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) dans le guide de démarrage rapide Visual Studio Code pour connaître la procédure de déploiement.

La capture d’écran suivante montre la commande CLI permettant de répertorier le compte de stockage nouvellement créé, ce qui indique que le chiffrement a été activé pour le stockage d’objets blob.

![Compte de stockage chiffré Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser la référence de modèle pour personnaliser un modèle existant. Pour apprendre à créer plusieurs instances de compte de stockage, voir :

> [!div class="nextstepaction"]
> [Créer plusieurs instances](./resource-manager-tutorial-create-multiple-instances.md)
