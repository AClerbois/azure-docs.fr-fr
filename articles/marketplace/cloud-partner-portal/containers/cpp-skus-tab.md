---
title: Références (SKU) pour une image de conteneurs Azure | Place de marché Azure
description: Configurez des références SKU pour un conteneur Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6953329bfabe99fc4bb28f2494cb412ba9cbbba0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942893"
---
# <a name="container-skus-tab"></a>Onglet Références SKU de conteneur

L’onglet **Références** de la page **Nouvelle offre** permet de créer une ou plusieurs références SKU et de les associer à votre nouvelle offre.  Vous pouvez utiliser différentes références SKU pour distinguer une solution selon son ensemble de fonctionnalités, son modèle de facturation ou d’autres caractéristiques.

## <a name="sku-settings"></a>Paramètres de la référence SKU

Lorsque vous commencez à créer une offre, aucune référence SKU n’est associée à celle-ci. Pour créer une référence SKU, procédez comme suit :

1. Dans l’onglet Références SKU, sélectionnez **Nouvelle référence SKU**.

   ![Invite de nouvelle référence SKU](./media/containers-sku-settings.png)

2. Indiquez les informations demandées sur la référence SKU et le conteneur. Chaque référence SKU correspond à une image conteneur. Une référence SKU comprend deux parties :

    -   Métadonnées de référence SKU
    -   Métadonnées de conteneur


### <a name="sku-metadata"></a>Métadonnées de référence SKU

Les métadonnées de la référence SKU contiennent une description du conteneur qui lui servira de vitrine.

![Métadonnées de référence SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Métadonnées de conteneur

Les métadonnées du conteneur contiennent les informations de référence du référentiel d’image dans Azure Container Registry (ACR). La Place de marché Microsoft Azure copie cette image dans un registre public spécifique à la Place de marché, puis la met à la disposition des clients après la certification. Toutes les requêtes émanant de l'utilisateur Azure et visant à utiliser une image de conteneur de la Place de marché Microsoft Azure sont traitées à partir du registre public de la Place de marché, et non d'ACR.

![Métadonnées de conteneur](./media/containers-image-repository.png)
    
Le **détails du dépôt Image** dans l’écran précédent capture contient les champs suivants.  Les champs obligatoires sont indiqués par un astérisque (*).

-   **ID d’abonnement\***  -ID d’abonnement Azure où se trouve l’ACR.
-   **Nom de groupe de ressources\***  -le nom du groupe de ressources de l’ACR.
-   **Nom de Registre\***  -nom de l’ACR.
-   **Nom du référentiel\***  -le nom du référentiel. Une fois le nom défini, sa valeur n’est plus modifiable. Utilisez un nom unique pour éviter tout conflit avec les autres offres de votre compte.
-   **Nom d’utilisateur\***  -le nom d’utilisateur (nom d’utilisateur administrateur) associé à l’image ACR.
-   **Mot de passe\***  -mot de passe associé à l’image ACR.

    >[!NOTE]
    >Le nom d’utilisateur et le mot de passe sont requis pour s’assurer que les partenaires ont accès au registre ACR décrit dans le processus de publication.


### <a name="image-version"></a>Version de l’image

Lorsque vous publiez une image conteneur, vous pouvez fournir une ou plusieurs balises d’image et synthèses SHA.

**Balise d’image\* ou Digest**
 
- Cette balise ou synthèse doit comporter une balise `latest` et une balise de version (par exemple, à partir de `xx.xx.xx-`, où xx est un nombre). Il doit y avoir des [balises de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger. 
- Vous pouvez ajouter plusieurs versions d’un conteneur à l’aide de balises. Toutes les balises de manifeste (sauf `latest`) doivent commencer par `X.Y-` ou `X.Y.Z-`, où X, Y et Z sont des entiers. <br/> Par exemple, si une balise `latest` pointe vers `1.0.1-linux-x64`, `1.0.1-linux-arm32` et `1.0.1-windows-arm32`, ces balises doivent être ajoutées ici.

>[!NOTE]
>Pensez à ajouter une **balise de test** à votre image pour pouvoir identifier l’image pendant les tests.


## <a name="next-steps"></a>Étapes suivantes

Utilisez [l’onglet Place de marché](./cpp-marketplace-tab.md) pour créer une description de la Place de marché pour votre offre. 
