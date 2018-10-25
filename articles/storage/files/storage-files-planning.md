---
title: Planification d’un déploiement Azure Files | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 0701049eb1aa86398e90484dbf21ef3781270fba
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831379"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planification d’un déploiement Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles via le protocole SMB standard. Comme Azure Files est entièrement managé, son déploiement dans des scénarios de production est beaucoup plus simple que le déploiement et la gestion d’un serveur de fichiers ou d’un appareil NAS. Cet article aborde les rubriques à prendre en compte lors du déploiement d’un partage de fichiers Azure pour une utilisation en production dans votre organisation.

## <a name="management-concepts"></a>Concepts de gestion
 Le diagramme suivant illustre la structure de gestion d’Azure Files :

![Structure de fichiers](./media/storage-files-introduction/files-concepts.png)

* **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Partage** : un partage de stockage de fichiers est un partage de fichiers SMB dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans la limite de la capacité totale de 5 Tio du partage de fichiers.

* **Répertoire** : hiérarchie facultative de répertoires.

* **Fichier** : fichier du partage. Un fichier peut avoir une taille de 1 Tio maximum.

* **Format d’URL** : pour les demandes adressées à un partage de fichiers Azure avec le protocole File REST, les fichiers sont accessibles via le format d’URL suivant :

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Méthode d’accès aux données
Azure Files offre deux méthodes intégrées et pratiques d’accès aux données, que vous pouvez utiliser séparément ou conjointement pour accéder à vos données :

1. **Accès direct au cloud** : tout partage de fichiers Azure peut être monté sur [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et/ou [Linux](storage-how-to-use-files-linux.md) avec le protocole SMB (Server Message Block) standard ou via l’API File REST. Avec SMB, les lectures et les écritures dans les fichiers du partage sont effectuées directement sur le partage de fichiers dans Azure. Pour un montage sur une machine virtuelle dans Azure, le client SMB dans le système d’exploitation doit prendre en charge au moins le protocole SMB 2.1. Pour un montage local, par exemple, sur une station de travail d’utilisateur, le client SMB pris en charge par la station de travail doit prendre en charge au moins le protocole SMB 3.0 (avec chiffrement). En plus du protocole SMB, les nouvelles applications ou les nouveaux services peuvent accéder directement au partage de fichiers via l’API REST de fichier, qui fournit une interface de programmation d’applications simple et scalable pour le développement de logiciels.
2. **Azure File Sync** : avec Azure File Sync, les partages peuvent être répliqués sur des serveurs Windows Server locaux ou dans Azure. Vos utilisateurs accèdent au partage de fichiers via Windows Server, tout comme avec un partage SMB ou NFS. Cela est utile dans les scénarios où les données sont consultées et modifiées loin d’un centre de données Azure, par exemple, dans une succursale. Les données peuvent être répliquées entre plusieurs points de terminaison Windows Server, c’est-à-dire entre plusieurs succursales. Enfin, les données peuvent être hiérarchisées dans Azure Files, de sorte que toutes les données sont toujours accessibles via le serveur, mais le serveur ne dispose pas d’une copie complète des données. Les données sont alors rappelées sans discontinuité quand elles sont ouvertes par l’utilisateur.

Le tableau suivant illustre la façon dont vos utilisateurs et les applications peuvent accéder à votre partage de fichiers Azure :

| | Accès direct au cloud | Azure File Sync |
|------------------------|------------|-----------------|
| Quels protocoles devez-vous utiliser ? | Azure Files prend en charge les protocoles SMB 2.1, SMB 3.0 et l’API REST de fichier. | Accédez à votre partage de fichiers Azure via un protocole pris en charge sur Windows Server (SMB, NFS, FTPS, etc.) |  
| Où exécutez-vous votre charge de travail ? | **Dans Azure** : Azure Files offre un accès direct à vos données. | **Localement avec un réseau lent** : les clients Windows, Linux et macOS peuvent monter un partage de fichiers Windows local comme cache rapide de votre partage de fichiers Azure. |
| Quel est le niveau d’ACL dont vous avez besoin ? | Au niveau du partage et des fichiers. | Au niveau du partage, des fichiers et de l’utilisateur. |

## <a name="data-security"></a>Sécurité des données
Azure Files propose plusieurs options intégrées pour garantir la sécurité des données :

* Prise en charge du chiffrement dans les deux protocoles réseau : chiffrement SMB 3.0 et API REST de fichier sur HTTPS. Par défaut : 
    * Les clients qui prennent en charge le chiffrement SMB 3.0 envoient et reçoivent des données sur un canal chiffré.
    * Les clients qui ne prennent pas en charge SMB 3.0 avec chiffrement peuvent communiquer au sein du centre de données sur SMB 2.1 ou SMB 3.0 sans chiffrement. Les clients SMB ne sont pas autorisés à communiquer entre centres de données sur SMB 2.1 ou SMB 3.0 sans chiffrement.
    * Les clients peuvent communiquer sur l’API REST de fichier avec HTTP ou HTTPS.
* Chiffrement au repos ([Chiffrement du service Stockage Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)) : Storage Service Encryption (SSE) est activé pour tous les comptes de stockage. Les données au repos sont chiffrées avec des clés entièrement gérées. Le chiffrement au repos n’augmente pas les coûts de stockage, ni ne réduit le niveau de performance. 
* Spécification facultative des données chiffrées en transit : quand Azure Files est sélectionné, il refuse l’accès aux données sur des canaux non chiffrés. Plus précisément, seules les connexions HTTPS et SMB 3.0 avec chiffrement sont autorisées. 

    > [!Important]  
    > Si vous demandez un transfert sécurisé des données, les anciens clients SMB qui ne sont pas capables de communiquer avec SMB 3.0 échouent. Pour plus d’informations, voir [Montage sur Windows](storage-how-to-use-files-windows.md), [Montage sur Linux](storage-how-to-use-files-linux.md) et [Montage sur macOS](storage-how-to-use-files-mac.md).

Pour une sécurité maximale, nous vous recommandons vivement de toujours activer le chiffrement au repos et le chiffrement des données en transit chaque fois que vous utilisez des clients modernes pour accéder à vos données. Par exemple, si vous devez monter un partage sur une machine virtuelle Windows Server 2008 R2 qui prend uniquement en charge SMB 2.1, vous devez autoriser le trafic non chiffré sur votre compte de stockage, car SMB 2.1 ne prend pas en charge le chiffrement.

Si vous utilisez Azure File Sync pour accéder à votre partage de fichiers Azure, nous utilisons toujours HTTPS et SMB 3.0 avec chiffrement pour synchroniser vos données sur vos serveurs Windows Server, que vous exigiez ou non le chiffrement des données au repos.

## <a name="file-share-performance-tiers"></a>Niveaux de performances de partage de fichiers
Azure Files prend en charge deux niveaux de performances : Standard et Premium.

* Les **partages de fichiers Standard** s’appuient sur des lecteurs de disque dur (HDD) rotatifs qui offrent des performances fiables pour les charges de travail d’E/S moins sensibles à la variabilité des performances, telles que les partages de fichiers à usage général et les environnements de Dev/Test. Les partages de fichiers standard sont disponibles uniquement dans le cadre d’un modèle de facturation avec paiement à l’utilisation.
* Les **partages de fichiers Premium (préversion)** s’appuient sur des disques SSD qui offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail les plus gourmandes en E/S. Ils sont ainsi adaptés à un vaste éventail de charges de travail telles que les bases de données, l’hébergement de site web, les environnements de développement, etc. Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement.

### <a name="provisioned-shares"></a>Partages approvisionnés
Les partages de fichiers Premium sont approvisionnés selon un ratio Gio/IOPS/débit fixe. Pour chaque Gio approvisionné, le partage reçoit un débit d’une IOPS et de 0,1 Mio/s, dans les limites maximales autorisées par partage. L’approvisionnement minimal autorisé est de 100 Gio avec un minimum d’E/S par seconde/débit. La taille du partage peut être augmentée ou réduite à tout moment, mais ne peut être réduite qu’une fois dans les 24 heures suivant la dernière augmentation.

Dans la mesure du possible, tous les partages peuvent atteindre en rafale jusqu’à trois IOPS par Gio de stockage approvisionné pendant 60 minutes ou plus, selon la taille du partage. Les nouveaux partages démarrent avec le crédit de rafale complète basé sur la capacité approvisionnée.

| Capacité allouée | 100 Gio | 500 Go | 1 Tio | 5 Tio | 
|----------------------|---------|---------|-------|-------|
| IOPS de base | 100 | 500 | 1 024 | 5 120 | 
| Limite de rafale | 300 | 1 500 | 3 072 | 15 360 | 
| Débit | 110 Mio/s | 150 Mio/s | 202 Mio/s | 612 Mio/s |

## <a name="file-share-redundancy"></a>Redondance de partage de fichiers
Azure Files prend en charge trois options de redondance des données : le stockage localement redondant (LRS), le stockage localement redondant (ZRS) et le stockage géoredondant (GRS). Les sections suivantes décrivent les différences entre les différentes options de redondance :

### <a name="locally-redundant-storage"></a>Stockage localement redondant
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Stockage redondant dans une zone
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Stockage géo-redondant
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Modèle de croissance des données
Aujourd’hui, la taille maximale d’un partage de fichiers Azure est de 5 Tio. En raison de cette limitation actuelle, vous devez prendre en compte la croissance attendue des données quand vous déployez un partage de fichiers Azure. 

Vous pouvez synchroniser plusieurs partages de fichiers Azure sur un même serveur de fichiers Windows avec Azure File Sync. Cela vous permet d’inclure dans Azure File Sync des partages de fichiers plus anciens et très volumineux que vous pouvez avoir localement. Pour plus d’informations, voir [Planification d’un déploiement Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Méthode de transfert des données
Il existe de nombreuses options pour facilement transférer en bloc les données d’un partage de fichiers existant, par exemple, un partage de fichiers local, dans Azure Files. En voici quelques-unes connues (liste non exhaustive) :

* **Azure File Sync** : lors de la première synchronisation entre un partage de fichiers Azure (un « point de terminaison cloud ») et un espace de noms de répertoire Windows (un « point de terminaison de serveur »), Azure File Sync réplique toutes les données du partage de fichiers existant sur Azure Files.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : le service Azure Import/Export vous permet de transférer de façon sécurisée de grandes quantités de données sur un partage de fichiers Azure en expédiant des disques durs à un centre de données Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**  : Robocopy est un outil de copie bien connu fourni avec Windows et Windows Server. Robocopy peut servir à transférer des données dans Azure Files en montant le partage de fichiers localement, puis en utilisant l’emplacement monté comme destination de la commande Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**  : AzCopy est un utilitaire de ligne de commande conçu pour copier avec des performances optimales des données vers et à partir d’Azure Files, ou de Stockage Blob Azure, en utilisant des commandes simples. AzCopy est disponible pour Windows et Linux.

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md)
* [Déploiement d’Azure Files](storage-files-deployment-guide.md)
* [Déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md)
