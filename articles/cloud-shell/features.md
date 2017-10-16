---
title: "Fonctionnalités de Bash dans Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Vue d’ensemble des fonctionnalités de Bash dans Azure Cloud Shell"
services: Azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 158b0b65ba53630050275c910f2a7acfc39b8759
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Fonctionnalités et outils pour Bash dans Azure Cloud Shell

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Outils et fonctionnalités dans [PowerShell](features-powershell.md) est également disponible.

Bash dans Cloud Shell s’exécute sur `Ubuntu 16.04 LTS`.

## <a name="features"></a>Caractéristiques

### <a name="secure-automatic-authentication"></a>Authentification automatique sécurisée

Bash dans Cloud Shell authentifie automatiquement et en toute sécurité l’accès au compte pour Azure CLI 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH sur les machines virtuelles Linux Azure

La création d’une machine virtuelle Linux à partir d’Azure CLI 2.0 peut créer une clé SSH par défaut et la placer dans votre répertoire `$Home` actif. Le placement des clés SSH dans `$Home` permet de diriger les connexions SSH vers les machines virtuelles Linux Azure directement à partir de l’interpréteur de Cloud Shell. Les clés sont stockées dans acc_<user>.img dans votre partage de fichiers. Utilisez les meilleures pratiques lors de l’utilisation ou du partage d’accès au partage de fichiers ou aux clés.

### <a name="home-persistence-across-sessions"></a>Persistance de $Home entre les sessions

Pour conserver les fichiers entre les sessions, Cloud Shell vous guide à travers le processus d’association d’un partage de fichiers Azure au premier lancement.
Par la suite, Cloud Shell associera automatiquement votre espace de stockage (monté sous forme de `$Home\clouddrive`) pour toutes les sessions à venir.
En outre, dans Bash dans Cloud Shell, le répertoire `$Home` est conservé en tant que fichier .img dans le partage de fichiers Azure.
Les fichiers en dehors de `$Home` et de l’état de la machine ne sont pas conservés entre les sessions.

[En savoir plus sur les fichiers persistants dans Bash dans Cloud Shell.](persisting-shell-storage.md)

## <a name="tools"></a>Outils

|Catégorie   |Nom   |
|---|---|
|Interpréteur de commandes Linux|Bash<br> sh               |
|Outils Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) et [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Lot chantier](https://github.com/Azure/batch-shipyard) <br>    |
|Éditeurs de texte           |vim<br> nano<br> emacs       |
|Contrôle de code source         |git                    |
|Outils de génération            |make<br> maven<br> npm<br> pip         |
|Conteneurs             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DC/OS](https://github.com/dcos/dcos-cli)         |
|Bases de données              |Client MySQL<br> Client PostgreSQL<br> [Utilitaire sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Autres                  |Client iPython<br> [CLI Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>Support multilingue

|language   |Version   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell  |[6.0 (bêta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 et 3.5 (par défaut)|

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md) <br>
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
