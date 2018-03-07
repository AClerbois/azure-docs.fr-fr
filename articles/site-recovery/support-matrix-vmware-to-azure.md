---
title: "Matrice de support Azure Site Recovery pour la réplication des machines virtuelles VMware et des serveurs physiques vers Azure | Microsoft Docs"
description: "Répertorie les systèmes d’exploitation pris en charge et les composants dédiés à la réplication des machines virtuelles VMware vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 31754cd765c90b9e36d16dc766b0a3546e6fd93e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matrice de support pour la réplication des machines virtuelles VMware et des serveurs physiques vers Azure


Cet article répertorie les composants et les paramètres pris en charge pour la reprise après sinistre de machines virtuelles VMware vers Azure, avec [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scénarios pris en charge

**Scénario** | **Détails**
--- | ---
**Machines virtuelles VMware** | Vous pouvez configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales. Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de PowerShell.
**Serveurs physiques** | Vous pouvez configurer la récupération d’urgence vers Azure pour des serveurs physiques Windows/Linux locaux. Vous pouvez déployer ce scénario dans le portail Azure.

## <a name="on-premises-virtualization-servers"></a>Serveurs de virtualisation locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
**VMware** | vCenter Server 6.5, 6.0 ou 5.5, ou vSphere 6.5, 6.0 ou 5.5 | Nous vous recommandons d’utiliser une instance de serveur vCenter.
**Serveurs physiques** | N/A


## <a name="replicated-machines"></a>Machines répliquées

Le tableau suivant résume la prise en charge de réplication pour les ordinateurs. Site Recovery prend en charge la réplication des charges de travail exécutées sur un ordinateur équipé d’un système d’exploitation pris en charge.

**Composant** | **Détails**
--- | ---
Configuration d’un ordinateur | Les ordinateurs qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#failed-over-azure-vm-requirements).
Système d’exploitation des ordinateurs (Windows) | Windows Server 2016 64 bits (Server Core, Server avec Expérience utilisateur), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1
Système d’exploitation des ordinateurs (Linux) | Red Hat Enterprise Linux : 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.4 <br/><br/>CentOS : 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.4 <br/><br/>Serveur LTS Ubuntu 14.04[ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Serveur LTS Ubuntu 16.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>La mise à niveau des machines de réplication SLES 11 SP3 vers SLES 11 SP4 n’est pas prise en charge. Si une machine SLES 11 SP3 répliquée a été mise à niveau vers SLES 11 SP4, vous devez désactiver la réplication et protéger à nouveau la machine après la mise à niveau.

>[!NOTE]
>
> - Dans les distributions Linux, seuls les noyaux de stockage qui font partie de la version/mise à jour mineure de la distribution sont pris en charge.
>
> - La mise à niveau d’une version majeure vers une autre pour une distribution Linux, située sur une machine virtuelle VMware ou un serveur physique protégés par Azure Site Recovery, n’est pas prise en charge. Lorsque vous mettez à niveau une version majeure d’un système d’exploitation vers une autre version majeure (par exemple, CentOS 6.* vers CentOS 7.*), désactivez la réplication pour la machine, mettez à niveau le système d’exploitation sur la machine, puis réactivez la réplication.
>

### <a name="ubuntu-kernel-versions"></a>Version du noyau Ubuntu


**Version prise en charge** | **Version du service Mobilité Azure Site Recovery** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic à 3.13.0-121-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic à 3.13.0-128-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-91-generic, |
14.04 LTS | 9.12 | 3.13.0-24-generic à 3.13.0-132-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic à 3.13.0-137-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-104-generic |
LTS 16.04 | 9.10 | 4.4.0-21-generic à 4.4.0-81-generic,<br/>4.8.0-34-generic à 4.8.0-56-generic,<br/>4.10.0-14-generic à 4.10.0-24-generic |
LTS 16.04 | 9.11 | 4.4.0-21-generic à 4.4.0-91-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-32-generic |
LTS 16.04 | 9.12 | 4.4.0-21-generic à 4.4.0-96-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-35-generic |
LTS 16.04 | 9.13 | 4.4.0-21-generic à 4.4.0-104-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Configuration de stockage invité/système de fichiers Linux

**Composant** | **Pris en charge**
--- | ---
Systèmes de fichiers | ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS
Gestionnaire de volume | LVM2
Logiciel multichemin | Mappeur d’appareil
Dispositif de stockage paravirtualisé | Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.
Unités de bloc d’entrée et de sortie en file d’attente | Non pris en charge.
Serveurs physiques avec le contrôleur de stockage HP CCISS | Non pris en charge.
Répertoires | Les répertoires suivants (s’ils sont configurés en tant que partitions/systèmes de fichiers séparés) doivent tous se trouver sur le même disque de système d’exploitation, sur le serveur source : /(root), /boot, /usr, /usr/local, /var, /etc. </br></br> Si le volume / (racine) est un volume LVM, le volume /boot doit être hébergé sur une partition distincte sur le même disque ; il ne doit pas s’agir d’un volume LVM.<br/><br/>
XFSv5 | Les fonctionnalités XFSv5 des systèmes de fichiers XFS, telles que les sommes de contrôle de métadonnées, sont prises en charge à partir de la version 9.10 du service Mobilité. Utilisez l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si ftype est défini sur 1, les fonctionnalités XFSv5 sont utilisées.



## <a name="network"></a>Réseau

**Composant** | **Pris en charge**
--- | ---
Réseau hôte : association de cartes réseau | Pris en charge pour les machines virtuelles VMware <br/><br/>Non pris en charge pour la réplication des machines physiques
Réseau hôte VLAN | OUI
Réseau hôte IPv4 | OUI
Réseau hôte IPv6 | Non 
Association de cartes réseau invité/serveur | Non 
Réseau invité/serveur IPv4 | OUI
Réseau invité/serveur IPv6 | Non 
Adresse IP statique du réseau invité/serveur (Windows) | OUI
Adresse IP statique du réseau invité/serveur (Linux) | OUI <br/><br/>Les machines virtuelles sont configurées pour utiliser le protocole DHCP lors de la restauration automatique.  
Plusieurs cartes réseau invité/serveur | OUI


## <a name="azure-vm-network-after-failover"></a>Réseau de machines virtuelles Azure (après le basculement)

**Composant** | **Pris en charge**
--- | ---
Azure ExpressRoute | OUI
ILB | OUI
ELB | OUI
Azure Traffic Manager | OUI
Plusieurs cartes réseau | OUI
Adresses IP réservées | OUI
IPv4 | OUI
Conserver l’adresse IP source | OUI
Points de terminaison du service Réseau virtuel Azure<br/><br/> (Pare-feu et réseaux virtuels du Stockage Azure) | Non 

## <a name="storage"></a>Stockage
**Composant** | **Pris en charge**
--- | ---
Hôte NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques
Hôte SAN (ISCSI) | OUI
Chemins d’accès multiples de l’hôte (MPIO) | Oui, testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON
VMDK invité/serveur | OUI
EFI/UEFI invité/serveur| Partiellement (migration vers Azure pour les machines virtuelles VMware et Windows Server 2012, et versions ultérieures, uniquement) </br></br> Voir la remarque au bas du tableau.
Disque de cluster partagé invité/serveur | Non 
Disque chiffré invité/serveur | Non 
NFS invité/serveur | Non 
SMB 3.0 invité/serveur | Non 
RDM invité/serveur | OUI<br/><br/> N/A pour les serveurs physiques
Disque invité/serveur > 1 To | OUI<br/><br/>Jusqu’à 4 095 Go
Disque invité/serveur avec une taille de secteur logique de 4 Ko et une taille de secteur physique de 4 K | OUI
Disque invité/serveur avec une taille de secteur logique de 4 K et une taille de secteur physique de 512 octets | OUI
Volume d’invité/de serveur avec disque à bandes > 4 To <br><br/>Gestion des volumes logiques | OUI
Invité/serveur - Espaces de stockage | Non 
Ajout/retrait à chaud de disque d’Invité/de serveur | Non 
Invité/serveur - Exclure le disque | OUI
Chemins d’accès multiples (MPIO) d’invité/de serveur | N/A

> [!NOTE]
> Les machines virtuelles VMware à démarrage UEFI exécutant Windows Server 2012 ou une version ultérieure peuvent être migrées vers Azure. Les restrictions suivantes s’appliquent :

> - Seule la migration vers Azure est prise en charge. La restauration automatique vers un site VMware local n’est pas prise en charge.
> - Le disque de système d’exploitation du serveur ne doit pas comprendre plus de 4 partitions.
> - Nécessite la version 9.13 du service Mobilité d’Azure Site Recovery, ou une version ultérieure.
> - Non pris en charge pour les serveurs physiques.

## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Pris en charge**
--- | ---
LRS | OUI
GRS | OUI
RA-GRS | OUI
Stockage froid | Non 
Stockage chaud| Non 
Objets blob de blocs | Non 
Chiffrement au repos (SSE)| OUI
Stockage Premium | OUI
Service Import/Export | Non 
Points de terminaison du service Réseau virtuel<br/><br/> Pare-feu et réseaux virtuels de stockage configurés dans le compte de stockage de cache/de stockage cible (utilisé pour stocker les données de réplication) | Non 
Comptes de stockage v2 à usage général (niveaux chaud et froid) | Non 

## <a name="azure-compute"></a>Calcul Azure

**Fonctionnalité** | **Pris en charge**
--- | ---
Groupes à haute disponibilité | OUI
HUB | OUI   
Disques gérés | OUI

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
**Système d’exploitation invité** | Vérifiez les [systèmes d’exploitation pris en charge](#replicated machines). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu’à 2 048 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | Le nombre est égal ou inférieur à 64 si vous répliquez des *machines virtuelles VMware vers Azure*. Le nombre est égal ou inférieur à 16 si vous répliquez des *machines virtuelles Hyper-V vers Azure*. | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 4 095 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Disque dur virtuel partagé** | Non pris en charge. | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge. | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à une restauration automatique vers un site local, les machines virtuelles continuent d’utiliser le format VHDX.
**BitLocker** | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine.
**Nom de la machine virtuelle** | De 1 et 63 caractères.<br/><br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine de Site Recovery.
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2--Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et un espace disque inférieur à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. Pour plus d’informations, consultez [Disaster recovery to Azure enhanced](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="vault-tasks"></a>Tâches de coffre

**Action** | **Pris en charge**
--- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 


## <a name="mobility-service"></a>Service Mobilité

**Name** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Programme d’installation unifiée Azure Site Recovery** | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/> Installé sur des serveurs VMware locaux | 9.12.4653.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)
**Service Mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer | 9.12.4653.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>étapes suivantes
[Découvrez comment](tutorial-prepare-azure.md) préparer Azure à la reprise après sinistre de machines virtuelles VMware.
