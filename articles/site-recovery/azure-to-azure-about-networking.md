---
title: Mise en réseau dans Azure pour la récupération d’urgence Azure dans Azure Site Recovery | Microsoft Docs
description: Fournit une vue d’ensemble de la mise en réseau en vue d’une réplication des machines virtuelles Azure avec Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/17/2018
ms.author: sujayt
ms.openlocfilehash: e3acedf4135166f5239b95eb21eb5dfd66d6100f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312625"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Mise en réseau dans Azure pour la réplication d’Azure

>[!NOTE]
> La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article fournit des instructions détaillées sur la mise en réseau quand vous répliquez et récupérez des machines virtuelles d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Avant de commencer

Découvrez comment Site Recovery permet la récupération d’urgence pour [ce scénario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastructure réseau classique

Le diagramme suivant illustre un environnement Azure classique pour des applications qui s’exécutent sur des machines virtuelles Azure :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si vous utilisez Azure ExpressRoute ou une connexion VPN de votre réseau local vers Azure, l’environnement ressemble à ceci :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Les réseaux sont généralement protégés à l’aide de pare-feu et de groupes de sécurité réseau. Les pare-feu se servent d’une liste blanche basée sur l’adresse IP ou l’URL pour contrôler la connectivité réseau. Les groupes de sécurité réseau appliquent des règles qui utilise les plages d’adresses IP pour contrôler la connectivité réseau.

>[!IMPORTANT]
> L’utilisation d’un proxy authentifié pour contrôler la connectivité réseau n’est pas pris en charge par Site Recovery, et la réplication ne peut pas être activée.


## <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez ces URL Site Recovery :


**URL** | **Détails**  
--- | ---
*.blob.core.windows.net | Nécessaire pour que les données puissent être écrites dans le compte de stockage de cache dans la région source à partir de la machine virtuelle.
login.microsoftonline.com | Nécessaire pour l’autorisation et l’authentification aux URL du service Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Nécessaire pour que la communication du service Site Recovery puisse avoir lieu à partir de la machine virtuelle.
*.servicebus.windows.net | Nécessaire pour que les données de surveillance et de diagnostic Site Recovery puissent être écrites à partir de la machine virtuelle.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Si vous utilisez des règles de groupe de sécurité réseau, de proxy ou de pare-feu basées sur une adresse IP pour contrôler la connectivité sortante, ces plages d’adresses IP doivent être autorisées.

- Toutes les plages d’adresses IP qui correspondent aux comptes de stockage dans la région source
    - Créez une règle de groupe de sécurité réseau basée sur une [balise de service de stockage](../virtual-network/security-overview.md#service-tags) pour la région source.
    - Autorisez ces adresses pour que les données puissent être écrites dans le compte de stockage de cache, à partir de la machine virtuelle.
- Toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
    - Si de nouvelles adresses sont ajoutées ultérieurement aux plages d’adresses IP Office 365, vous devez créer des règles de groupe de sécurité réseau.
- Adresses IP des points de terminaison du service Site Recovery, [disponibles dans un fichier XML](https://aka.ms/site-recovery-public-ips), qui dépendent de votre emplacement cible.
-  Pour créer automatiquement les règles requises sur le groupe de sécurité réseau, vous pouvez [télécharger et utiliser ce script](https://aka.ms/nsg-rule-script).
- Nous vous recommandons de créer les règles de groupe de sécurité réseau requises sur un groupe de sécurité réseau de test, et de vérifier qu’il n’y a aucun problème avant de créer les règles sur un groupe de sécurité réseau de production.


Les plages d’adresses IP Site Recovery sont les suivantes :

   **Cible** | **IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
   --- | --- | ---
   Est de l'Asie | 52.175.17.132 | 13.94.47.61
   Asie du Sud-Est | 52.187.58.193 | 13.76.179.223
   Inde centrale | 52.172.187.37 | 104.211.98.185
   Inde du Sud | 52.172.46.220 | 104.211.224.190
   Centre-Nord des États-Unis | 23.96.195.247 | 168.62.249.226
   Europe du Nord | 40.69.212.238 | 52.169.18.8
   Europe de l'Ouest | 52.166.13.64 | 40.68.93.145
   Est des États-Unis | 13.82.88.226 | 104.45.147.24
   États-Unis de l’Ouest | 40.83.179.48 | 104.40.26.199
   États-Unis - partie centrale méridionale | 13.84.148.14 | 104.210.146.250
   Centre des États-Unis | 40.69.144.231 | 52.165.34.144
   Est des États-Unis 2 | 52.184.158.163 | 40.79.44.59
   Est du Japon | 52.185.150.140 | 138.91.1.105
   Ouest du Japon | 52.175.146.69 | 138.91.17.38
   Sud du Brésil | 191.234.185.172 | 23.97.97.36
   Est de l’Australie | 104.210.113.114 | 191.239.64.144
   Sud-est de l’Australie | 13.70.159.158 | 191.239.160.45
   Centre du Canada | 52.228.36.192 | 40.85.226.62
   Est du Canada | 52.229.125.98 | 40.86.225.142
   Centre-Ouest des États-Unis | 52.161.20.168 | 13.78.149.209
   Ouest des États-Unis 2 | 52.183.45.166 | 13.66.228.204
   Ouest du Royaume-Uni | 51.141.3.203 | 51.141.14.113
   Sud du Royaume-Uni | 51.140.43.158 | 51.140.189.52
   Sud du Royaume-Uni 2 | 13.87.37.4| 13.87.34.139
   Nord du Royaume-Uni | 51.142.209.167 | 13.87.102.68
   Centre de la Corée | 52.231.28.253 | 52.231.32.85
   Corée du Sud | 52.231.298.185 | 52.231.200.144
   France-Centre | 52.143.138.106 | 52.143.136.55
   France-Sud | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>Exemple de configuration de groupe de sécurité réseau

Cet exemple montre comment configurer des règles de groupes de sécurité réseau pour une machine virtuelle à répliquer.

- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, utilisez des règles « Autoriser le trafic HTTPS sortant » sur port:443 pour toutes les plages d’adresses IP requises.
- L’exemple part du principe que « Est des États-Unis » est l’emplacement source de la machine virtuelle, et que « Centre des États-Unis » en est l’emplacement cible.

### <a name="nsg-rules---east-us"></a>Règles de groupe de sécurité réseau - Est des États-Unis

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.EastUS » sur le groupe de sécurité réseau comme indiqué dans la capture d’écran ci-dessous.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Créez des règles HTTPS sortantes (443) pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Créez des règles HTTPS sortantes (443) pour les adresses IP Site Recovery qui correspondent à l’emplacement cible :

   **Lieu** | **Adresse IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   Centre des États-Unis | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Règles de groupe de sécurité réseau - Centre des États-Unis

Ces règles sont nécessaires pour que la réplication puisse être activée de la région cible vers la région source après le basculement :

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.CentralUS » sur le groupe de sécurité réseau.

2. Créez des règles HTTPS sortantes (443) pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.

3. Créez des règles HTTPS sortantes (443) pour les adresses IP Site Recovery qui correspondent à l’emplacement source :

   **Lieu** | **Adresse IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   Centre des États-Unis | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Configuration des appliances virtuelles réseau

Si vous utilisez des appliances virtuelles réseau pour contrôler le trafic réseau sortant des machines virtuelles, l’appliance risque de présenter des limitations si tout le trafic de réplication passe par elle. Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau.

### <a name="create-network-service-endpoint-for-storage"></a>Créer un point de terminaison de service réseau pour le stockage
Vous pouvez créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication ne quitte pas la limite Azure.

- Sélectionnez votre réseau virtuel Azure et cliquez sur Points de terminaison de service

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Cliquez sur Ajouter et l’onglet Ajouter des points de terminaison de service s’ouvre
- Sélectionnez Microsoft.Storage sous Service et les sous-réseaux requis sous le champ Sous-réseaux, puis cliquez sur Ajouter

>[!NOTE]
>Ne limitez pas l’accès au réseau virtuel aux comptes de stockage utilisés pour ASR. Vous devez autoriser l’accès de Tous les réseaux

### <a name="forced-tunneling"></a>Tunneling forcé

Vous pouvez remplacer l’itinéraire système par défaut d’Azure pour le préfixe d’adresse 0.0.0.0/0 par un [itinéraire personnalisé](../virtual-network/virtual-networks-udr-overview.md#custom-routes) et rediriger le trafic des machines virtuelles vers une appliance virtuelle réseau (NVA) locale, mais cette configuration n’est pas recommandée pour la récupération Site Recovery. Si vous utilisez des itinéraires personnalisés, vous devez [créer un point de terminaison de service réseau](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication ne quitte pas la limite Azure.

## <a name="next-steps"></a>Étapes suivantes
- Commencer à protéger vos charges de travail en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
- En savoir plus sur la [conservation des adresses IP](site-recovery-retain-ip-azure-vm-failover.md) pour le basculement de machines virtuelles Azure.
- En savoir plus sur la récupération d’urgence de [machines virtuelles Azure via ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
