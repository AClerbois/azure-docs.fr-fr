---
title: "Utilisation du service Azure Import/Export pour transférer des données du et vers le stockage d’objets blob | Microsoft Docs"
description: "Découvrez comment créer des tâches d’importation et d’exportation dans le portail Azure pour transférer des données de et vers le stockage d’objets blob."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: muralikk
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: fc0fd0188261263aac550b0f0784076efc807215
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export
Le service Azure Import/Export vous permet de transférer en toute sécurité des volumes importants de données vers Azure Blob Storage en expédiant des disques durs vers un centre de données Azure. Vous pouvez également utiliser ce service pour transférer des données depuis Azure Blob Storage vers les disques durs et les expédier vers votre site local. Ce service est utile lorsque vous souhaitez transférer plusieurs téraoctets (To) de données vers ou depuis Azure, mais le transfert ou le téléchargement via le réseau est impossible à cause d’une bande passante limitée et de coûts de réseau élevés.

Ce service nécessite que les disques durs soient chiffrés par BitLocker pour garantir la sécurité des données. Ce service prend en charge les comptes de stockage classiques et Azure Resource Manager (niveau standard et froid) présents dans toutes les régions de la version publique d’Azure. Vous devez expédier les disques durs à l’un des emplacements pris en charge spécifiés plus loin dans cet article.

Dans cet article, vous allez découvrir le service Azure Import/Export et apprendre comment expédier des disques pour copier des données vers et depuis Azure Blob Storage.

## <a name="when-should-i-use-the-azure-importexport-service"></a>À quel moment dois-je utiliser le service Azure Import/Export ?
Envisagez d’utiliser le service Azure Import/Export lorsque le chargement ou le téléchargement de données sur le réseau est trop lent ou lorsque l’obtention d’une bande passante réseau supplémentaire est coûteuse.

Vous pouvez utiliser ce service dans des scénarios tels que :

* Migration de données vers le cloud : déplacez de grandes quantités de données vers Azure rapidement et à moindre coût.
* Distribution de contenu : envoyez rapidement des données aux sites de vos clients.
* Sauvegarde : sauvegardez vos données locales dans Azure Blob Storage.
* Récupération des données : récupérez les grandes quantités de données stockées dans Blob Storage pour les transférer vers votre site local.

## <a name="prerequisites"></a>Composants requis
Dans cette section, nous répertorions les composants requis pour utiliser ce service. Vérifiez-les soigneusement avant d’expédier vos disques.

### <a name="storage-account"></a>Compte de stockage
Vous devez disposer d’un abonnement Azure et d’un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une même tâche d’importation/exportation ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Types d’objet blob
Vous pouvez utiliser le service Azure Import/Export pour copier des données dans des objets blob de **bloc** ou de **page**. En revanche, vous ne pouvez qu’exporter les objets blob de **bloc**, de **page** ou **d’ajout** depuis le stockage Azure.

### <a name="job"></a>Travail
Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une tâche. Il peut s'agir d'une tâche d'importation ou d'une tâche d'exportation :

* Une tâche d’importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
* Créez une tâche d’exportation si vous souhaitez transférer les données actuellement stockées comme objets Blob dans votre compte de stockage vers des disques durs qui vous ont été livrés. Lorsque vous créez une tâche, vous informez le service d’importation/exportation que vous allez expédier un ou plusieurs disques durs à un centre de données Azure.

* Dans le cas d’un travail d’importation, vous expédiez des disques durs contenant vos données.
* Dans le cas d’un travail d’exportation, vous expédiez des disques durs vides.
* Vous pouvez expédier jusqu’à 10 disques durs par travail.

Vous pouvez créer une tâche d’importation ou d’exportation à l’aide du portail Azure ou de [l’API REST Import/Export du stockage Azure](/rest/api/storageimportexport).

### <a name="waimportexport-tool"></a>Outil WAImportExport
Pour créer une tâche **d’importation**, la première étape consiste à préparer les disques qui seront utilisés pour l’importation. Pour ce faire, vous devez les connecter à un serveur local et exécuter l’outil WAImportExport sur le serveur local. Cet outil facilite la copie de vos données sur le disque, leur chiffrement avec BitLocker et la génération des fichiers journaux du disque.

Ces fichiers journaux stockent des informations sur votre travail et sur le disque, par exemple le numéro de série du disque et le nom du compte de stockage. Ce fichier journal n’est pas stocké sur le disque. Il est utilisé lors de la création de travaux d’importation. Une procédure détaillée de la création de ces tâches est fournie plus loin dans cet article.

L’outil WAImportExport est compatible uniquement avec le système d’exploitation Windows 64 bits. Pour connaître les versions de système d’exploitation prises en charge, consultez la section [Système d’exploitation](#operating-system) .

Téléchargez la dernière version de l’[outil WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Pour plus d’informations sur l’utilisation de l’outil WAImportExport, consultez la page [Using the WAImportExport Tool](storage-import-export-tool-how-to.md) (Utilisation de l’outil WAImportExport).

>[!NOTE]
>**Version précédente :** vous pouvez [télécharger la version v1 de l’outil WAImportExpot](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) et consulter le [guide d’utilisation de WAImportExport v1](storage-import-export-tool-how-to-v1.md). La version v1 de l’outil WAImportExpot ne prend pas en charge **la préparation des disques lorsque des données sont préalablement écrites sur ces derniers**. Vous devez également utiliser l’outil WAImportExpot v1 si la seule clé disponible est une clé SAS.

>

### <a name="hard-disk-drives"></a>Disques durs
Le service Import/Export ne prend en charge que les disques durs internes SSD de 2,5 pouces ou SATA II ou III de 2,5 ou 3,5 pouces. Une même tâche d’importation/exportation peut avoir un maximum de 10 disques durs/SSD (quelle que soit la taille de chaque disque). Il est possible de répartir un grand nombre de disques entre plusieurs tâches, et il n’existe aucune limite quant au nombre de tâches pouvant être créées. 

Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS.

> [!IMPORTANT]
> Les disques durs externes équipés d’un adaptateur USB intégré ne sont pas pris en charge par ce service. En outre, le disque se trouvant à l’intérieur du boîtier d’un disque dur externe est inutilisable ; vous ne devez donc pas envoyer de disques durs externes.
> 
> 

Vous trouverez ci-dessous la liste des adaptateurs USB externes utilisés pour copier les données vers des disques durs internes. Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (6628 Series) Thermaltake BlacX Hot-Swap SATA External Hard Drive Docking Station (USB 2.0 & eSATA)

### <a name="encryption"></a>Chiffrement
Les données contenues dans le disque dur doivent être chiffrées à l’aide du chiffrement de lecteur BitLocker. Cette opération permet de protéger vos données pendant qu'elles sont en transit.

Pour les travaux d’importation, le chiffrement s’effectue de deux manières différentes. La première consiste à spécifier l’option lors de l’utilisation du fichier CSV du jeu de données pendant l’exécution de l’outil WAImportExport pour préparer le disque. La seconde consiste à activer manuellement le chiffrement BitLocker sur le disque et à spécifier la clé de chiffrement dans le fichier CSV du jeu de disques lors de l’exécution de la ligne de commande de l’outil WAImportExport pendant la préparation du disque.

Pour les travaux d’exportation, une fois vos données copiées sur le disque, le service chiffre le disque à l’aide de BitLocker avant de vous le renvoyer. La clé de chiffrement vous est fournie via le portail Azure.  

### <a name="operating-system"></a>Système d’exploitation
Vous pouvez utiliser l’un des systèmes d’exploitation 64 bits suivants pour préparer le disque dur à l’aide de l’outil WAImportExport avant de l’envoyer à Azure :

Windows 7 Entreprise, Windows 7 Édition intégrale, Windows 8 Professionnel, Windows 8 Entreprise, Windows 8.1 Professionnel, Windows 8.1 Entreprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tous ces systèmes d’exploitation prennent en charge le chiffrement de lecteur BitLocker.

### <a name="locations"></a>Emplacements
Le service Azure Import/Export prend en charge la copie des données vers et depuis tous les comptes de stockage publics Azure. Vous pouvez expédier les disques durs à l’un des emplacements suivants. Si votre compte de stockage se trouve dans un emplacement Azure public non spécifié ici, un autre emplacement d’expédition sera indiqué lorsque vous créerez la tâche à l’aide du portail Azure ou de l’API REST Import/Export.

Emplacements d’expédition pris en charge :

* Est des États-Unis
* Ouest des États-Unis
* Est des États-Unis 2
* Ouest des États-Unis 2
* Centre des États-Unis
* États-Unis - partie centrale septentrionale
* Centre-Sud des États-Unis
* Centre-Ouest des États-Unis
* Europe du Nord
* Europe de l'Ouest
* Est de l'Asie
* Asie du Sud-Est
* Est de l’Australie
* Sud-est de l’Australie
* Ouest du Japon
* Est du Japon
* Inde centrale
* Inde du Sud
* Inde occidentale
* Centre du Canada
* Est du Canada
* Sud du Brésil
* Centre de la Corée
* Gouvernement américain - Virginie
* US Gov Iowa
* Est des États-Unis – US DoD
* Centre des États-Unis – US DoD
* Chine orientale
* Chine du Nord
* Sud du Royaume-Uni

### <a name="shipping"></a>Expédition
**Expédition de disques au centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous recevez l’adresse de l’un des emplacements auquel expédier vos disques. L’adresse d’expédition fournie dépend de l’emplacement de votre compte de stockage, mais elle ne peut être différente de l’emplacement de ce dernier.

Vous pouvez faire appel à des transporteurs comme FedEx, DHL, UPS ou La Poste pour envoyer vos disques à l’adresse d’expédition.

**Expédition de disques depuis le centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous devez indiquer une adresse de retour que Microsoft utilisera pour vous remettre les disques une fois le travail terminé. Veillez à fournir une adresse de retour valide pour éviter les retards de traitement.

Vous pouvez faire appel au transporteur de votre choix pour expédier le disque dur. Le transporteur doit proposer un système de suivi approprié afin d’assurer la traçabilité du disque. Vous devez également indiquer un numéro de compte de transporteur FedEx ou DHL que Microsoft utilisera pour le retour des disques. Un numéro de compte FedEx est requis pour les retours de disque à partir des États-Unis et de l’Europe. Un numéro de compte DHL est requis pour les retours de disque à partir d’Asie et d’Australie. Vous pouvez créer un compte de transporteur [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et l’Europe) ou [DHL](http://www.dhl.com/) (pour l’Asie et l’Australie) si vous n’en avez pas. Si vous avez déjà un compte de transporteur, vérifiez qu’il est valide.

Lorsque vous expédiez vos colis, vous devez respecter les [conditions d'utilisation des services Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d’expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais. Par exemple, tout package qui franchira des frontières internationales (à l’exception de celles de l’Union européenne) doit être accompagné d’une facture commerciale. Vous pouvez imprimer une copie de la facture commerciale à partir du site web de support du transporteur. Exemples de factures commerciales : [facture commerciale DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) et [facture commerciale FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assurez-vous que Microsoft n’a pas été indiqué comme l’exportateur.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Fonctionnement du service Azure Import/Export
Vous pouvez transférer des données entre votre site local et Azure Blob Storage à l’aide du service Azure Import/Export en créant des travaux et en expédiant des disques durs à un centre de données Azure. Chaque disque dur expédié est associé à un seul travail. Chaque travail est associé à un seul compte de stockage. Consultez la section [Conditions préalables](#pre-requisites) pour en savoir plus sur les particularités de ce service, comme les types d’objet blob pris en charge, les types de disque, les emplacements et l’expédition.

Dans cette section, nous décrivons globalement la procédure permettant d’importer et d’exporter des travaux. Ensuite, dans la section [Démarrage rapide](#quick-start), nous expliquons comment créer un travail d’importation et d’exportation.

### <a name="inside-an-import-job"></a>Dans un travail d’importation
Globalement, un travail d’importation comprend les opérations suivantes :

* Déterminez les données à importer et le nombre de disques dont vous avez besoin.
* Identifiez l’emplacement des objets blob de destination de vos données dans le stockage Blob.
* Utilisez l’outil WAImportExport pour copier vos données sur un ou plusieurs disques durs et chiffrez-les à l’aide de BitLocker.
* Créez une tâche d’importation dans votre compte de stockage cible à l’aide du portail Azure ou de l’API REST Import/Export. Si vous utilisez le portail Azure, téléchargez les fichiers journaux des disques.
* Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
* Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
* Mettez à jour le numéro de suivi et envoyez le travail d’importation.
* Les disques sont réceptionnés et traités dans le centre de données Azure.
* Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.
  
    ![Figure 1 : flux d’importation de travail](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dans un travail d’exportation
Globalement, un travail d’exportation comprend les opérations suivantes :

* Déterminez les données à exporter et le nombre de disques dont vous avez besoin.
* Identifiez les objets blob source ou les chemins d’accès au conteneur de vos données dans Blob Storage.
* Créez une tâche d’exportation dans votre compte de stockage source à l’aide du portail Azure ou de l’API REST Import/Export.
* Indiquez les objets blob source ou les chemins d’accès au conteneur de vos données dans le travail d’exportation.
* Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
* Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
* Mettez à jour le numéro de suivi et envoyez le travail d’exportation.
* Les disques sont réceptionnés et traités dans le centre de données Azure.
* Les disques sont chiffrés à l’aide de BitLocker et les clés sont disponibles via le portail Azure.  
* Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.
  
    ![Figure 2 : flux d’exportation de travail](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Affichage de l’état de vos tâches et de vos disques
Vous pouvez suivre l’état de vos tâches d’importation ou d’exportation dans le portail Azure. Cliquez sur l’onglet **Import/Export**. La liste de vos tâches s'affiche sur la page.

![Afficher l’état des tâches](./media/storage-import-export-service/jobstate.png)

Selon la phase de traitement de votre disque, vous obtiendrez l’un des statuts suivants :

| Statut de tâche | Description |
|:--- |:--- |
| Creating | Après qu’une tâche a été créée, son état est défini sur Création. Pendant que la tâche se trouve dans l’état Création, le service Import/Export suppose que les disques n’ont pas été expédiés au centre de données. Une tâche peut rester dans l’état Création pendant deux semaines maximum. Passé ce délai, elle est automatiquement supprimée par le service. |
| Expédition | Après avoir expédié votre colis, vous devez mettre à jour les informations de suivi dans le portail Azure.  La tâche passera alors à l’état Expédition. La tâche restera dans l’état Expédition pendant deux semaines maximum. 
| Reçu | Lorsque tous les disques ont été reçus dans le centre de données, l’état de la tâche est défini sur Reçu. |
| Transferring | Une fois que le traitement a commencé pour au moins un disque, l’état de la tâche est défini sur Transfert. Pour plus d’informations, consultez la section État du disque ci-dessous. |
| Packaging | Une fois le traitement terminé pour tous les disques, la tâche est définie sur l’état Emballage jusqu’à ce que les disques vous aient été renvoyés. |
| Completed | Une fois que tous les disques ont été renvoyés au client, si la tâche s’est terminée sans erreur, la tâche est définie sur l’état Terminé. La tâche est automatiquement supprimée au bout de 90 jours dans l’état Terminé. |
| Fermés | Une fois que tous les disques ont été renvoyés au client, si des erreurs sont survenues pendant le traitement de la tâche, la tâche est définie sur l’état Clôturé(e). La tâche est automatiquement supprimée au bout de 90 jours dans l’état Clôturé(e). |

Le tableau ci-dessous décrit le cycle de vie d’un disque individuel tout au long d’une tâche d’importation ou d’exportation. L’état actuel de chaque disque d’une tâche est désormais visible dans le portail Azure.
Le tableau suivant décrit chacun des états par lesquels le disque d’une tâche peut passer.

| État du disque | Description |
|:--- |:--- |
| Spécifié | Pour une tâche d’importation, lorsque la tâche est créée à partir du portail Azure, l’état initial d’un disque est l’état Spécifié. Pour une tâche d’exportation, comme aucun disque n’est spécifié lors de la création de la tâche, l’état initial du disque est l’état Reçu. |
| Reçu | Le disque passe à l’état Reçu une fois que l’opérateur du service Import/Export a traité les disques reçus de l’entreprise de transport pour une tâche d’importation. Pour une tâche d’exportation, l’état initial du disque est l’état Reçu. |
| NeverReceived (Jamais reçu) | Le disque passe à l’état NeverReceived (Jamais reçu) lorsque le colis associé à une tâche est arrivé, mais ne contient pas le disque. Un disque peut également passer à cet état si le colis n’est toujours pas arrivé au centre de données deux semaines après réception des informations d’expédition par le service. |
| Transferring | Un disque passe à l’état Transfert lorsque le service commence à transférer les données du disque au stockage Microsoft Azure. |
| Completed | Un disque passe à l’état Terminé lorsque le service a transféré toutes les données sans erreurs.
| CompletedMoreInfo (Terminé avec des informations) | Un disque passe à l’état CompletedMoreInfo (Terminé avec des informations) lorsque le service a rencontré des erreurs pendant la copie des données à partir du disque ou sur celui-ci. Les informations peuvent inclure des erreurs, des avertissements ou des messages d’informations sur le remplacement des objets blob.
| ShippedBack (Renvoyé) | Le disque passe à l’état ShippedBack (Renvoyé) lorsqu’il a été renvoyé du centre de données à l’adresse de retour. |

Cette image à partir du portail Azure affiche l’état du lecteur d’un exemple de travail :

![Afficher l’état des disques](./media/storage-import-export-service/drivestate.png)

Le tableau suivant décrit les états associés aux défaillances de disque et les mesures mises en œuvre pour chacun de ces états.

| État du disque | Événement | Résolution / Étape suivante |
|:--- |:--- |:--- |
| NeverReceived (Jamais reçu) | Un disque qui a été marqué comme NeverReceived (Jamais reçu) car le colis associé à la tâche ne le contenait pas arrive dans un autre colis. | L’équipe responsable des opérations fait passer le disque à l’état Reçu. |
| N/A | Un disque qui n’est associé à aucune tâche arrive au centre de données dans le cadre d’une autre tâche. | Le disque est marqué en tant que disque supplémentaire et est retourné au client une fois la tâche associée au colis d’origine terminée. |

### <a name="time-to-process-job"></a>Temps de traitement du travail 
Le temps de traitement d’un travail d’importation/exportation varie en fonction de différents facteurs, tels que le délai de livraison, le type de travail, le type et la taille des données copiées, ou encore la taille des disques fournis. Le service Import/Export n’est adossé à aucun contrat de niveau de service. Vous pouvez utiliser l’API REST pour assurer le suivi détaillé de la progression du travail. Dans l’option d’affichage des travaux, un pourcentage d’achèvement terminé indique l’état d’avancement de la copie. Contactez-nous si vous avez besoin d’estimer la durée nécessaire à un travail d’importation/exportation.

### <a name="pricing"></a>Tarification
**Frais de manipulation de disque**

Des frais de manipulation sont appliqués pour chaque disque traité dans le cadre de votre travail d’importation/exportation. Pour plus d’informations, consultez [Tarification d’Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frais d’expédition**

Lorsque vous envoyez des disques à Azure, vous payez le coût d’expédition au transporteur. Lorsque Microsoft vous renvoie les disques, les frais d’expédition sont facturés au compte de transporteur que vous avez spécifié lors de la création du travail.

**Frais de transaction**

Aucun frais de transaction ne s’applique pour l’importation de données dans Blob Storage. Des frais de sortie standard s’appliquent lorsque les données sont exportées depuis Blob Storage. Pour plus d’informations sur les frais de transaction, consultez [Tarification - Transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Quick Start
Cette section vous explique en détail comment créer un travail d’importation et d’exportation. Vérifiez que vous remplissez toutes les [conditions préalables](#pre-requisites) avant de poursuivre.

## <a name="create-an-import-job"></a>Créer une tâche d’importation
Créez un travail d’importation pour copier les données de vos disques durs dans votre compte de stockage Azure en envoyant un ou plusieurs disques au centre de données spécifié. Ce travail d’importation transmet des informations sur les disques durs, les données à copier, le compte de stockage cible et l’adresse d’expédition au service Azure Import/Export. La création d’un travail d’importation comprend trois étapes. Tout d’abord, préparez vos disques à l’aide de l’outil WAImportExport. Ensuite, envoyez une tâche d’importation à l’aide du portail Azure. Enfin, envoyez les disques à l’adresse d’expédition fournie lors de la création du travail et mettez à jour les informations d’expédition dans les détails de votre travail.   

> [!IMPORTANT]
> Vous ne pouvez envoyer qu’un seul travail par compte de stockage. Chaque disque que vous expédiez peut être importé dans un compte de stockage. Par exemple, supposons que vous souhaitiez importer les données dans deux comptes de stockage. Vous devez utiliser un disque dur pour chaque compte de stockage et créer un travail distinct par compte de stockage.
> 
> 

### <a name="prepare-your-drives"></a>Préparation des lecteurs
Lors de l’importation des données à l’aide du service Azure Import/Export, la première étape consiste à préparer vos disques à l’aide de l’outil WAImportExport. Suivez la procédure ci-dessous pour préparer vos disques :

1. Identifiez les données à importer. Il peut s’agir de répertoires et de fichiers autonomes sur le serveur local ou sur un partage de réseau.  
2. Déterminez le nombre de disques nécessaires en fonction de la taille totale des données. Procurez-vous le nombre requis de disques durs SSD de 2,5 pouces ou SATA II ou III de 2,5 ou 3,5 pouces.
3. Identifiez le compte de stockage cible, le conteneur, les répertoires virtuels et les objets blob.
4.  Déterminez les répertoires et/ou les fichiers à copier sur chaque disque dur.
5.  Créez les fichiers CSV du jeu de données et du jeu de disques.
    
    **Fichier CSV du jeu de données**
    
    Voici un exemple de fichier CSV de jeu de données :
    
    ```
    BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
    "F:\50M_original\","containername/",BlockBlob,rename,"None",None 
    ```
   
    Dans le cadre de l’exemple ci-dessus, le fichier 100M_1.csv.txt sera copié à la racine du conteneur nommé « containername ». Si le nom de conteneur « containername » n’existe pas, un conteneur de ce nom sera créé. Tous les fichiers et dossiers présents sous 50M_original seront copiés de manière récursive dans containername. La structure des dossiers sera conservée.

    Vous trouverez plus d’informations sur la [préparation du fichier CSV du jeu de données ici](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    
    **Attention**: par défaut, les données sont importées sous la forme d’objets blob de blocs. Vous pouvez utiliser la valeur du champ BlobType pour importer les données sous la forme d’objets blob de page. Par exemple, si vous importez des fichiers de disque dur virtuel qui seront montés comme des disques sur une machine virtuelle Azure, vous devez les importer en tant qu’objets blob de page.

    **Fichier CSV du jeu de disques**

    La valeur de l’indicateur DriveSet est un fichier CSV qui contient la liste des disques auxquels les lettres de lecteur sont mappées afin que l’outil sélectionne correctement les disques à préparer. 

    Voici un exemple de fichier CSV de jeu de disques :
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    L’exemple ci-dessus part du principe que deux disques sont attachés et que des volumes NTFS de base présentant les lettres G:\ et H:\ ont été créés. L’outil formatera et chiffrera uniquement le disque contenant le volume H:\, le disque contenant le volume G:\ étant déjà formaté et chiffré.

    Vous trouverez plus d’informations sur la [préparation du fichier CSV du jeu de disques ici](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Utilisez l’outil [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) pour copier vos données sur un ou plusieurs disques durs.
7.  Vous pouvez spécifier « Encrypt » pour le champ Encryption dans le fichier CSV du jeu de disques afin d’activer le chiffrement BitLocker sur le disque dur. Vous pouvez également activer le chiffrement BitLocker manuellement sur le disque dur, spécifier « AlreadyEncrypted » pour ce même champ et fournir la clé dans le fichier CSV du jeu de disques lors de l’exécution de l’outil.

8. Ne modifiez ni les données ni le fichier journal après la préparation du disque.

> [!IMPORTANT]
> Un fichier journal est créé pour chaque disque dur que vous préparez. Lorsque vous créez la tâche d’importation à l’aide du portail Azure, vous devez télécharger tous les fichiers journaux des disques qui font partie de cette tâche. Les disques sans fichier journal ne sont pas traités.
> 
>

Voici les commandes et des exemples utiles pour préparer le disque dur à l’aide de l’outil WAImportExport.

Lors de la première session de copie, la commande PrepImport de l’outil WAImportExport permet de copier les répertoires ou les fichiers dans le cadre d’une nouvelle session de copie :

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Exemple d’importation 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Pour **ajouter des disques**, vous pouvez créer un nouveau fichier de jeu de disques et exécuter la commande indiquée ci-dessous. Pour les sessions de copie suivantes sur les disques non spécifiés dans le fichier CSV du paramètre InitialDriveSet, spécifiez un nouveau fichier CSV de jeu de disques et définissez-le en tant que valeur pour le paramètre « AdditionalDriveSet ». Utilisez le **même nom de fichier journal** et fournissez un **nouvel ID de session**. Le format du fichier CSV du paramètre AdditionalDriveSet est identique au format de celui du paramètre InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Exemple d’importation 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Afin d’ajouter d’autres données au même jeu de disques, la commande PrepImport de l’outil WAImportExport peut être appelée dans le cadre des sessions de copie suivantes pour copier les fichiers/répertoires supplémentaires : pour les sessions de copie suivantes sur les disques durs spécifiés dans le fichier CSV du paramètre InitialDriveSet, utilisez le **même nom de fichier journal** et fournissez un **nouvel ID de session** ; il est inutile de fournir la clé du compte de stockage.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Exemple d’importation 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Pour en savoir plus sur l’utilisation de l’outil WAImportExport, consultez [Préparation des disques durs pour une tâche d’importation](storage-import-export-tool-preparing-hard-drives-import.md).

Pour une description étape par étape, consultez [Exemple de flux de travail pour préparer des disques durs à un travail d’importation](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md).  

### <a name="create-the-import-job"></a>Création de la tâche d'importation
1. Après avoir préparé votre disque, accédez à votre compte de stockage dans le portail Azure, puis affichez le tableau de bord. Sous **Aperçu rapide**, cliquez sur **Créer une tâche d'importation**. Vérifiez les étapes et cochez la case pour indiquer que vous avez préparé votre disque et que son fichier journal est disponible.
2. À l’étape 1, indiquez les coordonnées de la personne responsable de ce travail d’importation ainsi qu’une adresse de retour valide. Pour enregistrer des données de journal détaillées pour la tâche d'importation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.
3. À l’étape 2, téléchargez les fichiers journaux que vous avez obtenus à l’étape de préparation du disque. Vous devez télécharger un fichier pour chaque disque préparé.
   
   ![Créer une tâche d'importation - Étape 3](./media/storage-import-export-service/import-job-03.png)
4. À l’étape 3, indiquez une description du travail d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que vous choisissez vous servira à suivre vos travaux pendant et après son exécution.
   
   Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.
5. À l’étape 4, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier les lecteurs une fois la tâche d’importation terminée.
   
   Si vous possédez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.
   
   Si vous n'avez pas encore de numéro de suivi, choisissez **I will provide my shipping information for this import job once I have shipped my package**, puis terminez le processus d'importation.
6. Pour entrer votre numéro de suivi après avoir expédié votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail Azure, sélectionnez votre tâche dans la liste, puis choisissez **Informations d’expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.
   
    Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.
   
    Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.
7. Vous pouvez suivre l’état d’avancement de votre travail sur le tableau de bord du portail. Pour connaître la signification de chaque état de travail dans la section précédente, consultez [Affichage de l’état de votre travail](#viewing-your-job-status).

## <a name="create-an-export-job"></a>Création d’une tâche d’exportation
Créez une tâche d’exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs disques vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les disques, qui vous seront ensuite renvoyés.

### <a name="prepare-your-drives"></a>Préparation des lecteurs
Les vérifications préalables suivantes sont recommandées pour préparer vos disques en vue d’un travail d’exportation :

1. Vérifiez le nombre de disques requis à l’aide de la commande PreviewExport de l’outil WAImportExport. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser.
2. Vérifiez que le disque dur expédié en vue de la tâche d’exportation est accessible en lecture/écriture.

### <a name="create-the-export-job"></a>Création du travail d’importation
1. Pour créer une tâche d’exportation, accédez à votre compte de stockage dans le portail Azure, puis affichez le tableau de bord. Sous **Aperçu rapide**, cliquez sur **Créer un travail d’exportation**, puis parcourez les étapes de l’Assistant.
2. À l’étape 2, fournissez les coordonnées de la personne responsable de cette tâche d’exportation. Pour enregistrer des données de journal détaillées pour la tâche d'exportation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.
3. À l'étape 3, indiquez les données d'objets blob que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d’exporter toutes les données d’objets blob contenues dans le compte de stockage ou indiquer les objets blob ou ensembles d’objets blob à exporter.
   
   Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To** , puis indiquez le chemin d’accès relatif de l’objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
   
   Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With** , puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.
   
   Le tableau suivant présente des exemples de chemins d’accès d’objet blob valides :
   
   | Sélecteur | Chemin d'accès d'objet blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporte tous les objets blob présents dans le compte de stockage. |
   | Starts With |/$root/ |Exporte tous les objets blob présents dans le conteneur racine. |
   | Starts With |/book |Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book** |
   | Starts With |/music/ |Exporte tous les objets blob présents dans le conteneur **music** |
   | Starts With |/music/love |Exporte tous les objets blob présents dans le conteneur **music** qui commencent par le préfixe **love**. |
   | Equal To |$root/logo.bmp |Exporte l'objet blob **logo.bmp** présent dans le conteneur racine. |
   | Equal To |videos/story.mp4 |Exporte l’objet blob **story.mp4** présent dans le conteneur **videos**. |
   
   Vous devez indiquer les chemins d’accès aux objets blob dans des formats valides pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran.
   
   ![Créer une tâche d'exportation - Étape 3](./media/storage-import-export-service/export-job-03.png)
4. À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et ne peut pas contenir d'espaces.
   
   La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.
5. À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'exportation terminée.
   
   Si vous possédez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.
   
   Si vous n'avez pas encore de numéro de suivi, choisissez **Je fournirai mes informations d'expédition pour ce travail d'exportation après envoi de mon colis**, puis terminez le processus d'exportation.
6. Pour entrer votre numéro de suivi après avoir expédié votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail Azure, sélectionnez votre tâche dans la liste, puis choisissez **Informations d’expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.
   
    Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.
   
    Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.
   
   > [!NOTE]
   > Si l’objet blob à exporter est en cours d’utilisation au moment de la copie sur le disque dur, le service d’importation/exportation Azure prend un instantané de l’objet blob et copie la capture instantanée.
   > 
   > 
7. Vous pouvez suivre l’état d’avancement de votre tâche dans le tableau de bord du portail Azure. Pour connaître la signification de chaque état de travail dans la section précédente, consultez « Affichage de l’état de votre travail ».
8. Après avoir réceptionné les disques avec vos données exportées, vous pouvez afficher et copier les clés BitLocker générées par le service pour votre disque. Accédez à votre compte de stockage dans le portail Azure, puis cliquez sur l’onglet Import/Export. Sélectionnez votre tâche d’exportation dans la liste, puis cliquez sur le bouton Afficher les clés. Les clés BitLocker s’affichent comme ci-dessous :
   
   ![Afficher les clés BitLocker pour une tâche d'exportation](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Accédez à la section FAQ ci-dessous, car elle aborde les questions les plus courantes concernant l’utilisation de ce service.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

**Puis-je copier des fichiers Azure à l’aide du service Azure Import/Export ?**

Non, le service Azure Import/Export ne prend en charge que les objets blob de bloc et de page. Les autres types de stockage, y compris les fichiers Azure, les tables et les files d’attente, ne sont pas pris en charge.

**Le service Azure Import/Export est-il disponible pour les abonnements de fournisseur de services de chiffrement ?**

Le service Azure Import/Export prend en charge les abonnements de fournisseur de services de chiffrement.

**Puis-je ignorer l’étape de préparation de disque pour un travail d’importation ou préparer un disque sans effectuer la copie ?**

Tout disque que vous souhaitez expédier en vue d’importer des données doit être préparé à l’aide de l’outil Azure WAImportExport. Vous devez utiliser cet outil pour copier les données sur le disque.

**Ai-je besoin d’effectuer la préparation du disque lors de la création d'une tâche d'exportation ?**

Non, mais certaines vérifications préalables sont recommandées. Vérifiez le nombre de disques requis à l’aide de la commande PreviewExport de l’outil WAImportExport. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser. Vérifiez également que le disque dur qui sera utilisé pour le travail d’exportation est accessible en lecture et en écriture.

**Que se passe-t-il si j’envoie par accident un disque dur non conforme au type pris en charge ?**

Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

**Puis-je annuler une tâche ?**

Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps l’état des tâches terminées est-il consultable dans le portail Azure ?**

L’état des travaux terminés est visible pendant 90 jours. Au-delà de ce délai, les travaux terminés sont supprimés.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

Le service Import/Export limite chaque tâche d’importation ou d’exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches. Les disques associés au même travail doivent être expédiés ensemble dans le même colis.

**Le service formate-t-il les disques avant de les renvoyer ?**

Non. Tous les disques sont chiffrés avec BitLocker.

**Est-il possible d’acheter des lecteurs auprès de Microsoft pour des tâches d’importation/exportation ?**

Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

** Comment puis-je accéder aux données importées par ce service ** Les données de votre compte Azure Storage sont accessibles via le portail Azure ou à l’aide d’un outil autonome appelé Explorateur de stockage. https://docs.Microsoft.com/fr-fr/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Une fois la tâche d’importation terminée, à quoi mes données ressembleront-elles dans le compte de stockage ? Mon arborescence sera-t-elle préservée ?**

Lorsque vous préparez un disque dur pour une tâche d’importation, la destination est spécifiée par le champ DstBlobPathOrPrefix du fichier CSV du jeu de données. Il s’agit du conteneur cible dans le compte de stockage dans lequel les données du disque dur sont copiées. Dans ce conteneur cible, des répertoires virtuels sont créés pour les dossiers du disque dur et des objets blob sont créés pour les fichiers. 

**Si le disque contient des fichiers déjà existants dans mon compte de stockage, le service va-t-il remplacer les objets blob dans mon compte de stockage ?**

Lors de la préparation du disque, vous pouvez indiquer si les fichiers cibles doivent être remplacés ou ignorés à l’aide du champ Disposition:< rename|no-overwrite|overwrite> du fichier CSV du jeu de données. Par défaut, le service ne remplace pas les objets blob mais renomme les nouveaux fichiers.

**L’outil WAImportExport est-il compatible avec un système d’exploitation Windows 32 bits ?**
Non. L’outil WAImportExport est compatible uniquement avec les systèmes d’exploitation Windows 64 bits. Pour obtenir la liste complète des versions de système d’exploitation pris en charge, consultez la section [Conditions préalables](#pre-requisites) .

**Dois-je ajouter quelque chose en plus du disque dur dans mon colis ?**

Incluez uniquement vos disques durs. N’incluez pas d’accessoires tels que des câbles d’alimentation ou USB.

**Dois-je expédier mes disques à l’aide de FedEx ou DHL ?**

Vous pouvez expédier des disques au centre de données à l’aide d’un transporteur connu comme FedEx, DHL, UPS ou La Poste. Toutefois, pour que les disques du centre de données puissent vous être retournés, vous devez fournir un numéro de compte FedEx aux États-Unis et dans l’UE, ou un numéro de compte DHL en Asie et en Australie.

**Existe-t-il des restrictions à l’expédition de mon disque dur hors des frontières ?**

Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d'expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais.

**Lorsque vous créez une tâche, l’adresse d’expédition désigne un emplacement différent de celui de mon compte de stockage. Que dois-je faire ?**

Certains emplacements de compte de stockage sont associés à d’autres emplacements d’expédition. Les emplacements d’expédition auparavant disponibles peuvent être temporairement associés à d’autres emplacements. Vérifiez toujours l’adresse d’expédition fournie lors de la création du travail avant d’expédier vos disques.

**Lors de l’expédition de mon disque, le transporteur me demande l’adresse et le numéro de téléphone du centre de données. Que dois-je indiquer ?**

Le numéro de téléphone et l’adresse du centre de données sont fournis dans le cadre de la création de la tâche.

**Puis-je utiliser le service Azure Import/Export pour copier des boîtes aux lettres PST et des données SharePoint vers O365 ?**

Consultez la rubrique [Importer des fichiers PST ou des données SharePoint dans Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Puis-je utiliser le service Azure Import/Export pour copier mes sauvegardes en mode hors connexion sur le service Azure Backup ?**

Consultez la rubrique [Flux de travail de la sauvegarde hors connexion dans Azure Backup](../backup/backup-azure-backup-import-export.md).

**Quel est le nombre maximal de disques durs par livraison ?**

Une livraison peut contenir n’importe quel nombre de disques durs et, si les disques appartiennent à plusieurs tâches, il est recommandé : a) d’indiquer les noms de tâche correspondants sur les disques. b) de mettre à jour les tâches avec un numéro de suivi se terminant par -1, -2, etc.
  
**Quelle est la taille maximale des objets blob de blocs et objets blob de pages prise en charge pour chaque importation/exportation de disque ?**

La taille maximale des objets blob de blocs est d’environ 4,768 To ou 5 000 000 Mo.
La taille maximale des objets blob de pages est de 1 To.
## <a name="next-steps"></a>Étapes suivantes

* [Configuration de l’outil WAImportExport](storage-import-export-tool-how-to.md)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Exemple d’API REST Azure Import Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)


