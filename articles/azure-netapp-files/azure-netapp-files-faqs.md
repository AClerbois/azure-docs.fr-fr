---
title: Questions fréquentes (FAQ) sur NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: f97bb4842d9e24d879dd47757fda75b16bca48cf
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494818"
---
# <a name="faqs-about-azure-netapp-files"></a>Questions fréquentes (FAQ) sur Azure NetApp Files

Cet article contient les réponses à certaines questions fréquemment posées sur Azure NetApp Files. 

## <a name="networking-faqs"></a>FAQ sur la mise en réseau

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Le chemin d’accès de données NFS passe-t-il par Internet ?  

Non. Le chemin d’accès de données NFS ne passe pas par Internet. Azure NetApp Files est un service natif Azure qui est déployé dans le réseau virtuel Azure (VNet) dans lequel le service est disponible. Azure NetApp Files utilise un sous-réseau délégué et configure une interface réseau directement sur le réseau virtuel. 

Voir [Consignes pour planifier un réseau Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour plus de détails.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Puis-je connecter un réseau virtuel que j’ai déjà créé pour le service Azure NetApp Files ?

Oui, vous pouvez connecter des réseaux virtuels que vous avez créés pour le service. 

Voir [Consignes pour planifier un réseau Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour plus de détails.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Puis-je monter un volume NFS d’Azure NetApp Files avec le nom FQDN du DNS ?

Oui, vous pouvez le faire si vous créez les entrées DNS requises. Azure NetApp Files fournit l’adresse IP de service pour le volume configuré. 

> [!NOTE] 
> Azure NetApp Files peut déployer des adresses IP supplémentaires pour le service en fonction des besoins.  Les entrées DNS peuvent avoir à être mises à jour régulièrement.

## <a name="security-faqs"></a>Forum aux questions sur la sécurité

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Le trafic réseau entre la machine virtuelle Azure et le stockage peut-il être chiffré ?

Le trafic de données (le trafic à partir du client NFSv3 ou SMBv3 fourni par les volumes Azure NetApp Files) n’est pas chiffré. Toutefois, le trafic à partir d’une machine virtuelle Azure (exécutant un client SMB ou NFS) vers Azure NetApp Files est aussi sécurisé que tout autre trafic entre machines virtuelles Azure. Ce trafic est local pour le réseau du centre de données Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Le stockage peut-il être chiffré au repos ?

Tous les volumes Azure NetApp Files sont chiffrés en suivant la norme FIPS 140-2. Toutes les clés sont gérées par le service Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Comment les clés de chiffrement sont-elles gérées ? 

La gestion des clés pour Azure NetApp Files est assurée par le service.  Actuellement, les clés gérées par l’utilisateur (Bring Your Own Key (BYOK)) ne sont pas prises en charge.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Puis-je configurer les règles de stratégie d’exportation NFS pour contrôler l’accès à la cible de montage du service Azure NetApp Files ?


Oui, vous pouvez configurer jusqu'à cinq règles dans une même stratégie d’exportation NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files prend-il en charge les groupes de sécurité réseau ?

Non, actuellement vous ne pouvez pas appliquer de Groupes de sécurité réseau au sous-réseau délégué d’Azure NetApp Files ou aux interfaces réseau créées par le service.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Puis-je utiliser Azure IAM avec Azure NetApp Files ?

Oui, Azure NetApp Files prend en charge les fonctionnalités RBAC avec Azure IAM.

## <a name="performance-faqs"></a>Questions fréquentes relatives aux performances

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Que puis-je faire pour optimiser ou ajuster les performances d’Azure NetApp Files ?

Vous pouvez effectuer les actions suivantes en fonction de vos exigences en matière de performances : 
- Assurez-vous que la taille de la machine virtuelle est adaptée.
- Activez la mise en réseau accélérée pour la machine virtuelle.
- Sélectionnez un niveau de service et une taille adaptés pour le pool de capacité.
- Créez un volume avec la taille de quota de votre choix pour la capacité et les performances.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Comment convertir les niveaux de service basés sur le débit d’Azure NetApp Files en IOPS ?

Vous pouvez convertir les Mo/s en IOPS à l’aide de la formule suivante :  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Comment modifier le niveau de service d’un volume ?

La modification du niveau de service d’un volume n’est pas prise en charge actuellement.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Comment surveiller les performances d’Azure NetApp Files ?

Azure NetApp Files fournit des métriques de performance des volumes. Vous pouvez également utiliser Azure Monitor pour surveiller les métriques d’utilisation pour Azure NetApp Files.  Consultez [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md) pour obtenir la liste des métriques de performance pour Azure NetApp Files.

## <a name="nfs-faqs"></a>Questions fréquentes sur NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Je veux monter un volume automatiquement lorsqu’une machine virtuelle Azure est démarrée ou redémarrée.  Comment configurer mon hôte pour les volumes NFS persistants ?

Pour qu’un volume NFS soit monté automatiquement au moment du démarrage ou du redémarrage de la machine virtuelle, ajoutez une entrée au fichier `/etc/fstab` sur l’hôte. 

Par exemple : `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    L’adresse IP du volume Azure NetApp Files se trouvant dans le volet Propriétés de volume
- $FILEPATH  
    Le chemin d’exportation du volume Azure NetApp Files
- $MOUNTPOINT  
    Le répertoire créé sur l’hôte Linux utilisé pour monter l’exportation NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Pourquoi la commande DF sur le client NFS n’affiche-t-elle pas la taille du volume approvisionné ?

La taille du volume signalée dans DF est la taille maximale que peut atteindre le volume Azure NetApp Files. La taille du volume Azure NetApp Files dans la commande DF ne représente pas le quota ou la taille du volume.  Vous pouvez obtenir la taille du volume Azure NetApp Files ou son quota par le biais du portail Azure ou de l’API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quelle version de NFS Azure NetApp Files prend-il en charge ?

Azure NetApp Files prend actuellement en charge NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Comment activer le root squash ?

Le root squash n’est actuellement pas pris en charge.

## <a name="smb-faqs"></a>Questions fréquentes sur SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Est-ce qu’une connexion Active Directory est requise pour l’accès à SMB ? 

Oui, vous devez créer une connexion Active Directory avant de déployer un volume SMB. Les contrôleurs de domaine spécifiés doivent être accessibles par le sous-réseau délégué d’Azure NetApp Files pour une connexion réussie.  Consultez [Créer un volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) pour plus d’informations. 

### <a name="how-many-active-directory-connections-are-supported"></a>Combien de connexions Active Directory sont prises en charge ?

Azure NetApp Files prend actuellement en charge une seule connexion Active Directory par abonnement. En outre, la connexion Active Directory est spécifique à un seul compte NetApp ; elle n’est pas partagée entre les comptes. 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Est-ce qu’Azure NetApp Files prend en charge Azure Active Directory ? 

[Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) et [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) sont tous deux pris en charge. Vous pouvez utiliser des contrôleurs de domaine Active Directory existants avec Azure NetApp Files. Les contrôleurs de domaine peuvent résider dans Azure en tant que machines virtuelles ou en local via ExpressRoute ou S2S VPN. Azure NetApp Files ne prend pas en charge la jonction AD pour [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) pour l’instant.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quelles versions de Windows Server Active Directory sont prises en charge ?

Azure NetApp Files prend en charge les versions Windows Server 2008r2SP1-2019 d’Active Directory Domain Services.

## <a name="capacity-management-faqs"></a>Questions fréquentes (FAQ) sur la gestion de la capacité

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Comment surveiller l’utilisation du pool de capacité et du volume Azure NetApp Files ? 

Azure NetApp Files fournit des métriques de pool de capacité et de volume d’utilisation. Vous pouvez également utiliser Azure Monitor pour surveiller l’utilisation d’Azure NetApp Files. Consultez [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md) pour plus d’informations. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Puis-je gérer Azure NetApp Files via l’Explorateur Stockage Azure ?

Non. Azure NetApp Files n’est pas pris en charge par l’Explorateur Stockage Azure.

## <a name="data-migration-and-protection-faqs"></a>Questions fréquentes sur la migration et la protection des données

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Comment migrer des données vers Azure NetApp Files ?
Azure NetApp Files fournit les volumes NFS et SMB.  Vous pouvez utiliser n’importe quel outil de copie basé sur le fichier pour migrer des données vers le service. 

NetApp offre une solution SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer les données NFS ou SMB dans des exportations NFS ou partages SMB d’Azure NetApp Files. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier les données. Pour NFS, vous pouvez utiliser des outils de charge de travail, comme [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données source dans un volume Azure NetApp Files. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou de dossier. 

La configuration requise pour la migration de données en local vers Azure NetApp Files est la suivante : 

- Vérifiez qu’Azure NetApp Files est disponible dans la région Azure cible.
- Validez la connectivité réseau entre la source et l’adresse IP du volume Azure NetApp Files cible. Le transfert de données entre la machine locale et le service Azure NetApp Files est pris en charge via ExpressRoute.
- Créez le volume Azure NetApp Files cible.
- Transférez les données de la source vers le volume cible à l’aide de votre outil de copie de fichiers par défaut.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Comment créer une copie d’un volume Azure NetApp Files dans une autre région Azure ?
    
Azure NetApp Files fournit les volumes NFS et SMB.  N’importe quel outil de copie basé sur le fichier peut être utilisé pour répliquer des données entre régions Azure. 

NetApp offre une solution SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer les données NFS ou SMB dans des exportations NFS ou partages SMB d’Azure NetApp Files. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier les données. Pour NFS, vous pouvez utiliser des outils de charge de travail, comme [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données source dans un volume Azure NetApp Files. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou de dossier. 

La configuration requise pour la réplication d’un volume Azure NetApp Files vers une autre région Azure est la suivante : 
- Vérifiez qu’Azure NetApp Files est disponible dans la région Azure cible.
- Validez la connectivité réseau entre les réseaux virtuels dans chaque région. Actuellement, l’homologation globale entre réseaux virtuels n’est pas prise en charge.  Vous pouvez établir la connectivité entre réseaux virtuels en les liant à un circuit ExpressRoute ou à l’aide d’une connexion S2S VPN. 
- Créez le volume Azure NetApp Files cible.
- Transférez les données de la source vers le volume cible à l’aide de votre outil de copie de fichiers par défaut.

### <a name="is-migration-with-azure-data-box-supported"></a>Est-ce que la migration avec Azure Data Box est prise en charge ?

Non. Azure Data Box ne prend pas en charge Azure NetApp Files actuellement. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Est-ce que la migration avec le service Azure Import/Export est prise en charge ?

Non. Le service Azure Import/Export ne prend pas en charge Azure NetApp Files actuellement.

## <a name="next-steps"></a>Étapes suivantes  

- [Questions fréquentes (FAQ) sur Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Questions fréquentes (FAQ) sur les réseaux virtuels Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
