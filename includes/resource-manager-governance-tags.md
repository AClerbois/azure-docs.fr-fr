---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 69c67f437d2f0b7bd6c1f5311eb5ba1d962d889a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
ms.locfileid: "29957310"
---
Vous appliquez des balises à vos ressources Azure pour les organiser de façon logique par catégories. Chaque balise se compose d’un nom et d’une valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Une fois que vous avez appliqué une balise, vous pouvez utiliser son nom et sa valeur pour récupérer toutes les ressources dans votre abonnement. Les balises vous permettent de récupérer les ressources associées de groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Les limites suivantes s’appliquent aux balises :

* Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires nom/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires nom/valeur de balise. Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à un seul nom de balise. Cet article présente un exemple d’affectation d’une chaîne JSON à la balise.
* Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères. Pour les comptes de stockage, le nom de balise est limité à 128 caractères, et la valeur de balise à 256 caractères.
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.
* Les balises ne peuvent pas être appliquées à des ressources classiques comme les Services cloud.
* Les noms de balise ne peuvent pas contenir ces caractères : `<`, `>`, `%`, `&`, `\`, `?`, `/`
