---
title: Niveaux d’accès chaud, froid et archive pour les objets blob - Stockage Azure
description: Niveaux d’accès chaud, froid et archive pour les comptes de stockage Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 48c6d6ed60045d906fcb711bd07ab492b6bbf488
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543686"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Stockage Blob Azure : niveaux d’accès chaud, froid et archive

Le Stockage Azure propose différents niveaux d’accès qui vous permettent de stocker vos objets blob de la manière la plus économique. Les niveaux d’accès disponibles incluent les suivants :

- **Chaud** : optimisé pour le stockage des données souvent sollicitées.
- **Froid** : optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 30 jours.
- **Archive**  : optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 180 jours, sous des conditions de latence flexibles (de l’ordre de quelques heures).

Les considérations suivantes s’appliquent aux différents niveaux de stockage :

- Seuls les niveaux d’accès chaud et froid peuvent être définis au niveau du compte. Le niveau d’accès archive n’est pas disponible au niveau du compte.
- Les niveaux chaud, froid et archive peuvent être définis au niveau de l’objet blob.
- Les données du niveau d’accès froid peuvent tolérer une disponibilité légèrement inférieure, mais nécessitent toujours des caractéristiques de durabilité élevée, de latence de récupération et de débit similaires à celles des données chaudes. Concernant les données froides, un contrat de niveau de service (SLA) de disponibilité légèrement inférieure et des coûts d’accès supérieurs comparés aux données chaudes sont des compromis acceptables pour des coûts de stockage plus faibles.
- Le stockage archive stocke des données hors connexion et offre les coûts de stockage les plus bas, mais également les coûts de réalimentation et d’accès aux données les plus élevés.

Les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue pour optimiser les coûts. Les données stockées dans le cloud peuvent être différentes en termes de mode de génération, de traitement et d’accès tout au long de leur durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées après avoir été stockées.

Chacun des scénarios d’accès aux données peut bénéficier des avantages d’un niveau d’accès différent, gage d’optimisation pour un modèle d’accès particulier. Les niveaux d’accès chaud, froid et archive permettent au stockage Blob Azure de répondre à ce besoin de niveaux d’accès différenciés avec des modèles de tarification distincts.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Comptes de stockage prenant en charge la hiérarchisation

La hiérarchisation des données de stockage d’objets aux niveaux chaud, froid ou archive est pris en charge uniquement sur des comptes de stockage d’objets blob ou à usage général v2 (GPv2). Les comptes Usage général v1 (GPv1) ne prennent pas en charge la hiérarchisation. Cependant, les clients peuvent facilement convertir leurs comptes de stockage GPv1 ou d’objets blob en des comptes GPv2 en un simple clic depuis le portail Azure. GPv2 fournit une nouvelle structure de tarification pour les objets blob, les fichiers et les files d’attente, ainsi que l’accès à de nouvelles fonctionnalités de stockage. De plus, de nouvelles fonctionnalités à venir et des réductions des prix ne seront offerts qu’aux comptes GPv2. Par conséquent, les clients doivent considérer le fait d’utiliser des comptes GPv2 après avoir examiné la nouvelle tarification car certaines charges de travail peuvent revenir plus chères sur GPv2 que sur GPv1. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

Les comptes de stockage d’objets blob et GPv2 exposent l’attribut **Niveau d’accès** au niveau du compte. Cet attribut vous permet de spécifier le niveau d’accès par défaut sur chaud ou froid pour tous les objets blob présents dans le compte de stockage et ne disposant pas déjà d’un niveau établi au niveau de l’objet. Le niveau du compte ne s’applique pas aux objets disposant d’un niveau établi au niveau de l’objet. Le niveau d’accès archive ne peut être appliqué qu’au niveau de l’objet. Vous pouvez passer d’un niveau d’accès à un autre à tout moment.

## <a name="premium-performance-block-blob-storage"></a>Stockage d’objets blob de blocs de performances Premium

Le stockage d’objets blob de blocs de performances Premium permet d’accéder aux données fréquemment sollicitées avec de hautes performances matérielles. Les données de ce niveau de performances sont stockées sur des disques SSD, qui sont optimisés pour une latence faible et cohérente. Les disques SSD offrent des taux transactionnels et un débit plus élevés que les disques durs traditionnels.

Le stockage d’objets blob de blocs de performances Premium est adapté aux charges de travail qui nécessitent des temps de réponse rapides et cohérents. Il convient parfaitement aux charges de travail qui effectuent de nombreuses petites transactions, comme la capture de données de télémétrie, la messagerie et la transformation de données. Les données impliquant l’intervention des utilisateurs finaux, comme le montage de vidéos interactives, les contenus web statiques et les transactions en ligne sont notamment recommandées.

Le stockage d’objets blob de blocs de performances Premium est uniquement disponible via un type de compte de stockage d’objets blob de blocs et ne prend actuellement pas en charge la hiérarchisation en niveaux d’accès chaud, froid ou archive.

## <a name="hot-access-tier"></a>Niveau d’accès chaud

Le niveau d’accès chaud possède des coûts de stockage plus élevés que les niveaux d’accès froid et archive, mais également les coûts d’accès les plus faibles. Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès chaud :

- Données activement utilisées ou censées être fréquemment sollicitées (accès en lecture et écriture).
- Données conservées pour traitement et migration finale vers le niveau d’accès froid.

## <a name="cool-access-tier"></a>Niveau d’accès froid

Le niveau d’accès froid possède des coûts de stockage plus faibles et des coûts d’accès plus élevés que le stockage chaud. Ce niveau est prévu pour les données qui resteront dans le niveau froid pendant au moins 30 jours. Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès froid :

- Sauvegarde à court terme et récupération d’urgence de jeux de données.
- Ancien contenu multimédia qui n’est plus consulté fréquemment mais qui est censé être disponible immédiatement lors d’un accès.
- Jeux de données volumineux qui doivent être stockés de manière économique tout en permettant la collecte de plus de données à des fins de traitement ultérieur. (*Par exemple*, le stockage à long terme de données scientifiques, les données de télémétrie brute d’un site de production)

## <a name="archive-access-tier"></a>Niveau d’accès archive

Le niveau d’accès archive dispose du plus faible coût de stockage et les coûts d’extraction de données les plus élevés par rapport aux niveaux chaud et froid. Ce niveau est destiné aux données qui peuvent tolérer plusieurs heures de latence de récupération et restant dans le niveau archive pendant au moins 180 jours.

Tant qu’un objet blob se trouve dans un stockage archive, les données d’objets blob ne peuvent être ni lues, ni copiées, ni remplacées, ni modifiées. Vous ne pouvez pas prendre de captures instantanées d’un objet blob dans un stockage archive. Toutefois, les métadonnées de l’objet blob restent en ligne et disponible, ce qui vous permet de répertorier l’objet blob et ses propriétés. Pour les objets blob du niveau archive, les seules opérations valides sont GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier et DeleteBlob.

Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès archive :

- Sauvegarde à long terme, sauvegarde secondaire et jeux de données d’archivage
- Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable (*Par exemple*, des fichiers multimédias bruts après transcodage dans d’autres formats)
- Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées (*Par exemple*, séquences vidéo de sécurité, anciens clichés de radiographie ou d’IRM pour des organismes de santé ou enregistrements audio et transcriptions d’appels de clients pour des services financiers)

### <a name="blob-rehydration"></a>Rafraîchissement des objets blob

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]
Pour en savoir plus, consultez [Réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md).  

## <a name="account-level-tiering"></a>Hiérarchisation au niveau du compte

Les objets blob des trois niveaux d’accès peuvent coexister au sein d’un même compte. Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte. Si le niveau d’accès est déduit à partir du compte, vous voyez la propriété de l’objet blob **Niveau d’accès déduit** définie sur la valeur « True », et la propriété de l’objet blob **Niveau d’accès** correspond au niveau du compte. Dans le portail Azure, la propriété du _niveau d’accès déduit_ est affichée avec le niveau d’accès des objets blob comme **Chaud (déduit)** ou **Froid (déduit)** .

La modification du niveau d’accès du compte s’applique à tous les objets de _niveau d’accès déduit_ stockés dans le compte et ne possédant pas un ensemble de niveau explicite. Si vous passez le niveau du compte de chaud à froid, vous sera facturé pour les opérations d’écriture (par 10 000) pour tous les objets blobs ne disposant pas d’un niveau configuré dans les comptes GPv2 uniquement. Ce changement n’est pas facturé dans les comptes de stockage Blob. Vous serez facturé pour les opérations d’écriture (par 10 000) et d’extraction de données (par Go), si vous passez votre compte de stockage d’objets blob ou GPv2 de froid à chaud.

## <a name="blob-level-tiering"></a>Hiérarchisation au niveau de l’objet blob

La hiérarchisation au niveau de l’objet blob vous permet de modifier le niveau de vos données au niveau de l’objet, à l’aide d’une seule opération nommée [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier). Vous pouvez facilement modifier le niveau d’accès (chaud, froid ou archive) d’un objet blob, comme si vous modifiez le mode d’utilisation, sans avoir à déplacer des données entre les comptes. Tous les changements de niveau prennent effet immédiatement. Toutefois, la réactivation d’un objet blob d’un niveau archive peut prendre plusieurs heures.

L’heure de la dernière modification du niveau de l’objet blob est exposée via la propriété de l’objet blob **Access Tier Change Time**. Lorsqu’un objet blob est stocké au niveau archive, il ne peut pas être remplacé ; le téléchargement du même objet blob n’est donc pas possible dans ce scénario. Vous pouvez remplacer un objet blob dans un niveau chaud ou froid, auquel cas le nouvel objet blob hérite le niveau de l’objet blob remplacé.

> [!NOTE]
> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs. De plus, vous ne pouvez actuellement pas modifier le niveau d’un objet blob de blocs comportant des instantanés.

### <a name="blob-lifecycle-management"></a>Gestion de cycle de vie des objets blob

La gestion du cycle de vie du Stockage Blob offre une stratégie enrichie, basée sur des règles, que vous pouvez utiliser pour faire passer vos données au niveau d’accès le plus adapté et faire expirer les données à la fin de leur cycle de vie. Consultez [Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md) pour en savoir plus.  

> [!NOTE]
> Pour le moment, les données stockées dans le niveau d’accès d’objets blob de blocs (performance Premium) ne peuvent pas être déplacées dans le niveau chaud, froid ou archive avec [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) ou à l’aide de la gestion du cycle de vie du Stockage Blob Azure.
> Pour déplacer les données, effectuez une copie synchrone du compte de stockage d’objets blob de blocs dans le niveau d’accès chaud d’un autre compte à l’aide de l’[API Put Block From URL](/rest/api/storageservices/put-block-from-url) ou d’une version d’AzCopy qui prend en charge cette API.
> L’API *Put Block From URL* copie les données sur le serveur de manière synchrone, ce qui signifie que l’appel est effectué uniquement quand toutes les données ont été déplacées de l’emplacement d’origine sur le serveur vers l’emplacement de destination.

### <a name="blob-level-tiering-billing"></a>Facturation de la hiérarchisation au niveau de l’objet blob

Quand un objet blob est déplacé vers un niveau plus froid (chaud -> froid, chaud -> archive ou froid -> archive), l’opération est facturée comme une opération d’écriture dans le niveau de destination, où les taux du niveau de destination s’appliquent également aux opérations d’écriture (par 10 000) ainsi qu’à l’écriture des données (par Go).

Lorsqu’un objet blob est déplacé vers un niveau plus chaud (archive->froid, archive->chaud ou froid->chaud), l’opération est facturée comme une lecture à partir du niveau source, facturée aux tarifs des opérations de lecture (par 10 000) et d’extraction de données (par Go) du niveau source. Des frais de suppression anticipée peut également s’appliquer pour tout objet blob déplacé hors du niveau froid ou archive. Le tableau suivant résume la facturation des changements de niveau.

| | **Tarif d'écriture (opération + accès)** | **Tarif de lecture (opération + accès)**
| ---- | ----- | ----- |
| **Direction SetBlobTier** | chaud -> froid,<br> chaud -> archive,<br> froid -> archive, | archive -> froid,<br> archive -> chaud,<br> froid -> chaud

### <a name="cool-and-archive-early-deletion"></a>Suppression anticipée dans les niveaux Froid et Archive

En plus des frais par Go et par mois, chaque objet blob déplacé dans le niveau froid (comptes GPv2 uniquement) est sujet à une période de suppression anticipée froide de 30 jours, et chaque objet blob déplacé dans le niveau archive est sujet à une période de suppression anticipée d’archive de 180 jours. Ces charges sont calculées au prorata. Par exemple, si un objet blob est déplacé en archive puis supprimé ou déplacé vers le niveau chaud après 45 jours, il vous sera facturé des frais de suppression anticipé équivalent à 135 (180 moins 45) jours de stockage de cet objet blob en archive.

Vous pouvez calculer la suppression anticipée en utilisant la propriété blob, **Last-Modified**, s’il n’y a pas eu de changement de niveau d’accès. Sinon, vous pouvez l’utiliser lorsque le niveau d’accès a été défini sur froid ou archive en affichant la propriété blob : **access-tier-change-time**. Pour plus d’informations sur les propriétés de l’objet blob, consultez [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparaison des options de stockage d’objets blob de blocs

Le tableau suivant présente une comparaison du stockage d’objets blob de blocs de performances premium, et les niveaux d’accès chaud, froid et archive.

|                                           | **Performances Premium**   | **Niveau chaud** | **Niveau froid**       | **Niveau archive**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilité**                          | 99,9 %                     | 99,9 %        | 99 %                 | Hors ligne           |
| **Disponibilité** <br> **(Lectures RA-GRS)**  | N/A                       | 99,99 %       | 99,9 %               | Hors ligne           |
| **Frais d’utilisation**                         | Coûts de stockage supérieurs, coût d’accès et de transaction inférieur | Coûts de stockage supérieurs, coûts d'accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d'accès et de transaction les plus élevés |
| **Taille minimale des objets**                   | N/A                       | N/A          | N/A                 | N/A               |
| **Durée de stockage minimale**              | N/A                       | N/A          | 30 jours<sup>1</sup> | 180 jours
| **Latence** <br> **(Temps jusqu’au premier octet)** | Millisecondes à un chiffre | millisecondes | millisecondes        | heures<sup>2</sup> |

<sup>1</sup> Les objets du niveau froid sur des comptes GPv2 ont une durée de rétention minimale de 30 jours. Les comptes de stockage d’objets blob n’ont pas de durée de rétention minimale pour le niveau froid.

<sup>2</sup> Stockage archive prend actuellement en charge 2 priorités de réalimentation, haute et standard, qui offrent différentes latences de récupération. Pour plus d’informations, consultez [Réalimentation d’objets blob](#blob-rehydration).

> [!NOTE]
> Les comptes de stockage d’objets blob présentent les mêmes objectifs de performance et d’évolutivité que les comptes de stockage à usage général v2. Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Scénarios de démarrage rapide

Dans cette section, les scénarios suivants sont décrits à l’aide du Portail Azure :

- Comment changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob.
- Comment changer le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets blob.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Pour accéder à votre compte de stockage, sélectionnez Toutes les ressources, puis sélectionnez votre compte de stockage.

1. Dans le panneau Paramètres, cliquez sur **Configuration** pour afficher et/ou modifier la configuration du compte.

1. Sélectionnez le niveau d’accès adapté à vos besoins : Affectez la valeur **Froid** ou **Chaud** au **Niveau d’accès**.

1. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets blob

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Pour accéder à votre objet blob dans votre compte de stockage, sélectionnez Toutes les ressources, puis votre compte de stockage et enfin sélectionnez votre objet blob.

1. Dans le panneau **Propriétés de l’objet blob**, sélectionnez le bouton **Changer le niveau** pour ouvrir le panneau des niveaux.

1. Sélectionnez le niveau d’accès **Chaud**, **Froid** ou **Archive**. Si votre objet blob est actuellement dans le niveau Archive et que vous souhaitez le réalimenter dans un niveau en ligne, vous pouvez également sélectionner une priorité de réalimentation **Standard** ou **Élevée**.

1. Cliquez sur **OK** au bas du panneau.

## <a name="pricing-and-billing"></a>Tarification et facturation

Tous les comptes de stockage utilisent un modèle tarifaire pour le stockage d’objet blob de blocs basé sur le niveau de chaque objet blob. Gardez à l’esprit les conditions de facturation suivantes :

- **Coûts de stockage** : Les coûts de stockage des données varient en fonction de la quantité de données stockées et du niveau d’accès. Le coût par gigaoctet diminue à mesure que le niveau refroidit.
- **Coûts d’accès aux données** : les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données des niveaux d’accès froid et archive, des frais d’accès aux données en lecture vous sont facturés par gigaoctet.
- **Coûts de transaction** : il existe des frais par transaction pour tous les niveaux, augmentant à mesure que le niveau refroidit.
- **Coûts de transfert de données de géoréplication** : ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, notamment GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.
- **Coûts de transfert de données sortantes** : les transferts de données sortantes (données transférées hors d’une région Azure) sont facturés pour l’utilisation de la bande passante par gigaoctet. Cette facturation est cohérente avec les comptes de stockage à usage général.
- **Modification du niveau d’accès** : La modification du niveau d’accès du compte entraîne des frais de modification de niveau pour les objets blob de _niveau d’accès déduit_ stockés dans le compte et ne possédant pas un ensemble de niveau explicite. Pour plus d’informations sur la modification du niveau d’accès pour un objet blob unique, reportez-vous à [Facturation de la hiérarchisation au niveau de l’objet blob](#blob-level-tiering-billing).

> [!NOTE]
> Pour plus d’informations sur la tarification des objets blob de blocs, consultez la page [Présentation de la tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Forum Aux Questions

**Dois-je utiliser un compte de stockage GPv2 ou d’objets blob si je souhaite niveler mes données ?**

Nous vous recommandons d’utiliser des comptes GPv2 pour la hiérarchisation à la place des comptes de stockage d’objets blob. GPv2 prend en charge toutes les fonctionnalités prises en charge par les comptes de stockage d’objets blob, plus beaucoup d’autres. La tarification entre le stockage d’objets blob et GPv2 est presque identique, mais certaines nouvelles fonctionnalités et réduction de tarifs sont uniquement disponibles sur les comptes GPv2. Les comptes GPv1 ne prennent pas en charge la hiérarchisation.

La structure de tarification entre des comptes GPv1 et GPv2 est différente et les clients doivent évaluer soigneusement le fait de passer à des comptes GPv2. Vous pouvez facilement convertir un compte de stockage d’objets blob ou GPv1 existant vers un compte GPv2 en un simple clic. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

**Est-ce que je peux stocker des objets dans les trois niveaux d’accès (chaud, froid et archive) au sein d’un même compte ?**

Oui. L’attribut **Niveau d’accès** configuré au niveau du compte est le niveau d’accès par défaut applicable à tous les objets du compte ne possédant pas un niveau d’ensemble explicite. Toutefois, la hiérarchisation au niveau de l’objet blob permet de définir le niveau d’accès au niveau de l’objet, quel que soit le paramètre de niveau accès du compte. Des objets blob configurés sur l’un des trois niveaux d’accès (chaud, froid ou archive) peuvent exister dans le même compte.

**Puis-je modifier le niveau d’accès par défaut de mon compte de stockage GPv2 ou d’objets blob ?**

Oui, vous pouvez modifier le niveau de compte par défaut en définissant l’attribut **Niveau d’accès** du compte de stockage. La modification du niveau de compte s’applique à tous les objets stockés dans le compte et ne possédant pas un ensemble de niveau explicite (par exemple, **Chaud (déduit)** ou **Froid (déduit)** ). Passer du niveau de compte chaud au niveau froid implique des opérations d’écriture (par 10 000) pour tous les objets blob sans niveau défini dans les comptes GPv2 uniquement, tandis que le passage du niveau froid au niveau chaud entraîne des opérations de lecture (par 10 000) et des frais d’extraction de données (par Go) pour tous les objets blob dans des comptes de stockage GPv2 et d’objets blob.

**Puis-je configurer le niveau d’accès par défaut de mon compte sur Archive ?**

Non. Seuls les niveaux d’accès chaud et froid peuvent être choisis comme le niveau d’accès par défaut du compte. Le niveau archive ne peut être choisi qu’au niveau d’un objet.

**Dans quelles régions les niveaux d’accès chaud, froid et archive sont-ils disponibles ?**

Les niveaux d’accès chaud et froid ainsi que la hiérarchisation au niveau de l’objet blob sont disponibles dans toutes les régions. Le stockage archive ne sera disponible au départ que dans certaines régions sélectionnées. Pour avoir une liste complète, voir [Régions Azure disponibles par région](https://azure.microsoft.com/regions/services/).

**Les objets blob au niveau d’accès froid se comportent-ils différemment de ceux se trouvant au niveau d’accès chaud ?**

Les objets blob au niveau d’accès chaud ont la même latence que les objets blob des comptes de stockage d’objets blob, GPv1 et GPv2. Les objets blob au niveau d’accès froid ont une latence similaire (en millisecondes) à celle des objets blob des comptes de stockage d’objets blob, GPv1 et GPv2. Les objets blob au niveau d’accès archive ont plusieurs heures de latence dans les comptes de stockage d’objets blob, GPv1 et GPv2.

Les objets blob au niveau d’accès froid ont un contrat de niveau de service SLA de disponibilité légèrement inférieur à celui des objets blob stockés au niveau d’accès chaud. Pour plus d’informations, consultez la page [Contrat SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Est-ce que les opérations dans les niveaux chaud, froid et archive sont les mêmes ?**

Toutes les opérations entre les niveaux chaud et froid sont 100% cohérents. Toutes les opérations d’archivage valides, notamment GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier et DeleteBlob sont cohérentes à 100 % avec les niveaux chaud et froid. Les données d’objet blob ne peuvent pas être lues ou modifiées dans le niveau archive tant qu’elles ne sont pas réalimentées ; seules les opérations de lecture des métadonnées d’objet blob sont prises en charge dans l’archive.

**Lors de la réalimentation d’un objet blob depuis le niveau archive vers le niveau chaud ou froid, comment serais-je averti de la fin de la réalimentation ?**

Lors de la réalimentation, vous pouvez utiliser l’opération d’obtention des propriétés d’objet blob afin d’interroger l’attribut **État d’archive** pour confirmer la fin du changement de niveau. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété **Niveau d’accès** de l’objet blob indique le niveau chaud ou froid.  

**Après le réglage du niveau d’un objet blob, quand vais-je commencer à être facturé au taux approprié ?**

Chaque objet blob est toujours facturé en fonction du niveau indiqué par la propriété **Niveau d’accès** de l’objet blob. Quand vous changez le niveau d’un objet blob, la propriété **Niveau d’accès** reflète instantanément le nouveau niveau pour toutes les transitions. Toutefois, la réactivation d’un objet blob d’un niveau archive vers un niveau chaud ou froid peut prendre plusieurs heures. Dans ce cas, vous êtes facturé aux tarifs archive jusqu’à ce que la réactivation soit terminée et que la propriété **Niveau d’accès** reflète le nouveau niveau. À partir de là, l’objet blob vous est facturé au tarif chaud ou froid.

**Comment puis-je déterminer si je vais faire l’objet de frais de suppression anticipée lors de la suppression ou du déplacement d’un objet blob en dehors du niveau froid ou archive ?**

Tout objet blob supprimé ou déplacé en dehors du niveau froid (comptes GPv2 uniquement) ou archive respectivement avant 30 jours et 180 jours fera l’objet de frais de suppression anticipée calculés au prorata. Vous pouvez déterminer combien de temps un objet blob est resté au niveau froid ou archive en vérifiant sa propriété **Access Tier Change Time** (Heure du changement de niveau d’accès), qui horodate le dernier changement de niveau. Si le niveau de l’objet blob n’a jamais été modifié, vous pouvez vérifier la propriété d’objet blob **Dernière modification**. Pour plus d’informations, consultez [Suppression anticipée froid et archive](#cool-and-archive-early-deletion).

**Quels outils et kits de développement Azure prennent en charge la hiérarchisation au niveau de l’objet blob et le stockage archive ?**

Le portail Azure, PowerShell et les outils CLI et les bibliothèques de client .NET, Java, Python, et Node.js prennent toutes en charge la hiérarchisation au niveau de l’objet blob et le stockage archive.  

**Combien de données puis-je stocker dans les niveaux chaud, froid et archive ?**

Le stockage des données ainsi que d’autres limites sont établis à partir du niveau de compte et pas à partir du niveau d’accès. Par conséquent, vous pouvez choisir d’utiliser toute votre limite sur un seul niveau ou sur les trois niveaux. Pour plus d’informations, consultez [Objectifs d’extensibilité et de performances de stockage Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>Évaluer les niveaux chaud, froid et archive dans les comptes de stockage d’objets blob GPv2

[Vérifier la disponibilité de niveau chaud, froid et archive par région](https://azure.microsoft.com/regions/#services)

[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)

[Découvrir comment réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md)

[Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../common/storage-enable-and-view-metrics.md)

[Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
