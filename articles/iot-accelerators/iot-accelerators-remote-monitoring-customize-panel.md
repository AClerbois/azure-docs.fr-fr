---
title: Ajouter un panneau à la surveillance à distance solution de l’interface utilisateur - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter un nouveau panneau au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447061"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter un panneau personnalisé au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance

Cet article vous montre comment ajouter un nouveau panneau à une page du tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article aborde les points suivants :

- Comment préparer un environnement de développement local.
- Comment ajouter un nouveau panneau à une page du tableau de bord dans l’interface utilisateur web.

L’exemple de panneau dans cet article s’affiche dans la page du tableau de bord existante.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>Avant de commencer

Vous devez effectuer les étapes décrites dans l’article [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md) avant de continuer.

## <a name="add-a-panel"></a>Ajouter un panneau

Pour ajouter un panneau à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent le panneau, puis modifier le tableau de bord pour afficher le panneau.

### <a name="add-the-new-files-that-define-the-panel"></a>Ajouter les nouveaux fichiers qui définissent le panneau

Pour vous aider à démarrer, le dossier **src/walkthrough/components/pages/dashboard/panels/examplePanel** contient les fichiers qui définissent un panneau, notamment :

**examplePanel.js**


Copiez le dossier **src/walkthrough/components/pages/dashboard/panels/examplePanel** dans le dossier **src/components/pages/dashboard/panels**.

Ajoutez l’exportation suivante au fichier **src/walkthrough/components/pages/dashboard/panels/index.js** :

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Ajouter le panneau au tableau de bord

Modifiez le fichier **src/components/pages/dashboard/dashboard.js** pour ajouter le panneau.

Ajoutez l’exemple de panneau à la liste des importations à partir de panneaux :

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Ajoutez la définition de cellule suivante à la grille dans le contenu de la page :

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Tester le menu volant

Si l’interface utilisateur web n’est pas déjà en cours d’exécution localement, exécutez la commande suivante à la racine de votre copie locale du référentiel :

```cmd/sh
npm start
```

La commande précédente exécute l’interface utilisateur localement sur [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Accédez à la page **Tableau de bord** pour afficher le nouveau panneau.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour ajouter ou personnaliser des tableaux de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).
