---
title: Exporter un modèle Resource Manager – Portail Azure
description: Utilisez le portail Azure pour exporter un modèle Azure Resource Manager à partir de ressources de votre abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0605e24590fa2d702a1385429a7808a7e1226809
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532347"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Export d’une ressource unique ou de plusieurs ressources vers un modèle dans le portail Azure

Pour aider à créer des modèles Azure Resource Manager, vous pouvez exporter un modèle à partir de ressources existantes. Le modèle exporté vous aide à comprendre la syntaxe JSON et les propriétés qui déploient vos ressources. Pour automatiser les déploiements futurs, commencez par le modèle exporté et modifiez-le en fonction de votre scénario.

Resource Manager vous permet de choisir une ou plusieurs ressources pour l’exportation vers un modèle. Vous pouvez vous concentrer exactement sur les ressources dont vous avez besoin dans le modèle.

Cet article explique comment exporter des modèles via le portail. Vous pouvez également utiliser [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates) ou l'[API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Choisir la bonne option d’exportation

Il existe deux façons d’exporter un modèle :

* **Exporter à partir d’un groupe de ressources ou d’une ressource**. Cette option génère un nouveau modèle à partir de ressources existantes. Le modèle exporté est un « instantané » de l’état actuel du groupe de ressources. Vous pouvez exporter un groupe de ressources complet ou des ressources spécifiques au sein de ce groupe de ressources.

* **Exporter avant le déploiement ou à partir de l’historique**. Cette option récupère une copie exacte d’un modèle utilisé pour le déploiement.

Selon l’option choisie, les modèles exportés ont différentes qualités.

| À partir d’un groupe de ressources ou d’une ressource | Avant le déploiement ou à partir de l’historique |
| --------------------- | ----------------- |
| Le modèle est un instantané de l’état actuel des ressources. Il inclut toutes les modifications manuelles apportées après le déploiement. | Le modèle affiche uniquement l’état des ressources au moment du déploiement. Toutes les modifications manuelles apportées après le déploiement ne sont pas incluses. |
| Vous pouvez sélectionner les ressources à exporter à partir d’un groupe de ressources. | Toutes les ressources pour un déploiement spécifique sont incluses. Vous ne pouvez pas choisir un sous-ensemble de ces ressources ou ajouter des ressources qui ont été ajoutés à un autre moment. |
| Le modèle inclut toutes les propriétés des ressources, y compris certaines propriétés que vous ne définiriez normalement pas pendant le déploiement. Vous souhaiterez peut-être supprimer ou nettoyer ces propriétés avant de réutiliser le modèle. | Le modèle inclut uniquement les propriétés nécessaires au déploiement. Le modèle est prêt à l’emploi. |
| Le modèle probablement n’inclut probablement pas tous les paramètres dont vous avez besoin pour le réutiliser. La plupart des valeurs de propriété sont codées en dur dans le modèle. Pour redéployer le modèle dans d’autres environnements, vous devez ajouter des paramètres qui augmentent la possibilité de configurer les ressources. | Le modèle inclut des paramètres qui permettent de facilement le redéployer dans différents environnements. |

Exportez le modèle à partir d’une ressource ou d’un groupe de ressources, lorsque :

* Vous devez capturer les modifications apportées aux ressources après le déploiement d’origine.
* Vous devez sélectionner les ressources à exporter.

Exportez le modèle avant le déploiement ou de l’historique, lorsque :

* Vous souhaitez un modèle facile à réutiliser.
* Vous n’avez pas besoin d’inclure les modifications apportées après le déploiement d’origine.

## <a name="export-template-from-a-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources

Pour exporter une ou plusieurs ressources à partir d’un groupe de ressources :

1. Sélectionnez le groupe de ressources qui contient les ressources que vous souhaitez exporter.

1. Sélectionnez une ou plusieurs ressources en cochant les cases correspondantes.  Pour sélectionner tout, cochez la case située à gauche de **Nom**. L’élément de menu **Exporter le modèle** devient actif seulement uniquement après la sélection d’au moins une ressource.

   ![Exporter toutes les ressources](./media/export-template-portal/select-all-resources.png)

    Dans la capture d’écran, seul le compte de stockage est sélectionné.
1. Sélectionnez **Exporter le modèle**.

1. Le modèle exporté s’affiche et est disponible au téléchargement et au déploiement.

   ![Afficher le modèle](./media/export-template-portal/show-template.png)

## <a name="export-template-from-a-resource"></a>Exportation du modèle à partir d’une ressource

Pour exporter une ressource :

1. Sélectionnez le groupe de ressources qui contient la ressource que vous souhaitez exporter.

1. Sélectionnez la ressource que vous souhaitez exporter pour ouvrir la ressource.

1. Pour cette ressource, sélectionnez **Exporter le modèle** dans le volet de gauche.

   ![Exporter une ressource](./media/export-template-portal/export-single-resource.png)

1. Le modèle exporté s’affiche et est disponible au téléchargement et au déploiement. Le modèle contient uniquement la ressource unique.

## <a name="export-template-before-deployment"></a>Exporter un modèle avant le déploiement

1. Sélectionnez le service Azure que vous souhaitez déployer.

1. Renseignez les valeurs pour le nouveau service.

1. Après la validation, mais avant de commencer le déploiement, sélectionnez **Télécharger un modèle pour l’automatisation**.

   ![Télécharger un modèle](./media/export-template-portal/download-before-deployment.png)

1. Le modèle s’affiche et est disponible au téléchargement et au déploiement.


## <a name="export-template-after-deployment"></a>Exporter un modèle après le déploiement

Vous pouvez exporter le modèle qui a été utilisé pour déployer les ressources existantes. Le modèle que vous obtenez est exactement celui qui a été utilisé pour le déploiement.

1. Sélectionnez le groupe de ressources que vous souhaitez exporter.

1. Sélectionnez le lien sous **Déploiements**.

   ![Sélectionner l’historique des déploiements](./media/export-template-portal/select-deployment-history.png)

1. Sélectionnez un des déploiements dans l’historique des déploiements.

   ![Sélectionner un déploiement](./media/export-template-portal/select-details.png)

1. Sélectionner **Modèle**. Le modèle utilisé pour ce déploiement s’affiche et est disponible au téléchargement.

   ![Sélectionner un modèle](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exporter des modèles avec [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates) ou l'[API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](/azure/azure-resource-manager/).
- Pour accéder aux schémas liés aux modèles Azure Resource Manager, consultez [Informations de référence sur les modèles](/azure/templates/).
