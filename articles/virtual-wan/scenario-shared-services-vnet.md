---
title: 'Scénario : Router vers des réseaux virtuels de services partagés'
titleSuffix: Azure Virtual WAN
description: 'Scénarios de routage : configurez des routes pour accéder à un réseau virtuel de service partagé avec une charge de travail à laquelle vous souhaitez que chaque réseau virtuel et chaque branche aient accès.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c72ff14246f3f96980fe1de2fdef16794bf23e4e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399988"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scénario : Router vers des réseaux virtuels de services partagés

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario, l’objectif est de configurer des routes pour accéder à un réseau virtuel de **service partagé** avec des charges de travail auxquelles vous souhaitez que chaque réseau virtuel et chaque branche (VPN/ER/P2S) aient accès. Les exemples de ces charges de travail partagées peuvent inclure des machines virtuelles avec des services tels que des contrôleurs de domaine ou des partages de fichiers, ou des services Azure exposés en interne via des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md).

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Conception

Nous pouvons utiliser une matrice de connectivité pour résumer les exigences de ce scénario. Dans la matrice, chaque cellule indique si une connexion de Virtual WAN (côté « De » du flux, les en-têtes de lignes dans la table) apprend un préfixe de destination (côté « À » du flux, en-têtes de colonne en italique dans la table) pour un flux de trafic spécifique. Un « X » signifie que la connectivité est assurée par Virtual WAN :

**Matrice de connectivité**

| Du             | Par :   |*Réseaux virtuels isolés*|*Réseau virtuel partagé*|*Branches*|
|---|---|---|---|---|
|**Réseaux virtuels isolés**|&#8594;|                |        X        |       X      |
|**Réseaux virtuels partagé**  |&#8594;|       X        |        X        |       X      |
|**Branches**      |&#8594;|       X        |        X        |       X      |

À l’instar du [scénario de réseau virtuel isolé](scenario-isolate-vnets.md), cette matrice de connectivité nous offre deux modèles de ligne différents, qui sont convertis en deux tables de routage (les réseaux virtuels de services partagés et les branches ont les mêmes exigences de connectivité). Virtual WAN possède déjà une table de routage par défaut. Nous aurons donc besoin d’une autre table de routage personnalisée, que nous appellerons **RT_SHARED** dans cet exemple.

Des réseaux virtuels seront associés à la table de routage **RT_SHARED**. Étant donné qu’ils ont besoin d’une connectivité aux branches et aux réseaux virtuels de services partagés, le réseau virtuel et les branches du service partagé doivent se propager à **RT_SHARED** (sinon, le réseaux virtuels n’apprendront pas les préfixes de branche et de réseau virtuel partagé). Étant donné que les branches sont toujours associées à la table de routage par défaut et que les exigences de connectivité sont les mêmes pour les réseaux virtuels de services partagés, nous allons également associer les réseaux virtuels de services partagés à la table de routage par défaut.

Voici donc la conception finale :

* Réseaux virtuels isolés :
  * Table de routage associée : **RT_SHARED**
  * Propagation aux tables de routage : **Par défaut**
* Réseaux virtuels de services partagés :
  * Table de routage associée : **Par défaut**
  * Propagation aux tables de routage : **RT_SHARED** et **Par défaut**
* Branches :
  * Table de routage associée : **Par défaut**
  * Propagation aux tables de routage : **RT_SHARED** et **Par défaut**

> [!NOTE]
> Si votre Virtual WAN est déployé sur plusieurs régions, vous devez créer la table de routage **RT_SHARED** dans chaque hub, et les routes à partir de chaque réseau virtuel de services partagés et connexion de branche doivent être propagés aux tables de routage dans chaque hub virtuel à l’aide des étiquettes de propagation.

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Flux de travail

Pour configurer le scénario, effectuez les étapes suivantes :

1. Identifiez le réseau virtuel des **services partagés**.
2. Créez une table de routage personnalisée. Dans l’exemple, la table de routage est nommée **RT_SHARED**. Pour connaître la procédure de création d’une table de routage, consultez le [Guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md). Utilisez les valeurs suivantes comme repère :

   * **Association**
     * Pour les **réseaux virtuels, *à l’exception* du réseau virtuel de services partagés**, sélectionnez les réseaux virtuels à isoler. Cela implique que tous ces réseaux virtuels (à l’exception du réseau virtuel de services partagés) sont en mesure d’atteindre la destination en fonction des routes de la table de routage RT_SHARED.

   * **Propagation**
      * Pour les **branches**, propagez les routes vers cette table de routage, en plus des autres tables de routage que vous avez déjà sélectionnées. En raison de cette étape, la table de routage RT_SHARED apprend les routes de toutes les connexions de branche (VPN/ER/VPN utilisateur).
      * Pour les **réseaux virtuels**, sélectionnez le **réseau virtuel des services partagés**. Grâce à cette étape, la table de routage RT_SHARED apprend les routes de la connexion au réseau virtuel de services partagés.

Cela entraîne la configuration de routage indiquée dans la figure suivante :

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Réseau virtuel des services partagés" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
