---
title: Publier une offre de module Azure IoT Edge | Place de marché Azure
description: Procédure de publication d’une offre de module IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813830"
---
# <a name="publish-iot-edge-module-offer"></a>Publier une offre de module IoT Edge

 Une fois que vous avez créé une offre en fournissant les informations correspondantes sur la page **Nouvelle offre**, vous pouvez publier cette offre. Sélectionnez **Publier** pour démarrer le processus de publication.

Le schéma ci-après illustre les principales étapes du processus de publication d’une offre.

![Procédure de publication d’une offre de module IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Description détaillée des étapes de publication

Le tableau ci-après décrit chacune des étapes de publication, avec une estimation de la durée (maximale) nécessaire à leur exécution.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Étape de publication**           | **Time**    | **Description**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Valider les prérequis         | 15 min   | Les informations de l’offre et les paramètres de l’offre sont validés.                        |
| Certification                  | 2 semaines | L’offre est analysée par l’équipe de certification d’Azure. Cette étape recherche des virus, des programmes malveillants ainsi que des problèmes de sécurité, et vérifie la conformité aux normes de sécurité. Elle vérifie également que cette offre de module IoT Edge remplit tous les critères d’éligibilité (consultez les articles décrivant les [prérequis](./cpp-prerequisites.md) et la [préparation de vos ressources techniques](./cpp-create-technical-assets.md)). Si un problème est détecté, des commentaires sont fournis. |
| Packaging | 1 heure  | Les ressources techniques de l’offre sont packagées de façon à être utilisées par le client, et les systèmes de prospection sont installés et configurés. |
|  Validation de l’éditeur             |  -        | Révision finale de l’éditeur et confirmation avant la mise en ligne de l’offre. Vous pouvez déployer votre offre dans les abonnements sélectionnés (dans les étapes d’informations de l’offre) pour vérifier qu’elle répond à toutes vos exigences.  Sélectionnez **Démarrer** pour faire passer votre offre à l’étape suivante. |
| Packaging                 | 1 heure | L’offre finalisée est répliquée dans les régions et systèmes de production de place de marché. | 
| En direct                           | 4 jours |L’offre est publiée, répliquée dans les régions requises et mise à la disposition du public. |

Allouez jusqu’à 10 jours ouvrables aux phases d’achèvement du processus de publication et de mise en production de l’offre. Une fois que vous avez terminé le processus de publication, votre offre de module IoT Edge est répertoriée dans [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre de module IoT Edge existante dans Place de marché Microsoft Azure](./cpp-update-existing-offer.md)
