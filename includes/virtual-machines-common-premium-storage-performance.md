---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c81b0926b88ad2f1dbb3af7c1a2c51e8a79430f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737076"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Stockage Azure Premium : conception sous le signe de la haute performance

Cet article fournit des instructions pour la création d’applications hautes performances avec Azure Premium Storage. Vous pouvez utiliser les instructions fournies dans ce document parallèlement aux bonnes pratiques de performances applicables aux technologies utilisées par votre application. Pour illustrer les instructions, nous avons utilisé comme exemple un SQL Server exécuté sur Premium Storage.

Bien que cet article couvre plusieurs scénarios de performances au niveau de la couche de stockage, vous devrez optimiser la couche applicative. Par exemple, si vous hébergez une batterie de serveurs SharePoint sur Azure Premium Storage, vous pouvez utiliser les exemples SQL Server de cet article pour optimiser le serveur de base de données. Vous devez également optimiser le serveur web et le serveur d’applications de la batterie de serveurs SharePoint pour obtenir de meilleures performances.

Cet article vous aidera à répondre à certaines questions courantes relatives à l’optimisation des performances applicatives sur Azure Premium Storage :

* Comment mesurer les performances de votre application ?  
* Pourquoi n’obtenez-vous pas les hautes performances attendues ?  
* Quels sont les facteurs qui influencent les performances de votre application sur Premium Storage ?  
* Comment ces facteurs influencent-ils les performances de votre application sur Premium Storage ?  
* Comment optimiser les performances d’E/S par seconde, de bande passante et de latence ?  

Ces instructions vous sont spécifiquement fournies pour Premium Storage, car les charges de travail exécutées sur Premium Storage sont extrêmement sensibles aux performances. Nous vous proposons des exemples lorsque cela s’y prête. Vous pouvez également appliquer certaines de ces instructions aux applications qui s’exécutent sur des machines virtuelles IaaS avec des disques de stockage Standard.

> [!NOTE]
> Parfois, ce qui semble être un problème de performances disque est en fait un goulot d’étranglement sur le réseau. Dans ces situations, vous devez optimiser vos [performances réseau](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md).
> Si votre machine virtuelle prend en charge la mise en réseau accélérée, vous devez vous assurer qu’elle est activée. Si elle n’est pas activée, vous pouvez l’activer sur les machines virtuelles déjà déployées sur [Windows](../articles/virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) et [Linux](../articles/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Avant de commencer, si vous ne connaissez pas le Stockage Premium, lisez tout d’abord les articles [Sélectionner un type de disque Azure pour les machines virtuelles IaaS](../articles/virtual-machines/windows/disks-types.md) et [Objectifs de performance et d’évolutivité du Stockage Azure](../articles/storage/common/storage-scalability-targets.md).

## <a name="application-performance-indicators"></a>Indicateurs de performances d’une application

Pour évaluer le degré de performances d’une application, nous nous appuyons sur divers indicateurs de performances, par exemple la vitesse à laquelle une application traite une demande utilisateur, la quantité de données qu’une application est capable de traiter pour chaque demande, le nombre de requêtes traitées par une application dans un intervalle donné, ou encore le temps d’attente que doit subir l’utilisateur avant d’obtenir une réponse à sa demande. Sur le plan technique, ces indicateurs de performances s’expriment en termes d’E/S par seconde, de débit (ou bande passante) et de latence.

Dans cette section, nous allons aborder les indicateurs de performances courants dans le contexte de Premium Storage. Dans la section suivante, intitulée « Collecte des exigences de performances de l’application », vous apprendrez à mesurer ces indicateurs de performances pour votre propre application. Dans la section consacrée à l’optimisation des performances applicatives, vous découvrirez les facteurs qui affectent ces indicateurs de performances et obtiendrez des recommandations afin de les optimiser.

## <a name="iops"></a>E/S par seconde

Le nombre d’E/S par seconde représente le nombre de demandes que votre application envoie aux disques de stockage en une seconde. Une opération d’entrée/sortie a pu être lu ou d’écriture, séquentielle ou aléatoire. Les applications OLTP, comme un site web de vente en ligne, par exemple, doivent traiter immédiatement de nombreuses requêtes d’utilisateurs simultanées. Les requêtes utilisateurs représentent des transactions d’insertion et de mise à jour qui pèsent lourdement sur la base de données, et que l’application doit traiter rapidement. Les applications OLTP requièrent dont un nombre d’E/S par seconde très élevé. Ces applications gèrent des millions de demandes d’E/S petites et aléatoires. Si vous possédez une telle application, vous devez concevoir votre infrastructure applicative de manière à optimiser les E/S. Dans la section *Optimisation des performances applicatives*, nous aborderons en détail tous les facteurs que vous devez prendre en compte pour obtenir un nombre d’E/S par seconde élevé.

Lorsque vous attachez un disque de stockage Premium à votre machine virtuelle à grande échelle, Azure met à disposition un nombre garanti d’E/S par seconde, conformément à la spécification de disque. Par exemple, un disque P50 configure 7500 E/S par seconde. Chaque taille de machine virtuelle à grande échelle est également associée à une limite spécifique d’E/S par seconde qu’elle peut prendre en charge. Par exemple, une machine virtuelle GS5 Standard a une limite de 80 000 E/S par seconde.

## <a name="throughput"></a>Débit

Le débit ou la bande passante est la quantité de données que votre application envoie aux disques de stockage dans un intervalle spécifié. Si votre application effectue des opérations d’entrée/sortie avec des tailles d’unité d’E/S importantes, elle aura besoin d’un débit élevé. Les applications d’entrepôt de données ont tendance à émettre des opérations d’analyse intensives qui accèdent simultanément à de grandes quantités de données et exécutent généralement des opérations en bloc. En d’autres termes, ces applications nécessitent un débit plus élevé. Si vous possédez une telle application, vous devez concevoir votre infrastructure de manière à en optimiser le débit. Dans la section suivante, nous décrirons en détail les facteurs que vous devrez ajuster pour y parvenir.

Lorsque vous attachez un disque de stockage Premium à une machine virtuelle à grande échelle, Azure met à disposition un débit conforme à la spécification de ce disque. Par exemple, un disque P50 configure un débit de disque de 250 Mo par seconde. Chaque taille de machine virtuelle à grande échelle est également associée à une limite spécifique de débit qu’elle peut prendre en charge. Par exemple, une machine virtuelle GS5 Standard a un débit maximal de 2 000 Mo par seconde.

La formule ci-dessous illustre la relation entre le débit et le nombre d’E/S par seconde.

![Relation entre le nombre d’E/S par seconde et le débit](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Par conséquent, il est important de déterminer les valeurs optimales de débit et d’E/S par seconde dont a besoin votre application. Lorsque vous essayez d’optimiser une de ces valeurs, l’autre est également affectée. Dans la section *Optimisation des performances applicatives*, nous aborderons plus en détail la question de l’optimisation des E/S par seconde et du débit.

## <a name="latency"></a>Latence

La latence est le temps nécessaire à une application pour recevoir une demande unique, l’envoyer aux disques de stockage et transmettre la réponse au client. Il s’agit d’une mesure critique des performances d’une application, qui s’ajoute à celle des E/S par seconde et du débit. La latence d’un disque de stockage premium correspond au temps nécessaire pour récupérer les informations d’une demande et les retourner à votre application. Premium Storage offre de faibles latences. Les disques Premium sont conçus pour offrir des latences en millisecondes à un chiffre pour la plupart des opérations d'E/S. Si vous activez une mise en cache de l’hôte en lecture seule sur des disques de stockage premium, vous pourrez obtenir une latence de lecture bien plus faible. Nous aborderons la mise en cache du disque plus en détail dans la section *Optimisation des performances applicatives*.

Lorsque vous optimisez votre application pour augmenter le nombre d’E/S par seconde et le débit, la latence de votre application s’en trouve affectée. Après avoir ajusté les performances de votre application, pensez toujours à évaluer la latence de l’application afin d’éviter un comportement de latence élevée inattendu.

Les opérations de plan de contrôle suivantes sur des disques gérés, vous devrez le déplacement du disque d’un emplacement de stockage à un autre. Cela est orchestrée par la copie de sauvegarde de données qui peuvent prendre plusieurs heures, généralement moins de 24 heures selon la quantité de données des disques. Pendant ce temps votre application peut être confrontés supérieure à celle de la latence de lecture habituelle comme des lectures puissent être redirigées vers l’emplacement d’origine et peuvent prendre plus de temps. Il n’y a aucun impact sur la latence d’écriture pendant cette période.

- Mettre à jour le type de stockage.
- Détacher et attacher un disque à partir d’une machine virtuelle à un autre.
- Créer un disque géré à partir d’un disque dur virtuel.
- Créer un disque géré à partir d’un instantané.
- Convertir des disques non gérés vers des disques gérés.

# <a name="performance-application-checklist-for-disks"></a>Liste de vérification des applications hautes performances pour les disques

La première étape de la conception d’applications hautes performances exécutées sur le Stockage Azure Premium consiste à comprendre les exigences de performances de votre application. Après avoir recueilli ces exigences de performances, vous pourrez optimiser votre application de manière à obtenir les meilleures performances possibles.

Dans la section précédente, nous avons expliqué les indicateurs de performances courants, à savoir le nombre d’E/S par seconde, le débit et la latence. Vous devez déterminer, parmi ces indicateurs de performances, lesquels sont essentiels à votre application pour fournir l’expérience utilisateur recherchée. Par exemple, un nombre d’E/S par seconde élevé est plus important pour les applications OLTP qui traitent des millions de transactions en une seconde. En revanche, un débit élevé sera plus pertinent pour les applications d’entrepôt de données qui traitent de grandes quantités de données en une seconde. De même, une très faible latence sera cruciale pour les applications en temps réel, telles que les sites web de diffusion en continu de vidéo en direct.

Ensuite, vous devrez mesurer les exigences de performances maximales de votre application tout au long de sa durée de vie. Utilisez l’exemple de liste de contrôle ci-dessous comme point de départ. Notez les exigences de performances maximales relevées au cours des périodes de charge de travail normales, des périodes de pointe et des heures creuses. En identifiant les conditions requises pour tous les niveaux de charges de travail, vous serez en mesure de déterminer les exigences de performances globales de votre application. Par exemple, la charge de travail normale d’un site web de commerce électronique correspond aux transactions habituellement traitées au quotidien au cours d’une année. Les pics de charge du site web correspondront aux transactions traitées pendant la période de Noël ou les périodes de soldes. Les pics de charge ne durent généralement que pendant un temps limité, mais ils peuvent vous obliger à mettre à l’échelle votre application en multipliant au moins par deux sa capacité par rapport à son fonctionnement normal. Déterminez les besoins au 50e percentile, au 90e percentile et au 99e percentile. Cela vous permettra d’éliminer les observations aberrantes en termes d’exigences de performances et de concentrer vos efforts sur l’optimisation des valeurs adéquates.

## <a name="application-performance-requirements-checklist"></a>Liste de contrôle pour les exigences de performances de l’application

| **Exigences de performances** | **50e percentile** | **90e percentile** | **99e percentile** |
| --- | --- | --- | --- |
| Bande passante Transactions par seconde | | | |
| % d’opérations de lecture | | | |
| % d’opérations d’écriture | | | |
| % d’opérations aléatoires | | | |
| % d’opérations séquentielles | | | |
| Taille de la demande d’E/S | | | |
| Débit moyen | | | |
| Bande passante Débit | | | |
| min. Latence | | | |
| Latence moyenne | | | |
| Bande passante UC | | | |
| Utilisation moyenne de l’UC | | | |
| Bande passante Mémoire | | | |
| Utilisation moyenne de la mémoire | | | |
| Profondeur de file d’attente | | | |

> [!NOTE]
>  vous devez anticiper la mise à l’échelle de ces nombres en fonction de la croissance prévue de votre application. Il est judicieux de planifier la croissance, car il pourrait être plus difficile de modifier l’infrastructure ultérieurement afin d’en améliorer les performances.

Si vous possédez une application existante et que vous souhaitez migrer vers Premium Storage, commencez par compléter la liste de contrôle ci-dessus pour l’application existante. Développez ensuite un prototype de votre application sur Premium Storage et concevez l’application selon les instructions décrites dans la section *Optimisation des performances applicatives* dans la suite de ce document. L’article suivant décrit les outils que vous pouvez utiliser pour collecter les mesures de performances.

### <a name="counters-to-measure-application-performance-requirements"></a>Compteurs de mesure des exigences de performances de l’application

La meilleure façon de mesurer les exigences de performances de votre application consiste à utiliser les outils d’analyse des performances fournies par le système d’exploitation du serveur. Vous pouvez utiliser PerfMon pour Windows et iostat pour Linux. Ces outils capturent des compteurs correspondant à chaque mesure expliquée dans la section ci-dessus. Vous devez capturer les valeurs de ces compteurs lorsque votre application s’exécute son vecteur normal, pointe et des charges de travail les heures creuses.

Les compteurs PerfMon sont disponibles pour le processeur, pour la mémoire et pour chaque disque logique et chaque disque physique de votre serveur. Lorsque vous utilisez les disques de stockage premium avec une machine virtuelle, les compteurs de disque physique s’appliquent à chaque disque de stockage premium et les compteurs de disque logique à chaque volume créé sur les disques de stockage premium. Vous devez capturer les valeurs des disques qui hébergent la charge de travail de votre application. S’il existe un mappage un-à-un entre les disques physiques et logiques, vous pouvez consulter les compteurs de disque physique ; sinon, référez-vous aux compteurs de disque logique. Sous Linux, la commande iostat génère un rapport d’utilisation du processeur et du disque. Le rapport d’utilisation du disque fournit des statistiques par unité physique ou par partition. Si vous avez un serveur de base de données avec ses données et les journaux sur des disques distincts, récupérez ces données pour les deux disques. Tableau ci-dessous décrit les compteurs des disques, des processeurs et mémoire :

| Compteur | Description | PerfMon | Iostat |
| --- | --- | --- | --- |
| **Nombre d’E/S par seconde ou de transactions par seconde** |Nombre de demandes d’E/S émises sur le disque de stockage par seconde. |Nb d’opérations de lectures de disque/s  <br>  Nb d’opération d’écriture de disque/s |tps  <br> r/s  <br>  w/s |
| **Opérations de lecture et d’écriture de disque** |% d’opérations de lecture et d’écriture exécutées sur le disque. |% temps de lecture du disque  <br>  % temps d’écriture du disque |r/s  <br>  w/s |
| **Débit** |Quantité de données lues ou écrites sur le disque par seconde. |Nb d’octets de lecture de disque/s  <br>  Nb d’octets d’écriture de disque/s |kB_read/s <br> kB_wrtn/s |
| **Latence** |Durée totale d’exécution d’une demande d’E/S sur le disque. |Temps de lecture moyen du disque/s  <br>  Temps d’écriture moyen du disque/s |await  <br>  svctm |
| **Taille d’E/S** |La taille des demandes d’E/S émises sur les disques de stockage. |Nb moyen d’octets en lecture du disque  <br>  Nb moyen d’octets en écriture du disque |avgrq-sz |
| **Profondeur de file d’attente** |Nombre de demandes d’E/S en attente de lecture ou d’écriture sur le disque de stockage. |Longueur de file d’attente actuelle du disque |avgqu-sz |
| **Bande passante Mémoire** |Quantité de mémoire nécessaire pour une exécution fluide de l’application |% d’octets dédiés utilisés |Use vmstat |
| **Bande passante UC** |Quantité d’UC nécessaire pour une application fluide de l’application |% temps processeur |%util |

En savoir plus sur [iostat](https://linux.die.net/man/1/iostat) et [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optimiser les performances de l'application

Les principaux facteurs qui influencent les performances d’une application exécutée sur Premium Storage sont les demandes de la Nature des e/s, taille de machine virtuelle, taille du disque, nombre de disques, le disque de mise en cache, le multithreading et la profondeur de file d’attente. Vous pouvez contrôler certains de ces facteurs avec les dispositifs fournis par le système. La plupart des applications ne vous permettront peut-être pas de modifier directement la taille d’E/S et la profondeur de file d’attente. Par exemple, si vous utilisez SQL Server, vous ne pouvez choisir ni la taille d’E/S ni la profondeur de file d’attente. SQL Server choisit les valeurs optimales de taille d’E/S et de profondeur de file d’attente profondeur de manière à obtenir les meilleures performances. Il est important de comprendre les effets de ces deux types de facteurs sur les performances de votre application, afin que vous puissiez configurer les ressources appropriées pour répondre à vos besoins de performances.

Dans cette section, reportez-vous à la liste de contrôle des exigences de performances applicatives que vous avez créée pour identifier le degré nécessaire d’optimisation des performances de votre application. Sur cette base, vous serez en mesure de déterminer les facteurs de cette section que vous devrez paramétrer. Pour évaluer les effets de chaque facteur sur les performances de votre application, exécutez les outils de benchmarking sur l’installation de votre application. Reportez-vous à la section de Benchmarking à la fin de cet article pour obtenir des instructions exécuter les outils d’évaluation courants sur les machines virtuelles Linux et Windows.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optimiser les e/s, le débit et la latence en un clin de œil

Le tableau ci-dessous récapitule les facteurs de performances et les étapes nécessaires pour optimiser les e/s, le débit et la latence. Les sections qui suivent ce résumé décrivent en détail chaque facteur.

Pour plus d’informations sur les tailles de machine virtuelle et sur les E/S par seconde, le débit et la latence disponible pour chaque type de machine virtuelle, consultez [Tailles des machines virtuelles Linux](../articles/virtual-machines/linux/sizes.md) ou [Tailles des machines virtuelles Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **D’OPÉRATIONS D’E/S PAR SECONDE** | **Débit** | **Latence** |
| --- | --- | --- | --- |
| **Exemple de scénario** |Application OLTP d’entreprise nécessitant un taux très élevé de transactions par seconde. |Application d’entreposage de données d’entreprise traitant de grandes quantités de données. |Applications en temps quasi-réel nécessitant une réponse instantanée aux demandes utilisateur, telles que les jeux en ligne. |
| Facteurs de performances | &nbsp; | &nbsp; | &nbsp; |
| **Taille d’E/S** |Une plus petite taille d’E/S génère un nombre d’E/S par seconde plus élevé. |Une plus grande taille d’E/S génère un débit plus élevé. | &nbsp;|
| **Taille de la machine virtuelle** |Utilisez une taille de machine virtuelle qui offre un nombre d’E/S par seconde supérieur aux besoins de votre application. |Utilisez une taille de machine virtuelle qui offre une limite de débit supérieure aux besoins de votre application. |Utilisez une taille de machine virtuelle qui offre une limite de mise à l’échelle supérieure aux besoins de votre application. |
| **Taille du disque** |Utilisez une taille de disque qui offre un nombre d’E/S par seconde supérieur aux besoins de votre application. |Utilisez une taille de disque qui offre une limite de débit supérieure aux besoins de votre application. |Utilisez une taille de disque qui offre une limite de mise à l’échelle supérieure aux besoins de votre application. |
| **Limites de mises à l’échelle des machines virtuelles et des disques** |La limite d’E/S par seconde de la taille de machine virtuelle choisie doit être supérieure au nombre total d’E/S par seconde générées par les disques de stockage premium qui lui sont associés. |La limite de débit par seconde de la taille de machine virtuelle choisie doit être supérieure au débit total généré par les disques de stockage premium qui lui sont associés. |Les limites de mise à l’échelle de la taille de machine virtuelle choisie doivent être supérieures aux limites totales de mise à l’échelle des disques de stockage premium qui lui sont associés. |
| **Mise en cache du disque** |Activez le cache en lecture seule sur les disques de stockage premium avec des opérations de lecture intensives pour obtenir un taux d’E/S en lecture par seconde plus élevé. | &nbsp; |Activez le cache en lecture seule sur les disques de stockage premium avec des opérations de lecture intensives pour obtenir de très faibles latences en lecture. |
| **Entrelacement de disques** |Utilisez plusieurs disques et entrelacez-les pour augmenter la limite combinée de débit et d’E/S par seconde. La limite combinée par machine virtuelle doit être supérieure aux limites combinées des disques premium associés. | &nbsp; | &nbsp; |
| **Taille de l’entrelacement** |Taille d’entrelacement plus petite pour les petits schémas d’E/S aléatoires associés aux applications OLTP. Par exemple, utiliser taille d’entrelacement de 64 Ko pour les applications OLTP SQL Server. |Plus grande taille d’entrelacement pour les grands schémas d’E/S séquentiels associés aux applications d’entrepôt de données. Par exemple, utilisez taille d’entrelacement de 256 Ko pour l’application de l’entrepôt de données SQL Server. | &nbsp; |
| **Traitement multithread** |Utilisez le traitement multithread pour envoyer un plus grand nombre de demandes à Premium Storage et augmenter ainsi le débit et le nombre d’E/S par seconde. Sur SQL Server, par exemple, définissez une valeur MAXDOP élevée pour allouer plus de ressources processeur à SQL Server. | &nbsp; | &nbsp; |
| **Profondeur de file d’attente** |Une plus grande profondeur de file d’attente génère un nombre plus élevé d’E/S par seconde. |Une plus grande profondeur de file d’attente génère un débit plus élevé. |Une plus petite profondeur de file d’attente réduit les latences. |

## <a name="nature-of-io-requests"></a>Nature des demandes d’E/S

Une demande d’E/S représente une unité d’opération d’entrée/sortie exécutée par votre application. Identifier la nature des demandes d’E/S (aléatoires ou séquentielles, en lecture ou en écriture, petites ou grandes) vous permet de déterminer les exigences de performances de votre application. Il est important de comprendre la nature des demandes d’e/s, à prendre les bonnes décisions lors de la conception de votre infrastructure d’application.

La taille des E/S compte parmi les facteurs les plus importants. La taille des E/S correspond à la taille de la demande d’opération d’entrée/sortie générée par votre application. La taille des E/S a un impact significatif sur les performances, en particulier sur le nombre d’E/S par seconde et sur la bande passante, que l’application sera en mesure de délivrer. La formule suivante montre la relation entre les e/s, taille d’e/s et de bande passante/débit.  
    ![](media/premium-storage-performance/image1.png)

Certaines applications vous permettent de modifier leur taille d’E/S, contrairement à d’autres. Par exemple, SQL Server détermine lui-même la taille d’E/S optimale et ne fournit aux utilisateurs aucun dispositif permettant de la modifier. De son côté, Oracle propose un paramètre appelé [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) que vous pouvez utiliser pour configurer la taille de la demande d’E/S de la base de données.

Si vous utilisez une application qui ne permet pas de modifier la taille d’E/S, suivez les indications de cet article pour optimiser les indicateurs de performances qui correspondent le mieux à votre application. Par exemple,

* Une application OLTP génère des millions de demandes d’E/S petites et aléatoires. Pour gérer ces types de demandes d’e/s, vous devez concevoir votre infrastructure d’application pour obtenir des e/s plus élevé.  
* Une application d’entrepôt de données génère d’importantes demandes d’E/S séquentielles. Pour gérer ces types de demandes d’e/s, vous devez concevoir votre infrastructure d’application pour obtenir la bande passante ou le débit plus élevé.

Si vous utilisez une application qui vous permet de modifier la taille d’E/S, utilisez cette règle générale tout en respectant les autres instructions de performances.

* Une plus petite taille d’E/S pour générer un nombre d’E/S par seconde plus élevé. Par exemple, 8 Ko pour une application OLTP.  
* Une plus grande taille d’E/S pour augmenter la bande passante ou le débit. Par exemple, 1 024 Ko pour une application d’entrepôt de données.

Voici un exemple de calcul du nombre d’E/S par seconde et du débit ou de la bande passante pour votre application. Supposons une application utilisant un disque P30. Un disque P30 peut atteindre au maximum 5 000 E/S par seconde et 200 Mo de débit/bande passante par seconde. À présent, si votre application a besoin d’utiliser le nombre maximal d’E/S par seconde du disque P30 alors que vous utilisez une taille d’E/S inférieure à 8 Ko, vous devriez obtenir 40 Mo de bande passante par seconde. En revanche, si votre application requiert la valeur maximale de débit et de bande passante à partir du disque P30 et que vous utilisez une taille d’E/S supérieure à 1 024 Ko, vous obtiendrez seulement 200 E/S par seconde. Vous devez donc ajuster la taille d’E/S pour qu’elle réponde aux besoins de votre application tant en terme d’E/S que de débit et de bande passante. Le tableau ci-dessous récapitule les différentes tailles d’E/S en indiquant le nombre d’E/S par seconde et le débit correspondant dans le cas d’un disque P30.

| Spécification de l’application | Taille des E/S | E/S par seconde | Débit/bande passante |
| --- | --- | --- | --- |
| Nb max. d’E/S par seconde |8 Ko |5 000 |40 Mo par seconde |
| Débit max. |1 024 Ko |200 |200 Mo par seconde |
| Débit max + nb élevé d’E/S par seconde |64 Ko |3 200 |200 Mo par seconde |
| Nb max d’E/S par seconde + débit élevé |32 Ko |5 000 |160 Mo par seconde |

Pour obtenir une valeur d’E/S par seconde et de bande passante supérieure à la valeur maximale d’un seul disque de stockage premium, utilisez plusieurs disques premium entrelacés ensemble. Par exemple, entrelacez deux disques P30 pour obtenir un total de 10 000 E/S par seconde ou un débit combiné de 400 Mo par seconde. Comme nous l’expliquons dans la section suivante, vous devez utiliser une taille de machine virtuelle qui prend en charge la valeur combinée d’E/S par seconde et de débit du disque.

> [!NOTE]
>  lorsque vous augmentez les E/S par seconde ou le débit, l’autre valeur augmente également ; assurez-vous de ne pas franchir les limites de débit ou d’E/S par seconde du disque ou de la machine virtuelle lorsque vous augmentez l’une de ces valeurs.

Pour évaluer les effets de la taille des E/S sur les performances de l’application, vous pouvez exécuter des outils d’évaluation sur votre machine virtuelle et sur vos disques. Créez plusieurs séries de tests et utilisez une taille d’E/S différente pour chaque exécution afin d’en déterminer l’impact. Reportez-vous à la section de Benchmarking à la fin de cet article pour plus d’informations.

## <a name="high-scale-vm-sizes"></a>Tailles des machines virtuelles à grande échelle

Lorsque vous commencez la conception d’une application, l’une des premières choses à faire est de choisir une machine virtuelle qui hébergera votre application. Premium Storage est fourni avec des tailles de machine virtuelle à grande échelle capables d’exécuter des applications qui requièrent une plus grande puissance de calcul et de hautes performances d’E/S du disque local. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et l’utilisation d’un disque SSD comme disque local. Les machines virtuelles des séries DS, DSv2 et GS sont des exemples de ces machines virtuelles prenant en charge le stockage Premium.

Ces machines virtuelles sont disponibles dans différentes tailles, avec un nombre différent de cœurs de processeur, mémoire, du système d’exploitation et taille du disque temporaire. Chaque taille de chaque machine virtuelle possède également un nombre maximal de disques de données que vous pouvez attacher à la machine virtuelle. Par conséquent, la taille de machine virtuelle choisie affectera la quantité de traitement, de mémoire et de capacité de stockage disponible pour votre application. Elle affecte également le coût de traitement et de stockage. Vous trouverez ci-dessous les spécifications de la plus grande taille de machine virtuelle d’une série DS, d’une série DSv2 et d’une série GS :

| Taille de la machine virtuelle | Cœurs d’unité centrale | Mémoire | Tailles du disque de la machine virtuelle | Bande passante disques de données | Taille du cache | E/S par seconde | Limites d’E/S du cache de bande passante |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 Go |OS = 1023 Go  <br>  SSD local = 224 Go |32 |576 Go |50 000 E/S par seconde  <br>  512 Mo par seconde |4 000 E/S par seconde et 33 Mo par seconde |
| Standard_GS5 |32 |448 Go |OS = 1023 Go  <br>  SSD local = 896 Go |64 |4 224 Go |80 000 E/S par seconde  <br>  2 000 Mo par seconde |5 000 E/S par seconde et 50 Mo par seconde |

Pour afficher une liste complète de toutes les tailles de machine virtuelle Azure disponibles, consultez les articles [Tailles des machines virtuelles dans Azure (Windows)](../articles/virtual-machines/windows/sizes.md) ou [Tailles des machines virtuelles dans Azure (Linux)](../articles/virtual-machines/linux/sizes.md). Choisissez une taille de machine virtuelle capable de s’adapter aux exigences de performances souhaitées de votre application. En outre, prenez en compte les considérations suivantes lors du choix de tailles de machine virtuelle.

*Limites de mise à l’échelle*  
 Les limites d’E/S par seconde par machine virtuelle et par disque sont différentes et indépendantes les unes des autres. Assurez-vous que l’application génère un nombre d’E/S par seconde dans les limites de la machine virtuelle et des disques premium qui lui sont associés. Dans le cas contraire, les performances de l’application seront limitées.

Par exemple, supposons une application exigeant un maximum de 4 000 E/S par seconde. Pour atteindre cette valeur, vous devez configurer un disque P30 sur une machine virtuelle DS1. Le disque P30 peut fournir jusqu’à 5 000 E/S par seconde. En revanche, la machine virtuelle DS1 est limitée à 3 200 E/S par seconde. Par conséquent, les performances de l’application seront contraintes par la limite de la machine virtuelle à 3 200 E/S par seconde, ce qui affectera les performances. Pour éviter cette situation, choisissez une taille de machine virtuelle et de disque qui satisfont toutes deux aux exigences de l’application.

*Coût d’exploitation*  
 Dans de nombreux cas, il est possible que le coût global d’exploitation lié à l’utilisation de Premium Storage soit inférieur à celui associé à l’utilisation d’un stockage Standard.

Imaginez par exemple une application nécessitant 16 000 E/S par seconde. Pour atteindre ces performances, vous devez une norme\_D14 machine virtuelle IaaS Azure, qui peut octroyer à un nombre maximal d’IOPS de 16 000 à l’aide de 32 disques de 1 To de stockage standard. Chaque disque de stockage standard de 1 to peut atteindre un maximum de 500 e/s. Le coût mensuel estimé de cette machine virtuelle sera de 1 570 $. Le coût mensuel de 32 disques de stockage standard sera de 1 638 $. Le coût total mensuel estimé sera donc de 3 208 $.

Toutefois, si vous aviez hébergé la même application sur Premium Storage, vous auriez besoin d’une plus petite taille de machine virtuelle et de moins de disques de stockage premium, ce qui réduirait le coût global. Une machine virtuelle Standard\_StandardDS13 peut couvrir les 16 000 E/S par seconde requis à l’aide de quatre disques P30. La machine virtuelle DS13 délivre 25 600 E/S par seconde au maximum et chaque disque P30 délivre 5 000 E/S par seconde. Globalement, cette configuration peut atteindre 5 000 x 4 = 20 000 E/S par seconde. Le coût mensuel estimé de cette machine virtuelle sera de 1 003 $. Le coût mensuel de quatre disques de stockage premium P30 sera de 544,34 $. Le coût total mensuel estimé sera donc de 1 544 $.

Le tableau ci-dessous résume la répartition des coûts de ce scénario pour un stockage Standard et Premium.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Coût de la machine virtuelle par mois** |1 570,58 $ (Standard\_StandardD14) |1 003,66 $ (Standard\_StandardDS13) |
| **Coût des disques par mois** |1 638,40 $ (disques 32 x 1 To) |544,34 $ (4 disques P30) |
| **Coût global par mois** |3 208,98 $ |1 544,34 $ |

*Distributions Linux*  

Avec Azure Premium Storage, vous obtenez le même niveau de performances pour les machines virtuelles exécutant Windows et Linux. Nous prenons en charge diverses distributions Linux, dont vous trouverez la liste complète [ici](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Il est important de noter que les différentes distributions sont mieux adaptées à différents types de charges de travail. Vous verrez différents niveaux de performances selon la distribution sur laquelle votre charge de travail est exécutée. Testez les distributions Linux avec votre application et choisissez celle qui vous convient le mieux.

Lorsque vous exécutez Linux avec Premium Storage, vérifiez les dernières mises à jour sur les pilotes requis pour garantir de meilleures performances.

## <a name="premium-storage-disk-sizes"></a>Tailles de disques Stockage Premium

Azure Premium Storage offre trois tailles de disque qui sont actuellement en version préliminaire et les huit tailles de disque de la disponibilité générale. Chaque taille de disque a une limite de mise à l’échelle différents pour les e/s, la bande passante et de stockage. Choisissez la taille de disque Premium Storage adaptée aux exigences de l’application et à la taille de machine virtuelle à grande échelle. Le tableau ci-dessous répertorie les tailles de 11 disque et de leurs fonctionnalités. Les tailles P4, P6, P15, P60, P70 et P80 ne sont actuellement prises en charge que par la fonctionnalité Disques managés.

| Type de disque Premium  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| Taille du disque           | 32 Gio | 64 Gio | 128 Go| 256 Gio| 512 Go            | 1 024 Gio (1 Tio)    | 2 048 Gio (2 Tio)    | 4 095 Gio (4 Tio)    | 8 192 Gio (8 Tio)    | 16 384 Gio (16 Tio)    | 32 767 Gio (32 Tio)    |
| IOPS par disque       | 120   | 240   | 500   | 1100 | 2 300              | 5 000              | 7500              | 7500              | 12 500              | 15 000              | 20 000              |
| Débit par disque | 25 Mio par seconde  | 50 Mio par seconde  | 100 Mio par seconde |125 Mio par seconde | 150 Mio par seconde | 200 Mio par seconde | 250 Mio par seconde | 250 Mio par seconde | 480 Mio par seconde | 750 Mio par seconde | 750 Mio par seconde |

Le nombre de disques que vous choisissez dépend de la taille de disque choisie. Vous pouvez utiliser un seul disque P50 ou plusieurs disques P10 pour répondre aux besoins de votre application. Tenez compte des remarques ci-dessous pour faire votre choix.

*Limites de mise à l’échelle (E/S par seconde et débit)*  
 Les limites d’E/S par seconde et de débit de chaque taille de disque Premium sont différentes et indépendantes des limites de mise à l’échelle de la machine virtuelle. Assurez-vous que le nombre total d’E/S par seconde et le débit total des disques respectent bien les limites de mise à l’échelle de la taille de machine virtuelle choisie.

Imaginez par exemple que votre application requiert un débit maximum de 250 Mo/s et que vous utilisez une machine virtuelle DS4 avec un seul disque P30. La machine virtuelle DS4 peut délivrer un débit maximum de 256 Mo/s. Cependant, un disque P30 a une limite de débit de 200 Mo par seconde. De ce fait, l’application sera contrainte à 200 Mo/s en raison de la limite du disque. Pour contourner cette limite, configurez plusieurs disques de données sur la machine virtuelle ou redimensionnez vos disques (de P40 à P50).

> [!NOTE]
>  Les lectures traitées par le cache n’étant pas incluses dans les E/S et le débit de disque, elles ne sont pas soumises aux limites de disque. Le cache possède sa propre limite de débit et d’E/S pour chaque machine virtuelle.
>
> Par exemple, les lectures et écritures sont initialement de 60 Mo/s et 40 Mo/s respectivement. Au fil du temps, le cache augmente et sert de plus en plus de lectures. Vous pouvez alors obtenir un plus grand débit en écriture à partir du disque.

*Nombre de disques*  
 Déterminez le nombre de disques dont vous aurez besoin en évaluant les besoins de l’application. Chaque taille de chaque machine virtuelle est limitée quant au nombre de disques que vous pouvez lui associer. En règle générale, ce nombre est égal à deux fois le nombre de cœurs. Assurez-vous que la taille de machine virtuelle que vous choisissez est capable de prendre en charge le nombre de disques nécessaires.

N’oubliez pas les disques Premium Storage délivrent des performances supérieures à celles des disques de stockage Standard. Par conséquent, si vous migrez votre application d’un stockage Standard à un stockage Premium à partir d’une machine virtuelle IaaS Azure, vous aurez probablement besoin de moins de disques premium pour atteindre des performances identiques ou supérieures pour votre application.

## <a name="disk-caching"></a>Mise en cache du disque

Les machines virtuelles à grande échelle qui exploitent Azure Premium Storage ont une technologie de mise en cache à plusieurs niveaux appelée BlobCache. BlobCache utilise à la fois la RAM de la machine virtuelle et le SSD local pour la mise en cache. Ce cache est disponible pour les disques persistants Premium Storage et pour les disques locaux de la machine virtuelle. Par défaut, ce paramètre de cache est défini en lecture/écriture pour les disques du système d’exploitation et en lecture seule pour les disques de données hébergés sur Premium Storage. Lorsque la mise en cache du disque est activée sur les disques Premium Storage, les machines virtuelles à grande échelle peuvent atteindre des niveaux de performances extrêmement élevés qui dépassent les performances du disque sous-jacent.

> [!WARNING]
> Mise en cache disque n’est pas pris en charge pour les disques supérieurs à 4 TIO. Si plusieurs disques sont attachés à votre machine virtuelle, chaque disque est 4 To ou plus petits prendra en charge la mise en cache.
>
> La modification du paramètre de cache d’un disque Azure détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle redémarre. Arrêtez toutes les applications et services qui risquent d’être affectés par cette indisponibilité avant de modifier le paramètre de cache du disque.

Pour en savoir plus sur le fonctionnement de BlobCache, reportez-vous à l’article du blog interne [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Il est important d’activer le cache sur le jeu de disques approprié. Le fait de devoir activer ou non la mise en cache du disque sur un disque premium dépendra du modèle de charge de travail que ce disque doit gérer. Le tableau ci-dessous répertorie les paramètres de cache par défaut pour les disques du système d’exploitation et les disques de données.

| **Type de disque** | **Paramètre de cache par défaut** |
| --- | --- |
| Disque de système d’exploitation |Lecture/écriture |
| Disque de données |Lecture seule |

Voici les paramètres de cache de disque recommandés pour les disques de données :

| **Paramètre de mise en cache du disque** | **Conditions recommandées d’utilisation de ce paramètre** |
| --- | --- |
| Aucun |Configurer le cache hôte sur cette option pour les disques en lecture seule et les disques gourmands en écriture. |
| Lecture seule |Configurer le cache hôte en lecture seule pour les disques en lecture seule et les disques en lecture/écriture. |
| Lecture/écriture |Configurer le cache hôte en lecture/écriture uniquement si votre application gère correctement l’écriture des données mises en cache sur les disques persistants lorsque cela est nécessaire. |

*Lecture seule*  
 En configurant une mise en cache en lecture seule sur des disques de données Premium Storage, vous pouvez obtenir une faible latence de lecture et obtenir de très hautes performances d’E/S et de débit en lecture pour votre application. Cela est dû à deux raisons :

1. Les lectures effectuées à partir du cache, qui se trouvent sur la mémoire virtuelle et sur le SSD local, sont beaucoup plus rapides que les lectures effectuées à partir du disque de données, qui réside sur Azure Blob Storage.  
1. Premium Storage ne tient pas compte des lectures traitées à partir du cache pour le calcul du nombre d’E/S par seconde et du débit du disque. Par conséquent, votre application est en mesure de délivrer de meilleures performances totales en termes d’E/S par seconde et de débit.

*Lecture/écriture*  
 Par défaut, le cache en lecture/écriture est activé sur les disques du système d’exploitation. Nous avons récemment ajouté la prise en charge de la mise en cache en lecture/écriture sur les données des disques. Si vous utilisez la mise en cache en lecture/écriture, vous devez disposer d’un moyen approprié d’écrire les données du cache sur des disques persistants. Par exemple, SQL Server gère lui-même l’écriture de données mises en cache sur les disques de stockage persistants. L’utilisation d’un cache en lecture/écriture avec une application qui ne gère pas la persistance des données requises peut entraîner des pertes de données en cas de panne de la machine virtuelle.

Par exemple, vous pouvez appliquer ces instructions à une instance SQL Server exécutée sur Premium Storage de la manière suivante :

1. Configurez le cache en lecture seule sur les disques de stockage Premium qui hébergent des fichiers de données.  
   a.  Les lectures rapides du cache réduisent le temps de requête de SQL Server puisque les pages de données sont récupérées à partir du cache bien plus rapidement que lorsque l’opération s’effectue directement à partir des disques de données.  
   b.  Le traitement des lectures à partir du cache signifie que les disques de données premium délivrent un débit supplémentaire. SQL Server peut utiliser ce débit supplémentaire pour la récupération d’un plus grand nombre de pages de données et d’autres opérations telles que la sauvegarde/restauration, les charges de traitement par lots et les reconstructions d’index.  
1. Choisissez l’option « Aucun » pour le cache sur les disques de stockage Premium qui hébergent des fichiers journaux.  
   a.  Les fichiers journaux ont principalement des opérations d’écriture intensives. Ils ne bénéficient donc pas du cache en lecture seule.

### <a name="optimize-performance-on-linux-vms"></a>Optimiser les performances sur les machines virtuelles Linux

Pour tous les disques ultra ou SSD Premium dont le paramètre de cache est **ReadOnly** ou **None**, vous devez désactiver les « barrières » lorsque vous montez le système de fichiers. Ces barrières sont inutiles dans ce cas de figure, car les écritures sur les disques de stockage Premium sont pérennes avec ces paramètres de cache. Lorsque la demande d’écriture se termine, les données sont stockées dans le magasin permanent. Utilisez l’une des méthodes suivantes pour désactiver les « barrières ». Choisissez celle qui correspond à votre système de fichiers :
  
* Pour **reiserFS**, pour désactiver les barrières, utilisez l’option de montage `barrier=none`. (Pour activer les barrières, utilisez `barrier=flush`.)
* Pour **ext3/ext4**, pour désactiver les barrières, utilisez l’option de montage `barrier=0`. (Pour activer les barrières, utilisez `barrier=1`.)
* Pour **XFS**, pour désactiver les barrières, utilisez l’option de montage `nobarrier`. (Pour activer les barrières, utilisez `barrier`.)
* Pour les disques de stockage Premium avec une mise en cache définie sur **ReadWrite**, activez les barrières de durabilité de l’écriture.
* Pour conserver les étiquettes de volume après avoir redémarré la machine virtuelle, vous devez mettre à jour /etc/fstab avec les références identificateur unique universel (UUID) aux disques. Pour plus d’informations, consultez [Ajouter un disque géré à une machine virtuelle Linux](../articles/virtual-machines/linux/add-disk.md).

Les distributions Linux suivantes ont été validées pour les SSD Premium. Nous vous recommandons de mettre vos machines virtuelles au niveau de l’une de ces versions ou d’une version ultérieure pour améliorer les performances et la stabilité des SSD Premium. 

Certaines versions nécessitent également la dernière version 4.0 de Linux Integration Services (LIS) pour Azure. Pour télécharger et installer une distribution, suivez le lien figurant dans le tableau suivant. Nous ajoutons des images à la liste à chaque validation. Nos validations indiquent que les performances varient pour chaque image. Les performances dépendent des caractéristiques de la charge de travail et de vos paramètres d’image. Chaque image est optimisée pour des charges de travail particulières.

| Distribution | Version | Noyau pris en charge | Détails |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 requis](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consultez la remarque dans la section suivante* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recommandé](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consultez la remarque dans la section suivante* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

## <a name="lis-drivers-for-openlogic-centos"></a>Pilotes LIS pour Openlogic CentOS

Si vous exécutez des machines virtuelles OpenLogic CentOS, exécutez la commande suivante pour installer les pilotes les plus récents :

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Pour activer les nouveaux pilotes, redémarrez la machine virtuelle.

## <a name="disk-striping"></a>Entrelacement de disques

Lorsqu’une machine virtuelle à grande échelle est connectée à plusieurs disques de stockage premium persistants, les disques peuvent être entrelacés ensemble pour agréger les E/S par seconde, la bande passante et la capacité de stockage.

Sous Windows, vous pouvez utiliser les espaces de stockage pour entrelacer les disques. Vous devez configurer une seule colonne pour chaque disque dans un pool. Dans le cas contraire, les performances globales du volume entrelacé peuvent être limitées, en raison d’une distribution inégale du trafic sur les disques.

Important : À l’aide de l’IU du Gestionnaire de serveur, vous pouvez définir un nombre maximal de 8 colonnes au total pour un volume agrégé par bandes. Lors de l’attachement de disques de plus de huit, utilisez PowerShell pour créer le volume. PowerShell vous permet de définir un nombre de colonnes égal au nombre de disques. Par exemple, s’il existe 16 disques dans un agrégat unique, spécifiez 16 colonnes dans le paramètre *NumberOfColumns* de l’applet de commande *New-VirtualDisk*.

Sous Linux, utilisez l’utilitaire MDADM pour entrelacer les disques. Pour obtenir des instructions détaillées sur l’entrelacement de disques sous Linux, reportez-vous à [Configuration d’un RAID logiciel sous Linux](../articles/virtual-machines/linux/configure-raid.md).

*Taille de l’entrelacement*  
 La taille d’entrelacement constitue un facteur important dans la configuration de l’entrelacement de disques. La taille d’entrelacement ou la taille de bloc représente la plus petite partie des données que l’application peut traiter sur un volume entrelacé. La taille d’entrelacement que vous configurez varie selon le type d’application et le modèle de demande associé. Si vous choisissez une taille d’entrelacement incorrecte, vous risquez de rencontrer un défaut d’alignement des E/S, ce qui conduirait à une dégradation des performances de votre application.

Par exemple, si une demande d’E/S générée par votre application est supérieure à la taille d’entrelacement de disque, le système de stockage écrira au-delà des limites d’unité de bande sur plusieurs disques. Au moment d’accéder aux données, il devra effectuer une recherche sur plusieurs unités de bande pour exécuter la demande. L’effet cumulatif de ce comportement peut entraîner une dégradation significative des performances. En revanche, si la taille de la demande d’E/S est inférieure à la taille d’entrelacement, et si ces E/S sont aléatoires par nature, les demandes d’E/S peuvent s’ajouter sur le même disque et provoquer un goulot d’étranglement responsable d’une dégradation des performances d’E/S.

Selon le type de charge de travail que votre application exécute, choisissez une taille d’entrelacement appropriée. Pour les petites demandes d’E/S aléatoires, utilisez une plus petite taille d’entrelacement. Alors que pour les grandes e/s séquentielles demandes utilisent une plus grande taille d’entrelacement. Découvrez les tailles d’entrelacement recommandées pour l’application exécutée sur Premium Storage. Pour SQL Server, configurez la taille d’entrelacement de 64 Ko pour les charges de travail OLTP et sur 256 Ko pour les charges d’entreposage de données. Pour en savoir plus, consultez [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance)

> [!NOTE]
>  vous pouvez entrelacer au maximum 32 disques de stockage premium sur une machine virtuelle DS et 64 disques de stockage premium sur une machine virtuelle GS.

## <a name="multi-threading"></a>Multithreading

Azure a conçu une plate-forme Premium Storage hautement parallèle. Par conséquent, une application multithread délivre de bien meilleures performances qu’une application à thread unique. Une application multithread répartit ses tâches sur plusieurs threads et augmente l’efficacité de son exécution en utilisant au maximum les ressources de machine virtuelle et de disque.

Par exemple, si votre application s’exécute sur une machine virtuelle monocœur utilisant deux threads, le processeur peut basculer entre les deux threads pour gagner en efficacité. En attendant l’exécution d’un thread sur une E/S de disque, le processeur peut basculer vers l’autre thread. De cette façon, deux threads peuvent accomplir bien plus qu’un seul thread. Si la machine virtuelle possède plusieurs cœurs, elle réduit davantage le temps d’exécution dans la mesure où chaque cœur peut exécuter des tâches en parallèle.

Vous n’êtes peut-être pas en mesure de modifier la façon dont une application du commerce implémente un seul thread ou plusieurs threads. Par exemple, SQL Server est capable de gérer plusieurs processeurs et plusieurs nœuds. SQL Server détermine cependant dans quelles conditions il utilisera un ou plusieurs threads pour traiter une requête. Il peut exécuter des requêtes et créer des index à l’aide du traitement multithread. Dans le cas d’une requête impliquant une fusion de tables volumineuses et un tri des données avant le renvoi des résultats à l’utilisateur, SQL Server utilisera probablement plusieurs threads. Toutefois, un utilisateur ne peut pas décider si SQL Server exécutera une requête à l’aide d’un ou plusieurs threads.

Certains paramètres de configuration peuvent être modifiés pour influencer ce traitement multithread ou parallèle d’une application. Dans le cas de SQL Server, par exemple, il s’agit de la configuration maximale de degré de parallélisme. Ce paramètre appelé MAXDOP permet de configurer le nombre maximal de processeurs que SQL Server peut utiliser lors d’un de traitement parallèle. Vous pouvez configurer MAXDOP pour des requêtes individuelles ou pour des opérations d’index. Cela vous sera utile si vous souhaitez équilibrer les ressources de votre système pour une application stratégique en termes de performances.

Supposons par exemple que votre application basée sur SQL Server exécute simultanément une grande requête et une opération d’index. Imaginons que vous souhaitiez que l’opération d’index soit plus performante que la requête volumineuse. Dans ce cas, vous pouvez spécifier une valeur MAXDOP de l’opération d’index supérieure à la valeur MAXDOP de la requête. SQL Server dispose ainsi d’un plus grand nombre de processeurs exploitables pour l’opération d’index par rapport au nombre de processeurs qu’il peut consacrer à la requête volumineuse. N’oubliez pas que vous n’avez aucun contrôle sur le nombre de threads que SQL Server utilisera pour chaque opération. Vous pouvez contrôler le nombre maximal de processeurs dédiés au traitement multithread.

En savoir plus sur [Degrés de parallélisme](https://technet.microsoft.com/library/ms188611.aspx) dans SQL Server. Découvrez les paramètres qui influencent le traitement multithread dans votre application et leurs configurations pour optimiser les performances.

## <a name="queue-depth"></a>Profondeur de file d’attente

La profondeur de file d’attente ou la longueur de file d’attente ou la taille de la file d’attente est le nombre de demandes d’e/s en attente dans le système. La valeur de profondeur de file d’attente détermine le nombre d’opérations d’e/s votre application peut aligner et qui les disques de stockage traitera. Elle affecte tous les indicateurs de performance trois application que nous avons abordés dans cet article à savoir le, e/s, débit et la latence.

La profondeur de file d’attente et le traitement multithread sont étroitement liés. La valeur de profondeur de file d’attente indique dans quelle mesure l’application peut prendre en charge un traitement multithread. Si la profondeur de file d’attente est importante, l’application peut exécuter davantage d’opérations simultanément, autrement dit un meilleur traitement multithread. Si la profondeur de file d’attente est petite, même si l’application est de type multithread, le nombre de demandes alignées sera insuffisant pour permettre une exécution simultanée.

En règle générale, les applications du commerce ne vous permettent pas de modifier la profondeur de la file d’attente, car un paramétrage incorrect peut faire plus de mal que de bien. Les applications définissent la valeur de la profondeur de file d’attente appropriée pour obtenir des performances optimales. Il est toutefois important de comprendre ce concept afin de pouvoir résoudre les problèmes de performances que vous pouvez rencontrer avec votre application. Vous pouvez également observer les effets de la profondeur de file d’attente en exécutant les outils de benchmarking sur votre système.

Certaines applications fournissent des paramètres qui influencent la profondeur de file d’attente. Le paramètre MAXDOP (degré maximal de parallélisme) dans SQL Server, par exemple, est expliqué dans la section précédente. MAXDOP offre un moyen d’influencer la profondeur de file d’attente et le traitement multithread, sans pour autant modifier directement la valeur de profondeur de file d’attente de SQL Server.

*Profondeur de file d’attente élevé*  
 Une grande profondeur de file d’attente permet d’aligner davantage d’opérations sur le disque. Le disque peut anticiper la prochaine demande placée dans sa file d’attente. Il peut ainsi planifier les opérations à l’avance et les traiter dans un ordre optimal. Étant donné que l’application envoie davantage de requêtes sur le disque, ce dernier peut traiter un plus grand nombre d’E/S parallèles. Au final, l’application sera en mesure d’atteindre un taux supérieur d’E/S par seconde. Puisque l’application traite davantage de demandes, le débit total de l’application augmente également.

En général, une application peut atteindre un débit maximal avec 8-16 E/S en attente par disque connecté. Si une profondeur de file d’attente, application n’envoie pas suffisamment IOs au système, et il traite moins de pendant une période donnée. En d’autres termes, elle atteindra un débit inférieur.

Par exemple, dans SQL Server, une valeur MAXDOP de 4 pour une requête indique à SQL Server qu’il peut utiliser jusqu’à quatre cœurs pour exécuter la requête. SQL Server déterminera la meilleure valeur de profondeur de file d’attente ainsi que le nombre de cœurs à utiliser pour l’exécution de la requête.

*Profondeur optimale de file d’attente*  
 Une valeur de profondeur de file d’attente très élevée présente aussi des inconvénients. Si la valeur de profondeur de file d’attente est trop importante, l’application tentera de générer un taux d’E/S par seconde très élevé. À moins que l’application repose sur des disques persistants associés à un nombre suffisant d’E/S par seconde, cela peut affecter sérieusement les latences d’application. La formule suivante montre la relation entre les e/s, la latence et profondeur de file d’attente.  
    ![](media/premium-storage-performance/image6.png)

Vous ne devez pas configurer la profondeur de file d’attente à une valeur élevée, mais plutôt à une valeur optimale, qui peut générer suffisamment d’E/S par seconde pour l’application sans affecter les latences. Par exemple, si la latence de l’application doit être de 1 milliseconde, la profondeur de file d’attente requise pour atteindre 5 000 E/S par seconde sera : PF = 5 000 x 0,001 = 5.

*Profondeur de file d’attente pour le volume entrelacé*  
 Pour un volume entrelacé, conservez une profondeur de file d’attente suffisamment élevée de sorte que chaque disque dispose individuellement d’un pic de profondeur de file d’attente. Par exemple, considérez une application qui exécute un push d’une profondeur de file d’attente de 2, et il existe quatre disques de la répartition. Les deux demandes d’E/S seront transmises à deux disques et les deux disques restants seront inactifs. Vous devez par conséquent configurer la profondeur de file d’attente afin que tous les disques puissent être occupés. La formule ci-dessous montre comment déterminer la profondeur de file d’attente des volumes entrelacés.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitation

Azure Premium Storage configure la valeur spécifiée d’E/S par seconde et de débit en fonction des tailles de machine virtuelle et des tailles de disque que vous choisissez. Chaque fois que votre application tentera de dépasser les limites de ce que la machine virtuelle ou le disque peut gérer, Premium Storage lui imposera une limitation. Cette limitation se manifeste sous la forme d’une dégradation des performances de votre application, Cela peut signifier une latence plus élevée, un débit inférieur ou les e/s inférieure. Sans cette limitation, votre application risquerait de planter en demandant plus que ses ressources ne lui permettent d’effectuer. Par conséquent, pour éviter les problèmes de performances associés à une limitation, veillez à toujours fournir suffisamment de ressources pour votre application. Tenez compte des explications données ci-dessus dans les sections relatives aux tailles de disque et aux tailles de machine virtuelle. Le benchmarking offre le meilleur moyen de déterminer les ressources dont vous aurez besoin pour héberger votre application.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les types de disque disponibles :

* [Sélectionner un type de disque](../articles/virtual-machines/windows/disks-types.md)  

Pour les utilisateurs de SQL Server, consultez les articles relatifs aux meilleures pratiques de performances de SQL Server :

* [Bonnes pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage provides highest performance for SQL Server in Azure VM (en anglais)](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
