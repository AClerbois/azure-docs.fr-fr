---
title: "Notes de publication pour l’explorateur de stockage Microsoft Azure (aperçu) | Documents Microsoft"
description: "Notes de publication pour l’explorateur de stockage Microsoft Azure (aperçu)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 3525661bd55aead07ce8d97464ba16393d28c04c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Notes de publication pour l’explorateur de stockage Microsoft Azure (aperçu)

Cet article contient les notes de publication pour l’explorateur de stockage Azure 0.8.15. (aperçu), ainsi que les notes de publication des versions précédentes.

[L’explorateur de stockage Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux.

## <a name="version-0815-preview"></a>Version 0.8.15 (aperçu)
7/13/2017

### <a name="download-azure-storage-explorer-0815-preview"></a>Télécharger l’explorateur de stockage Azure 0.8.15 (aperçu)
- [Explorateur de stockage Azure 0.8.15 (aperçu) pour Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorateur de stockage Azure 0.8.15 (aperçu) pour Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorateur de stockage Azure 0.8.15 (aperçu) pour Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nouveau

* Amélioration des performances de chargement et de téléchargement de l’objet blob.
* Amélioration de l’expérience de fichiers en conflit pour le chargement et le téléchargement de l’objet blob.
* Amélioration de l’affichage des erreurs dans le journal d’activité pendant le chargement et le téléchargement de l’objet blob.
* Augmentation de la taille maximale des chargements et des téléchargements de l’objet blob à 8 To pour les objets blob de pages et ~4,7 To pour les objets blob de blocs.

### <a name="known-issues"></a>Problèmes connus

* L’annulation d’une tâche peut prendre un certain temps après avoir cliqué sur « Annuler ». Il s’agit d’une [limitation de la bibliothèque de nœuds de stockage Azure](https://github.com/Azure/azure-storage-node/issues/317).
* Après avoir effectué un chargement de l’objet blob, l’onglet qui a initié le chargement est actualisé. Il s’agit d’une modification du comportement précédent, qui entraîne également un retour vers la racine du conteneur dans lequel vous vous trouvez. 
* Si vous choisissez un certificat de code PIN/carte à puce incorrect vous devrez redémarrer l’explorateur de stockage afin qu’il oublie cette décision.
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements.
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Lors d’un changement de nom, toutes les autres propriétés et métadonnées des objets blob, fichiers et entités sont conservées.
* Bien que Azure Stack ne prend actuellement pas en charge les partages de fichiers, un nœud de partages de fichiers apparaît toujours sous un compte de stockage d’Azure Stack joint.
* La version gcc doit être mise à jour ou mise à niveau lors de l’installation d’Ubuntu 14.04 : vous trouverez les étapes de mise à niveau ci-dessous : 
    * sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    * sudo apt-get update
    * sudo apt-get upgrade
    * sudo apt-get dist-upgrade
* Pour les utilisateurs sur Ubuntu 17.04, l’installation de GConf est nécessaire. Elle peut être effectuée en exécutant les commandes suivantes, puis en redémarrant votre ordinateur : 
    * sudo apt-get install libgconf-2-4

## <a name="version-0814-preview"></a>Version 0.8.14 (aperçu)
06/22/2017

### <a name="download-azure-storage-explorer-0814-preview"></a>Télécharger l’explorateur de stockage Azure 0.8.14 (aperçu)
* [Télécharger l’explorateur de stockage Azure 0.8.14 (aperçu) pour Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Télécharger l’explorateur de stockage Azure 0.8.14 (aperçu) pour Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Télécharger l’explorateur de stockage Azure 0.8.14 (aperçu) pour Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Nouveau

* Mise à jour de la version Électron sur 1.7.2 afin de tirer parti de plusieurs mises à jour critiques
* Vous pouvez maintenant accéder rapidement au guide de dépannage en ligne depuis le menu d’aide
* [Guide][2] de dépannage de l’explorateur de stockage
* [Instructions][3] sur la connexion à un abonnement Azure Stack

### <a name="known-issues"></a>Problèmes connus

* Les boutons de la boîte de dialogue de confirmation de la suppression des dossiers ne sont pas référencés par des clics de souris sur Linux. Une solution de contournement consiste à utiliser la touche Entrée
* Si vous choisissez un certificat de code PIN/carte à puce incorrect vous devrez redémarrer afin que l’explorateur de stockage oublie cette décision
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou fichiers simultanément
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Lors d’un changement de nom, toutes les autres propriétés et métadonnées des objets blob, fichiers et entités sont conservées.
* Bien qu’Azure Stack ne prend actuellement pas en charge les partages de fichiers, un nœud de partages de fichiers apparaît toujours sous un compte de stockage d’Azure Stack joint. 
* La version gcc doit être mise à jour ou mise à niveau lors de l’installation d’Ubuntu 14.04 : vous trouverez les étapes de mise à niveau ci-dessous :
    * sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    * sudo apt-get update
    * sudo apt-get upgrade
    * sudo apt-get dist-upgrade

## <a name="previous-releases"></a>Versions précédentes

* [Version 0.8.13](#version-0813)
* [Version 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Version 0.8.9 / 0.8.8](#version-089--088)
* [Version 0.8.7](#version-087)
* [Version 0.8.6](#version-086)
* [Version 0.8.5](#version-085)
* [Version 0.8.4](#version-084)
* [Version 0.8.3](#version-083)
* [Version 0.8.2](#version-082)
* [Version 0.8.0](#version-080)
* [Version 0.7.20160509.0](#version-07201605090)
* [Version 0.7.20160325.0](#version-07201603250)
* [Version 0.7.20160129.1](#version-07201601291)
* [Version 0.7.20160105.0](#version-07201601050)
* [Version 0.7.20151116.0](#version-07201511160)

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Nouveau

* [Guide][2] de dépannage de l’explorateur de stockage
* [Instructions][3] sur la connexion à un abonnement Azure Stack

#### <a name="fixes"></a>Correctifs

* Problème résolu : le chargement du fichier peut provoquer très probablement une erreur de mémoire insuffisante
* Problème résolu : vous pouvez maintenant vous connecter avec le code PIN/carte à puce
* Problème résolu : l’ouverture dans le portail fonctionne à présent avec Azure Chine, Azure Allemagne, Azure US Government et Azure Stack
* Problème résolu : lors du téléchargement d’un dossier dans un conteneur d’objets blob, une erreur « Opération non conforme » peut se produire
* Problème résolu : sélectionner tout est désactivé lors de gestion des instantanés
* Problème résolu : les métadonnées de l’objet blob de base peuvent être remplacées lorsque vous avez affiché les propriétés de ses instantanés

#### <a name="known-issues"></a>Problèmes connus

* Si vous choisissez un certificat de code PIN/carte à puce incorrect vous devrez redémarrer afin que l’explorateur de stockage oublie cette décision
* En mode zoom avant ou arrière, le niveau de zoom peut être momentanément rétabli sur le niveau par défaut
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou fichiers simultanément
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Lors d’un changement de nom, toutes les autres propriétés et métadonnées des objets blob, fichiers et entités sont conservées.
* Bien que Azure Stack ne prend actuellement pas en charge les partages de fichiers, un nœud de partages de fichiers apparaît toujours sous un compte de stockage d’Azure Stack joint. 
* La version gcc doit être mise à jour ou mise à niveau lors de l’installation d’Ubuntu 14.04 : vous trouverez les étapes de mise à niveau ci-dessous :
    * sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    * sudo apt-get update
    * sudo apt-get upgrade
    * sudo apt-get dist-upgrade

### <a name="version-0812--0811--0810"></a>Version 0.8.12 / 0.8.11 / 0.8.10
04/07/2017

#### <a name="new"></a>Nouveau

* L’explorateur de stockage se ferme désormais automatiquement lorsque vous installez une mise à jour depuis la notification de mise à jour
* L’accès rapide sur place fournit une expérience améliorée pour utiliser vos ressources fréquemment sollicitées
* Dans l’éditeur de conteneur d’objets Blob, vous pouvez maintenant voir à quelle machine virtuelle appartient un objet blob loué
* Vous pouvez réduire maintenant le panneau de gauche
* La détection s’exécute maintenant en même temps que le téléchargement
* Utilisez des statistiques dans les éditeurs de conteneurs d’objets Blob, partages de fichiers et tables pour afficher la taille de vos ressources ou de la sélection
* Vous pouvez maintenant vous connecter à Azure Active Directory (AAD) basé sur les comptes Azure Stack. 
* Vous pouvez maintenant télécharger les fichiers d’archive de plus de 32 Mo sur les comptes de stockage Premium
* Prise en charge de l’accessibilité améliorée
* Vous pouvez maintenant ajouter des certificats SSL X.509 encodés de base 64 approuvés en accédant à Modifier -&gt; Certificats SSL -&gt; Importer certificats

#### <a name="fixes"></a>Correctifs

* Problème résolu : après l’actualisation des informations d’identification d’un compte, l’arborescence n’est parfois pas actualisée automatiquement
* Problème résolu : générer une SAP pour les tables et les files d’attente de l’émulateur peut entraîner une URL non valide
* Problème résolu : les comptes de stockage premium peuvent maintenant être développés lorsqu’un proxy est activé
* Problème résolu : le bouton Appliquer de la page de gestion de comptes ne fonctionne pas si vous avez 1 ou 0 compte sélectionné
* Problème résolu : le téléchargement d’objets blob qui nécessitent des résolutions de conflit peut échouer - résolu dans 0.8.11 
* Problème résolu : l’envoi de commentaires a été interrompu dans 0.8.11 - résolu dans 0.8.12 

#### <a name="known-issues"></a>Problèmes connus

* Après la mise à niveau vers 0.8.10, vous devez actualiser toutes vos informations d’identification.
* En mode zoom avant ou arrière, le niveau de zoom peut être momentanément rétabli sur le niveau par défaut.
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou de fichiers simultanément.
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements.
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Lors d’un changement de nom, toutes les autres propriétés et métadonnées des objets blob, fichiers et entités sont conservées.
* Bien que Azure Stack ne prend actuellement pas en charge les partages de fichiers, un nœud de partages de fichiers apparaît toujours sous un compte de stockage d’Azure Stack joint. 
* La version gcc doit être mise à jour ou mise à niveau lors de l’installation d’Ubuntu 14.04 : vous trouverez les étapes de mise à niveau ci-dessous :
    * sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    * sudo apt-get update
    * sudo apt-get upgrade
    * sudo apt-get dist-upgrade

### <a name="version-089--088"></a>Version 0.8.9 / 0.8.8
02/23/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Nouveau

* L’explorateur de stockage 0.8.9 télécharge automatiquement la dernière version des mises à jour.
* Correctif : l’utilisation d’un portail URI SAP généré pour attacher un compte de stockage entraîne une erreur.
* Vous pouvez désormais créer, gérer et promouvoir des instantanés d’objets blob.
* Vous pouvez désormais vous connecter aux comptes Azure Chine, Azure Allemagne et Azure US Government.
* Vous pouvez maintenant modifier le niveau de zoom. Utilisez les options dans le menu Affichage pour effectuer un zoom avant, un zoom arrière et réinitialiser le zoom.
* Les caractères Unicode sont désormais pris en charge dans les métadonnées de l’utilisateur pour les fichiers et les objets blob.
* Améliorations de l’accessibilité.
* Les notes de publication de la version suivante peuvent être affichées depuis la notification de mise à jour. Vous pouvez également afficher les notes de publication courantes dans le menu Aide.

#### <a name="fixes"></a>Correctifs

* Problème résolu : le numéro de version s’affiche désormais correctement dans le panneau de configuration sur Windows
* Problème résolu : la recherche n’est plus limitée à 50 000 nœuds
* Problème résolu : le téléchargement vers un partage de fichiers tourne indéfiniment si le répertoire de destination n’existe pas déjà
* Problème résolu : stabilité améliorée pour de longs chargements et téléchargements

#### <a name="known-issues"></a>Problèmes connus

* En mode zoom avant ou arrière, le niveau de zoom peut être momentanément rétabli sur le niveau par défaut.
* L’Accès rapide fonctionne uniquement avec les éléments basés sur l’abonnement. Les ressources locales ou attachées par le biais d’une clé ou d’un jeton SAP ne sont pas prises en charge dans cette version.
* L’Accès rapide peut prendre quelques secondes pour accéder à la ressource cible, selon le nombre de ressources dont vous disposez.
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou de fichiers simultanément.

12/16/2016
### <a name="version-087"></a>Version 0.8.7

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nouveau

* Vous pouvez choisir la façon dont vous souhaitez résoudre les conflits au début d’une session de mise à jour, de téléchargement ou de copie dans la fenêtre Activités
* Pointez sur un onglet pour afficher le chemin d’accès complet de la ressource de stockage
* Lorsque vous cliquez sur un onglet, il se synchronise avec son emplacement dans le volet de navigation de gauche

#### <a name="fixes"></a>Correctifs

* Problème résolu : l’explorateur de stockage est désormais une application approuvée sur Mac
* Problème résolu : Ubuntu 14.04 est de nouveau pris en charge
* Problème résolu : l’interface utilisateur de compte d’ajout clignote parfois lors du chargement des abonnements
* Problème résolu : certaines ressources de stockage ne sont pas répertoriées dans le volet de navigation de gauche
* Problème résolu : le volet d’action affiche parfois des actions vides
* Problème résolu : la taille de la fenêtre de la dernière session fermée est désormais conservée
* Problème résolu : vous pouvez ouvrir plusieurs onglets pour la même ressource à l’aide du menu contextuel

#### <a name="known-issues"></a>Problèmes connus

* L’Accès rapide fonctionne uniquement avec les éléments basés sur l’abonnement. Les ressources locales ou attachées par le biais d’une clé ou d’un jeton SAP ne sont pas prises en charge dans cette version
* L’Accès rapide peut prendre quelques secondes pour accéder à la ressource cible, selon le nombre de ressources dont vous disposez
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou fichiers simultanément
* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affectent les performances ou peuvent entraîner des exceptions non prises en charge
* Lorsque vous utilisez l’explorateur de stockage sur macOS pour la première fois, il se peut que plusieurs invites vous demandent l’autorisation de l’utilisateur pour accéder au trousseau. Nous vous suggérons de sélectionner Toujours autoriser, de sorte que l’invite cesse de s’afficher

11/18/2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Nouveau

* Vous pouvez désormais épingler les services les plus fréquemment utilisés à l’Accès rapide pour faciliter la navigation
* Vous pouvez désormais ouvrir plusieurs éditeurs dans différents onglets. Cliquez une fois pour ouvrir un onglet temporaire et double-cliquez pour ouvrir un onglet permanent. Vous pouvez également cliquer sur l’onglet temporaire pour le rendre permanent
* Des améliorations notables en termes de stabilité et de performances pour les chargements et téléchargements ont été réalisés, en particulier pour les fichiers volumineux sur des machines rapides
* Des dossiers « virtuels » vides peuvent maintenant être créés dans des conteneurs d’objets blob
* Nous avons réintroduit une recherche étendue avec notre nouvelle recherche de sous-chaîne améliorée. Vous disposez désormais de deux options de recherche : 
    * Recherche globale : il suffit d’entrer un terme à rechercher dans la zone de texte de recherche
    * Recherche étendue : cliquez sur l’icône de loupe, en regard d’un nœud, puis ajouter un terme de recherche à la fin du chemin ou cliquez avec le bouton droit et sélectionnez « Rechercher d’ici »
* Nous avons ajouté différents thèmes : Clair (par défaut), Foncé, Contraste noir élevé et Contraste blanc élevé. Allez à Modifier -&gt; Thèmes pour modifier vos préférences de thème
* Vous pouvez modifier les propriétés des objets blob et des fichiers
* Nous prenons désormais en charge les messages des files d’attente codés (base 64) et non codés
* Sous Linux, un système d’exploitation 64 bits est désormais obligatoire. Pour cette version, nous ne prenons en charge que Ubuntu 16.04.1 LTS 64 bits
* Nous avons mis à jour notre logo.

#### <a name="fixes"></a>Correctifs

* Problème résolu : blocage de l’écran
* Sécurité améliorée
* Problème résolu : il peut arriver que des comptes joints en double s’affichent
* Problème résolu : un objet blob avec un type de contenu non défini peut générer une exception
* Problème résolu : impossible d’ouvrir le panneau de requête sur une table vide
* Problème résolu : bogues différents dans la recherche
* Problème résolu : augmentation du nombre de ressources chargées de 50 à 100 lorsque vous cliquez sur « Charger plus »
* Problème résolu : lors de la première exécution, si un compte est connecté, tous les abonnements sont sélectionnés pour ce compte par défaut 

#### <a name="known-issues"></a>Problèmes connus

* Cette version de l’explorateur de stockage ne fonctionne pas sur Ubuntu 14.04
* Pour ouvrir plusieurs onglets pour la même ressource, ne cliquez pas toujours sur la même ressource. Cliquez sur une autre ressource, puis revenez en arrière et cliquez sur la ressource d’origine pour l’ouvrir dans un autre onglet 
* L’Accès rapide fonctionne uniquement avec les éléments basés sur l’abonnement. Les ressources locales ou attachées par le biais d’une clé ou d’un jeton SAP ne sont pas prises en charge dans cette version
* L’Accès rapide peut prendre quelques secondes pour accéder à la ressource cible, selon le nombre de ressources dont vous disposez
* Des erreurs peuvent survenir si vous chargez plus de trois groupes d’objets blob ou fichiers simultanément
* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affectent les performances ou peuvent entraîner des exceptions non prises en charge

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Nouveau

* Il est maintenant possible d’utiliser les clés SAP générées par le portail pour les joindre à des comptes de stockage et de ressources

#### <a name="fixes"></a>Correctifs

* Problème résolu : la condition de concurrence lors de la recherche provoque parfois la non extensibilité des nœuds
* Problème résolu : « Utiliser HTTP » ne fonctionne pas lors de la connexion à des comptes de stockage avec la clé et le nom du compte
* Problème résolu : les clés SAP (en particulier celles qui sont générées par le portail) renvoient une erreur « barre oblique »
* Problème résolu : problèmes d’importation de tables
    * Parfois, la clé de partition et la clé de ligne ont été inversées
    * Impossible de lire les clés de partition « nul »

#### <a name="known-issues"></a>Problèmes connus

* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affectent les performances
* Actuellement, Azure Stack ne prend pas en charge les fichiers. Par conséquent, toute tentative de développement des Fichiers entraîne une erreur.

09/12/2016
### <a name="version-084"></a>Version 0.8.4

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nouveau

* Générez des liens directs vers des comptes, conteneurs de stockage, files d’attente, tables ou partages de fichiers pour le partage et l’accès facile à vos ressources. Prise en charge Windows et Mac OS
* Recherchez vos conteneurs d’objets blob, tables, files d’attente, partages de fichiers ou comptes de stockage à partir de la zone de recherche
* Vous pouvez maintenant regrouper des clauses dans le générateur de requêtes de tables
* Renommer et copier/coller des conteneurs d’objets blob, des partages de fichiers, des tables, des objets blob, des dossiers d’objets blob, des fichiers et répertoires depuis des comptes et conteneurs joint par SAP
* Le changement de nom et la& copie des conteneurs d’objets blob et des partages de fichiers préservent désormais les métadonnées et propriétés

#### <a name="fixes"></a>Correctifs

* Problème résolu : impossible de modifier des entités de tables si elles contiennent des propriétés booléennes ou binaires

#### <a name="known-issues"></a>Problèmes connus

* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affectent les performances

08/03/2016
### <a name="version-083"></a>Version 0.8.3

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nouveau

* Renommez des conteneurs, tables et partages de fichiers
* Meilleure expérience de générateur de requête
* Possibilité d’enregistrer et de charger des requêtes
* Liens directs à des conteneurs ou comptes de stockage, files d’attente, tables ou partages de fichiers pour partager vos ressources et y accéder facilement (Windows uniquement, prise en charge macOS très prochainement !)
* Possibilité de gérer et configurer des règles CORS

#### <a name="fixes"></a>Correctifs

* Problème résolu : les comptes Microsoft nécessitent une nouvelle authentification toutes les 8 à 12 heures

#### <a name="known-issues"></a>Problèmes connus

* Parfois, l’interface utilisateur peut sembler bloquée, l’agrandissement de la fenêtre permet de résoudre ce problème
* L’installation de macOS peut nécessiter des autorisations élevées
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements
* Le changement de nom des partages de fichiers, conteneurs d’objets blob et tables ne conserve pas les métadonnées ou autres propriétés sur le conteneur, tels que le quota de partage de fichiers, le niveau d’accès public ou les stratégies d’accès
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Lors d’un changement de nom, toutes les autres propriétés et métadonnées des objets blob, fichiers et entités sont conservées
* Copier ou renommer des ressources ne fonctionne pas à l’intérieur des comptes joints par SAP

07/07/2016
### <a name="version-082"></a>Version 0.8.2

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nouveau

* Les comptes de stockage sont regroupés par abonnements ; le stockage de développement et les ressources jointes via la clé ou SAP sont affichés sous le nœud (local et joint)
* Se déconnecter depuis des comptes dans le panneau « Paramètres du compte Azure »
* Configurer les paramètres de proxy pour activer et gérer la connexion
* Créer et annuler des baux d’objets blob
* Ouvrir des conteneurs d’objets blob, des files d’attente, des tables et des fichiers avec un simple clic

#### <a name="fixes"></a>Correctifs

* Problème résolu : les messages de files d’attente insérés à l’aide des bibliothèques .NET ou Java ne sont pas correctement décodés depuis base64
* Problème résolu : les tables $metrics ne figurent pas pour les comptes de stockage d’objets blob
* Problème résolu : le nœud de la table ne fonctionne pas pour le stockage local (développement)

#### <a name="known-issues"></a>Problèmes connus

* L’installation de macOS peut nécessiter des autorisations élevées

06/15/2016
### <a name="version-080"></a>Version 0.8.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nouveau

* Prise en charge du partage de fichiers : affichage, téléchargement, chargement, copie de fichiers et de répertoires, URI SAP (créer et connecter)
* Amélioration de l’expérience utilisateur pour la connexion au stockage avec des URI SAP ou des clés de compte
* Exporter des résultats de requêtes de table
* Réorganisation et personnalisation des colonnes de table
* Affichage des conteneurs d’objets blob $logs et de tables $metrics pour les comptes de stockage avec les mesures activées
* L’amélioration du comportement d’exportation et d’importation, inclut désormais le type de valeur de propriété

#### <a name="fixes"></a>Correctifs

* Problème résolu : le chargement ou le téléchargement d’objets blob volumineux peut entraîner des chargements/téléchargements incomplets
* Problème résolu : la modification, l’ajout ou l’importation d’une entité avec une valeur de chaîne numérique (« 1 ») la convertit en double
* Problème résolu : impossible de développer le nœud de la table dans l’environnement de développement local

#### <a name="known-issues"></a>Problèmes connus

* Les tables $metrics ne figurent pas pour les comptes de stockage d’objets blob
* Les messages de files d’attente ajoutés par programme ne peuvent pas s’afficher correctement si les messages sont encodés à l’aide de l’encodage Base64

05/17/2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Nouveau

* Une meilleure gestion des erreurs pour les incidents d’applications

#### <a name="fixes"></a>Correctifs

* Bogue résolu dans lequel les messages de la barre d’informations parfois n’apparaissent pas lorsque les informations d’identification de connexion sont requises

#### <a name="known-issues"></a>Problèmes connus

* Tables : Ajout, modification ou importation d’une entité qui a une propriété avec une valeur numérique ambiguë, comme « 1 » ou « 1.0 » et l’utilisateur tente de l’envoyer en tant que `Edm.String`, la valeur revient via l’API client comme un Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Nouveau

* Prise en charge de la table : opérations d’affichage, d’interrogation, d’exportation, d’importation et CRUD pour les entités
* Prise en charge de la file d’attente : affichage, ajout, retrait de la file d’attente des messages
* Génération d’URI SAP pour les comptes de stockage
* Connexion à des comptes de stockage avec des URI SAP
* Mettre à jour des notifications pour les futures mises à jour de l’explorateur de stockage
* Apparence mise à jour

#### <a name="fixes"></a>Correctifs

* Améliorations des performances et de la fiabilité

### <a name="known-issues-amp-mitigations"></a>Problèmes connus &amp; atténuations des risques

* Le téléchargement de fichiers d’objets blob volumineux ne fonctionne pas correctement, nous vous recommandons d’utiliser AzCopy pendant que nous résolvons ce problème 
* Les informations d’identification ne sont pas récupérées ni mises en cache s’il est impossible de trouver ou d’écrire sur le dossier de base
* Si nous ajoutons, modifions ou importons une entité qui a une propriété avec une valeur numérique ambiguë, comme « 1 » ou « 1.0 » et que l’utilisateur tente de l’envoyer en tant que `Edm.String`, la valeur revient via l’API client comme un Edm.Double
* Lorsque vous importez des fichiers CSV avec des enregistrements multilignes, les données peuvent être tronquées ou embrouillées

02/03/2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Correctifs

* Amélioration des performances globales lors du chargement, du téléchargement et de la copie des objets blob

01/14/2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Nouveau

* Prise en charge de Linux (fonctionnalités de parité vers OSX)
* Ajouter des conteneurs d’objets blob avec une clé Signatures d’accès partagé (SAP)
* Ajouter des comptes de stockage Azure Chine
* Ajouter des comptes de stockage avec des points de terminaison personnalisés
* Ouvrez et affichez les blobs de texte et d’image du contenu
* Affichez et modifiez les propriétés et métadonnées des blobs

#### <a name="fixes"></a>Correctifs

* Problème résolu : le chargement ou le téléchargement d’un grand nombre d’objets blob (plus de 500) peut parfois provoquer l’affichage d’un écran blanc sur l’application 
* Problème résolu : lors de la définition du niveau d’accès public du conteneur d’objets blob, la nouvelle valeur n'est pas mise à jour tant vous n’avez pas redéfini le focus sur le conteneur. En outre, la boîte de dialogue affiche toujours par défaut « Aucun accès public » et non la valeur actuelle réelle.
* Une meilleure prise en charge globale du clavier/accessibilité et de l’interface utilisateur
* Le texte de l’historique des vues miniatures est encapsulé lorsqu’il est long avec un espace blanc
* La boîte de dialogue SAP prend en charge la validation d’entrée
* Le stockage local reste disponible même si les informations d’identification de l’utilisateur ont expiré
* Lorsqu’un conteneur d’objets blob ouvert est supprimé, l’explorateur d’objets blob sur le côté droit est fermé

#### <a name="known-issues"></a>Problèmes connus

* La version gcc doit être mise à jour ou mise à niveau lors de l’installation de Linux : vous trouverez les étapes de mise à niveau ci-dessous : 
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Nouveau

* Versions macOS et Windows
* Connectez-vous pour afficher vos comptes de stockage : utilisez votre compte professionnel, Microsoft, 2FA, etc.
* Stockage de développement local (utilisez l’émulateur de stockage, Windows uniquement)
* Prise en charge d’Azure Resource Manager et des ressources classiques
* Créez et supprimez des objets blob, des files d’attente ou des tables
* Recherchez des objets blob, des files d’attente ou des tables spécifiques
* Explorez le contenu de conteneurs d’objets blob
* Affichez et parcourez les répertoires
* Chargez, téléchargez et supprimez des objets blob et des dossiers
* Affichez et modifiez les propriétés et métadonnées des blobs
* Générez des clés SAP
* Gérez et créez des stratégies d’accès stockés (SAS)
* Recherchez les blobs par préfixe
* Glissez-déplacez les fichiers à charger ou télécharger

#### <a name="known-issues"></a>Problèmes connus

* Lors de la définition du niveau d’accès public au conteneur d’objets blob, la nouvelle valeur n'est pas mise à jour tant vous n’avez pas redéfini le focus sur le conteneur
* Lorsque vous ouvrez la boîte de dialogue pour définir le niveau d’accès public, « Aucun accès public » est toujours affichée en tant que valeur par défaut et pas la valeur actuelle réelle
* Impossible de renommer des objets blob téléchargés
* Les entrées du journal d’activité sont parfois « coincées » dans un état En cours lorsqu’une erreur se produit et l’erreur n’est pas affichée
* Il se bloque parfois ou devient blanc lorsque vous tentez de charger ou télécharger un grand nombre d’objets blob
* Parfois, l’annulation d’une opération de copie ne fonctionne pas
* Au cours de la création d’un conteneur (objet blob/file d’attente/table), si vous entrez un nom non valide et passez à la création d’un autre sous un type de conteneur différent, il est impossible de définir le focus sur le nouveau type
* Impossible de créer un nouveau dossier ou de renommer un dossier




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
