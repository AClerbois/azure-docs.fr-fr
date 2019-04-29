---
title: Résoudre les problèmes de mise à niveau du fournisseur Microsoft Azure Site Recovery | Microsoft Docs
description: Comprendre et
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565699"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Résoudre les problèmes de mise à niveau du fournisseur Microsoft Azure Site Recovery

Cet article aide à résoudre les problèmes susceptibles de provoquer des défaillances lors de la mise à niveau du fournisseur Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Échec de la mise à niveau : la dernière version du fournisseur Site Recovery est déjà installée

Lors de la mise à niveau du fournisseur Microsoft Azure Site Recovery (DRA), le programme d’installation unifiée échoue en émettant le message d’erreur suivant :

La mise à niveau n’est pas prise en charge, car une version plus récente du logiciel est déjà installée.

Suivez les étapes ci-dessous pour effectuer la mise à niveau :

1. Téléchargez le programme d’installation unifiée de Microsoft Azure Site Recovery :
   1. Dans la section « Liens des correctifs cumulatifs actuellement pris en charge » de l’article [Mises à jour de service dans Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups), sélectionnez le fournisseur que vous souhaitez installer.
   2. Sur la page des correctifs cumulatifs, localisez la section **Informations de mise à jour** et téléchargez le correctif cumulatif du programme d’installation unifiée de Microsoft Azure Site Recovery.

2. Ouvrez une invite de commandes et accédez au dossier dans lequel vous avez téléchargé le fichier d’installation unifiée. Extrayez les fichiers d’installation du téléchargement avec la commande suivante : MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;chemin d'accès du dossier des fichiers extraits&gt;.
    
    Exemple de commande :

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. Dans l’invite de commandes, accédez au dossier dans lequel vous avez extrait les fichiers et exécutez les commandes d’installation suivantes :
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Retournez dans le dossier où vous avez téléchargé le programme d’installation unifiée et exécutez MicrosoftAzureSiteRecoveryUnifiedSetup.exe pour terminer la mise à niveau. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Échec de mise à niveau, car le dossier 3 rd-party renommé

Pour la mise à niveau réussisse, le dossier 3 rd-party ne doit pas être renommé.

Résolution du problème :

1. Lancez l’Éditeur du Registre (regedit.exe) et ouvrez la branche HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Vérifiez la valeur de la clé `Build_Version`. S’il s’agit de la dernière version, réduisez le numéro de version. Par exemple, si la dernière version est 9.22. \* et que la clé `Build_Version` est définie sur cette valeur de clé, réduisez-la à 9.21.\*.
1. Téléchargez la dernière version du programme d’installation unifiée de Microsoft Azure Site Recovery :
   1. Dans la section « Liens des correctifs cumulatifs actuellement pris en charge » de l’article [Mises à jour de service dans Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups), sélectionnez le fournisseur que vous souhaitez installer.
   2. Sur la page des correctifs cumulatifs, localisez la section **Informations de mise à jour** et téléchargez le correctif cumulatif du programme d’installation unifiée de Microsoft Azure Site Recovery.
1. Ouvrez une invite de commandes et accédez au dossier dans lequel vous avez téléchargé le fichier d’installation unifiée. Extrayez les fichiers d’installation du téléchargement avec la commande suivante : MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;chemin d'accès du dossier des fichiers extraits&gt;.

    Exemple de commande :

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. Dans l’invite de commandes, accédez au dossier dans lequel vous avez extrait les fichiers et exécutez les commandes d’installation suivantes :
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Utilisez le Gestionnaire des tâches pour suivre la progression de l’installation. Lorsque le processus CX_THIRDPARTY_SETUP. EXE n’est plus visible dans le Gestionnaire des tâches, passez à l’étape suivante.
1. Vérifiez que C:\thirdparty existe et que le dossier contient les bibliothèques RRD.
1. Retournez dans le dossier où vous avez téléchargé le programme d’installation unifiée et exécutez MicrosoftAzureSiteRecoveryUnifiedSetup.exe pour terminer la mise à niveau. 