---
title: Guide pratique pour planifier l’implémentation de la jonction Azure AD Hybride dans Azure Active Directory (Azure AD) | Microsoft Docs
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f8d0e418f43648665b95f5bf1f672e9f9dae28
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779455"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procédure : Planifier l’implémentation de la jonction Azure AD Hybride

À l’instar d’un utilisateur, un appareil est une autre identité essentielle que vous souhaitez protéger et utiliser pour protéger vos ressources à tout moment et en tout lieu. Vous pouvez atteindre cet objectif en intégrant et en gérant les identités des appareils dans Azure AD à l’aide d’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Si vous disposez d’un environnement Active Directory (AD) local et que vous souhaitez lier vos ordinateurs joints au domaine AD à Azure AD, vous pouvez y parvenir en effectuant une jonction Azure AD Hybride. Cet article présente les étapes à suivre pour implémenter une jointure Azure AD hybride dans un environnement. 

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez lu la [Présentation de la gestion des identités des appareils dans Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Les niveaux fonctionnels de domaine et de forêt minimaux requis pour une jonction Azure AD Hybride Windows 10 correspondent à Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation Azure AD hybride, prenez connaissance de ces étapes :

|   |   |
| --- | --- |
| ![Vérification][1] | Vérifier les appareils pris en charge |
| ![Vérification][1] | Connaître les points à savoir |
| ![Vérification][1] | Vérifier la validation contrôlée de la jonction Azure AD Hybride |
| ![Vérification][1] | Sélectionner votre scénario en fonction de votre infrastructure d’identité |
| ![Vérification][1] | Vérifier la prise en charge d’un UPN AD local pour la jonction Azure AD Hybride |

## <a name="review-supported-devices"></a>Vérifier les appareils pris en charge

La jointure Azure AD hybride prend en charge un large éventail d’appareils Windows. Dans la mesure où la configuration des appareils fonctionnant sous des versions antérieures de Windows implique des étapes supplémentaires ou différentes, les appareils pris en charge sont divisés en deux catégories :

### <a name="windows-current-devices"></a>Appareils Windows actuels

- Windows 10
- Windows Server 2016
- Windows Server 2019

Pour les appareils exécutant le système d’exploitation Windows, les versions prises en charge sont répertoriées dans l'article [Informations de publication relatives à Windows 10](https://docs.microsoft.com/windows/release-information/). En guise de meilleure pratique, Microsoft recommande d'effectuer une mise à niveau vers la dernière version de Windows 10.

### <a name="windows-down-level-devices"></a>Appareils Windows de bas niveau

- Windows 8.1
- Windows 7 Pour des informations relatives à la prise en charge de Windows 7, consultez l'article [Fin de la prise en charge de Windows 7](https://www.microsoft.com/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

La première étape de la planification consiste à examiner l’environnement et à déterminer s’il est nécessaire de prendre en charge les appareils Windows de bas niveau.

## <a name="review-things-you-should-know"></a>Connaître les points à savoir

La jonction Azure AD Hybride n'est pas prise en charge si votre environnement se compose d’une seule forêt AD synchronisant les données d’identité sur plusieurs locataires Azure AD.

La jonction Azure AD Hybride n'est pas prise en charge avec l'infrastructure VDI (Virtual Desktop Infrastructure).

La jonction Azure AD Hybride n’est pas prise en charge pour les modules TPM compatibles FIPS. Si vos appareils sont dotés de modules TPM compatibles FIPS, vous devez les désactiver avant de procéder à la jonction Azure AD Hybride. Microsoft ne propose aucun outil permettant de désactiver le mode FIPS pour les modules TPM car il dépend du fabricant de ces modules. Pour obtenir de l'aide, contactez votre fabricant OEM.

La jonction Azure AD Hybride n'est pas prise en charge pour Windows Server avec le rôle de contrôleur de domaine.

La jonction Azure AD Hybride n’est pas prise en charge sur les appareils Windows de bas niveau lors de l’utilisation de l’itinérance des informations d’identification ou du profil utilisateur.

Si vous comptez sur l’outil de préparation système (Sysprep) et utilisez une image **pré-Windows 10 1809** pour l’installation, vérifiez que cette image ne provient pas d'un appareil déjà inscrit auprès d’Azure AD en tant que jonction Azure AD Hybride.

Si vous utilisez une capture instantanée de machine virtuelle pour créer des machines virtuelles supplémentaires, vérifiez qu’elle ne provient pas d'une machine virtuelle déjà inscrite auprès d'Azure AD en tant que jonction Azure AD Hybride.

Si vos appareils joints au domaine Windows 10 sont déjà [inscrits auprès d’Azure AD](overview.md#getting-devices-in-azure-ad) sur votre locataire, nous vous recommandons vivement de supprimer cet état avant d’activer la jonction Azure AD Hybride. À partir de la version 1809 de Windows 10, les modifications suivantes ont été apportées pour éviter ce double état :

- Tout état existant inscrit à Azure AD est automatiquement supprimé dès lors que l’appareil est joint à Azure AD Hybride.
- Vous pouvez éviter que votre appareil joint au domaine soit inscrit à Azure AD en ajoutant cette clé de Registre - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- Cette modification est désormais disponible pour Windows 10 version 1803 avec application de KB4489894. Toutefois, si vous avez configuré Windows Hello Entreprise, l’utilisateur doit reconfigurer Windows Hello Entreprise après le nettoyage de double état.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Vérifier la validation contrôlée de la jonction Azure AD Hybride

Lorsque toutes les conditions préalables sont en place, les appareils Windows sont inscrits automatiquement en tant qu’appareils dans votre locataire Azure AD. L’état de ces identités d’appareils dans Azure AD est appelé jonction Azure AD Hybride. Des informations supplémentaires sur les concepts abordés dans cet article sont disponibles dans les articles [Présentation de la gestion des identités des appareils dans Azure Active Directory](overview.md) et [Planifier votre implémentation de jonction Azure Active Directory Hybride](hybrid-azuread-join-plan.md).

Les organisations peuvent vouloir effectuer une validation contrôlée de la jonction Azure AD Hybride avant de l’activer dans leur organisation entière en une fois. Consultez l'article [Validation contrôlée de la jonction Azure AD Hybride](hybrid-azuread-join-control.md) pour savoir comment procéder.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Sélectionner votre scénario en fonction de votre infrastructure d’identité

La jonction Azure AD Hybride fonctionne à la fois avec les environnements managés et fédérés.  

### <a name="managed-environment"></a>Environnement géré

Un environnement managé peut être déployé par le biais de la [synchronisation de hachage de mot de passe (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou de l’[authentification directe (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) avec l’[authentification unique fluide](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Ces scénarios ne nécessitent pas la configuration d’un serveur de fédération pour l’authentification.

### <a name="federated-environment"></a>Environnement fédéré

Un environnement fédéré doit disposer d’un fournisseur d’identité qui prend en charge les exigences suivantes : Si vous disposez d’un environnement fédéré utilisant les services de fédération Active Directory (AD FS), les exigences ci-dessous sont déjà prises en charge.

- **Revendication WIAORMULTIAUTHN :** Cette revendication est nécessaire à des fins de jonction Azure AD Hybride pour les appareils Windows de bas niveau.
- **Protocole WS-Trust :** Ce protocole est nécessaire pour authentifier auprès d’Azure AD les appareils Windows actuels ayant fait l’objet d’une jonction Azure AD Hybride. Lorsque vous utilisez AD FS, vous devez activer les points de terminaison WS-Trust suivants : `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** ou **adfs/services/trust/13/windowstransport** doivent tous les deux être activés en tant que points de terminaison uniquement accessibles sur intranet ; ils NE doivent PAS être exposés comme points de terminaison extranet via le proxy d’application web. Pour en savoir plus sur la désactivation des points de terminaison Windows WS-Trust, consultez [Désactiver les points de terminaison Windows WS-Trust sur le proxy](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Vous pouvez visualiser les points de terminaison qui sont activés par le biais de la console de gestion AD FS sous **Service** > **Points de terminaison**.

> [!NOTE]
> Azure AD ne prend pas en charge les cartes à puce ou les certificats dans les domaines gérés.

Depuis la version 1.1.819.0, Azure AD Connect comporte un Assistant permettant de configurer la jointure Azure AD hybride. Il simplifie considérablement le processus de configuration. Si vous n’avez pas la possibilité d’installer la version requise d’Azure AD Connect, consultez le [Guide pratique pour configurer manuellement l’inscription des appareils](hybrid-azuread-join-manual.md). 

Selon le scénario correspondant à votre infrastructure d’identité, consultez :

- [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
- [Configurer la jonction Azure Active Directory Hybride pour un environnement managé](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Vérifier la prise en charge d’un UPN AD local pour la jonction Azure AD Hybride

Parfois, vos UPN AD locaux peuvent différer de vos UPN AD Azure. Dans ce cas, une jonction Azure AD Hybride Windows 10 offre une prise en charge limitée des UPN AD locaux, variable selon la [méthode d’authentification](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), le type de domaine et la version de Windows 10. Deux types d’UPN AD locaux peuvent exister dans votre environnement :

- UPN routable : un UPN routable possède un domaine vérifié valide, qui est inscrit auprès d’un bureau d’enregistrement de domaines. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.org est le domaine principal dans l’AD local appartenant à Contoso et [vérifié dans Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- UPN non routable : un UPN non routable ne dispose pas d’un domaine vérifié. Il est applicable uniquement au sein du réseau privé de votre organisation. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.local est le domaine principal dans l’AD local, mais n’est pas un domaine vérifiable sur Internet et n’est utilisé qu’à l’intérieur du réseau de Contoso.

Le tableau ci-dessous fournit des détails sur la prise en charge de ces UPN AD locaux dans une jointure Azure AD Hybride Windows 10

| Type d’UPN AD local | Type de domaine | version Windows 10 | Description |
| ----- | ----- | ----- | ----- |
| Routable | Adresses IP fédérées | À partir de la version 1703 | Mise à la disposition générale |
| Non routable | Adresses IP fédérées | À partir de la version 1803 | Mise à la disposition générale |
| Routable | Adresses IP gérées | Non pris en charge | |
| Non routable | Adresses IP gérées | Non pris en charge | |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
> [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
