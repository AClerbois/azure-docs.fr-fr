---
title: Mappages de régions d’Azure Monitor pour conteneurs
description: Cet article décrit les mappages de région pris en charge entre Azure Monitor pour conteneurs, l’espace de travail Log Analytics et les métriques personnalisées.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518123"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mappages de régions pris en charge par Azure Monitor pour conteneurs

 Lorsque vous activez Azure Monitor pour conteneurs, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un cluster AKS, ainsi que pour la collecte de métriques personnalisées envoyées à Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappages pris en charge sur l’espace de travail Log Analytics

Les ressources du cluster AKS ou l’espace de travail Log Analytics peuvent résider dans d’autres régions. Le tableau suivant présente nos mappages.

|**Région du cluster AKS** | **Région de l’espace de travail Log Analytics** |
|-----------------------|------------------------------------|
|**Afrique** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australie** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asie-Pacifique** | |
|EastAsia |EastAsia |
|AsieSudEst |AsieSudEst |
|**Brésil** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europe** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|RoyaumeUniSud |RoyaumeUniSud |
|UKWest |RoyaumeUniSud |
|WestEurope |WestEurope |
|**Inde** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japon** | |
|JaponEst |JaponEst |
|JapanWest |JaponEst |
|**Corée** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**États-Unis** | |
|CentralUS |CentralUS|
|USAEst |USAEst |
|USAEst2 |USAEst2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> En raison de restrictions de capacité, la région n’est pas disponible lors de la création de ressources. Cela comprend les espaces de travail Log Analytics. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

## <a name="custom-metrics-supported-regions"></a>Régions prises en charge su les métriques personnalisées

La collecte de métriques des pods et nœuds de cluster Azure Kubernetes Service (AKS) est prise en charge dans le cadre de la publication en tant que métriques personnalisées dans les [régions Azure](../platform/metrics-custom-overview.md#supported-regions) suivantes.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment activer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance.  