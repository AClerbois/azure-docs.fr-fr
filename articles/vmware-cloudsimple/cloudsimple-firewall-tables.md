---
title: Tables de pare-feu - Solution VMware de CloudSimple - Azure
description: Découvrez les tables de pare-feu et les règles de pare-feu de cloud privé CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d25aa9252f061cee7f4cffdca42f00d84f719a3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812665"
---
# <a name="firewall-tables-overview"></a>Vue d’ensemble des tables de pare-feu

Une table de pare-feu liste les règles permettant de filtrer le trafic réseau vers et en provenance de ressources de cloud privé. Vous pouvez les appliquer à un réseau local virtuel ou à un sous-réseau. Les règles contrôlent ensuite le trafic réseau entre un réseau source ou une adresse IP et un réseau de destination ou une adresse IP.

## <a name="firewall-rules"></a>Règles de pare-feu

Le tableau suivant décrit les paramètres d’une règle de pare-feu.

| Propriété | Détails |
| ---------| --------|
| **Nom** | Nom qui identifie de façon unique la règle de pare-feu et son objectif. |
| **Priorité** | Nombre compris entre 100 et 4 096, 100 étant la priorité la plus élevée. Les règles sont traitées par ordre de priorité. Quand le trafic rencontre une correspondance de règle, le traitement des règles s’arrête. Par conséquent, les règles avec des priorités plus faibles et ayant les mêmes attributs que les règles de priorité supérieure ne sont pas traitées.  Veillez à éviter les règles en conflit. |
| **Suivi de l’état** | Le suivi peut être sans état (cloud privé, Internet ou VPN) ou avec état (adresse IP publique).  |
| **Protocole** | Les options disponibles sont Tout, TCP ou UDP. Si vous avez besoin d’ICMP, utilisez Tout. |
| **Direction** | Indique si la règle s’applique au trafic entrant ou sortant. |
| **Action** | Autorisez ou refusez le type de trafic défini dans la règle. |
| **Source** | Bloc de routage interdomaine sans classe (CIDR) d’adresses IP (10.0.0.0/24, par exemple) ou Tout.  La spécification d’une plage, d’une balise de service ou d’un groupe de sécurité d’application vous permet de créer moins des règles de sécurité. |
| **Port source** | Port d’où provient le trafic réseau.  Vous pouvez spécifier un port individuel ou une plage de ports ; par exemple, 443 ou 8000-8080. La spécification de plages vous permet de créer moins de règles de sécurité. |
| **Destination** | Bloc de routage interdomaine sans classe (CIDR) d’adresses IP (10.0.0.0/24, par exemple) ou Tout.  La spécification d’une plage, d’une balise de service ou d’un groupe de sécurité d’application vous permet de créer moins des règles de sécurité.  |
| **Port de destination** | Port vers lequel le trafic réseau circule.  Vous pouvez spécifier un port individuel ou une plage de ports ; par exemple, 443 ou 8000-8080. La spécification de plages vous permet de créer moins de règles de sécurité.|

### <a name="stateless"></a>Sans état

Une règle sans état examine uniquement les paquets et les filtre en fonction de la règle.  
Des règles supplémentaires peuvent être nécessaires pour que le trafic circule en sens inverse.  Utilisez des règles sans état pour le trafic entre les points suivants :

* Sous-réseaux de clouds privés
* Sous-réseau local et un sous-réseau de cloud privé
* Trafic Internet à partir des clous privés

### <a name="stateful"></a>Avec état

 Une règle avec état a connaissance des connexions qui la franchissent. Un enregistrement de flux est créé pour les connexions existantes. La communication est autorisée ou refusée en fonction de l’état de connexion de l’enregistrement de flux.  Utilisez ce type de règle pour les adresses IP publiques afin de filtrer le trafic provenant d’Internet.

### <a name="default-rules"></a>Règles par défaut

Les règles par défaut suivantes sont créées sur chaque table de pare-feu.

|Priorité|Nom|Suivi de l’état|Direction|Type de trafic|Protocole|Source|Port source|Destination|Port de destination|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65 000|allow-all-to-internet|Avec état|Règle de trafic sortant|Adresse IP publique ou trafic Internet|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|
|65 001|deny-all-from-internet|Avec état|Trafic entrant|Adresse IP publique ou trafic Internet|Tous|Quelconque|Quelconque|Quelconque|Quelconque|Deny|
|65002|allow-all-to-intranet|Sans état|Règle de trafic sortant|Cloud privé interne ou trafic VPN|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|
|65003|allow-all-from-intranet|Sans état|Trafic entrant|Cloud privé interne ou trafic VPN|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des règles et tables de pare-feu](https://docs.azure.cloudsimple.com/firewall/)