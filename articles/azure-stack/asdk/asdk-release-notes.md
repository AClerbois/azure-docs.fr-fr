---
title: Notes de publication du Kit de développement Microsoft Azure Stack | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3335fdd3a1bb20c378bf36307d742491de0e46ad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du Kit de développement Azure Stack
Ces notes de publication fournissent des informations sur les améliorations, les correctifs et les problèmes connus relatifs au Kit de développement Azure Stack. Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](.\.\azure-stack-updates.md#determine-the-current-version).

> Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-201803291"></a>Build 20180329.1

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Les nouveaux correctifs et fonctionnalités publiés pour les systèmes intégrés Azure Stack version 1803 s’appliquent au Kit de développement Azure Stack. Pour plus d’informations, consultez les sections relatives aux [nouvelles fonctionnalités](.\.\azure-stack-update-1803.md#new-features) et aux [problèmes résolus](.\.\azure-stack-update-1803.md#fixed-issues) dans les notes de publication des mises à jour d’Azure Stack 1803.  
> [!IMPORTANT]    
> Certains des éléments répertoriés dans ces sections concernent uniquement les systèmes intégrés Azure Stack.

### <a name="changes"></a>Changements
- La façon de changer l’état d’une offre nouvellement créée de *privé* à *public* ou à *désactivé* a été modifiée. Pour plus d’informations, consultez [Créer une offre](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problèmes connus
 
#### <a name="portal"></a>Portail
- La possibilité [d’ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Quand vous affichez les propriétés d’une ressource ou d’un groupe de ressources, le bouton **Déplacer** est désactivé. Il s’agit du comportement attendu. Le déplacement de ressources ou de groupes de ressources entre des groupes de ressources ou des abonnements n’est pas pris en charge actuellement.
 
- Un message d’avertissement **Activation requise** s’affiche pour vous inviter à inscrire votre Kit de développement Azure Stack. Il s’agit du comportement attendu.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- Dans le tableau de bord du portail d’administration, la vignette Mise à jour ne parvient pas à afficher les informations sur les mises à jour. Pour résoudre ce problème, cliquez sur la vignette pour l’actualiser.

-   Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom, la description et la correction de l’alerte s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité. 



#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

-  Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Mise en réseau
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.

- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.



- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.
 
- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.



#### <a name="sql-and-mysql"></a>SQL et MySQL 
- Il faut parfois attendre une heure pour que les utilisateurs puissent créer des bases de données dans une nouvelle référence SKU SQL ou MySQL.

- Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.
 
#### <a name="usage"></a>Usage  
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.






## <a name="build-201803021"></a>Build 20180302.1

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Consultez la section [Nouvelles fonctionnalités et correctifs](.\.\azure-stack-update-1802.md#new-features-and-fixes) des notes de publication des mises à jour d’Azure Stack 1802 pour les systèmes intégrés Azure Stack.

> [!IMPORTANT]    
> Certains des éléments répertoriés dans la section **Nouvelles fonctionnalités et correctifs** concernent uniquement les systèmes intégrés Azure Stack.


### <a name="known-issues"></a>Problèmes connus
 
#### <a name="portal"></a>Portail
- La possibilité [d’ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Quand vous affichez les propriétés d’une ressource ou d’un groupe de ressources, le bouton **Déplacer** est désactivé. Il s’agit du comportement attendu. Le déplacement de ressources ou de groupes de ressources entre des groupes de ressources ou des abonnements n’est pas pris en charge actuellement.
 
- Un message d’avertissement **Activation requise** s’affiche pour vous inviter à inscrire votre Kit de développement Azure Stack. Il s’agit du comportement attendu.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- Dans le tableau de bord du portail d’administration, la vignette Mise à jour ne parvient pas à afficher les informations sur les mises à jour. Pour résoudre ce problème, cliquez sur la vignette pour l’actualiser.

-   Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom, la description et la correction de l’alerte s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité. 

- Dans le portail d’administration et le portail utilisateur, le panneau Vue d’ensemble ne parvient pas à charger lorsque vous sélectionnez le panneau Vue d’ensemble des comptes de stockage qui ont été créés avec une ancienne version de l’API (exemple : 2015-06-15). 

  Pour résoudre ce problème, utilisez PowerShell pour exécuter le script **ResourceSynchronization.ps1** pour restaurer l’accès aux détails du compte de stockage. [Le script est disponible à partir de GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) et doit s’exécuter avec des informations d’identification d’administrateur de service sur le kit de développement hôte si vous utilisez l’ASDK.  

- Le chargement du panneau **Service Health** a échoué. Si vous ouvrez le panneau Service Health dans le portail d’administration ou utilisateur, Azure Stack affiche une erreur et ne charge pas les informations. Ce comportement est normal. Même s’il est possible de sélectionner et d’ouvrir Service Health, cette fonctionnalité n’est pas encore disponible ; elle sera implémentée dans une prochaine version d’Azure Stack.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance
Dans le portail d’administration Azure Stack, vous pouvez voir une alerte critique portant le nom **Certificat externe sur le point d’expirer**.  Cette alerte peut être ignorée en toute sécurité et n’affecte pas les opérations du Kit de développement Azure Stack. 


#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- Azure Stack prend en charge l’utilisation de disques durs virtuels de type fixe uniquement. Certaines images proposées par le biais de la Place de Marché sur Azure Stack utilisent des disques durs virtuels dynamiques, mais elles ont été supprimées. Le redimensionnement d’une machine virtuelle à laquelle un disque dynamique est attaché laisse la machine virtuelle dans un état d’échec.

  Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer le disque de la machine virtuelle, un objet blob VHD dans un compte de stockage. Convertissez ensuite le disque dur virtuel de disque dynamique en disque fixe, puis recréez la machine virtuelle.

- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

-  Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Mise en réseau
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.

- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

-   La fonctionnalité de transfert IP est visible dans le portail ; toutefois son activation n’a aucun effet. Cette fonctionnalité n’est pas encore prise en charge.

- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.
 
- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.



#### <a name="sql-and-mysql"></a>SQL et MySQL 
- Il faut parfois attendre une heure pour que les utilisateurs puissent créer des bases de données dans une nouvelle référence SKU SQL ou MySQL.

- Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.
 
#### <a name="usage"></a>Usage  
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.




## <a name="build-201801032"></a>Build 20180103.2

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

- Consultez la section [Nouvelles fonctionnalités et correctifs](.\.\azure-stack-update-1712.md#new-features-and-fixes) des notes de mise à jour d’Azure Stack 1712 pour les systèmes intégrés Azure Stack.

    > [!IMPORTANT]
    > Certains des éléments répertoriés dans la section **Nouvelles fonctionnalités et correctifs** concernent uniquement les systèmes intégrés Azure Stack.

### <a name="known-issues"></a>Problèmes connus
 
#### <a name="deployment"></a>Déploiement
- Vous devez spécifier un serveur de temps par adresse IP au cours du déploiement.

#### <a name="infrastructure-management"></a>Gestion de l’infrastructure
- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.
- Le modèle et l’adresse IP du contrôleur BMC (Baseboard Management Controller) ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle. Ce comportement est attendu dans le Kit de développement Azure Stack.

#### <a name="portal"></a>Portail
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
-  Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide. 

   Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.

- Le message d’avertissement **Activation requise** s’affichera pour vous inviter à enregistrer votre Kit de développement Azure Stack. Il s’agit du comportement attendu.
- Si vous cliquez sur le lien **Composant** à partir de n'importe quelle alerte **Rôle d'infrastructure**, le panneau **Vue d'ensemble** qui en résulte essaie de se charger et échoue. De plus, le panneau **Vue d'ensemble** n'expire pas.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
- Le chargement du panneau **Service Health** a échoué. Si vous ouvrez le panneau Service Health dans le portail d’administration ou utilisateur, Azure Stack affiche une erreur et ne charge pas les informations. Ce comportement est normal. Même s’il est possible de sélectionner et d’ouvrir Service Health, cette fonctionnalité n’est pas encore disponible ; elle sera implémentée dans une prochaine version d’Azure Stack.
#### <a name="marketplace"></a>Marketplace
- Certains éléments du marketplace sont supprimés dans cette version pour des raisons de compatibilité. Ils seront réactivés après une validation supplémentaire.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création. 
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

#### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.
- Les opérateurs Azure Stack peuvent être dans l’impossibilité de déployer, supprimer ou modifier des réseaux virtuels ou des groupes de sécurité réseau. Ce problème se produit principalement lors des tentatives de mise à jour ultérieures du même package. Il est dû à un problème d’empaquetage avec une mise à jour, que nous étudions actuellement.
- L’équilibrage de charge interne gère de façon incorrecte les adresses MAC des machines virtuelles principales, ce qui supprime des paquets sur le réseau principal lors de l’utilisation d’instances Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
 
#### <a name="usage"></a>Usage  
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

#### <a name="identity"></a>Identité

Dans les environnements déployés des services de fédération Azure Active Directory (AD FS), le compte **azurestack\azurestackadmin** n’est plus le propriétaire de l’abonnement Fournisseur par défaut. Au lieu de vous connecter au **portail d’administration / point de terminaison adminmanagement** avec le compte **azurestack\azurestackadmin**, vous pouvez utiliser le compte **azurestack\cloudadmin**, afin de pouvoir gérer et utiliser l’abonnement Fournisseur par défaut.

> [!IMPORTANT]
> Même si le compte **azurestack\cloudadmin** est propriétaire de l’abonnement Fournisseur par défaut dans les environnements AD FS déployés, il ne dispose pas des autorisations nécessaires pour établir une connexion RDP avec l’hôte. Continuez à utiliser le compte **azurestack\azurestackadmin** ou le compte d’administrateur local pour vous connecter, accéder à l’hôte et le gérer en fonction des besoins.


