---
title: Fichier include
description: Fichier include
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545516"
---
Dans la section **Gérer** (menu en haut à droite) de la page **Ressources Azure** (menu de gauche), copiez l’URL **Exemple de requête**, puis collez-le dans un nouvel onglet de navigateur.

L’URL du point de terminaison ressemble au format suivant, avec votre propre sous-domaine personnalisé, l’ID d’application et la même clé de point de terminaison qui remplacent APP-ID et KEY-ID :

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```