---
title: Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure | Microsoft Docs
description: Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 03623c64aad875ed46e7f578350e77cbd17c7c3b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141499"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Pour configurer un cluster Pacemaker dans Azure, deux options s’offrent à vous : vous pouvez soit utiliser un agent d’isolation, qui s’occupe de redémarrer les nœuds défaillants via les API Azure, soit utiliser un appareil SBD.

L’appareil SBD nécessite au moins une machine virtuelle supplémentaire pour jouer le rôle de serveur cible iSCSI et fournir un appareil SBD. Ces serveurs cibles iSCSI peuvent toutefois être partagés avec d’autres clusters Pacemaker. L’utilisation d’un appareil SBD présente l’avantage de garantir un temps de basculement plus rapide et, si vous faites appel à des appareils SBD en local, de ne pas vous obliger à revoir la façon dont vous exploitez le cluster Pacemaker. Vous pouvez utiliser jusqu’à trois appareils SBD pour qu’un cluster Pacemaker autorise l’indisponibilité d’un appareil SBD, par exemple lors de la mise à jour corrective du système d’exploitation du serveur cible iSCSI. Si vous souhaitez utiliser plusieurs appareil SBD par Pacemaker, veillez à déployer plusieurs serveurs cibles iSCSI et à connecter un SBD depuis chaque serveur cible iSCSI. Nous vous recommandons d’utiliser soit un appareil SBD, soit trois appareils SBD. Pacemaker n’est pas en mesure d’isoler automatiquement un nœud de cluster si vous ne configurez que deux appareils SBD et que l’un d’eux n’est pas disponible. Si vous souhaitez pouvoir procéder à une isolation lorsqu’un serveur cible iSCSI est inactif, vous devez utiliser trois appareils SBD, et donc trois serveurs cibles iSCSI.

Si vous ne voulez pas investir dans une machine virtuelle supplémentaire, vous pouvez également utiliser l’agent d’isolation Azure. L’inconvénient, c’est qu’un basculement peut prendre entre 10 et 15 minutes si une ressource échoue ou si les nœuds de cluster ne peuvent plus communiquer entre eux.

![Vue d’ensemble de Pacemaker sur SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Lors de la planification et du déploiement des nœuds de cluster Linux Pacemaker et des périphériques SBD, il est essentiel pour la fiabilité globale de la configuration complète du cluster que le routage entre les machines virtuelles impliquées et les machines virtuelles hébergeant le ou les périphériques SBD ne passent pas par d’autres appareils comme les [NVA](https://azure.microsoft.com/solutions/network-appliances/). Sinon, les problèmes et les événements de maintenance avec l’appliance virtuelle réseau peuvent avoir un impact négatif sur la stabilité et la fiabilité de la configuration générale du cluster. Afin d’éviter de tels obstacles, ne définissez pas de règles d’acheminement d’appliances virtuelles réseau ou de [règles d’acheminement définies par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) qui acheminent le trafic entre les nœuds de cluster et des appareils SBD via les appliances virtuelles réseau et des périphériques similaires lors de la planification et du déploiement des nœuds de cluster Linux Pacemaker et des périphériques SBD. 
>

## <a name="sbd-fencing"></a>Isolation SBD

Si vous souhaitez utiliser un appareil SBD pour l’isolation, procédez comme suit.

### <a name="set-up-iscsi-target-servers"></a>Configurer des serveurs cibles iSCSI

Vous devez tout d’abord créer les machines virtuelles cibles iSCSI. Les serveurs cibles iSCSI peuvent être partagés avec plusieurs clusters Pacemaker.

1. Déployez de nouvelles machines virtuelles SLES 12 SP1 ou version ultérieure, et connectez-vous à ces machines via le protocole SSH. Les tailles des machines n’ont pas besoin d’être importantes. Une taille de machine virtuelle telle que Standard_E2s_v3 ou Standard_D2s_v3 est suffisante. Veillez à utiliser le disque de système d’exploitation en stockage Premium.

Exécutez les commandes suivantes sur toutes les **machines virtuelles cibles iSCSI**.

1. Mettre à jour SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Supprimer des packages

   Pour éviter un problème connu avec targetcli et SLES 12 SP3, désinstallez les packages suivants. Vous pouvez ignorer les erreurs concernant des packages introuvables.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installer les packages cibles iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Activer le service cible iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Créer un périphérique iSCSI sur le serveur cible iSCSI

Exécutez les commandes suivantes sur toutes les **machines virtuelles cibles iSCSI** pour créer les disques iSCSI des clusters utilisés par vos systèmes SAP. Dans l’exemple suivant, des appareils SBD sont créés pour plusieurs clusters. Il vous montre comment utiliser un serveur cible iSCSI pour plusieurs clusters. Les appareils SBD sont placés sur le disque de système d’exploitation. Assurez-vous d’avoir suffisamment d’espace.

**nfs** est utilisé pour identifier le cluster NFS, **ascsnw1** est utilisé pour identifier le cluster ASCS de **NW1**, **dbnw1** est utilisé pour identifier le cluster de base de données de **NW1** ; **nfs-0** et **nfs-1** sont les noms d’hôte des nœuds du cluster NFS, **nw1-xscs-0** et **nw1-xscs-1** sont les noms d’hôte des nœuds du cluster ASCS **NW1** et **nw1-db-0** et **nw1-db-1** sont les noms d’hôte des nœuds du cluster de base de données. Remplacez-les par les noms d’hôte de vos nœuds de cluster, et par le SID de votre système SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluter of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Vous pouvez vérifier si tout a été correctement configuré avec

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Configurer l’appareil SBD

Connectez-vous au périphérique iSCSI créé à l’étape précédente à partir du cluster.
Exécutez les commandes suivantes sur les nœuds du nouveau cluster que vous souhaitez créer.
Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]**  Se connecter aux périphériques iSCSI

   Tout d’abord, activez les services iSCSI et SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Modifier le nom de l’initiateur sur le premier nœud

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modifiez le contenu du fichier pour qu’il corresponde aux listes de contrôle d’accès (ACL) utilisées lors de la création de l’appareil iSCSI sur le serveur cible iSCSI, par exemple pour le serveur NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Modifier le nom de l’initiateur sur le deuxième nœud

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modifiez le contenu du fichier pour qu’il corresponde aux listes de contrôle d’accès (ACL) utilisées lors de la création du périphérique iSCSI sur le serveur cible iSCSI.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Redémarrer le service iSCSI

   Maintenant, redémarrez le service iSCSI pour appliquer les modifications.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Connectez les périphériques iSCSI. Dans l’exemple ci-dessous, 10.0.0.17 est l’adresse IP du serveur cible iSCSI et 3260 le port par défaut. <b>iqn.2006-04.nfs.local:nfs</b> est un des noms cibles qui est répertorié lorsque vous exécutez la première commande ci-dessous (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Assurez-vous que les appareils iSCSI sont disponibles et notez leur nom (« /dev/sde » dans l’exemple suivant).

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Maintenant, récupérez les ID des appareils iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   La commande fait apparaître trois ID d’appareil pour chaque appareil SBD. Nous vous recommandons d’utiliser l’ID qui commence par scsi-3. Dans l’exemple ci-dessus, il s’agit de l’ID :

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Créer l’appareil SBD

   Utilisez l’ID d’appareil des appareils iSCSI pour créer de nouveaux appareils SBD sur le premier nœud de cluster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Adapter la configuration SBD

   Ouvrez le fichier de configuration SBD.

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Modifiez la propriété de l’appareil SBD, activez l’intégration de Pacemaker et modifiez le mode de démarrage de SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Créez le fichier de configuration softdog.

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Maintenant, chargez le module.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Installation du cluster

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Mettre à jour SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Configurer le système d’exploitation

   Dans certains cas, Pacemaker crée de nombreux processus, et de ce fait dépasse le nombre autorisé de processus. Dans ce genre de situation, une pulsation entre les nœuds du cluster peut échouer et entraîner le basculement de vos ressources. Nous vous recommandons d’augmenter le nombre maximal autorisé de processus en définissant le paramètre suivant.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Réduisez la taille du cache d’intégrité. Pour plus d’informations, consultez [Faibles performances en écriture sur les serveurs SLES 11/12 avec une grande quantité de RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2**] Activer l’accès SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Installer des agents Fence
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]** Configurer la résolution de nom d’hôte

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes. L’avantage d’utiliser/etc/hosts réside dans le fait que votre cluster devient indépendant du serveur DNS, ce qui peut aussi être un point de défaillance unique.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installer le cluster

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Ajouter un nœud au cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Changer le mot de passe hacluster pour utiliser le même mot de passe

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Configurer corosync pour utiliser les autres transports et ajouter la liste de nœuds Le cluster ne fonctionne pas dans le cas contraire.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Ajoutez le contenu ci-dessous en gras dans le fichier si les valeurs sont absentes ou différentes. Veillez à modifier le jeton sur 30 000 pour autoriser la maintenance avec préservation de la mémoire. Pour en savoir plus, consultez [cet article pour Linux][virtual-machines-linux-maintenance] ou [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Redémarrez ensuite le service corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Créer un appareil STONITH agent d’isolation Azure

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID de répertoire. Il s’agit de **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le type d’application Application/API web, saisissez une URL de connexion (par exemple, http://localhost), puis cliquez sur Créer.
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application et cliquez sur Clés dans l’onglet Paramètres
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Enregistrer
1. Notez la valeur. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure ci-dessous) du principal de service

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Créer un rôle personnalisé pour l’agent d’isolation

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal de service les autorisations de démarrer et arrêter (libérer) toutes les machines virtuelles du cluster. Si vous n’avez pas encore créé le rôle personnalisé, vous pouvez le créer à l’aide de [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) ou de l’[interface de ligne de commande Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role).

Utilisez le contenu suivant pour le fichier d’entrée. Vous devez adapter le contenu à vos abonnements, c’est-à-dire remplacer c276fc76-9cd4-44c9-99a7-4fd71546436e et e91d47c4-76f3-4271-a796-21b4ecfe3624 par les ID de vos abonnements. Si vous n’avez qu’un seul abonnement, supprimez la deuxième entrée dans AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Affecter le rôle personnalisé au principal de service

Affectez au principal de service le rôle personnalisé Linux Fence Agent Role (Rôle d’agent d’isolation Linux) créé dans la section précédente. N’utilisez plus le rôle Propriétaire !

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Toutes les ressources
1. Sélectionnez la machine virtuelle du premier nœud de cluster.
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter.
1. Sélectionnez le rôle Linux Fence Agent Role (Rôle d’agent d’isolation Linux).
1. Entrez le nom de l’application que vous avez créée ci-dessus
1. Cliquez sur OK

Répétez les étapes ci-dessus pour le deuxième nœud de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Créer les appareils STONITH

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les appareils STONITH dans le cluster.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configuration Pacemaker par défaut pour SBD

1. **[1]** Activer l’utilisation d’un appareil STONITH et définir le délai d’isolation

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="next-steps"></a>Étapes suivantes

* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server][sles-nfs-guide]
* [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP][sles-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].
