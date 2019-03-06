---
title: Exemples de modèles Azure Resource Manager - Azure Front Door Service | Microsoft Docs
description: Exemples de modèles Azure Resource Manager pour Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: c0dccdd38f4b945df3702178e7351db58687bf82
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883894"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modèles de déploiement Azure Resource Manager pour Front Door

Le tableau suivant comprend des liens vers des modèles de déploiement Azure Resource Manager pour Azure Front Door Service. 

| | |
| ---| ---|
| [Créer une porte d’entrée de base](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Crée une configuration de porte d’entrée de base avec un seul backend. |
| [Créer une porte d’entrée avec plusieurs backends et pools de backends, et un routage basé sur les URL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Crée une porte d’entrée avec l’équilibrage de charge configuré pour plusieurs backends du pool de backends, ainsi qu’entre des pools de backends, basé sur les chemins d’URL. |
| [Intégrer un domaine personnalisé avec HTTPS (certificat géré par Front Door) avec Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Ajoutez un domaine personnalisé à votre porte d’entrée et activez le trafic HTTPS pour celui-ci avec un certificat géré par Front Door et généré via DigiCert. |
| [Créer une porte d’entrée avec filtrage géographique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Créez une porte d’entrée qui autorise/bloque le trafic provenant de certains pays. |
| [Contrôler les sondes d’intégrité pour vos backends sur Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Mettez à jour votre porte d’entrée pour modifier les paramètres de sonde d’intégrité en changeant le chemin des sondes, ainsi que les intervalles auxquels les sondes sont envoyées. |
| [Créer une porte d’entrée avec une configuration de backend Active/En veille](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Crée une porte d’entrée qui montre le routage en fonction de la priorité pour la topologie d’application Actif/En veille, c’est-à-dire qui envoie par défaut tout le trafic au backend principal (priorité la plus élevée) jusqu’à ce qu’il devienne indisponible. |
| [Créer une porte d’entrée avec la mise en cache activée pour certaines routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Crée une porte d’entrée avec la mise en cache activée pour la configuration de routage définie, ce qui a pour effet de mettre en cache les ressources statiques pour votre charge de travail. |
| [Configurer l’affinité de session pour les noms d’hôte de votre porte d’entrée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Met à jour une porte d’entrée pour activer l’affinité de session pour l’hôte de votre frontend, envoyant ainsi le trafic résultant de la même session utilisateur au même backend. |
| [Intégrer un domaine personnalisé avec un certificat SSL personnalisé (pour HTTPS) pour votre porte d’entrée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-custom-byoc) | Intègre un domaine personnalisé pour une porte d’entrée avec un certificat SSL personnalisé, également appelé scénario BYOC (Bring Your Own Certificate). |
| [Configurer Front Door pour le placement en liste verte ou rouge d’adresses IP clientes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Configure une porte d’entrée pour limiter le trafic de certaines adresses IP avec un contrôle d’accès personnalisé basé sur les adresses IP clientes. |
| [Configurer Front Door pour effectuer une action avec des paramètres HTTP spécifiques](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configure une porte d’entrée pour autoriser ou bloquer un certain trafic en fonction des paramètres HTTP de la requête entrante, avec des règles personnalisées pour le contrôle d’accès en fonction de paramètres HTTP. |
| [Configurer la limitation du débit de Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Configure une porte d’entrée pour limiter le débit du trafic entrant pour un hôte frontend donné. |
| | |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).