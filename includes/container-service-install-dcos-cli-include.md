---
title: Installer l’interface de ligne de commande DC/OS | Microsoft Docs
description: Installer l’interface de ligne de commande DC/OS.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Conteneurs, micro-services, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557532"
---
> [!NOTE]
> Cela concerne l’utilisation des clusters ACS basés sur DC/OS. Cette opération est inutile pour les clusters ACS à base de Swarm.
> 
> 

Commencez par [vous connecter à votre cluster ACS basé sur DC/OS](../articles/container-service/container-service-connect.md). Une fois cette opération terminée, installez l’interface de ligne de commande DC/OS sur votre ordinateur client à l’aide des commandes ci-dessous :

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Si vous utilisez une ancienne version de Python, vous remarquerez peut-être des messages « InsecurePlatformWarning ». Vous pouvez ignorer ces erreurs.

Pour démarrer sans avoir à relancer votre interpréteur de commandes, exécutez la commande suivante :

```bash
source ~/.bashrc
```

Cette étape n’est pas nécessaire lorsque vous lancez de nouveaux interpréteurs de commandes.

À présent, vous pouvez confirmer l’installation de l’interface de ligne de commande :

```bash
dcos --help
```