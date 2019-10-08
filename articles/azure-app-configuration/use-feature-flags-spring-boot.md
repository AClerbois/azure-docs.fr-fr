---
title: Tutoriel sur l’utilisation des indicateurs de fonctionnalités dans une application Spring Boot - Azure App Configuration | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment implémenter des indicateurs de fonctionnalités dans des applications Spring Boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687199"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Tutoriel : Utiliser des indicateurs de fonctionnalités dans une application Spring Boot

Les bibliothèques de gestion des fonctionnalités Spring Boot Core fournissent une prise en charge de l’implémentation des indicateurs de fonctionnalités dans une application Spring Boot. Ces bibliothèques vous permettent d’ajouter des indicateurs de fonctionnalités à votre code de façon déclarative.

Les bibliothèques de gestion des fonctionnalités gèrent également les cycles de vie des indicateurs de fonctionnalités en arrière-plan. Par exemple, elles actualisent et mettent en cache l’état des indicateurs ou garantissent qu’un état d’indicateur est immuable pendant un appel de demande. De plus, la bibliothèque Spring Boot offre des intégrations, notamment des intergiciels (middleware), des routes et des actions de contrôleur MVC.

L’article [Démarrage rapide : Ajouter des indicateurs de fonctionnalités à une application Spring Boot](./quickstart-feature-flag-spring-boot.md) montre plusieurs façons d’ajouter des indicateurs de fonctionnalités à une application Spring Boot. Ce tutoriel explique plus en détails ces différentes méthodes.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter des indicateurs de fonctionnalités dans des parties clés de votre application afin de contrôler la disponibilité des fonctionnalités
> * Effectuer une intégration à App Configuration quand vous l’utilisez pour gérer les indicateurs de fonctionnalités.

## <a name="set-up-feature-management"></a>Configurer la gestion des fonctionnalités

Le gestionnaire de fonctionnalités Spring Boot `FeatureManager` obtient les indicateurs de fonctionnalités à partir du système de configuration natif du framework. Ainsi, vous pouvez définir les indicateurs de fonctionnalités de votre application à l’aide de n’importe quelle source de configuration prise en charge par Spring Boot, notamment le fichier *bootstrap.yml* local ou des variables d’environnement. `FeatureManager` s’appuie sur l’injection de dépendances. Vous pouvez inscrire les services de gestion des fonctionnalités à l’aide de conventions standard :

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Nous vous recommandons de conserver les indicateurs de fonctionnalités en dehors de l’application et de les gérer séparément. Cela vous permet de modifier l’état des indicateurs à tout moment, et d’appliquer immédiatement ces changements dans l’application. Le service App Configuration fournit un emplacement centralisé pour organiser et contrôler tous vos indicateurs de fonctionnalités par le biais d’une interface utilisateur de portail dédiée. Il remet également les indicateurs à votre application directement par le biais de ses bibliothèques de client Spring Boot.

Le moyen le plus simple de connecter votre application Spring Boot à App Configuration est de passer par le fournisseur de configuration :

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Déclaration d’indicateur de fonctionnalité

Chaque indicateur de fonctionnalité comporte deux parties : un nom et une liste d’un ou plusieurs filtres qui servent à évaluer si l’état d’une fonctionnalité est *on* (autrement dit, quand sa valeur est `True`). Un filtre définit un cas d’usage pour le moment où une fonctionnalité doit être activée.

Si un indicateur de fonctionnalité a plusieurs filtres, la liste des filtres est parcourue dans l’ordre jusqu’à ce que l’un d’eux détermine que la fonctionnalité doit être activée. À ce stade, l’indicateur de fonctionnalité est *on* (activée) et les résultats du filtrage restants sont ignorés. Si aucun filtre n’indique que la fonctionnalité doit être activée, l’indicateur de fonctionnalité est *off* (désactivée).

Le gestionnaire de fonctionnalités prend en charge *application.yml* comme source de configuration pour les indicateurs de fonctionnalités. L’exemple suivant montre comment configurer des indicateurs de fonctionnalités dans un fichier YAML :

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Par convention, la section `feature-management` de ce document YAML est utilisée pour les paramètres des indicateurs de fonctionnalités. L’exemple précédent montre trois indicateurs de fonctionnalités avec leurs filtres définis dans la propriété `EnabledFor` :

* `FeatureA` est défini sur *on*.
* `FeatureB` est défini sur *off*.
* `FeatureC` spécifie un filtre nommé `Percentage` avec une propriété `Parameters`. `Percentage` est un filtre configurable. Dans cet exemple, `Percentage` spécifie une probabilité de 50 % que l’indicateur `FeatureC` soit défini sur *on*.

## <a name="feature-flag-checks"></a>Vérifications des indicateurs de fonctionnalités

Le modèle de gestion des fonctionnalités de base consiste à d’abord vérifier si un indicateur de fonctionnalité est défini sur *on*. Si c’est le cas, le gestionnaire de fonctionnalités exécute les actions que la fonctionnalité contient. Par exemple :

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injection de dépendances

Dans Spring Boot, vous pouvez accéder au gestionnaire de fonctionnalités `FeatureManager` par le biais de l’injection de dépendances :

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Actions de contrôleur

Dans les contrôleurs MVC, vous utilisez un attribut `@FeatureGate` pour déterminer si une action spécifique est activée. L’action `Index` suivante exige que `FeatureA` soit défini sur *on* pour pouvoir être exécutée :

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Quand une action ou un contrôleur MVC est bloqué car l’indicateur de fonctionnalité de contrôle est défini sur *off*, une interface `IDisabledFeaturesHandler` inscrite est appelée. L’interface `IDisabledFeaturesHandler` par défaut retourne un code d’état 404 au client sans corps de réponse.

## <a name="mvc-filters"></a>Filtres MVC

Vous pouvez configurer des filtres MVC de sorte que leur activation soit basée sur l’état d’un indicateur de fonctionnalité. Le code suivant ajoute un filtre MVC nommé `FeatureFlagFilter`. Ce filtre est déclenché dans le pipeline MVC seulement si `FeatureA` est activé.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Itinéraires

Vous pouvez utiliser des indicateurs de fonctionnalités pour rediriger des routes. Le code suivant redirige un utilisateur à partir de `FeatureA` :

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment implémenter des indicateurs de fonctionnalités dans votre application Spring Boot en utilisant les bibliothèques `spring-cloud-azure-feature-management-web`. Pour plus d’informations sur la prise en charge de la gestion des fonctionnalités dans Spring Boot et App Configuration, consultez les ressources suivantes :

* [Exemple de code d’indicateur de fonctionnalité Spring Boot](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md)
