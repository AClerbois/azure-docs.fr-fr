---
title: Configurer un modèle d’appareil dans une application Azure IoT Central | Microsoft Docs
description: Découvrez comment configurer un modèle d’appareil avec des mesures, des paramètres, des propriétés, des règles et un tableau de bord.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e3925f2d6c2a1034cc96475721de7936deeb4a9d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894413"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Configurer et gérer un modèle d’appareil (fonctionnalités de préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Un modèle d’appareil est un blueprint qui définit les caractéristiques et les comportements d’un type d’appareil se connectant à une application Azure IoT Central.

Par exemple, un concepteur peut créer un modèle d’appareil pour un ventilateur connecté qui présente les caractéristiques suivantes :

- Envoie des données de télémétrie de température
- Envoie la propriété de l’emplacement
- Envoie les événements d’erreur du moteur du ventilateur
- Envoie l’état de fonctionnement du ventilateur
- Propriété de vitesse du ventilateur inscriptible
- Commande pour redémarrer l’appareil
- Un tableau de bord qui vous donne une vision globale de l’appareil

À partir de ce modèle d’appareil, un opérateur peut créer et connecter des appareils qui sont des ventilateurs réels. Tous ces ventilateurs ont des mesures, des propriétés et des commandes que les opérateurs utilisent pour les surveiller et les gérer. Les opérateurs utilisent les tableaux de bord et les formulaires des appareils pour interagir avec les ventilateurs.

> [!NOTE]
> Seuls les concepteurs et les administrateurs peuvent créer, modifier et supprimer des modèles d’appareils. Tous les utilisateurs peuvent créer des appareils sur la page **Appareils** à partir de modèles d’appareils existants.

[IoT Plug-and-Play](../../iot-pnp/overview-iot-plug-and-play.md) permet à IoT Central d’intégrer des appareils sans que vous n’ayez à écrire de code d’appareil incorporé. Au cœur d’IoT Plug-and-Play est un schéma de modèle de fonctionnalité d’appareil qui décrit les fonctionnalités de l’appareil. Dans une application IoT Central en préversion, les modèles d’appareil utilisent ces modèles de fonctionnalité d’appareil IoT Plug-and-Play.

Plusieurs choix s’offrent à vous pour créer des modèles d’appareil :

- Concevez le modèle d’appareil dans IoT Central, puis implémentez son modèle de fonctionnalité d’appareil dans le code de votre appareil.
- Importez un modèle de fonctionnalité d’appareil à partir du [catalogue d’appareils Azure Certified pour IoT](https://aka.ms/iotdevcat), puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle. Importez manuellement le modèle de fonctionnalité d’appareil dans votre application IoT Central, puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle et connectez votre appareil réel à votre application IoT Central à l’aide d’une première connexion à l’appareil. IoT Central recherche et importe pour vous le modèle de capacité d’appareil à partir du référentiel public. Vous pouvez ensuite ajouter des propriétés de cloud, des personnalisations et des tableaux de bord dont votre application IoT Central a besoin pour le modèle d’appareil.

## <a name="create-a-device-template-from-the-device-catalog"></a>Créer un modèle d’appareil à partir du catalogue d’appareils

Vous pouvez rapidement commencer à créer votre solution à l’aide d’un appareil IOT Plug-and-Play certifié répertorié dans le [catalogue d’appareils Azure IOT](https://catalog.azureiotsolutions.com/alldevices). IoT Central s’intègre au catalogue d’appareils pour vous permettre d’importer un modèle de capacité d’appareil parmi les appareils IoT Plug-and-Play certifiés. Pour créer un modèle d’appareil à partir de l’un de ces appareils dans IoT Central :

1. Accédez à la page **Modèles d’appareil** dans votre application IoT Central.
1. Sélectionnez **+ Nouveau**, puis sélectionnez les appareils certifiés IoT Plug-and-Play dans le catalogue répertorié ci-dessous. IoT Central crée un modèle d’appareil basé sur ce modèle de capacité d’appareil.
1. Ajoutez des propriétés de Cloud, des personnalisations ou des vues à votre modèle d’appareil.
1. Sélectionnez **Publier** pour publier ce modèle d’appareil et le rendre disponible afin que les opérateurs affichent et connectent des appareils.

## <a name="create-a-device-template-from-scratch"></a>Créer un modèle d’appareil en partant de zéro

Un modèle d’appareil contient :

- Un _modèle de capacité d’appareil_ qui spécifie les données de télémétrie, les propriétés et les commandes implémentées par l’appareil. Ces capacités de l'appareil sont organisées en une ou plusieurs interfaces.
- Des _propriétés de cloud_ qui définissent les informations sur vos appareils stockées par votre application IOT Central. Par exemple, une propriété de cloud peut enregistrer les données concernant la date de la dernière maintenance d’un appareil. Ces informations ne sont jamais partagées avec l’appareil.
- Les _personnalisations_ permettent au concepteur de remplacer certaines des définitions du modèle de capacité d’appareil. Par exemple, le concepteur peut remplacer le nom d’une propriété de l’appareil. Les noms de propriétés s’affichent dans les tableaux de bord et les formulaires IoT Central.
- _Les tableaux de bord et les formulaires_ permettent au concepteur de créer une interface utilisateur qui permet aux opérateurs de surveiller et de gérer les appareils connectés à votre application.

Pour créer un modèle d’appareil dans IoT Central :

1. Accédez à la page **Modèles d’appareil** dans votre application IoT Central.
1. Sélectionnez **+ Nouveau**, puis **Personnalisé**.
1. Entrez un nom pour votre modèle, par exemple **Capteur d’environnement**.
1. Appuyez sur **Entrée**. IoT Central crée un modèle d’appareil vide.

## <a name="manage-a-device-template"></a>Gérer un modèle d’appareil

Vous pouvez renommer ou supprimer un modèle à partir de la page d’hébergement du modèle.

Après avoir ajouté un modèle de capacité d’appareil à votre modèle, vous pouvez le publier. Vous ne pouvez pas connecter un appareil basé sur ce modèle de telle manière que vos opérateurs le voient sur la page **Appareils** avant d’avoir publié le modèle.

## <a name="create-a-capability-model"></a>Créer un modèle de capacité

Pour créer un modèle de capacité d’appareil, vous pouvez :

- Utilisez IoT Central pour créer un modèle personnalisé en partant de zéro.
- Importez un modèle à partir d’un fichier JSON. Un concepteur d’appareils peut avoir utilisé Visual Studio Code pour créer un modèle de capacité de l’appareil pour votre application.
- Sélectionnez l’un des appareils dans le catalogue d’appareils. Cette option importe le modèle de capacité de l’appareil que le fabricant a publié pour cet appareil. Un modèle de capacité d’appareil importé de cette façon est automatiquement publié.

## <a name="manage-a-capability-model"></a>Gérer un modèle de capacité

Après avoir créé un modèle de capacité d’appareil, vous pouvez effectuer les opérations suivantes :

- Ajouter des interfaces au modèle. Un modèle doit avoir au moins une interface.
- Modifier les métadonnées du modèle, telles que son ID, son espace de noms et son nom.
- Supprimer le modèle.

## <a name="create-an-interface"></a>Créer une interface

Une capacité d’appareil doit avoir au moins une interface. Une interface est une collection réutilisable de fonctionnalités.

Pour créer une interface :

1. Accédez à votre modèle de capacité d’appareil et choisissez **+ Ajouter une interface**.

1. Sur la page **Sélectionner une interface**, vous pouvez effectuer les opérations suivantes :

    - Créer un connecteur personnalisé en partant de zéro.
    - Importer une interface existante à partir d’un fichier. Un concepteur d’appareils peut avoir utilisé Visual Studio Code pour créer une interface pour votre appareil.
    - Choisissez l’une des interfaces standard, telles que l’interface **Informations sur l’appareil**. Les interfaces standard spécifient les fonctionnalités communes à de nombreux appareils. Ces interfaces standard sont publiées par Microsoft Azure IoT et ne peuvent ni faire l’objet d’un contrôle de version ni être modifiées.

1. Après avoir créé une interface, choisissez **Modifier l'identité** pour modifier le nom d’affichage de l’interface.

1. Si vous choisissez de créer une interface personnalisée en partant de zéro, vous pouvez ajouter les fonctionnalités de votre appareil. Les fonctionnalités de l’appareil sont les données de télémétrie, les propriétés et les commandes.

### <a name="telemetry"></a>Télémétrie

Les données de télémétrie sont un flux de valeurs envoyées à partir de l’appareil, généralement par un capteur. Par exemple, un capteur peut indiquer la température ambiante.

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de données de télémétrie :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur des données de télémétrie utilisée sur les tableaux de bord et les formulaires. |
| Nom | Nom du champ dans le message de données de télémétrie. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. |
| Type de fonctionnalité | Données de télémétrie. |
| Type de sémantique | Type sémantique des données de télémétrie telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| Schéma | Type de données de télémétrie, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. Le schéma n’est pas disponible pour les types sémantiques d’événement et d’état. |
| severity | Disponible uniquement pour le type sémantique d’événement. **Erreur**, **Information**ou **Avertissement**. |
| Valeurs d’état | Disponible uniquement pour le type sémantique d’état. Définissez les valeurs d’état possibles, chacune ayant un nom d’affichage, un nom, un type d’énumération et une valeur. |
| Unité | Unité pour la valeur des données de télémétrie telles que **mph**, **%** ou **&deg;C**. |
| Unités d'affichage | Unité d’affichage à utiliser sur les tableaux de bord et les formulaires. |
| Commentaire | Commentaires sur la fonctionnalité de données de télémétrie. |
| Description | Description de la fonctionnalité de données de télémétrie. |

### <a name="properties"></a>properties

Les propriétés représentent des valeurs à un moment donné. Par exemple, un appareil peut utiliser une propriété pour indiquer la température cible qu’il tente d’atteindre. Vous pouvez définir des propriétés inscriptibles à partir d’IoT Central.

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de données de propriété :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur de propriété utilisée sur les tableaux de bord et les formulaires. |
| Nom | Nom de la propriété. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. |
| Type de fonctionnalité | Propriété. |
| Type sémantique | Type sémantique de la propriété telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| Schéma | Type de données de propriété, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. Le schéma n’est pas disponible pour les types sémantiques d’événement et d’état. |
| Inscriptible | Si la propriété n’est pas inscriptible, l’appareil peut signaler des valeurs de propriété à IoT Central. Si la propriété est inscriptible, l’appareil peut signaler des valeurs de propriétés à IoT Central, et IoT Central peut envoyer des mises à jour de propriétés à l’appareil.
| severity | Disponible uniquement pour le type sémantique d’événement. **Erreur**, **Information**ou **Avertissement**. |
| Valeurs d’état | Disponible uniquement pour le type sémantique d’état. Définissez les valeurs d’état possibles, chacune ayant un nom d’affichage, un nom, un type d’énumération et une valeur. |
| Unité | Unité pour la valeur des propriétés telles que **mph**, **%** ou **&deg;C**. |
| Unités d'affichage | Unité d’affichage à utiliser sur les tableaux de bord et les formulaires. |
| Commentaire | Commentaires sur la fonctionnalité de propriété. |
| Description | Description de la fonctionnalité de propriété. |

### <a name="commands"></a>Commandes

Vous pouvez appeler des commandes d’appareil à partir d’IoT Central. Les commandes transmettent éventuellement des paramètres à l’appareil et reçoivent une réponse de ce dernier. Par exemple, vous pouvez appeler une commande pour redémarrer un appareil en 10 secondes.

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de commande :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom d’affichage de la commande utilisée sur les tableaux de bord et les formulaires. |
| Nom | Nom de la commande. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. |
| Type de fonctionnalité | Commande |
| Commande | SynchronousExecutionType. |
| Commentaire | Commentaires sur la fonctionnalité de commande. |
| Description | Description de la fonctionnalité de commande. |
| Requête | Si cette option est activée, il s’agit d’une définition du paramètre de requête, notamment le nom, le nom d’affichage, le schéma, l’unité et l’unité d’affichage. |
| response | Si cette option est activée, il s’agit d’une définition de la réponse de commande, notamment le nom, le nom d’affichage, le schéma, l’unité et l’unité d’affichage. |

## <a name="manage-an-interface"></a>Gérer une interface

Si vous n’avez pas publié l’interface, vous pouvez modifier les fonctionnalités définies par l’interface. Une fois l’interface publiée, vous devez créer une nouvelle version du modèle d’appareil et une version de l’interface pour apporter des modifications. Les modifications qui ne nécessitent pas de contrôle de version, telles que des noms d’affichage ou des unités, peuvent être effectuées dans la section **Personnaliser**.

Vous pouvez également exporter l’interface en tant que fichier JSON si vous souhaitez la réutiliser dans un autre modèle de capacité.

## <a name="add-cloud-properties"></a>Ajouter des propriétés du cloud

Utilisez les propriétés de cloud pour stocker des informations sur les appareils dans IoT Central. Les propriétés de cloud ne sont jamais envoyées à un appareil. Par exemple, vous pouvez utiliser les propriétés de cloud pour stocker le nom du client qui a installé l’appareil ou la date de la dernière maintenance de l’appareil.

Le tableau suivant illustre les paramètres de configuration d’une propriété de cloud :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur de propriété de cloud utilisée sur les tableaux de bord et les formulaires. |
| Nom | Nom de la propriété de cloud. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. |
| Type sémantique | Type sémantique de la propriété telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| Schéma | Type de données de propriété de cloud, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. |

## <a name="add-customizations"></a>Ajouter des personnalisations

Utilisez des personnalisations quand vous devez modifier une interface importée ou ajouter des fonctionnalités propres à IoT Central à une capacité. Vous pouvez uniquement personnaliser les champs qui n’interrompent pas la compatibilité de l’interface. Vous pouvez par exemple :

- Personnaliser le nom complet et les unités d’une capacité.
- Ajouter une couleur par défaut à utiliser quand la valeur apparaît sur un graphique.
- Spécifier les valeurs initiales, minimales et maximales d’une propriété.

Vous ne pouvez pas personnaliser le nom ou le type de la capacité. Si vous ne pouvez pas apporter certaines modifications dans la section **Personnaliser**, vous devez contrôler la version de votre modèle d’appareil et de votre interface pour modifier la capacité.

### <a name="generate-default-views"></a>Générer des vues par défaut

La génération de vues par défaut permet de visualiser rapidement les informations importantes de votre appareil. Vous avez jusqu’à trois vues par défaut générées pour votre modèle d’appareil :

1. **Commandes** fournit une vue avec des commandes d’appareil et permet à votre opérateur de les répartir sur votre appareil.
1. **Vue d’ensemble** fournit une vue avec les données de télémétrie de l’appareil, qui montre les graphiques et les métriques.
1. **À propos de** fournit une vue avec les informations sur l’appareil, qui montre les propriétés de l’appareil.

Une fois que vous avez sélectionné **Générer des vues par défaut**, elles sont ajoutées automatiquement sous la section **Vues** de votre modèle d’appareil.

## <a name="add-dashboards"></a>Ajouter des tableaux de bord

Ajoutez des tableaux de bord à un modèle d’appareil pour permettre aux opérateurs de visualiser un appareil à l’aide de graphiques et de métriques. Vous pouvez avoir plusieurs tableaux de bord pour un modèle d’appareil.

Pour ajouter un tableau de bord à un modèle d’appareil :

1. Accédez à votre modèle d’appareil et sélectionnez **Vues**.
1. Choisissez ensuite **Visualisation de l'appareil**.
1. Entrez un nom pour votre tableau de bord dans **Nom du tableau de bord**.
1. Ajoutez des vignettes à votre tableau de bord à partir de la liste des vignettes statiques, des propriétés, des propriétés de cloud, des données de télémétrie et des commandes. Faites glisser et déplacez les vignettes que vous souhaitez ajouter à votre tableau de bord.
1. Pour tracer plusieurs valeurs de données de télémétrie sur une seule vignette de graphique, sélectionnez les valeurs de données de télémétrie, puis sélectionnez **Combiner**.
1. Configurez chaque vignette que vous ajoutez pour personnaliser le mode d’affichage des données en sélectionnant l’icône d'engrenage ou en sélectionnant le bouton **Modifier la configuration** sur la vignette de votre graphique.
1. Réorganisez et redimensionnez les vignettes sur votre tableau de bord.
1. Enregistrez les modifications.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurer l’aperçu de l’appareil pour afficher le tableau de bord

Pour afficher et tester votre tableau de bord, vous pouvez sélectionner **Configurer l’aperçu de l’appareil**, ce qui vous permet de voir le tableau de bord que votre opérateur voit une fois qu’il a été publié. Cette option vous permet de vérifier que vos vues affichent les données correctes. Vous pouvez choisir n’importe quel aperçu de l’appareil, le véritable appareil de test que vous avez configuré pour votre modèle d’appareil ou un appareil existant dans votre application à l’aide de l’ID d’appareil.

## <a name="add-forms"></a>Ajouter des formulaires

Ajoutez des formulaires à un modèle d’appareil pour permettre aux opérateurs de gérer un appareil en affichant et en définissant des propriétés. Les opérateurs peuvent uniquement modifier des propriétés de cloud et des propriétés d’appareil inscriptibles. Vous pouvez avoir plusieurs formulaires pour un modèle d’appareil.

Pour ajouter un formulaire à un modèle d’appareil :

1. Accédez à votre modèle d’appareil et sélectionnez **Vues**.
1. Choisissez ensuite **Modification des données de l'appareil et du cloud**.
1. Entrez un nom pour votre formulaire dans **Nom du formulaire**.
1. Sélectionnez le nombre de colonnes à utiliser pour disposer votre formulaire.
1. Ajoutez des propriétés à une section existante de votre formulaire, ou sélectionnez des propriétés et choisissez **Ajouter une section**. Utilisez des sections pour regrouper les propriétés sur votre formulaire. Vous pouvez ajouter un titre à une section.
1. Configurez chaque propriété du formulaire pour personnaliser son comportement.
1. Disposez les propriétés sur votre formulaire.
1. Enregistrez les modifications.

## <a name="publish-a-device-template"></a>Publier un modèle d’appareil

Avant de pouvoir connecter un appareil qui implémente votre modèle de capacité d’appareil, vous devez publier votre modèle d’appareil.

Après avoir publié un modèle d’appareil, vous ne pouvez apporter que des modifications limitées au modèle de capacité de l’appareil. Pour modifier une interface, vous devez [créer et publier une nouvelle version](./howto-version-device-template.md).

Pour publier un modèle d’appareil, accédez à votre modèle d’appareil et sélectionnez **Publier**.

Une fois que vous avez publié un modèle d’appareil, un opérateur peut accéder à la page **Appareils** et ajouter des appareils, réels ou simulés, qui utilisent votre modèle d’appareil. Vous pouvez continuer à modifier et à enregistrer votre modèle d’appareil au fur et à mesure que vous apportez des modifications. Toutefois, lorsque vous souhaitez envoyer ces modifications à l'opérateur pour qu’elles s’affichent dans la page **Appareils**, vous devez sélectionner **Publier** à chaque fois.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer un modèle d’appareil dans votre application Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Créer une nouvelle version de modèle d’appareil](howto-version-device-template.md)
> [Connecter un appareil MXChip IoT DevKit à votre application Azure IoT Central](howto-connect-devkit.md)
