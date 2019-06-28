---
title: Fichier Include
description: Fichier Include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/13/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 813c8e92907a60046c2e53f97d4dd05125076241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133078"
---
<a name="virtual-networking-limits-classic"></a>Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais du modèle de déploiement **classique** par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Réseaux virtuels |50 |100 |
| Sites de réseau local |20 |Contactez le support technique. |
| Serveurs DNS par réseau virtuel |20 |20 |
| Adresses IP privées par réseau virtuel |4 096 |4 096 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500 000, jusqu’à 1 000 000 pour deux cartes réseau ou plus. |500 000, jusqu’à 1 000 000 pour deux cartes réseau ou plus. |
| Groupes de sécurité réseau (NSG) |200 |200 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |1 000 |1 000 |
| Tables d’itinéraires définies par l’utilisateur |200 |200 |
| Itinéraires définis par l’utilisateur par table d’itinéraire |400 |400 |
| Adresses IP publiques (dynamiques) |5\. |Contacter le support technique |
| Adresses IP publiques réservées |20 |Contacter le support technique |
| Adresse IP virtuelle publique par déploiement |5\. |Contacter le support technique |
| Adresses IP virtuelles privées (équilibrage de charge interne) par déploiement |1 |1 |
| Listes de contrôle d’accès (ACL) par point de terminaison |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de mise en réseau - Azure Resource Manager
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’**Azure Resource Manager** par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Récemment, nous avons augmenté toutes les limites par défaut à leur niveau maximal. Si aucune colonne de limite maximale n’est présente, la ressource spécifiée ne possède pas de limites ajustables. Si ces limites ont été augmentées par le support dans le passé et si vous ne voyez pas les limites mises à jour dans les tables suivantes, vous pouvez [ouvrir une demande de support clientèle en ligne sans frais](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Ressource | Limite maximale/par défaut | 
| --- | --- |
| Réseaux virtuels |1 000 |
| Nombre de sous-réseaux par réseau virtuel |3 000 |
| Homologations VNet par réseau virtuel |500 |
| Serveurs DNS par réseau virtuel |20 |
| Adresses IP privées par réseau virtuel |65 536 |
| Adresses IP privées par interface réseau |256 |
| Adresses IP privées par machine virtuelle |256 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500 000 |
| Cartes d’interface réseau |65 536 |
| Network Security Group |5 000 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |1 000 |
| Adresses IP et plages spécifiées pour la source et la destination dans un groupe de sécurité |4 000 |
| Groupes de sécurité d’application |3 000 |
| Groupes de sécurité d’application par configuration IP, par carte réseau |20 |
| Configurations IP par groupe de sécurité d’application |4 000 |
| Groupes de sécurité d’application qui peuvent être spécifiés dans toutes les règles de sécurité d’un groupe de sécurité réseau |100 |
| Tables d’itinéraires définies par l’utilisateur |200 |
| Itinéraires définis par l’utilisateur par table d’itinéraire |400 |
| Certificats racines point à site pour chaque passerelle VPN Azure |20 |
| TAP de réseau virtuel |100 |
| Configurations TAP d’interface réseau de chaque réseau virtuel TAP |100 |

#### <a name="publicip-address"></a>Limites de l’adresse IP publique
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Adresses IP publiques (dynamiques) | 1 000 pour De base. |Contactez le support technique. |
| Adresses IP publiques (statiques) | 1 000 pour De base. |Contactez le support technique. |
| Adresses IP publiques (statiques) | 200 pour Standard.|Contactez le support technique. |
| Taille de préfixe d’adresse IP publique | /28 | Contactez le support technique. |

#### <a name="load-balancer"></a>Limites de l’équilibreur de charge
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

| Ressource | Limite maximale/par défaut |
| --- | --- |
| Équilibreurs de charge | 1 000 | 
| Règles par ressource, De base | 250 |
| Règles par ressource, Standard | 1 500 | 
| Règles par configuration IP | 299 |
| Règles par groupe de sécurité réseau | 300 |
| Configuration d’adresses IP frontales, De base | 200 |
| Configurations d’adresses IP frontales, Standard | 600 |
| Pool principal, De base | 100, un seul groupe à haute disponibilité |
| Pool principal, Standard | 1 000, un seul réseau virtuel |
| Ressources principales par équilibreur de charge, Standard<sup>1</sup> | 150 |
| Ports de haute disponibilité, Standard | 1 par serveur frontal interne |

<sup>1</sup>La limite est fixée à 150 ressources, toute combinaison de ressources de machines virtuelles autonomes, ressources de groupes à haute disponibilité et ressources de groupes de machines virtuelles identiques.

