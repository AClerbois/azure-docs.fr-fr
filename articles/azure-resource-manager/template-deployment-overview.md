---
title: Vue d’ensemble des modèles
description: Décrit les avantages apportés par l’utilisation de modèles Azure Resource Manager pour le déploiement de ressources.
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0765ec1cd5952a05a168dfed72ba4d6fd78ed89c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150287"
---
# <a name="azure-resource-manager-templates-overview"></a>Vue d’ensemble des modèles Azure Resource Manager

Avec la migration vers le cloud, de nombreuses équipes ont adopté des méthodes de développement agile. Ces équipes effectuent des itérations rapides. Elles doivent déployer à plusieurs reprises leurs solutions dans le cloud et savoir que leur infrastructure est dans un état fiable. Comme l’infrastructure fait partie intégrante du processus itératif, la séparation entre les opérations et le développement a disparu. Les équipes doivent gérer le code d’infrastructure et le code d’application à l’aide d’un processus unifié.

Pour relever ces défis, vous pouvez automatiser les déploiements et utiliser la pratique de l’infrastructure en tant que code. Dans le code, vous définissez l’infrastructure qui doit être déployée. Le code d’infrastructure devient partie intégrante de votre projet. Tout comme le code d’application, vous stockez le code d’infrastructure dans un dépôt source et vous gérez ses versions. Toute personne de votre équipe peut exécuter le code et déployer des environnements similaires.

Pour implémenter une infrastructure en tant que code pour vos solutions Azure, utilisez des modèles Azure Resource Manager. Le modèle est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative, qui vous permet d’indiquer ce que vous envisagez de déployer sans avoir à écrire la séquence de commandes de programmation pour le créer. Dans le modèle, vous spécifiez les ressources à déployer et les propriétés de ces ressources.

## <a name="why-choose-resource-manager-templates"></a>Pourquoi choisir des modèles Resource Manager ?

Si vous essayez de choisir entre utiliser des modèles Resource Manager et l’un des autres services d’infrastructure en tant que code, tenez compte des avantages suivants de l’utilisation de modèles :

* **Syntaxe déclarative** : Les modèles Resource Manager vous permettent de créer et de déployer une infrastructure Azure entière de façon déclarative. Par exemple, vous pouvez déployer non seulement des machines virtuelles, mais également l’infrastructure réseau, les systèmes de stockage et toute autre ressource dont vous pouvez avoir besoin.

* **Résultats reproductibles** : Déployez votre infrastructure à plusieurs reprises tout au long du cycle de vie de développement. Vous avez ainsi l’assurance que vos ressources sont déployées de façon cohérente. Les modèles sont idempotents, ce qui signifie que vous pouvez déployer le même modèle plusieurs fois et obtenir les mêmes types de ressources dans le même état. Vous pouvez développer un modèle qui représente l’état souhaité, plutôt que de développer un grand nombre de modèles distincts pour représenter des mises à jour.

* **Orchestration** : Vous n’avez pas à vous soucier de la complexité des opérations de commande. Resource Manager orchestre le déploiement de ressources interdépendantes afin qu’elles soient créées dans l’ordre approprié. Lorsque cela est possible, Resource Manager déploie des ressources en parallèle afin que vos déploiements se terminent plus rapidement que les déploiements en série. Vous déployez le modèle par le biais d’une seule commande, plutôt que par le biais de plusieurs commandes impératives.

   ![Comparaison de déploiements de modèles](./media/template-deployment-overview/template-processing.png)

* **Validation intégrée** : Votre modèle est déployé uniquement après avoir réussi la validation. Resource Manager vérifie le modèle avant de commencer le déploiement pour être sûr que celui-ci va réussir. Votre déploiement est moins susceptible de s’arrêter dans un état à moitié terminé.

* **Fichiers modulaires** : Vous pouvez diviser vos modèles en composants plus petits et réutilisables et les lier au moment du déploiement. Vous pouvez également imbriquer un modèle dans d’autres modèles.

* **Créez une ressource Azure** : Vous pouvez utiliser immédiatement les nouveaux services et fonctionnalités Azure dans les modèles. Dès qu’un fournisseur de ressources introduit de nouvelles ressources, vous pouvez les déployer par le biais de modèles. Vous n’avez pas besoin d’attendre la mise à jour des outils ou des modules avant d’utiliser les nouveaux services.

* **Déploiements suivis** : Dans le portail Azure, vous pouvez consulter l’historique des déploiements et obtenir des informations sur le déploiement des modèles. Vous pouvez voir le modèle qui a été déployé, les valeurs de paramètre passées et toutes les valeurs de sortie. Les autres services d’infrastructure en tant que code ne font pas l’objet d’un suivi par le biais du portail.

   ![Historique de déploiement](./media/template-deployment-overview/deployment-history.png)

* **Stratégie en tant que code** : [Azure Policy](../governance/policy/overview.md) est une stratégie en tant qu’infrastructure de code pour automatiser la gouvernance. Si vous utilisez des stratégies Azure, la correction des stratégies est effectuée sur des ressources non conformes quand elles sont déployées par le biais de modèles.

* **Blueprints de déploiement** : Vous pouvez tirer parti des [plans](../governance/blueprints/overview.md) fournis par Microsoft pour respecter les normes réglementaires et de conformité. Ces blueprints incluent des modèles prédéfinis pour différentes architectures.

* **Intégration CI/CD** : Vous pouvez intégrer des modèles dans vos outils d’intégration et de déploiement continus (CI/CD), qui peuvent automatiser vos pipelines de mise en production en vue d’effectuer des mises à jour rapides et fiables des applications et des infrastructures. À l’aide de la tâche de modèle Resource Manager et Azure DevOps, vous pouvez utiliser Azure Pipelines pour générer et déployer des projets de modèle Azure Resource Manager en continu. Pour en savoir plus, consultez [Projet VS avec des pipelines](./vs-resource-groups-project-devops-pipelines.md) et [Intégration continue avec Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md).

* **Code exportable** : Vous pouvez obtenir un modèle pour un groupe de ressources existant en exportant l’état actuel du groupe de ressources ou en consultant le modèle utilisé pour un déploiement particulier. L’affichage du [modèle exporté](export-template-portal.md) est un moyen utile pour en découvrir plus sur sa syntaxe.

* **Outils de création** : Vous pouvez créer des modèles avec [Visual Studio Code](resource-manager-tools-vs-code.md) et l’extension d’outil de modèle. Vous bénéficiez d’IntelliSense, de la mise en surbrillance de la syntaxe, de l’aide en ligne et de nombreuses autres fonctions de langage. Outre Visual Studio code, vous pouvez également utiliser [Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="template-file"></a>Fichier de modèle

Dans votre modèle, vous pouvez écrire des [expressions de modèle](template-expressions.md) qui étendent les fonctionnalités de JSON. Ces expressions utilisent les [fonctions](resource-group-template-functions.md) fournies par Resource Manager.

Le modèle comprend les sections suivantes :

* [Paramètres](template-parameters.md) - Pendant le déploiement, fournissez des valeurs qui permettent d’utiliser le même modèle avec différents environnements.

* [Variables](template-variables.md) - Définissez des valeurs qui sont réutilisées dans vos modèles. Elles peuvent être construites à partir de valeurs de paramètres.

* [Fonctions définies par l’utilisateur](template-user-defined-functions.md) - Créez des fonctions personnalisées qui simplifient votre modèle.

* [Ressources](resource-group-authoring-templates.md#resources) - Spécifiez les ressources à déployer.

* [Sorties](template-outputs.md) - Retournez des valeurs à partir des ressources déployées.

## <a name="template-deployment-process"></a>Processus de déploiement de modèle

Quand vous déployez un modèle, Resource Manager le convertit en opérations d’API REST. Par exemple, lorsque Resource Manager reçoit un modèle avec la définition de ressource suivante :

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Il convertit la définition en opération API REST suivante, qui est envoyée au fournisseur de ressources Microsoft.Storage :

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Conception de modèle

La manière dont vous définissez les modèles et les groupes de ressources dépend entièrement de vous et de la façon dont vous voulez gérer votre solution. Par exemple, vous pouvez déployer votre application à trois niveaux via un modèle unique pour un groupe de ressources unique.

![modèle à trois niveaux](./media/template-deployment-overview/3-tier-template.png)

Cependant, il est inutile de définir toute votre infrastructure dans un seul modèle. Il peut être judicieux de diviser les exigences de votre déploiement dans un ensemble de modèles ciblés destinés à un usage particulier. Vous pouvez facilement réutiliser ces modèles pour différentes solutions. Pour déployer une solution particulière, créez un modèle de référence qui relie tous les modèles requis. L’illustration suivante montre comment déployer une solution à trois niveaux via un modèle parent qui inclut trois modèles imbriqués.

![modèle à niveaux imbriqués](./media/template-deployment-overview/nested-tiers-template.png)

Si vos niveaux vont avoir des cycles de vie distincts, vous pouvez déployer les trois niveaux dans des groupes de ressources séparés. Notez que les ressources peuvent toujours être liées aux ressources dans d’autres groupes.

![modèle niveau](./media/template-deployment-overview/tier-templates.png)

Pour plus d’informations sur les modèles imbriqués, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un didacticiel pas à pas vous guidant tout au long du processus de création d’un modèle, consultez [Didacticiel : Créer et déployer votre premier modèle Azure Resource Manager](template-tutorial-create-first-template.md).
* Pour plus d’informations sur les propriétés de fichiers de modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur l’exportation de modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).
