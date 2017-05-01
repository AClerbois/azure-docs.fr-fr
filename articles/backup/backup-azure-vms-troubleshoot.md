---
title: "Résoudre les erreurs de sauvegarde avec les machines virtuelles Azure | Microsoft Docs"
description: "Dépannage de la sauvegarde et de la restauration de machines virtuelles Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2017
ms.author: trinadhk;markgal;jpallavi;
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 61f62d606b44b3390e6500ea2b30b20d7d2929ff
ms.lasthandoff: 04/06/2017


---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Dépannage de la sauvegarde de machine virtuelle Azure
> [!div class="op_single_selector"]
> * [Coffre Recovery Services](backup-azure-vms-troubleshoot.md)
> * [Archivage de sauvegarde](backup-azure-vms-troubleshoot-classic.md)
>
>

Vous pouvez résoudre les erreurs rencontrées pendant l’utilisation d’Azure Backup à l’aide des informations figurant dans le tableau ci-dessous.

## <a name="backup"></a>Sauvegarde
| Détails de l’erreur | Solution de contournement |
| --- | --- |
| Impossible d’effectuer l’opération, car la machine virtuelle n’existe plus. - Arrêtez la protection de la machine virtuelle sans supprimer les données de sauvegarde. Plus de détails à l’adresse http://go.microsoft.com/fwlink/?LinkId=808124 |Cela se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle à sauvegarder. Pour résoudre ce problème : <ol><li> Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources [nom du service cloud]<br>(OU)</li><li> Arrêtez la protection de la machine virtuelle en supprimant ou non les données de sauvegarde. [En savoir plus](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané. -Vérifiez que la machine virtuelle a accès à Internet. En outre, mettez à jour l’agent de machine virtuelle comme l’indique le guide de dépannage à l’adresse http://go.microsoft.com/fwlink/?LinkId=800034 |Cette erreur est générée si un problème existe avec l’agent de machine virtuelle ou si l’accès réseau à l’infrastructure Azure est bloqué. [En savoir plus](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) sur le débogage des problèmes d’instantanés de machines virtuelles.<br> Si l’agent de machine virtuelle n’est pas à l’origine des problèmes, redémarrez la machine virtuelle. Il arrive que l’état incorrect d’une machine virtuelle provoque des problèmes et le redémarrage de la machine virtuelle réinitialise cet « état incorrect » |
| Échec de l’opération d’extension de Recovery Services. - Vérifiez que l’agent de machine virtuelle le plus récent est présent sur la machine virtuelle et que le service de l’agent est en cours d’exécution. Relancez l’opération de sauvegarde ; en cas d’échec, contactez le support Microsoft. |Cette erreur est levée lorsque l’agent de machine virtuelle est obsolète. Consultez la section « Mise à jour de l’agent de machine virtuelle » ci-dessous pour mettre à jour l’agent de machine virtuelle. |
| La machine virtuelle n’existe pas. -Vérifiez que cette machine virtuelle existe ou sélectionnez une autre machine virtuelle. |Cela se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle pour effectuer la sauvegarde. Pour résoudre ce problème : <ol><li> Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources [nom du service cloud]<br>(OU)<br></li><li>Arrêtez la protection de la machine virtuelle sans supprimer les données de sauvegarde. [En savoir plus](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Échec de l’exécution de la commande. - Une autre opération est en cours sur cet élément. Attendez que l’opération précédente aboutisse, puis réessayez. |Une sauvegarde existante sur la machine virtuelle est en cours d’exécution, et aucun nouveau travail ne peut être démarré pendant l’exécution d’un travail existant. |
| La copie des disques durs virtuels à partir de l’archive de sauvegarde a expiré - Retentez l’opération dans quelques minutes. Si le problème persiste, contactez le support technique Microsoft. | Cela se produit en cas d’erreur temporaire côté stockage ou si le service de sauvegarde n’obtient pas suffisamment d’opérations d’E/S par seconde à partir du compte de stockage qui héberge la machine virtuelle afin de transférer des données avant expiration du délai vers le coffre. Assurez-vous d’avoir suivi les [meilleures pratiques](backup-azure-vms-introduction.md#best-practices) lors de la configuration de la sauvegarde. Essayez de déplacer la machine virtuelle vers un autre compte de stockage qui n’est pas chargé et recommencez la sauvegarde.|
| Échec de l’opération de sauvegarde avec une erreur interne - Retentez l’opération dans quelques minutes. Si le problème persiste, contactez le support technique Microsoft. |Vous pouvez obtenir cette erreur pour 2 raisons :  <ol><li> Il existe un problème temporaire d’accès au stockage de la machine virtuelle. Vérifiez [l’état Azure](https://azure.microsoft.com/en-us/status/) pour voir si des problèmes relatifs au calcul, au stockage ou à la mise en réseau dans la région persistent. Recommencez le travail de sauvegarde une fois le problème résolu. <li>La machine virtuelle d’origine a été supprimée et, par conséquent, le point de récupération ne peut pas être choisi. Afin de conserver les données de sauvegarde d’une machine virtuelle supprimée, mais de supprimer les erreurs de sauvegarde, ôtez la protection de la machine virtuelle, puis choisissez l’option de conservation des données. Cette action arrête le travail planifié de sauvegarde et les messages d’erreur récurrents. |
| Échec de l’installation de l’extension Azure Recovery Services sur l’élément sélectionné - L’agent de machine virtuelle est un composant requis pour l’extension Azure Recovery Services. Installez d’abord l’agent de machine virtuelle Azure, puis recommencez l’opération d’inscription. |<ol> <li>Vérifiez si l’agent de machine virtuelle a été installé correctement. <li>Vérifiez que l’indicateur de la configuration de la machine virtuelle est défini correctement.</ol> [En savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |
| L’installation de l’extension a échoué en renvoyant l’erreur « COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator |Cela signifie généralement que le service COM+ ne fonctionne pas. Pour résoudre ce problème, contactez le support Microsoft. |
| L’opération d’instantané a échoué, en renvoyant l’erreur d’opération VSS « Ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du panneau de configuration. |Désactivez BitLocker pour tous les lecteurs sur l’ordinateur virtuel et vérifiez si le problème VSS est résolu |
| La machine virtuelle n’est pas dans un état permettant les sauvegardes. |<ul><li>Vérifiez si la machine virtuelle est dans un état transitoire situé entre En cours d’exécution et Arrêt. Si c’est le cas, attendez que la machine virtuelle passe à un de ces états avant de déclencher à nouveau la sauvegarde. <li> Si la machine virtuelle est de type Linux et utilise le module de noyau [Linux à sécurité avancée), vous devez exclure le chemin d’accès de l’agent Linux (_/var/lib/waagent_) à partir de la stratégie de sécurité pour vous assurer de l’installation des extensions de sauvegarde.  |
| Machine virtuelle Azure introuvable |Cela se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle pour effectuer la sauvegarde. Pour résoudre ce problème : <ol><li>Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources [nom du service cloud] <br>(OU) <li> Désactivez la protection de cette machine virtuelle afin que les travaux de sauvegarde ne soient pas créés. </ol> |
| L’agent de machine virtuelle n’est pas présent sur la machine virtuelle. Veuillez installer les logiciels prérequis et l’agent de machine virtuelle, puis relancer l’opération. |[En savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et la validation de cette opération. |
| Échec d’opération de capture d’instantané en raison de l’état incorrect d’enregistreurs VSS |Vous devez redémarrer les enregistreurs VSS (Volume Shadow copy Service) qui sont dans un état erroné. Pour ce faire, exécutez _enregistreurs de liste vssadmin_ à partir d’une invite de commandes avec élévation de privilèges. La sortie contient tous les enregistreurs VSS et leur état. Pour chaque enregistreur VSS dont l’état n’est pas « [1] Stable », redémarrez l’enregistreur VSS en exécutant les commandes suivantes à partir d’une invite de commandes avec élévation de privilèges<br> _net stop serviceName_ <br> _net start serviceName_|
| L’opération de capture d’instantané a échoué en raison d’une erreur d’analyse de la configuration |Cela se produit en raison modifications des autorisations sur le répertoire MachineKeys : _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Exécutez la commande ci-dessous et vérifiez que les autorisations sur le répertoire MachineKeys sont celles par défaut :<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Les autorisations par défaut sont :<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Si vous voyez des autorisations sur le répertoire MachineKeys différente des autorisations par défaut, suivez étapes ci-dessous pour les corriger, supprimez le certificat et déclenchez la sauvegarde.<ol><li>Corrigez les autorisations sur le répertoire MachineKeys.<br>À l’aide des propriétés de sécurité de l’Explorateur et des paramètres de sécurité avancés dans le répertoire, réinitialisez les autorisations sur les valeurs par défaut, supprimez tout objet utilisateur supplémentaire (dont la valeur n’est pas une valeur par défaut) du répertoire et assurez-vous que les autorisations « Everyone » bénéficient d’un accès spécial pour :<br>- répertorier les fichiers d’un dossier <br>- lire les attributs <br>- lire des attributs étendus <br>- créer des fichiers / écrire des données <br>- créer des dossiers / ajouter des données<br>- écrire des attributs<br>- écrire des attributs étendus<br>- lire des autorisations<br><br><li>Supprimez le certificat comportant le champ « Issued To » = « Windows Azure Service Management for Extensions » ou « Windows Azure CRP Certificate Generator ».<ul><li>[Ouvrez la console Certificats (ordinateur local)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Supprimez le certificat (sous Personal -> Certificates) comportant le champ « Issued To » = « Windows Azure Service Management for Extensions » ou « Windows Azure CRP Certificate Generator ».</ul><li>Déclenchez la sauvegarde de machine virtuelle. </ol>|
| La validation a échoué, car la machine virtuelle est chiffrée avec une clé BEK uniquement. Les sauvegardes peuvent être activées uniquement pour les machines virtuelles chiffrées aussi bien avec des clés BEK qu’avec des clés KEK. |La machine virtuelle doit être chiffrée à l’aide de la clé de chiffrement BitLocker et de la clé KEK (Key Encryption Key, clé de chiffrement de clé). Après l’application de ce chiffrement, la sauvegarde doit être activée. |
|L’installation de l’extension de capture instantanée de a échoué en renvoyant une erreur : COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator | Essayez de démarrer le service Windows « COM+ System Application » (dans une invite de commandes avec élévation de privilèges, _net start COMSysApp_). <br>En cas d’échec lors du démarrage, suivez les étapes ci-dessous :<ol><li> Vérifiez que le compte d’ouverture de session du service « Distributed Transaction Coordinator » est « Network Service ». Si ce n’est pas le cas, remplacez-le par « Network Service », redémarrez ce service et réessayez de démarrer le service « COM+ System Application ».<li>S’il ne démarre toujours pas, désinstallez/installez le service « Distributed Transaction Coordinator » en suivant les étapes ci-dessous :<br> - Arrêtez le service MSDTC<br> - Ouvrez une invite de commandes (cmd) <br> - Exécutez la commande « msdtc -uninstall » <br> - Exécutez la commande « msdtc -install » <br> - Lancez le service MSDTC<li>Démarrer le service Windows « COM+ System Application » et, après son démarrage, déclenchez la sauvegarde à partir du portail.</ol> |
| Impossible de figer un ou plusieurs points de montage de la machine virtuelle pour prendre une capture instantanée cohérente au niveau système de fichiers | <ol><li>Vérifiez l’état du système de fichiers de tous les périphériques montés avec la commande _« tune2fs »_.<br> Par exemple : tune2fs -l /dev/sdb1 \| grep "Filesystem state" <li>Démontez les périphériques pour lesquels l’état du système de fichiers n’est pas propre avec la commande _« unmount »_. <li> Exécutez une vérification FileSystemConsistency sur ces périphériques avec la commande _« fsck »_. <li> Remontez les périphériques et tentez une sauvegarde.</ol> |
| Échec de l’opération de capture instantanée en raison de l’échec de la création du canal de communication réseau sécurisé | <ol><Li> Ouvrez l’Éditeur du Registre en exécutant regedit.exe avec élévation de privilèges. <li> Identifiez toutes les versions de. NET Framework présentes dans le système. Elles se trouvent dans la hiérarchie de la clé de Registre « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft ». <li> Pour chaque .NET Framework présent dans la clé de Registre, ajoutez la clé suivante : <br> "SchUseStrongCrypto"=dword:00000001 </ol>| 
| Échec de l’opération de capture instantanée en raison de l’échec de l’installation de Redistribuable Visual C++ pour Visual Studio 2012 | Accédez à C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion et installez vcredist2012_x64. Vérifiez que la valeur de la clé de Registre autorisant l’installation de ce service est correcte, c’est-à-dire que la clé de Registre _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ a pour valeur 3 et non 4. Si vous rencontrez toujours des problèmes d’installation, redémarrez le service d’installation en exécutant _MSIEXEC /UNREGISTER_ suivi de _MSIEXEC /REGISTER_ dans une invite de commandes avec élévation de privilèges.  |


## <a name="jobs"></a>Travaux
| Détails de l’erreur | Solution de contournement |
| --- | --- |
| L’annulation n’est pas prise en charge pour ce type de tâche. Veuillez patienter jusqu’à ce que le travail soit terminé. |Aucun |
| Le travail n’est pas dans un état annulable. Veuillez patienter jusqu’à ce que le travail soit terminé. <br>OU<br> L’état du travail sélectionné n’est pas annulable. Veuillez patienter jusqu’à ce que le travail soit terminé. |Selon toute probabilité, le travail est presque terminé. Attendez que le travail soit terminé.|
| Impossible d’annuler le travail, car il n’est pas en cours. L’annulation est prise en charge uniquement pour les travaux en cours. Veuillez tenter l’annulation de la tâche en cours d’exécution. |Cet événement est dû à un état provisoire. Attendez une minute et relancez l’opération d’annulation |
| Impossible d’annuler le travail. Veuillez patienter jusqu’à la fin du travail. |Aucun |

## <a name="restore"></a>Restauration
| Détails de l’erreur | Solution de contournement |
| --- | --- |
| Échec de la restauration avec une erreur interne du cloud |<ol><li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec des paramètres DNS. Vous pouvez vérifier  <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si une adresse est configurée, cela signifie que les paramètres DNS sont configurés.<br> <li>Le service cloud sur lequel vous tentez d’effectuer la restauration est configuré avec une adresse IP réservée, et les machines virtuelles existantes dans le service cloud sont à l’état arrêté.<br>Vous pouvez vérifier qu’un service cloud a une adresse IP réservée à l’aide des applets de commande PowerShell suivantes :<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Vous essayez de restaurer une machine virtuelle avec les configurations réseau spéciales suivantes dans le même service cloud. <br>- Machines virtuelles avec configuration d’un équilibreur de charge (interne et externe)<br>- Machines virtuelles avec plusieurs adresses IP réservées<br>- Machines virtuelles avec plusieurs NIC<br>Sélectionnez un service cloud dans l’IU ou consultez les [considérations relatives à la restauration](backup-azure-arm-restore-vms.md#restoring-vms-with-special-network-configurations) des machines virtuelles affichant des configurations de réseau spéciales</ol> |
| Le nom DNS sélectionné est déjà utilisé. Veuillez spécifier un autre nom DNS et réessayez. |Le nom DNS fait référence au nom du service cloud (se termine généralement par .cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom de machine virtuelle pendant la restauration. <br><br> Cette erreur ne s’affiche que pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell va réussir, car elle ne fait que restaurer les disques et ne crée pas de machine virtuelle. L’erreur se rencontre lorsque la machine virtuelle est explicitement créée par vos soins après l’opération de restauration du disque. |
| La configuration de réseau virtuel spécifiée n’est pas correcte. Veuillez indiquer une autre configuration de réseau virtuel et réessayez. |Aucun |
| Le service cloud spécifié utilise une adresse IP réservée, ce qui ne correspond pas à la configuration de machine virtuelle en cours de restauration. Veuillez spécifier un autre service cloud n’utilisant pas l’adresse IP réservée ou choisir un autre point de restauration. |Aucun |
| Le service cloud a atteint la limite du nombre de points de terminaison d’entrée. Recommencez l’opération en spécifiant un autre service cloud ou en utilisant un point de terminaison existant. |Aucun |
| Le coffre de stockage et le compte de stockage cible se trouvent dans deux régions différentes. Vérifiez que le compte de stockage spécifié dans l’opération de restauration se trouve dans la même région Azure que le coffre de sauvegarde. |Aucun |
| Le compte de stockage spécifié pour l’opération de restauration n’est pas pris en charge. Seuls les comptes de stockage de base/Standard avec des paramètres de réplication redondants au niveau géographique ou local sont pris en charge. Veuillez sélectionner un compte de stockage pris en charge |Aucun |
| Le type de compte de stockage spécifié pour l’opération de restauration n’est pas en ligne. Assurez-vous que le compte de stockage spécifié dans l’opération de restauration est en ligne |Cela peut se produire cas d’erreur temporaire dans le stockage Azure ou en cas de panne. Veuillez choisir un autre compte de stockage. |
| Le quota de groupe de ressources a été atteint. Veuillez supprimer certains groupes de ressources du portail Azure ou contactez le support Azure pour étendre les limites. |Aucun |
| Le sous-réseau sélectionné n’existe pas. Veuillez sélectionner un sous-réseau qui existe |Aucun |
| Le service de sauvegarde n’a pas l’autorisation d’accéder aux ressources dans votre abonnement. |Pour résoudre ce problème, commencez par restaurer les disques en suivant les étapes mentionnées dans la section **Restore backed up disks** (Restaurer les disques sauvegardés) dans [Choosing VM restore configuration](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration) (Choisir la configuration de restauration de la machine virtuelle). Après cela, suivez les étapes de PowerShell mentionnées dans [Create a VM from restored disks](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) (Créer une machine virtuelle à partir de disques restaurés) pour créer une machine virtuelle complète à partir de disques restaurés. |

## <a name="backup-or-restore-taking-time"></a>Sauvegarde ou restauration qui prend du temps
Si vous constatez que votre sauvegarde (> 12 heures) ou votre restauration (> 6 heures) prend du temps, assurez-vous de suivre les [Meilleures pratiques de sauvegarde](backup-azure-vms-introduction.md#best-practices). Vérifiez également que vos applications utilisent [le stockage Azure de manière optimale](backup-azure-vms-introduction.md#total-vm-backup-time) pour la sauvegarde. 

## <a name="vm-agent"></a>Agent VM
### <a name="setting-up-the-vm-agent"></a>Configuration de l’agent de machine virtuelle
En règle générale, l’agent de machine virtuelle est déjà présent dans les machines virtuelles qui sont créées à partir de la galerie Azure. Cependant, les machines virtuelles qui sont migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite. Pour en savoir plus, consultez la page [Installation de l’agent de machine virtuelle sur une machine virtuelle existante](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles Windows :

* Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation.
* [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

Pour les machines virtuelles Linux :

* Installez l’ [agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de github.
* [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

### <a name="updating-the-vm-agent"></a>Mise à jour de l’agent de machine virtuelle
Pour les machines virtuelles Windows :

* La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Toutefois, vous devez vous assurer qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle.

Pour les machines virtuelles Linux :

* Suivez les instructions dans la rubrique [Mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Nous **recommandons vivement** la mise à jour de l’agent uniquement par le biais de référentiel de distribution. Nous ne recommandons pas de télécharger le code de l’agent à partir de github directement et d’effectuer la mise à jour. Si le dernier agent n’est pas disponible pour la distribution, contactez le support de distribution pour obtenir des instructions sur l’installation de l’agent le plus récent. Vous pouvez vérifier les informations sur [l’agent Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) le plus récent dans le référentiel github.

### <a name="validating-vm-agent-installation"></a>Validation de l’installation de l’agent de machine virtuelle
Pour vérifier la version de l’agent de machine virtuelle sur les machines virtuelles Windows :

1. Ouvrez une session sur la machine virtuelle Azure et accédez au dossier *C:\WindowsAzure\Packages*. Le fichier WaAppAgent.exe doit être présent.
2. Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure

## <a name="troubleshoot-vm-snapshot-issues"></a>Résoudre les problèmes de capture instantanée de machine virtuelle
La sauvegarde de machines virtuelles émet des commandes de capture instantanée à destination du stockage sous-jacent. Le fait de ne pas avoir accès au stockage ou tout retard dans l’exécution d’une tâche de capture instantanée peut faire échouer le travail de sauvegarde. Voici les causes possibles de l’échec d’une tâche de capture instantanée.

1. Accès réseau au stockage bloqué par l’utilisation du groupe de sécurité réseau<br>
   Découvrez plus en détail comment [activer l’accès réseau](backup-azure-vms-prepare.md#network-connectivity) au stockage en utilisant une liste d’adresses IP approuvées ou à travers un serveur proxy.
2. Les machines virtuelles sur lesquelles la sauvegarde SQL Server est configurée peuvent provoquer des retards de tâches d’instantanés <br>
   Par défaut, la sauvegarde de machines virtuelles exécute une sauvegarde complète VSS sur les machines virtuelles Windows. Sur les machines virtuelles qui exécutent des serveurs SQL Server et sur lesquelles la sauvegarde SQL Server est configurée, cela peut occasionner des retards d’exécution des captures instantanées. Définissez la clé de Registre suivante si vous rencontrez des échecs de sauvegarde en raison de problèmes de capture instantanée.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. État de la machine virtuelle rapporté de manière incorrecte en raison de l’arrêt de la machine virtuelle dans RDP.  <br>
   Si vous avez arrêté la machine virtuelle dans RDP, retournez sur le portail pour vérifier que l’état de la machine virtuelle est correct. Si ce n’est pas le cas, arrêtez la machine virtuelle sur le portail à l’aide de l’option « Arrêter » dans son tableau de bord.
4. Si plus de quatre machines virtuelles partagent le même service cloud, configurez plusieurs stratégies de sauvegarde pour répartir les heures de sauvegarde afin qu’il n’y ait pas plus de quatre sauvegardes de machines virtuelles démarrées en même temps. Essayez de répartir les heures de début de la sauvegarde d’une heure entre les stratégies.
5. Forte sollicitation du processeur/de la mémoire sur la machine virtuelle.<br>
   Si la machine virtuelle sollicite fortement le processeur (utilisation supérieure à 90 %) ou la mémoire, la tâche de capture instantanée est mise en file d’attente, et le retard qui en résulte débouche sur une expiration de délai. En pareille situation, essayez de procéder à des sauvegardes à la demande.

<br>

## <a name="networking"></a>Mise en réseau
Comme toutes les extensions, l’extension de sauvegarde a besoin d’accéder à l’Internet public pour fonctionner. En l’absence d’accès Internet public, plusieurs cas de figure sont possibles :

* L’installation de l’extension peut échouer.
* Les opérations de sauvegarde (telles que l’instantané de disque) peuvent échouer.
* L’affichage de l’état de l’opération de sauvegarde peut échouer.

La nécessité de résoudre des adresses Internet publiques a été évoquée [ici](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vous devez vérifier la configuration DNS du réseau virtuel et vous assurer que les URI Azure peuvent être résolus.

Une fois que la résolution de noms a été effectuée correctement, l’accès aux adresses IP Azure doit également être fourni. Pour débloquer l’accès à l’infrastructure Azure, effectuez une des opérations suivantes :

1. Mettez sur liste blanche les plages IP de datacenter Azure.
   * Obtenez la liste des [adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) à débloquer.
   * Débloquez les adresses IP à l’aide de l’applet de commande [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Exécutez cette applet de commande dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécution en tant qu’administrateur).
   * Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l'accès aux adresses IP.
2. Créez un chemin d'accès pour le trafic HTTP
   * Si vous des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic. Les étapes du déploiement d’un serveur proxy HTTP sont expliquées [ici](backup-azure-vms-prepare.md#network-connectivity).
   * Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l'accès à Internet à partir du proxy HTTP.

> [!NOTE]
> Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne.  Si vous avez besoin d’une adresse IP privée statique, vous devez la configurer via la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être laissée désactivée.
> En savoir plus sur la [définition d’une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>

