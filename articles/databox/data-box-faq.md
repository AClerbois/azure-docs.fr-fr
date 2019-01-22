---
title: FAQ Microsoft Azure Data Box | Microsoft Docs sur les données
description: Contient des questions fréquemment posées et des réponses au sujet d’Azure Data Box, une solution cloud qui vous permet de transférer de grandes quantités de données dans Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 01/16/2019
ms.author: alkohli
ms.openlocfilehash: c8aa08dc189a77a206ea24b535aedf64454838c7
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359406"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box : Forum Aux Questions (FAQ)

La solution hybride Microsoft Azure Data Box Disk vous permet d’envoyer des téraoctets de données vers Azure de façon rapide, économique et fiable, via un appareil de transfert. Cette FAQ contient des réponses aux questions que vous pouvez vous poser lorsque vous utilisez Data Box sur le portail Azure. 

Les questions/réponses sont classées dans les catégories suivantes :

- À propos du service
- Commander un appareil
- Configuration et connexion 
- Suivi de l'état
- Copier des données 
- Expédier un appareil
- Vérification et chargement de données 
- Prise en charge de la chaîne de responsabilité

## <a name="about-the-service"></a>À propos du service

### <a name="q-what-is-azure-data-box-service"></a>Q. Qu’est-ce que le service Azure Data Box ? 
R.  Le service Azure Data Box a été conçu pour l’ingestion des données hors connexion. Ce service gère une gamme de produits offrant différentes capacités de stockage, tous conçus pour le transport des données. 

### <a name="q-what-is-azure-data-box"></a>Q. Qu’est-ce qu’Azure Data Box ?
R. Azure Data Box permet un transfert rapide, économique et sécurisé de plusieurs téraoctets de données sur et depuis Azure. Vous commandez l’appareil Data Box via le portail Azure. Microsoft vous envoie un appareil de stockage d’une capacité utile de 80 To via un transporteur régional. 

Une fois l’appareil reçu, vous le configurez rapidement à l’aide de l’interface utilisateur web locale. Copiez les données à partir de vos serveurs vers l’appareil et réexpédiez l’appareil à Azure. Dans le centre de données Azure, vos données sont automatiquement chargées de l’appareil vers Azure. L’ensemble du processus est suivi de bout en bout par le service Data Box dans le portail Azure.

### <a name="q-when-should-i-use-data-box"></a>Q. Quand dois-je utiliser Data Box ?
R. Si vous souhaitez transférer vers Azure 40 à 500 To de données, Data Box pourra vous être utile. Pour les tailles de données < 40 To, utilisez Data Box Disk, et pour les tailles de données > 500 To, optez pour Data Box Disk.

### <a name="q-what-is-the-price-of-data-box"></a>Q. Combien coûte Data Box ?
R. Data Box est disponible à un tarif réduit pendant 10 jours. Lorsque vous sélectionnez le modèle de produit lors de la création d’une commande dans le portail Azure, les frais pour l’appareil sont affichés. L’expédition est également gratuite, toutefois, les frais de stockage Azure s’appliquent. Pour plus d’informations, consultez [Tarification Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. Quelle est la quantité maximale de données que je peux transférer avec Data Box dans une seule instance ?
R. Data Box offre une capacité brute de 100 To et une capacité utile de 80 To. Vous pouvez transférer jusqu’à 80 To de données avec Data Box. Pour transférer davantage de données, vous devez commander davantage d’appareils.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. Comment puis-je vérifier si Data Box est disponible dans ma région ? 
R.  Pour plus d’informations sur les pays dans lesquels Data Box est disponible, accédez à [Disponibilité des régions](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. Dans quelles régions puis-je stocker des données avec Data Box ?
R. La solution Data Box est prise en charge dans toutes les régions des États-Unis, en Europe de l’Ouest, en Europe du Nord, en France et au Royaume-Uni. Seules les régions du cloud public Azure sont prises en charge. Azure Government et les autres clouds souverains ne sont pas pris en charge. Pour plus d’informations, consultez [Disponibilité des régions](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>Q. Qui dois-je contacter si je rencontre des problèmes avec Data Box ?
R. Si vous rencontrez des problèmes avec Data Box, veuillez contacter le [support Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="order-device"></a>Commander un appareil

### <a name="q-how-do-i-get-data-box"></a>Q. Comment faire pour obtenir Data Box ? 
R.  Pour obtenir Azure Data Box, connectez-vous au portail Azure et créez une commande Data Box. Indiquez vos coordonnées et vos paramètres de notification. Une fois que vous avez passé une commande, un appareil Data Box vous sera expédié sous 10 jours, en fonction de la disponibilité. Pour plus d’informations, accédez à [Commander une Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>Q. Je n’ai pas pu créer une commande Data Box dans le portail Azure. Quel est le problème ?
R. Si vous n’avez pas pu créer une commande Data Box, le problème vient de votre type d’abonnement ou de votre accès. 

Vérifiez votre abonnement. Data Box est uniquement disponible dans le cadre des offres Accord Entreprise (EA), Fournisseur de solutions Cloud (CSP) ou Abonnement avec paiement à l’utilisation. Si votre abonnement ne figure pas dans la liste ci-dessus, contactez le support Microsoft pour mettre à niveau votre abonnement.

Si vous disposez d’un type d’offre pris en charge pour l’abonnement, vérifiez le niveau d’accès de votre abonnement. Vous devez être contributeur ou propriétaire de votre abonnement pour créer une commande.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>Q. J’ai commandé deux appareils Data Box. Mais je ne peux pas créer d’autres commandes. Quel est le problème ?
R. Nous autorisons un maximum de 5 commandes actives par abonnement et par zone commerciale (paire pays/région sélectionnée). Si vous avez besoin de commander un appareil supplémentaire, contactez le support Microsoft pour augmenter la limite de votre abonnement.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. Lorsque j’essaie de créer une commande, je reçois une notification indiquant que le service Data Box n’est pas disponible. Qu’est-ce que cela signifie ?
R. Cela signifie que le service Data Box n’est pas disponible pour la paire pays/région que vous avez sélectionnée. Modifier cette paire vous permettra sans doute de bénéficier du service Data Box. Pour obtenir la liste des régions où le service est disponible, accédez à [Disponibilité des régions pour Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. J’ai passé ma commande Data Box il y a quelques jours. Quand recevrai-je ma Data Box ?
R. Lorsque vous passez commande, nous vérifions si l’appareil commandé est disponible. Si l’appareil est disponible, nous vous l’expédierons sous 10 jours. Mais nous sommes parfois confrontés à des périodes de forte demande. Dans ce cas, votre commande est mise en attente et vous pouvez suivre son statut dans le portail Azure. La commande est automatiquement annulée si elle n’est pas finalisée sous 90 jours. 

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. J’ai rempli ma Data Box de données et je dois en commander une autre. Existe-t-il un moyen de passer rapidement une commande ?
R. Vous pouvez cloner votre commande précédente. Ainsi, vous créez une commande identique à la précédente et vous pouvez en modifier les détails sans avoir à entrer votre adresse, vos informations de contact et vos préférences de notification. 

## <a name="configure-and-connect"></a>Configuration et connexion

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. Comment faire pour déverrouiller la Data Box ? 
R.  Dans le portail Azure, affichez votre commande Data Box, puis accédez à **Informations sur l’appareil**. Copiez le mot de passe de déverrouillage. Utilisez ce mot de passe pour vous connecter à l’interface utilisateur web locale sur votre Data Box. Pour plus d’informations, consultez [Tutoriel : Déballer, brancher et connecter votre Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. Puis-je utiliser un ordinateur hôte Linux pour me connecter et copier les données sur Data Box ?
R.  Oui. Vous pouvez utiliser Data Box pour vous connecter à des clients SMB et NFS. Pour plus d’informations, accédez à la liste des [systèmes d’exploitation pris en charge](data-box-system-requirements.md) pour votre ordinateur hôte.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Ma Data Box a été expédié, mais je souhaite maintenant annuler ma commande. Pourquoi le bouton Annuler n’est-il pas disponible ?
R.  Vous pouvez annuler votre commande Data Box uniquement si celle-ci n’a pas encore été traitée. Une fois la commande Data Box traitée, vous ne pouvez plus l’annuler. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. Puis-je connecter une Data Box à plusieurs ordinateurs hôtes simultanément pour transférer des données ?
R. Oui. Plusieurs ordinateurs hôtes peuvent se connecter à Data Box pour transférer des données, et plusieurs travaux de copie peuvent être exécutés en parallèle. Pour plus d’informations, consultez [Tutoriel : Copier des données sur Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. Le voyant LED indiquant une erreur système est allumé sur le panneau de commande avant. Que dois-je faire ?
R. L’allumage du voyant LED d’erreur système indique que votre système est défectueux. [contactez le support technique Microsoft](data-box-disk-contact-microsoft-support.md) .

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>Q. Impossible d’accéder au mode de passe de déverrouillage Data Box dans le portail Azure. Quel est le problème ?
R. Si vous ne pouvez pas accéder au mot de passe de déverrouillage dans le portail Azure, vérifiez les autorisations de votre abonnement et de votre compte de stockage. Assurez-vous de disposer des autorisations de collaborateur ou de propriétaire au niveau du groupe de ressources. Si ce n’est pas le cas, vous devez avoir au moins une autorisation de rôle d’opérateur Data Box pour voir les identifiants d’accès.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. La configuration du canal de port est-elle prise en charge sur Data Box ? Qu’en est-il de MPIO ?
R. Nous ne prenons pas en charge la configuration du canal de port, la configuration MPIO (Multipath I/O) ou la configuration du réseau local virtuel (VLAN) sur Data Box.

## <a name="track-status"></a>Suivi de l'état

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. Comment faire pour suivre la Data Box depuis sa commande jusqu’à son renvoi ? 
R.  Vous pouvez suivre l’état de la commande Data Box dans le portail Azure. Lorsque vous créez la commande, vous êtes également invité à fournir une adresse e-mail pour recevoir des notifications. Si vous en avez fourni une, vous serez averti par e-mail de toutes les modifications d’état de la commande. Plus d’informations sur la manière de [configurer les notifications par e-mail](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>Q. Comment faire pour renvoyer l’appareil ? 
R.  Microsoft affiche une étiquette d’expédition sur l’écran E-ink. Si l’étiquette d’expédition n’apparaît pas sur l’écran E-ink, accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**. Téléchargez et imprimez l’étiquette, insérez-la dans l’onglet en plastique transparent fixé sur l’appareil, puis déposez l’appareil au point de dépôt de votre transporteur. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. J’ai reçu une notification par e-mail que mon appareil est arrivé au centre de données Azure. Comment savoir si le chargement des données est en cours ?
R. Vous pouvez accéder à votre commande Data Box dans la **vue d’ensemble** du portail Azure. Si le chargement des données vers Azure a démarré, vous verrez la progression de la copie dans le volet droit. 

## <a name="migrate-data"></a>Migration des données

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. Quel volume de données puis-je utiliser au maximum avec Data Box ?  
R.  Data Box a une capacité de stockage utile de 80 To. Vous pouvez utiliser un seul appareil Data Box pour les données dont la taille est comprise entre 40 To et 80 To. Pour les tailles de données supérieures à 500 To, vous pouvez commander plusieurs appareils Data Box. Pour les tailles de données supérieures à 500 To, optez pour Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Quelle est la taille maximale des objets blob de blocs et des objets blob de pages prise en charge par Data Box ? 
R.  Les tailles maximales dépendent des limites du Stockage Azure. La taille maximale pour un objet blob de blocs est d’environ 4,768 Tio et la taille maximale pour un objet blob de page est de 8 Tio. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](../storage/common/storage-scalability-targets.md). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. Comment faire pour savoir si mes données sont sécurisées pendant le transfert ? 
R. Plusieurs fonctionnalités de sécurité ont été implémentées pour garantir la sécurité de votre Data Box pendant le transit. Certaines d'entre elles incluent des scellés inviolables, une protection contre la falsification du matériel et des logiciels, ainsi qu’un mot de passe de déverrouillage de l’appareil. Pour plus d’informations, accédez à [Azure Data Box security and data protection](data-box-security.md) (Protection des données et sécurité Azure Data Box).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. Comment faire pour copier des données sur la Data Box ? 
R.  Si vous utilisez un client SMB, vous pouvez exploiter un outil de copie SMN tel que Robocopy, Diskboss, voire la fonction glisser-déplacer de l’explorateur de fichiers Windows pour copier des données sur l’appareil. 

Si vous utilisez un client NFS, vous pouvez utiliser [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/). 

Pour plus d’informations, consultez [Tutoriel : Copier des données sur Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Y a-t-il des astuces permettant d’accélérer la copie des données ?
R.  Pour accélérer le processus de copie :

- Utilisez plusieurs flux de copie des données. Par exemple, avec Robocopy, utilisez l’option multithread. Pour plus d’informations sur la commande exacte utilisée, accédez à [Didacticiel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-deploy-copy-data.md).
- Utilisez plusieurs sessions.
- Au lieu de copier sur un partage réseau (où vous pouvez être limité par la vitesse du réseau), vérifiez que les données résident en local sur l’ordinateur auquel Data Box est connecté.
- Évaluez les performances de l’ordinateur utilisé pour copier les données. Téléchargez et utilisez l’[outil Bluestop FIO](https://bluestop.org/fio/) pour tester les performances du matériel du serveur.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. Puis-je utiliser plusieurs comptes de stockage avec Data Box ?
R.  Oui. Data Box prend en charge jusqu’à 10 comptes de stockage, à usage général, classiques ou de stockage d’objets blob. Les objets blob chauds et froids sont pris en charge. Lors de la mise à disposition générale, seuls les comptes de stockage aux États-Unis, en Europe de l’Ouest, en Europe du Nord, en France et au Royaume-Uni dans le cloud public Azure sont pris en charge.


## <a name="ship-device"></a>Expédier un appareil

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. Mon appareil a été livré, mais il semble endommagé. Que dois-je faire ?
R. Si votre appareil est arrivé endommagé ou présente des signes de falsification, ne l’utilisez pas. [Contactez le support Microsoft](data-box-disk-contact-microsoft-support.md) et retournez l’appareil dès que possible. Vous pouvez également créer une nouvelle commande Data Box pour obtenir un appareil de remplacement. Dans ce cas, vous n'êtes pas facturé pour cet appareil de remplacement.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>Q. Puis-je utiliser mon propre transporteur pour expédier ma Data Box ?
R. Pour le service Data Box, Microsoft gère la livraison vers et depuis le centre de données Azure. Si vous souhaitez utiliser votre propre transporteur, vous pouvez utiliser le service Azure Import/Export. Pour plus d’informations, consultez [Qu’est-ce que le service Azure Import/Export ?](../storage/common/storage-import-export-service.md)

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. L’écran E-ink n’affiche pas l’étiquette de retour. Que dois-je faire ?
R. Si l’écran E-ink n’affiche pas l’étiquette de retour, procédez comme suit :
- Décollez l’ancienne étiquette d’expédition et l’éventuel autocollant du précédent colis.
- Accédez à votre commande dans le portail Azure. Accédez à **Vue d’ensemble**, puis à **Télécharger une étiquette d’expédition**. Pour plus d’informations, consultez [Télécharger une étiquette d’expédition](data-box-portal-admin.md#download-shipping-label).
- Imprimez l’étiquette d’expédition et insérez-la dans la pochette en plastique transparent accolée à l’appareil. 
- Vérifiez que l’étiquette d’expédition est clairement visible. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Comment mes données sont-elles protégées pendant le transfert ? 
R.  Au cours du transit, les fonctionnalités suivantes de l’aide Data Box protègent les données.
 - Les disques Data Box sont chiffrés à l’aide du chiffrement AES 256 bits. 
 - L’appareil est verrouillé et nécessite un mot de passe de déverrouillage pour saisir des données et y accéder.
Pour plus d’informations, consultez [Fonctionnalités de sécurité Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>Q. J’ai terminé de préparer la livraison et j’ai arrêté l’appareil. Puis-je quand même ajouter des données à Data Box ?
R. Oui. Vous pouvez allumer l’appareil et ajouter d’autres données. Vous devrez relancer **Préparer l’expédition**, une fois la copie des données terminée.
  

## <a name="verify-and-upload"></a>Vérification et chargement

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. Après combien de temps puis-je accéder à mes données dans Azure une fois que j’ai renvoyé la Data Box ? 
R.  Dès que l’état de la commande pour la **copie des données** s’affiche comme **complet**, vous devriez pouvoir accéder à vos données.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Où se trouvent mes données dans Azure après leur téléchargement ?
R.  Lorsque vous copiez les données sur Data Box, selon qu’il s’agit d’objets blob de blocs, d’objets blob de pages ou de fichiers Azure, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.
 - `https://<storage_account_name>.blob.core.windows.net/<containername>` 
 -  `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
 Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Je viens de remarquer que je n’ai pas suivi les conventions de dénomination pour mes conteneurs Azure. Le chargement de mes données sur Azure risque-t-il d’échouer ?
R.  Si les noms des conteneurs contiennent des lettres majuscules, celles-ci sont automatiquement converties en minuscules. Si les noms ne sont pas conformes d’une autre manière (caractères spéciaux, autres langues, etc.), le chargement échouera. Pour plus d’informations sur les meilleures pratiques pour nommer les partages, les conteneurs et les fichiers, consultez : 
- [Affectation de noms et de références aux partages](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. Comment faire pour vérifier les données que j’ai copiées sur Data Box ?
R.  Une fois la copie des données terminée, lorsque vous exécutez **Préparer l’expédition**, vos données sont validées. Data Box génère une liste de fichiers et de sommes de contrôle pour les données pendant le processus de validation. Vous pouvez télécharger cette liste de fichiers et la comparer aux fichiers de la source de données. Pour plus d’informations, consultez [Préparer l’expédition](data-box-deploy-copy-data.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>Q. Que deviennent mes données une fois que j’ai renvoyé la Data Box ?
R.  Une fois la copie des données vers Azure terminée, les données des disques sur la Data Box sont effacées en toute sécurité, conformément aux instructions du standard NIST SP 800-88 Revision 1. Pour plus d’informations, consultez [Effacer les données de la Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Rapport d’audit

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Comment le service Azure Data Box peut-il m’aider à prendre en charge la chaîne de responsabilité des clients ?
R.  En mode natif, le service Azure Data Box fournit des rapports que vous pouvez utiliser pour documenter votre chaîne de responsabilité. Les journaux d’audit et de copie sont disponibles dans votre compte de stockage dans Azure, et vous pouvez [télécharger l’historique des commandes](data-box-portal-admin.md#download-order-history) dans le portail Azure, une fois la commande terminée.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Quels sont les types de rapports disponibles pour prendre en charge la chaîne de responsabilité ?
R.  Les rapports suivants sont disponibles pour prendre en charge la chaîne de responsabilité :

- Logistique du transport UPS.
- Journalisation de la mise sous tension et de l’accès au partage utilisateur.
- Fichier manifeste avec une vérification de redondance cyclique 64 bits (CRC-64) ou somme de contrôle pour chaque fichier reçu avec succès dans la Data Box.
- Création de rapports sur les fichiers qui n’ont pas pu être téléchargés vers le compte Stockage Azure.
- Nettoyage de l’appareil Data Box (conformément aux normes NIST 800 88R1), une fois les données copiées sur votre compte Stockage Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Les journaux de suivi du transporteur (UPS) sont-ils disponibles ? 
R.  Les journaux de suivi du transporteur sont consignés dans l’historique des commandes Data Box. Ce rapport est à votre disposition une fois que l’appareil a été retourné au centre de données Azure et que les données stockées sur les disques de l’appareil ont été nettoyées. Pour vos besoins immédiats, vous pouvez également accéder directement au site Web du transporteur et y saisir le numéro de suivi de la commande pour obtenir les informations de suivi.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Puis-je apporter la Data Box au centre de données Azure ? 
R.   Non. Actuellement, le centre de données Azure n’accepte pas la remise des Data Box des clients ou de transporteurs autres que UPS.


## <a name="next-steps"></a>Étapes suivantes

- Relire les [Conditions requises pour le système Data Box](data-box-system-requirements.md).
- Comprendre les limites de [Data Box](data-box-limits.md).
- Déployez rapidement [Azure Data Box](data-box-quickstart-portal.md) sur le portail Azure.
