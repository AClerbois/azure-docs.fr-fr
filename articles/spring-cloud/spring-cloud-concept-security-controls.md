---
title: Concept – Contrôles de sécurité pour le service Azure Spring Cloud
description: Utilisez les contrôles de sécurité intégrés dans le service Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1472da4c87dc4579a30290460fb7811cf228be47
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892488"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Contrôles de sécurité pour le service Azure Spring Cloud

**Cet article s'applique à :** ✔️ Java ✔️ C#

Des contrôles de sécurité sont intégrés dans le service Azure Spring Cloud.

Un contrôle de sécurité est une qualité ou une caractéristique d’un service Azure qui lui permet de prévenir, détecter et répondre aux failles de sécurité.  Pour chaque contrôle, nous utilisons *Oui* ou *Non* pour indiquer s’il est actuellement en place pour le service.  Nous utilisons *N/A* pour un contrôle qui n’est pas applicable au service. 

**Contrôles de sécurité de la protection des données**

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | La source et les artefacts chargés par l’utilisateur, les paramètres du serveur de configuration, les paramètres de l’application et les données dans le stockage persistant sont stockés dans Stockage Azure qui chiffre automatiquement le contenu au repos.<br><br>Le cache du serveur de configuration, les fichiers binaires du runtime générés à partir de la source téléchargée et les journaux des applications consignés pendant la durée de vie de l'application sont enregistrés sur un disque managé Azure qui chiffre automatiquement le contenu au repos.<br><br>Les images conteneur générées à partir de la source chargée par l’utilisateur sont enregistrées dans Azure Container Registry qui chiffre automatiquement le contenu de l’image au repos. | [Chiffrement du stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Chiffrement côté serveur de disques managés Azure](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Stockage des images conteneur dans Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Chiffrement en transit | Oui | Les points de terminaison publics de l’application utilisateur utilisent le protocole HTTPs pour le trafic entrant par défaut. |  |
| Appels d’API chiffrés | Oui | Les appels de gestion pour configurer le service Azure Spring Cloud se font via des appels Azure Resource Manager sur HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Contrôles de sécurité d’accès réseau**

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Étiquette du service | Oui | Utilisez la balise de service **AzureSpringCloud** pour définir les contrôles d’accès réseau sortants sur des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ou [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/service-tags) pour autoriser le trafic vers les applications Azure Spring Cloud.<br><br>*Remarque :* Actuellement, seule la nouvelle instance de service Azure Spring Cloud créée après le 14/07/2020 prend en charge la balise de service **AzureSpringCloud**. | [Balises de service](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Déployer votre première application Azure Spring Cloud](spring-cloud-quickstart.md)
