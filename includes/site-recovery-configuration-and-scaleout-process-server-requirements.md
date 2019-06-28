---
title: Fichier Include
description: Fichier Include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176770"
---
**Exigences des serveur de configuration/de traitement**

**Composant** | **Prérequis** 
--- | ---
**PARAMÈTRES MATÉRIELS** | 
Cœurs d’unité centrale | 8 
RAM | 16 Go
Nombre de disques | 3, y compris le disque du système d’exploitation, le disque de cache du serveur de processus et le lecteur de conservation pour la restauration automatique 
Espace disque disponible (cache du serveur de traitement) | 600 Go
Espace disque disponible (disque de rétention) | 600 Go
 | 
**PARAMÈTRES LOGICIELS** | 
Système d’exploitation | Windows Server 2012 R2 <br> Windows Server 2016
Paramètres régionaux du système d’exploitation | Anglais (en-us)
Rôles Windows Server | N’activez pas ces rôles : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Stratégies de groupe | N’activez pas ces stratégies de groupe : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Aucun site web par défaut préexistant <br> - Aucune application/aucun site web préexistants ne doivent écouter le port 443 <br>- Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
| 
**PARAMÈTRES RÉSEAU** | 
Type d’adresse IP | statique 
Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données) 
Type de carte réseau | VMXNET3 (si le serveur de configuration est une machine virtuelle VMware)
 |
**Accès Internet**  (Le serveur doit également accéder aux URL suivantes - directement ou par le biais d’un proxy) :|
\*.backup.windowsazure.com | Élément utilisé pour la coordination et le transfert des données répliquées
\*.store.core.windows.net | Élément utilisé pour la coordination et le transfert des données répliquées
\*.blob.core.windows.net | Utilisé pour l’accès au compte de stockage qui stocke les données répliquées
\*.hypervrecoverymanager.windowsazure.com | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication
https:\//management.azure.com | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication 
*.services.visualstudio.com | Utilisé dans le cadre de la télémétrie (facultatif)
time.nist.gov | Éléments utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale.
time.windows.com | Éléments utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | L’installation OVF nécessite l’accès à ces URL. Azure Active Directory utilise ces adresses pour le contrôle d’accès et la gestion des identités
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Pour terminer le téléchargement de MySQL
|
**LOGICIELS À INSTALLER** | 
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) doit être installé si le serveur de configuration est en cours d’exécution sur une machine virtuelle VMware.
MYSQL | MySQL doit être installé. Vous pouvez l’installer manuellement ou laisser Site Recovery le faire. (Reportez-vous à la [configuration des paramètres](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) pour plus d’informations)

**Exigences de dimensionnement des serveurs de configuration/de traitement**

**UC** | **Mémoire** | **Disque cache** | **Taux de modification des données** | **Machines répliquées**
--- | --- | --- | --- | ---
8 processeurs virtuels<br/><br/> 2 sockets * 4 cœurs \@ 2,5 GHz | 16 Go | 300 Go | 500 Go ou moins | < 100 machines
12 processeurs virtuels<br/><br/> 2 sockets * 6 cœurs \@ 2,5 GHz | 18 Go | 600 Go | 500 Go à 1 To | 100 à 150 machines
16 processeurs virtuels<br/><br/> 2 sockets * 8 cœurs \@ 2,5 GHz | 32 Go | 1 To | 1 à 2 To | 150 à 200 machines

