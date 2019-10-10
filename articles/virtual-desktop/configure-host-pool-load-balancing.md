---
title: Configurer la méthode d’équilibrage de charge Windows Virtual Desktop - Azure
description: Comment configurer la méthode d’équilibrage de charge pour un environnement Windows Virtual Desktop ?
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 3a940dbf592087878cb9dd19f856f1a3d94291c5
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676776"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurer la méthode d’équilibrage de charge de Windows Virtual Desktop

La configuration de la méthode d’équilibrage de charge pour un pool d’hôtes vous permet d’ajuster l’environnement Windows Virtual Desktop afin de mieux répondre à vos besoins.

>[!NOTE]
> Cela ne s’applique pas à un pool d'hôtes de bureau persistant, car les utilisateurs disposent toujours d'un mappage 1:1 vers un hôte de session au sein du pool d’hôtes.

## <a name="configure-breadth-first-load-balancing"></a>Configurer l’équilibrage de charge de largeur

L’équilibrage de charge de largeur correspond à la configuration par défaut des nouveaux pools d'hôtes non persistants. L’équilibrage de charge de largeur répartit les nouvelles sessions utilisateur entre tous les hôtes de session du pool. Lorsque vous configurez l'équilibrage de charge de largeur, vous pouvez définir une limite maximale de sessions par hôte de session du pool.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de largeur sans ajuster la limite maximale de sessions, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de largeur et utiliser une nouvelle limite maximale de sessions, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurer l’équilibrage de charge de profondeur

L’équilibrage de charge de profondeur répartit les nouvelles sessions utilisateur vers l'hôte de session disponible doté du plus grand nombre de connexions, sans avoir atteint sa limite maximale de sessions. Lorsque vous configurez l'équilibrage de charge de profondeur, vous **devez** définir une limite maximale de sessions par hôte de session du pool.

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de profondeur, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
