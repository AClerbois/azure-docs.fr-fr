---
title: Résidence des données
description: Résidence des données et informations sur les serveurs avec Azure Arc.
ms.topic: reference
ms.date: 09/02/2020
ms.custom: references_regions
ms.openlocfilehash: 8b4b8171bd7133e52928a5227c488bd6234ce686
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908121"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Serveurs avec Azure Arc : Résidence des données

Cet article explique le concept de résidence des données et comment il s’applique aux serveurs avec Azure Arc.

Les serveurs avec Azure Arc sont **[disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** dans les régions **États-Unis, Europe ou Asie-Pacifique**.

## <a name="data-residency"></a>Résidence des données

Les serveurs avec Azure Arc stockent les paramètres de configuration d’[extension de machine virtuelle Azure](manage-vm-extensions.md) (autrement dit, les valeurs de propriétés) qui doivent être spécifiés pour l’extension avant toute tentative d’activation sur la machine connectée. Par exemple, lorsque vous activez l’extension de machine virtuelle Log Analytics, l’**ID d’espace de travail** et la **clé primaire** Log Analytics vous sont demandés.

Les informations de métadonnées relatives à la machine connectée sont également collectées. Plus précisément :

* Nom et version du système d’exploitation
* Nom de l'ordinateur
* Nom de domaine complet (FQDN) de l’ordinateur
* Version de l’agent Machine connectée

Les serveurs avec Azure Arc vous permettent de spécifier la région où vos données seront stockées. Microsoft peut répliquer vers d’autres régions à des fins de résilience des données, mais Microsoft ne réplique pas ou ne déplace pas les données en dehors de la zone géographique. Ces données sont stockées dans la région où la ressource de machine Azure Arc est configurée. Par exemple, si la machine est inscrite auprès d’Arc dans la région USA Est, ces données sont stockées dans la région USA.

Pour plus d’informations sur notre prise en charge de la résilience régionale et de la conformité, consultez [Géographie Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la conception de la [résilience Azure](/azure/architecture/reliability/architect).