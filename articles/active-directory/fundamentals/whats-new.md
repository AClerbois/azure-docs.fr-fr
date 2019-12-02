---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les modifications à venir.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6c6e680de6253f5e822ba282df2e2397093d003
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270993"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` dans votre lecteur de flux ![icône RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Active Directory](whats-new-archive.md).

---

## <a name="october-2019"></a>Octobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Dépréciation de l’API identityRiskEvent pour la détection des risques par Azure AD Identity Protection  

**Type :** Modification planifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Suite aux commentaires des développeurs, les abonnés à Azure AD Premium P2 peuvent désormais effectuer des requêtes complexes sur les données de détection des risques d’Azure AD Identity Protection à l’aide de la nouvelle API riskDetection pour Microsoft Graph. La version bêta de l’API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) existante cessera de renvoyer des données vers le **10 janvier 2020**. Si votre organisation utilise l’API identityRiskEvent, vous devez passer à la nouvelle API riskDetection.

Pour plus d’informations sur la nouvelle API riskDetection, consultez la [Documentation de référence sur l’API de détection des risques](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Prise en charge du proxy d'application pour l'attribut SameSite et Chrome 80

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

À quelques semaines de la publication du navigateur Chrome 80, nous envisageons de mettre à jour la manière dont les cookies du proxy d’application traitent l’attribut **SameSite**. Avec Chrome 80, tous les cookies qui ne spécifieront pas l’attribut **SameSite** seront traités comme s'ils avaient été définis sur `SameSite=Lax`.

Pour éviter tout impact négatif lié à ce changement, nous mettons à jour l’accès au proxy d’application et les cookies de session en procédant comme suit :

- Définition de la valeur par défaut du paramètre **Utiliser un cookie sécurisé** sur **Oui**.

- Définition de la valeur par défaut de l’attribut **SameSite** sur **Aucun**.

    >[!NOTE]
    > Les cookies d’accès au proxy d’application ont toujours été transmis exclusivement via des canaux sécurisés. Ces modifications s’appliquent uniquement aux cookies de session.

Pour plus d’informations sur les paramètres de cookies du proxy d'application, consultez [Paramètres de cookies pour l’accès aux applications locales dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Les inscriptions d’applications (héritées) et la gestion des applications convergées à partir du portail d’inscription des applications (apps.dev.microsoft.com) ne seront plus disponibles.

**Type :** Modification planifiée  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Expérience de développement

Dans un proche avenir, les utilisateurs disposant de comptes Azure AD ne seront plus en mesure d’inscrire et de gérer les applications convergées à l’aide du portail d’inscription des applications (apps.dev.microsoft.com), ni d’inscrire et de gérer les applications dans l'expérience des inscriptions d'applications (héritées) du portail Azure.

Pour en savoir plus sur la nouvelle expérience en matière d'inscriptions d’applications, consultez le [Guide de formation sur les inscriptions d’applications dans le portail Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Les utilisateurs ne sont plus tenus de se réinscrire lors de la migration de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel.

**Type :** Résolution  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous avons résolu un problème connu qui obligeait les utilisateurs à se réinscrire s’ils étaient désactivés de l’authentification multifacteur par utilisateur, puis activés pour l’authentification multifacteur basée sur l’accès conditionnel.

Pour contraindre les utilisateurs à se réinscrire, vous pouvez sélectionner l’option **Réinscription à l'authentification multifacteur requise** à partir des méthodes d’authentification de l’utilisateur dans le portail Azure AD. Pour plus d’informations sur la migration des utilisateurs de l’authentification multifacteur (MFA) par utilisateur vers l’authentification MFA basée sur l’accès conditionnel, consultez [Convertir des utilisateurs de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nouvelles fonctionnalités de transformation et d’envoi de revendications dans votre jeton SAML

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

Nous avons ajouté des fonctionnalités supplémentaires pour vous aider à personnaliser et à envoyer des revendications dans votre jeton SAML. Ces fonctionnalités sont les suivantes :

- Fonctions de transformation des revendications supplémentaires pour vous aider à modifier la valeur que vous envoyez dans la revendication.

- Possibilité d’appliquer plusieurs transformations à une même revendication.

- Possibilité de spécifier la source de la revendication en fonction du type d’utilisateur et du groupe auquel appartient l’utilisateur.

Pour plus d’informations sur ces nouvelles fonctionnalités, notamment sur leur utilisation, consultez [Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nouvelle page Mes connexions pour les utilisateurs finaux dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons ajouté une nouvelle page **Mes connexions** https://mysignins.microsoft.com) pour permettre aux utilisateurs de votre organisation d’afficher leur historique de connexion récent afin d'y détecter toute activité inhabituelle. Cette nouvelle page permet à vos utilisateurs de voir :

- Si quelqu’un tente de deviner leur mot de passe.

- Si un attaquant s’est connecté à leur compte et depuis quel emplacement.

- Les applications auxquelles l’attaquant a tenté d’accéder.

Pour plus d’informations, consultez le billet de blog [Les utilisateurs peuvent désormais consulter leur historique afin d'y détecter toute activité inhabituelle](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migration d'Azure AD Domain Services (Azure AD DS) des réseaux virtuels classiques aux réseaux virtuels Azure Resource Manager

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes porteurs d'une bonne nouvelle pour nos clients bloqués sur des réseaux virtuels classiques. Vous pouvez désormais effectuer une migration unique à partir d’un réseau virtuel classique vers un réseau virtuel existant Resource Manager. Après avoir migré vers le réseau virtuel Resource Manager, vous pourrez tirer parti de fonctionnalités supplémentaires et mises à niveau, telles que les stratégies de mot de passe affinées, les notifications par e-mail et les journaux d’audit.

Pour plus d'informations, consultez [Préversion - Migrer Azure Active Directory Domain Services depuis le modèle de réseau virtuel classique vers Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Mise à jour de la disposition de contrat de page Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Nous avons apporté de nouvelles modifications à la version 1.2.0 du contrat de page Azure AD B2C. Dans cette version mise à jour, vous pouvez maintenant contrôler l’ordre de chargement des éléments, ce qui permet aussi de mettre fin au scintillement qui survient lorsque la feuille de style (CSS) est chargée.

Pour obtenir la liste complète des modifications apportées au contrat de page, consultez le [Journal des modifications de version](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Mise à jour de la page Mes applications avec les nouveaux espaces de travail (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Contrôle d’accès

Vous pouvez désormais personnaliser la manière dont les utilisateurs de votre organisation affichent et accèdent à la nouvelle expérience Mes applications, notamment l’utilisation de la nouvelle fonctionnalité Espaces de travail pour faciliter la recherche d’applications. La nouvelle fonctionnalité Espaces de travail fait office de filtre pour les applications auxquelles les utilisateurs de votre organisation ont déjà accès.

Pour plus d’informations sur le déploiement de la nouvelle expérience Mes applications et la création d’espaces de travail, consultez [Créer des espaces de travail sur le portail Mes applications (préversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Prise en charge du modèle de facturation basée sur les utilisateurs actifs mensuels (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Azure AD B2C prend désormais en charge la facturation basée sur les utilisateurs actifs mensuels. Cette facturation repose sur le nombre d’utilisateurs uniques avec activité d’authentification pendant un mois civil. Les clients existants peuvent à tout moment opter pour cette nouvelle méthode de facturation.

Depuis le 1er novembre 2019, tous les nouveaux clients sont automatiquement facturés à l’aide de cette méthode. Cette méthode de facturation offre aux clients des avantages en termes de coût et de planification.

Pour plus d’informations, consultez [Mise à niveau vers le modèle de facturation basée sur les utilisateurs actifs mensuels](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Élément de menu de sécurité consolidé dans le portail Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pouvez désormais accéder à toutes les fonctionnalités de sécurité Azure AD disponibles à partir du nouvel élément de menu **Sécurité** et de la barre **Rechercher** du portail Azure. En outre, la nouvelle page d’accueil **Sécurité**, appelée **Sécurité - Prise en main**, propose des liens vers notre documentation publique, des conseils de sécurité, ainsi que des guides de déploiement.

Le nouveau menu **Sécurité** comprend ce qui suit :

- Accès conditionnel
- Identity Protection
- Security Center
- Identity Secure Score
- Méthodes d’authentification
- MFA
- Rapports sur les risques - Utilisateurs à risque, connexions risquées, détections de risques
- Et bien plus...

Pour plus d’informations, consultez [Sécurité - Prise en main](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Stratégie d’expiration des groupes Office 365 améliorée avec renouvellement automatique

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

La stratégie d’expiration des groupes Office 365 a été améliorée pour renouveler automatiquement les groupes utilisés de manière active par leurs membres. Les groupes sont renouvelés sur la base de l'activité des utilisateurs dans toutes les applications Office 365, y compris Outlook, SharePoint et Teams.

Cette amélioration permet de réduire les notifications d’expiration de votre groupe et de veiller à ce que les groupes actifs continuent d'être disponibles. Si vous disposez déjà d’une stratégie d’expiration active pour vos groupes Office 365, vous n’avez rien à faire pour activer cette nouvelle fonctionnalité.

Pour plus d'informations, consultez [Configurer la stratégie d’expiration pour les groupes Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Expérience de création Azure AD Domain Services (Azure AD DS) mise à jour

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous avons mis à jour Azure AD Domain Services (Azure AD DS) pour y inclure une nouvelle expérience de création améliorée, qui vous permet de créer un domaine géré en seulement trois clics. En outre, vous pouvez désormais charger et déployer Azure AD DS à partir d’un modèle.

Pour plus d’informations, consultez [Tutoriel : Créer et configurer une instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Septembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planifier la modification : Dépréciation des packs de contenu Power BI

**Type :** Modification planifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

À partir du 1er octobre 2019, Power BI commencera à déprécier tous les packs de contenu, y compris le pack de contenu Azure AD Power BI. En guise d’alternative à ce pack de contenu, vous pouvez utiliser des classeurs Azure AD pour obtenir des informations sur les services liés à Azure AD. D’autres classeurs seront bientôt accessibles, y compris des classeurs pour les stratégies d’accès conditionnel en mode rapport uniquement, des insights basés sur le consentement de l’application, et plus.

Pour plus d’informations sur les classeurs, découvrez [comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Pour plus d’informations sur la dépréciation des packs de contenu, consultez le billet de blog [Annonce de la disponibilité générale des applications de modèle Power BI](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mon profil change de nom et s’intègre à la page du compte Microsoft Office

**Type :** Modification planifiée  
**Catégorie de service :** Mon profil/compte  
**Fonctionnalité de produit :** Collaboration

À compter d’octobre, l’expérience de la page Mon profil deviendra Mon compte. Dans le cadre de cette modification, **Mon compte** s’affichera partout où il est actuellement indiqué **Mon profil**. Outre le changement de nom et quelques améliorations en matière de conception, la mise à jour de l’expérience offrira une intégration supplémentaire à la page du compte Microsoft Office. En particulier, vous serez en mesure d’accéder aux installations et aux abonnements Office à partir de la page **Vue d’ensemble du compte** ainsi qu’à des préférences de contact Office à partir de la page **Confidentialité**.

Pour plus d’informations sur l’expérience de la page Mon profil (préversion), consultez [Vue d’ensemble du portail Mon profil (préversion)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gérer en bloc les groupes et les membres à l’aide de fichiers CSV dans le portail Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous sommes heureux d’annoncer la disponibilité de la préversion publique des expériences de gestion en bloc des groupes dans le portail Azure AD. Vous pouvez maintenant utiliser un fichier CSV et le portail Azure AD pour gérer les groupes et les listes de membres, notamment :

- Ajout ou suppression de membres dans un groupe.

- Téléchargement de la liste des groupes à partir de l’annuaire.

- Téléchargement de la liste des membres d’un groupe spécifique.

Pour plus d’informations, consultez [Ajouter des membres en bloc](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Supprimer en bloc des membres](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [Télécharger une liste de membres en bloc](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) et [Télécharger une liste de groupes en bloc](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Le consentement dynamique est désormais pris en charge par un nouveau point de terminaison de consentement administrateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Nous avons créé un point de terminaison de consentement administrateur pour prendre en charge le consentement dynamique, ce qui est utile pour les applications qui souhaitent utiliser le modèle de consentement dynamique sur la plateforme d’identités Microsoft.

Pour plus d’informations sur l’utilisation de ce nouveau point de terminaison, consultez [Utilisation du point de terminaison de consentement administrateur](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Nouveau rôle Lecteur général Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès

À compter du 24 septembre 2019, nous allons commencer à déployer un nouveau rôle Azure Active Directory (AD) nommé Lecteur général. Ce déploiement commencera avec les clients de cloud international et de production pour finir à l’échelle mondiale en octobre.

Le rôle Lecteur général est l’équivalent en lecture seule de l’administrateur général. Les utilisateurs dans ce rôle peuvent lire les paramètres et les informations d’administration entre les services Microsoft 365, mais ne peuvent pas entreprendre d’actions de gestion. Nous avons créé le rôle Lecteur général pour permettre de réduire le nombre d’administrateurs généraux de votre organisation. Étant donné que les comptes d’administrateur général sont puissants et vulnérables aux attaques, nous vous recommandons d’avoir moins de cinq administrateurs généraux. Nous vous recommandons d’utiliser le rôle Lecteur général pour la planification, les audits ou les examens. Nous vous recommandons également d’utiliser le rôle Lecteur général associé à d’autres rôles d’administrateur limités, comme Administrateur Exchange, pour vous aider à effectuer des tâches sans avoir besoin du rôle Administrateur général.

Le rôle Lecteur général fonctionne avec le nouveau Centre d’administration Microsoft 365, le Centre d’administration Exchange, le Centre d’administration Teams, le Centre de sécurité, le Centre de conformité, le Centre d’administration Azure AD et le Centre d’administration de gestion des appareils.

>[!NOTE]
> Au début de la préversion publique, le rôle Lecteur général ne fonctionnera pas avec : SharePoint, Privileged Access Management, Customer Lockbox, les étiquettes de confidentialité, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management et Teams App Catalog. Tous ces services sont conçus pour fonctionner avec le rôle à l’avenir.

Pour plus d’informations, consultez [Autorisations des rôles d’administrateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accéder à un serveur de rapports local à partir de votre application mobile Power BI à l’aide du proxy d’application Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Une nouvelle intégration entre l’application mobile Power BI et le proxy d’application Azure Active Directory vous permet de vous connecter en toute sécurité à l’application mobile Power BI et de voir les rapports de votre organisation hébergés sur Power BI Report Server en local.

Pour plus d’informations sur l’application mobile Power BI, notamment l’emplacement de téléchargement de l’application, consultez le [site Power BI](https://powerbi.microsoft.com/mobile/). Pour plus d’informations sur la configuration de l’application mobile Power BI avec le proxy d’application Azure Active Directory, consultez [Activer l’accès distant à Power BI Mobile avec le proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Une nouvelle version du module PowerShell AzureADPreview est disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire

De nouvelles applets de commande ont été ajoutées au module AzureADPreview pour vous aider à définir et assigner des rôles personnalisés dans Azure AD, avec notamment ce qui suit :

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nouvelle version d’Azure AD Connect

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire

Nous avons publié une version mise à jour d’Azure AD Connect pour les clients de mise à niveau automatique. Cette nouvelle version comprend plusieurs nouvelles fonctionnalités, améliorations et correctifs de bogues. Pour plus d’informations sur cette nouvelle version, consultez [Azure AD Connect : historique de publication des versions](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Le serveur Azure Multi-Factor Authentication (MFA) version 8.0.2 est désormais disponible

**Type :** Résolution  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

Si vous êtes un client existant qui a activé le serveur MFA avant le 1er juillet 2019, vous pouvez maintenant télécharger la dernière version du serveur MFA (version 8.0.2). Dans cette nouvelle version, nous avons :

- résolu un problème de sorte qu’un e-mail est envoyé à l’utilisateur quand la synchronisation Azure AD fait passer l’état d’un utilisateur de désactivé à activé.

- résolu un problème de sorte que les clients peuvent effectuer correctement la mise à niveau tout en continuant d’utiliser la fonctionnalité Balises.

- ajouté l’indicatif de pays du Kosovo (+383).

- ajouté l’enregistrement d’audit de contournement à usage unique au journal MultiFactorAuthSvc.log.

- amélioré les performances du kit SDK du service web.

- corrigé d’autres bogues mineurs.

À partir du 1er juillet 2019, Microsoft a cessé d’offrir un serveur MFA pour les nouveaux déploiements. Les nouveaux clients qui exigent une authentification multifacteur doivent utiliser la fonction Azure Multi-Factor Authentication basée sur le cloud. Pour plus d’informations, consultez [Planification d’un déploiement Azure Multi-Factor Authentication basé sur le cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Août 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>La recherche, le filtrage et le tri améliorés pour les groupes sont disponibles sur le portail Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous sommes heureux d'annoncer la disponibilité de la préversion publique des expériences améliorées liées aux groupes dans le portail Azure AD. Ces améliorations vous aident à mieux gérer les groupes et les listes de membres, en fournissant les éléments suivants :

- Fonctionnalités avancées de recherche, notamment la recherche de sous-chaînes dans des listes de groupes.
- Options avancées de filtrage et de tri sur des listes de membres et de propriétaires.
- Nouvelles fonctionnalités de recherche pour les listes de membres et de propriétaires.
- Comptage de groupes plus précis pour les grands groupes.

Pour plus d’informations, voir [Gérer des groupes dans le portail Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>De nouveaux rôles personnalisés sont disponibles pour la gestion de l’inscription des applications (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès

Les rôles personnalisés (disponibles avec un abonnement Azure AD P1 ou P2) peuvent désormais vous fournir un accès affiné, en vous permettant de créer des définitions de rôles avec des autorisations spécifiques, puis d’attribuer ces rôles à des ressources spécifiques. Actuellement, vous créez des rôles personnalisés en utilisant des autorisations pour gérer les inscriptions d’applications, puis en affectant le rôle à une application spécifique. Pour plus d’informations sur les rôles personnalisés, consultez [Rôles d’administrateur personnalisés dans Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Si vous avez besoin de la prise en charge d’autorisations ou de ressources supplémentaires et que vous ne les voyez pas actuellement, vous pouvez envoyer des commentaires sur notre [site de retours relatifs à Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) et nous ajouterons votre demande à notre feuille de route de mise à jour.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Les nouveaux journaux d’approvisionnement peuvent vous aider à surveiller et à résoudre les problèmes de votre déploiement d’approvisionnement d’application (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

De nouveaux journaux de provisionnement sont disponibles pour vous aider à surveiller et à résoudre les problèmes de déploiement de l’approvisionnement des utilisateurs et des groupes. Ces nouveaux fichiers journaux incluent des informations sur les éléments suivants :

- Quels groupes ont été créés avec succès dans [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quels rôles ont été importés à partir [d’Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws) ?
- Les employés qui n’ont pas été importés à partir de [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Pour en savoir plus, voir [Approvisionnement des rapports dans le portail Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nouveaux rapports de sécurité pour tous les administrateurs Azure AD (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Par défaut, tous les administrateurs Azure AD seront bientôt en mesure d’accéder à des rapports de sécurité modernes au sein d’Azure AD. Jusqu’à la fin du mois de septembre, vous pourrez utiliser la bannière en haut des rapports de sécurité modernes pour revenir aux anciens rapports.

Les rapports de sécurité modernes fournissent des fonctionnalités supplémentaires par rapport aux versions antérieures, notamment ce qui suit :

- Filtrage et tri avancés
- Actions en bloc, telles que le rejet de risque utilisateur
- Confirmation d’entités compromises ou sûres
- État du risque, à savoir : À risque, Ignoré, Corrigé ou Compromission confirmée.
- Nouvelles détections liées aux risques (disponibles pour les abonnés Azure AD Premium)

Pour plus d’informations, consultez [Utilisateurs à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Connexions à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins) et [Détections de risques](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L’identité gérée affectée par l’utilisateur est disponible pour les machines virtuelles et les groupes de machines virtuelles identiques (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identités gérées pour les ressources Azure  
**Fonctionnalité de produit :** Expérience de développement

Les identités gérées affectées par l’utilisateur sont désormais généralement disponibles pour les machines virtuelles et les groupes de machines virtuelles identiques. Dans le cadre de cet ajout, Azure peut créer une identité dans le locataire Azure AD approuvé par l’abonnement utilisé et peut être affecté à une ou plusieurs instances de service Azure. Pour en savoir plus sur les identités managées affectées par l’utilisateur, consultez la section [Que sont les identités managées pour les ressources Azure ?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Les utilisateurs peuvent réinitialiser leur mot de passe à l’aide d’une application mobile ou d’un jeton matériel (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Authentification utilisateur

Les utilisateurs qui ont inscrit une application mobile auprès de votre organisation peuvent désormais réinitialiser leur propre mot de passe en approuvant une notification de l’application Microsoft Authenticator ou en entrant un code à partir de leur application mobile ou d’un jeton matériel.

Pour plus d’informations, découvrez le [fonctionnement de ce processus : Réinitialisation de mot de passe en libre-service Azure AD](https://aka.ms/authappsspr). Pour plus d’informations sur l’expérience utilisateur, consultez [Vue d’ensemble de la réinitialisation d’un mot de passe professionnel ou scolaire](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignore le cache partagé MSAL.NET pour les scénarios On-Behalf-Of

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Depuis la version 5.0.0-preview de la bibliothèque d’authentification Azure AD (ADAL.NET), les développeurs d’applications doivent [sérialiser un cache par compte pour les applications web et les API web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). À défaut, certains scénarios utilisant le [flux On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow) avec certains cas d’usage spécifiques de `UserAssertion` peuvent entraîner une élévation de privilège. Pour éviter cette vulnérabilité, ADAL.NET ignore désormais la bibliothèque d’authentification Microsoft pour le cache partagé dotnet (MSAL.NET) dans les scénarios On-Behalf-Of.

Pour plus d’informations sur ce problème, consultez le document qui traite de la [vulnérabilité liée à l’élévation de privilège de la bibliothèque d’authentification Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Août 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En août 2019, nous avons ajouté à notre galerie d’applications les 26 applications suivantes qui prennent en charge la fédération :

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Europe)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media™ Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>De nouvelles versions des modules PowerShell AzureAD et AzureADPreview sont disponibles

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire

De nouvelles mises à jour des modules PowerShell AzureAD et AzureADPreview sont disponibles :

- Un nouveau paramètre `-Filter` a été ajouté au paramètre `Get-AzureADDirectoryRole` dans le module AzureAD. Ce paramètre vous permet de filtrer sur les rôles d’annuaire retournés par l’applet de commande.
- De nouvelles applets de commande ont été ajoutées au module AzureADPreview pour vous aider à définir et assigner des rôles personnalisés dans Azure AD, avec notamment ce qui suit :

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Améliorations apportées à l’interface utilisateur du générateur de règles de groupe dynamique dans le portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous avons apporté des améliorations à l’interface utilisateur du générateur de règles de groupe dynamique, disponible dans le portail Azure, pour vous aider à configurer plus facilement une nouvelle règle ou à modifier les règles existantes. Cette amélioration de la conception vous permet de créer des règles avec cinq expressions au lieu d’une seule. Nous avons également mis à jour la liste des propriétés d’appareil pour supprimer les propriétés d’appareil déconseillées.

Pour plus d’informations, consultez gérer les [règles d’appartenance dynamique](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nouvelle autorisation d’application Microsoft Graph disponible pour une utilisation avec des révisions d’accès

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités

Nous avons introduit une nouvelle autorisation d’application Microsoft Graph, `AccessReview.ReadWrite.Membership`, qui permet aux applications de créer et de récupérer automatiquement les révisions d’accès pour les appartenances aux groupes et les affectations d’applications. Cette autorisation peut être utilisée par vos tâches planifiées ou dans le cadre de votre automation, sans nécessiter de contexte d’utilisateur connecté.

Pour plus d’informations, consultez [Exemple de création de révisions d’accès Azure AD avec les autorisations d’application Microsoft Graph sur le blog PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Les journaux d’activité Azure AD sont désormais disponibles pour les instances Government Cloud dans Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons le plaisir de vous annoncer que les journaux d’activité Azure AD sont désormais disponibles pour les instances Government Cloud dans Azure Monitor. Vous pouvez désormais envoyer des journaux Azure AD à votre compte de stockage ou à un Event Hub pour les intégrer à vos outils SIEM, tels que [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk) et [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Pour plus d’informations sur la configuration d’Azure Monitor, consultez [Journaux d’activité Azure Active Directory dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Mettez à jour vos utilisateurs vers la nouvelle expérience d’informations de sécurité améliorée

**Type :** Fonctionnalité modifiée  
**Catégorie de service :**  Authentifications (connexions)   
**Fonctionnalité de produit :** Authentification utilisateur

Le 25 septembre 2019, nous allons désactiver l’ancienne expérience d’informations de sécurité non améliorée pour l’inscription et la gestion des informations de sécurité utilisateur. Seule la [nouvelle version améliorée](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) sera disponible. Cela signifie que vos utilisateurs ne seront plus en mesure d’utiliser l’ancienne expérience.

Pour plus d’informations sur l’expérience d’informations de sécurité améliorée, consultez notre [documentation administrateur](https://aka.ms/securityinfodocs)  et notre [documentation utilisateur](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pour activer cette nouvelle expérience, vous devez :

1. Connectez-vous au portail Azure en tant qu’Administrateur général ou Administrateur d’utilisateurs.

2. Accédez à  **Azure Active Directory > Paramètres utilisateur > Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.

3. Dans la zone **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité - amélioré**, sélectionnez **Sélectionné**, puis choisissez un groupe d’utilisateurs ou **Tout** pour activer cette fonctionnalité pour tous les utilisateurs du locataire.

4. Sous la zone **Users can use preview features for registering and managing security **info**,**(les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité), sélectionnez **None** (Aucun).

5. Enregistrez vos paramètres.

    Une fois que vous avez enregistré vos paramètres, vous n’avez plus accès à l’ancienne expérience d’informations de sécurité.

>[!Important]
>Si vous n’effectuez pas ces étapes avant le 25 septembre 2019, votre client Azure Active Directory passera automatiquement à l’expérience améliorée. En cas de questions, veuillez nous contacter à l’adresse registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Les demandes d’authentification utilisant des connexions POST seront plus rigoureusement validées

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Standards

À partir du 2 septembre 2019, les demandes d’authentification à l’aide de la méthode POST seront plus rigoureusement validées par rapport aux normes HTTP. Plus précisément, les espaces et les guillemets doubles (") ne seront plus supprimés des valeurs du formulaire de demande. Ces modifications ne devraient pas bloquer les clients existants et permettront de s’assurer que les demandes envoyées à Azure AD sont gérées de manière fiable à chaque fois.

Pour plus d’informations, consultez les [avis relatifs aux changements cassants d’Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Juillet 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planifier la modification : Mise à jour du service proxy d’application pour prendre en charge uniquement le protocole TLS 1.2

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Pour vous aider à bénéficier de notre chiffrement le plus puissant, nous allons commencer à restreindre l’accès au service proxy d’application aux protocoles TLS 1.2. Cette limitation sera initialement déployée vers les clients qui utilisent déjà les protocoles TLS 1.2. Elle sera donc sans incidence sur vous. L’abandon complet des protocoles TLS 1.0 et TLS 1.1 sera terminé le 31 août 2019. Les clients qui utilisent toujours TLS 1.0 et TLS 1.1 recevront un préavis pour pouvoir se préparer à ce changement.

Pour maintenir la connexion au service proxy d’application tout au long de cette modification, nous vous recommandons de vous assurer que vos combinaisons client-serveur et navigateur-serveur sont à jour pour utiliser TLS 1.2. Nous vous recommandons également de veiller à inclure tous les systèmes client utilisés par vos employés pour accéder aux applications publiées via la service proxy d’application.

Pour plus d’informations, consultez [Ajouter une application locale pour un accès à distance via le service Proxy d’application d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planifier la modification : Mises à jour de conception prochainement disponibles pour la Galerie d’applications

**Type :** Modification planifiée  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

De nouvelles modifications d’interface utilisateur vont être apportées à la conception de la zone **Ajouter à partir de la galerie** du panneau **Ajouter une application**. Ces modifications vous aideront à trouver plus facilement vos applications qui prennent en charge l’approvisionnement automatique, OpenID Connect, Security Assertion Markup Language (SAML) et l’authentification unique (SSO) par mot de passe.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planifier la modification : Suppression de l’adresse IP de serveur MFA de l’adresse IP Office 365

**Type :** Modification planifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous supprimons l’adresse IP de serveur MFA du [service web d’adresse IP et d’URL Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Si vous utilisez actuellement ces pages pour mettre à jour les paramètres de votre pare-feu, vous devez également veiller à inclure la liste des adresses IP évoquée dans la section **Configuration requise du serveur Azure Multi-Factor Authentication** de l’article [Prise en main du serveur Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Les jetons réservés aux applications exigent à présent que l’application cliente existe dans le locataire de ressource

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Le 26 juillet 2019, nous avons modifié la manière dont nous fournissons les jetons d’application uniquement par l’[octroi des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Auparavant, les applications pouvaient recevoir des jetons pour appeler d’autres applications, que l’application client se trouve ou non dans le locataire. Nous avons mis à jour ce comportement afin que les ressources à locataire unique, parfois appelées API web, ne puissent être appelées que par des applications clientes existant dans le locataire de ressource.

Si votre application ne se trouve pas dans le locataire de ressource, vous recevez le message d’erreur suivant : `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`. Pour résoudre ce problème, vous devez créer le principal du service de l’application cliente dans le locataire, à l’aide du [point de terminaison de consentement de l’administrateur](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) ou via [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), ce qui garantit que votre locataire a octroyé la permission d’application pour opérer dans le locataire.

Pour plus d’informations, voir [Quelles sont les nouveautés en matière d’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Un consentement existant entre le client et l’API n’est toujours pas requis. Les applications doivent toujours effectuer leurs propres vérifications d’autorisation.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nouvelle connexion sans mot de passe à Azure AD à l’aide de clés de sécurité FIDO2

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les clients Azure AD peuvent désormais définir des stratégies pour gérer les clés de sécurité FIDO2 pour les utilisateurs et groupes de leur organisation. Les utilisateurs finaux peuvent également enregistrer eux-mêmes leurs clés de sécurité, les utiliser pour se connecter à leurs comptes Microsoft sur des sites web à l’aide d’appareils compatibles FIDO, ainsi que pour se connecter à leurs appareils Windows 10 connectés à Azure AD.

Pour plus de détails, voir la section [Activer l’authentification sans mot de passe pour Azure AD (préversion)](/azure/active-directory/authentication/concept-authentication-passwordless) qui traite des informations relatives à l’administrateur, et la section [Configurer des informations de sécurité pour utiliser une clé de sécurité (préversion)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) qui traite des informations relatives à l’utilisateur final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App – Juillet 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En juillet 2019, nous avons ajouté à notre galerie d’applications les 18 nouvelles applications suivantes qui prennent en charge la fédération :

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nouvelle balise de service Azure AD Domain Services pour groupe de sécurité réseau

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Si vous en avez assez de gérer de longues listes d’adresses et de plages d’adresses IP, vous pouvez utiliser la nouvelle étiquette de service réseau **AzureActiveDirectoryDomainServices** dans votre groupe de sécurité réseau Azure pour sécuriser le trafic entrant vers votre sous-réseau de réseau virtuel Azure AD Domain Services.

Pour plus d’informations sur cette nouvelle étiquette de service, voir [Groupes de sécurité réseau pour Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nouveaux audits de sécurité pour Azure AD Domain Services (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes heureux d’annoncer le mise en production de l’audit de sécurité du service de domaine Azure AD en préversion publique. L’audit de sécurité vous aide à recueillir des informations essentielles sur vos services d’authentification en diffusant en continu les événements d’audit de sécurité vers des ressources ciblées, dont Stockage Azure, des espaces de travail Log Analytics et Azure Event Analytics, en utilisant le portail du service de domaine Azure AD.

Pour plus d’informations, voir [Activer les audits de sécurité pour Azure Active Directory Domain Services (préversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Utilisation et enseignements des nouvelles méthodes d’authentification (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Monitoring et création de rapports

Les nouveaux rapports sur l’utilisation et les enseignements des méthodes d’authentification peuvent vous aider à comprendre comment des fonctionnalités telles que Microsoft Azure Multi-Factor Authentication et la réinitialisation du mot de passe en libre-service sont inscrites et utilisées dans votre organisation, en indiquant notamment le nombre d’utilisateurs inscrits pour chaque fonctionnalité, la fréquence de réinitialisation du mot de passe en libre-service et la méthode utilisée pour la réinitialisation.

Pour plus d’informations, voir [Utilisation et enseignements des méthodes d’authentification (préversion)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nouveaux rapports de sécurité disponibles pour tous les administrateurs Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Tous les administrateurs Azure AD peuvent désormais sélectionner la bannière en haut des rapports de sécurité existants, tels que le rapport **Utilisateurs avec indicateur de risque**, pour commencer à utiliser la nouvelle expérience de sécurité, comme indiqué dans les rapports **Utilisateurs à risque** et **Connexions à risque**. Au fil du temps, tous les rapports de sécurité seront migrés des anciennes versions vers les nouvelles versions, les nouveaux rapports offrant les fonctionnalités supplémentaires suivantes :

- Filtrage et tri avancés

- Actions en bloc, telles que le rejet de risque utilisateur

- Confirmation d’entités compromises ou sûres

- État du risque, à savoir : À risque, Ignoré, Corrigé ou Compromission confirmée.

Pour plus d’informations, consultez [rapport Utilisateurs à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) et [rapport Connexions à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nouveaux audits de sécurité pour Azure AD Domain Services (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes heureux d’annoncer le mise en production de l’audit de sécurité du service de domaine Azure AD en préversion publique. L’audit de sécurité vous aide à recueillir des informations essentielles sur vos services d’authentification en diffusant en continu les événements d’audit de sécurité vers des ressources ciblées, dont Stockage Azure, des espaces de travail Log Analytics et Azure Event Analytics, en utilisant le portail du service de domaine Azure AD.

Pour plus d’informations, voir [Activer les audits de sécurité pour Azure Active Directory Domain Services (préversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nouvelle fédération directe B2B utilisant SAML/WS-FED (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Une fédération directe vous aide à travailler avec des partenaires dont la solution d’identité gérée par l’informatique n’est pas Azure AD, en utilisant des systèmes d’identité qui prennent en charge les normes SAML ou WS-FED. Après que vous avez défini une relation de fédération directe avec un partenaire, tout nouvel utilisateur de ce domaine que vous invitez peut collaborer avec vous à l’aide de son compte organisationnel existant, ce qui contribue à rendre l’expérience utilisateur de vos invités plus transparente.

Pour plus d’informations, voir [Fédération directe avec AD FS et des fournisseurs tiers pour les utilisateurs invités (préversion)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nouvelle vérification des noms de groupes en double dans le portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

À présent, lorsque vous créez ou mettez à jour un nom de groupe à partir du portail Azure AD, nous vérifions que vous ne dupliquez pas un nom de groupe existant dans votre ressource. Si nous déterminons que le nom est déjà utilisé pour un autre groupe, vous êtes invité à modifier votre nom.

Pour plus d’informations, voir [Gérer des groupes dans le portail Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD prend désormais en charge les paramètres de requête statique dans les URI de réponse (redirection)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les applications Azure AD peuvent désormais inscrire et utiliser des URI de réponse (redirection) avec des paramètres de requête statique (par exemple, `https://contoso.com/oauth2?idp=microsoft`) pour les demandes OAuth 2.0. Le paramètre de requête statique est soumis à la correspondance des chaînes pour les URI de réponse, comme n’importe quelle autre partie de l’URI de réponse. Si aucune chaîne inscrite ne correspond à l’URI de redirection décodée par URL, la demande est rejetée. Si l’URI de réponse est trouvé, la chaîne entière est utilisée pour rediriger l’utilisateur, y compris le paramètre de requête statique.

Les URI de réponse dynamique restent interdits parce qu’ils constituent un risque pour la sécurité, et ne peuvent pas être utilisé pour conserver des informations d’état dans une demande d’authentification. Pour cela, utilisez le paramètre `state`.

Actuellement, les écrans d’inscription d’application du portail Azure bloquent toujours les paramètres de requête. Toutefois, vous pouvez modifier manuellement le manifeste de l’application pour ajouter et tester des paramètres de requête de test dans votre application. Pour plus d’informations, voir [Quelles sont les nouveautés en matière d’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Journaux d’activité (API MS Graph) pour Azure AD désormais disponibles via des cmdlets PowerShell

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous sommes heureux d’annoncer que des journaux d’activité Azure AD (rapports d’audit et de connexion) sont désormais disponibles via le module Azure AD PowerShell. Auparavant, vous pouviez créer vos propres scripts à l’aide de points de terminaison de l’API MS Graph, et nous avons désormais étendu cette fonctionnalité aux cmdlets PowerShell.

Pour plus d’informations sur l’utilisation de ces cmdlets, voir [Cmdlets Azure AD PowerShell pour la création de rapports.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Contrôles de filtre mis à jour pour les journaux d’audit et de connexion dans Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons mis à jour les rapports de journal d’audit et de connexion afin que vous puissiez appliquer différents filtres sans devoir les ajouter en tant que colonnes sur les écrans de rapport. En outre, vous pouvez maintenant décider du nombre de filtres que vous souhaitez afficher à l’écran. Ces mises à jour interagissent pour faciliter la lecture des rapports et adapter leur étendue à vos besoins.

Pour plus d’informations sur ces mises à jour, voir [Filtrer les journaux d’audit](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) et [Filtrer les activités de connexion](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juin 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nouvelle API riskDetections pour Microsoft Graph (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes heureux d’annoncer que la nouvelle API riskDetections pour Microsoft Graph est désormais disponible en préversion publique. Vous pouvez utiliser cette nouvelle API pour afficher une liste des détections de risque au niveau des utilisateurs et des connexions dans le cadre du dispositif de protection des identités de votre organisation. Vous pouvez également utiliser cette API pour interroger plus efficacement vos détections de risque, avec notamment des détails sur le type de détection, son état, son niveau et bien plus encore.

Pour plus d’informations, voir [Documentation de référence sur l’API de détection des risques](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Juin 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En juin 2019, nous avons ajouté à notre galerie d’applications les 22 applications suivantes qui prennent en charge la fédération :

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Afficher la progression en temps réel du service d’approvisionnement Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Nous avons mis à jour le mécanisme d’approvisionnement d’Azure AD et y avons inclus une nouvelle barre de progression indiquant l’avancement du processus d’approvisionnement des utilisateurs. Cette mise à jour fournit désormais également des informations sur le nombre d’utilisateurs approvisionnés au cours du cycle actuel et à ce jour.

Pour plus d’informations, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>La marque de société s’affiche désormais sur les écrans de déconnexion et d’erreur

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Nous avons mis à jour Azure AD afin que la marque de votre société s’affiche sur les écrans de déconnexion et d’erreur, ainsi que sur la page de connexion. Vous n’avez rien à faire pour activer cette fonctionnalité. Azure AD utilise simplement les ressources que vous avez déjà configurées dans la section **Marque de société** du Portail Azure.

Pour plus d’informations sur la configuration de la marque de votre société, consultez [Personnaliser les pages Azure Active Directory de votre organisation](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Le serveur d’authentification multifacteur (MFA) Azure n’est plus disponible pour les nouveaux déploiements

**Type :** Déconseillé  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

À compter du 1er juillet 2019, Microsoft ne proposera plus le serveur MFA pour les nouveaux déploiements. Les nouveaux clients qui veulent une authentification multifacteur dans leur organisation doivent utiliser la fonction Microsoft Azure Multi-Factor Authentication basée sur le cloud. Les clients ayant activé le serveur MFA avant le 1er juillet ne verront aucune modification. Vous serez toujours en mesure de télécharger la version la plus récente, d’obtenir les mises à jour ultérieures et de générer des informations d’identification d’activation.

Pour plus d’informations, consultez [Prise en main du serveur Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Pour plus d’informations sur la fonction Azure Multi-Factor Authentication basée sur le cloud, consultez [Planification d’un déploiement Azure multi-Factor Authentication basé sur le cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Mai 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modification du service : Future prise en charge uniquement pour les protocoles TLS 1.2 sur le service Proxy d’application

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Pour fournir un chiffrement de qualité à nos clients, nous autorisons l’accès uniquement aux protocoles TLS 1.2 sur le service Proxy d’application. Cette modification est progressivement déployée chez les clients utilisant déjà exclusivement des protocoles TLS 1.2, vous ne devriez donc voir aucun changement.

L’abandon des protocoles TLS 1.0 et TLS 1.1 se produira sur le 31 août 2019, mais nous vous avertirons de nouveau afin que vous ayez le temps de vous y préparer. Pour vous préparer à cette modification, vérifiez que vos combinaisons client-serveur et navigateur-serveur, y compris les clients dont vos utilisateurs e servent pour accéder aux applications publiées via le Proxy d’application, sont mises à jour pour utiliser le protocole TLS 1.2 pour maintenir la connexion au service Proxy d’application. Pour plus d’informations, consultez [Ajouter une application locale pour un accès à distance via le service Proxy d’application d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utiliser le rapport d’utilisation et d’insights pour afficher les données de connexion liées aux applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez maintenant utiliser le rapport d’utilisation et d’insights, situé dans la section **Applications d’entreprise** du Portail Azure, pour obtenir une vue orientée application de vos données de connexion, notamment des informations sur :

- Les applications principalement utilisées dans votre organisation

- Les applications présentant le plus grand nombre d’échecs de connexion

- Les erreurs de connexion principales pour chaque application

Pour plus d’informations sur cette fonctionnalité, consultez [Rapport d’utilisation et d’insights dans le Portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report).

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiser l’approvisionnement de vos utilisateurs dans les applications cloud avec Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Suivez ces nouveaux didacticiels pour utiliser le service d’approvisionnement Azure AD pour automatiser la création, la suppression et la mise à jour des comptes utilisateur pour les applications cloud suivantes :

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Vous pouvez également suivre ce nouveau [didacticiel Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), qui fournit des informations sur l’approvisionnement d’objets de groupe.

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identity Secure Score est désormais disponible dans Azure AD (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pouvez désormais surveiller et améliorer votre stratégie de sécurité relative aux identités avec la fonctionnalité Identity Secure Score d’Azure AD. Cette fonctionnalité utilise un tableau de bord unique qui offre les avantages suivants :

- Évaluer votre méthode de sécurité relative aux identités de façon objective, avec un score compris entre 1 et 223

- Planifier les améliorations à apporter à la sécurité des identités

- Évaluer la réussite de vos améliorations en matière de sécurité

Pour plus d’informations sur la fonctionnalité Identity Security Score, consultez [Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nouvelle expérience d’inscription des applications désormais disponible (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Expérience de développement

La nouvelle expérience de la fonction [Inscriptions d’applications](https://aka.ms/appregistrations) est désormais en disponibilité générale. Cette nouvelle expérience inclut toutes les fonctionnalités clés du Portail Azure et du portail d’inscription des applications que vous connaissez déjà, et les améliore :

- **Meilleure gestion des applications.** Au lieu de voir vos applications sur des portails différents, vous pouvez maintenant voir toutes vos applications au même emplacement.

- **Inscription simplifiée des applications.** Avec une meilleure expérience de navigation et une sélection des autorisations repensée, il est désormais plus facile d’inscrire et de gérer vos applications.

- **Informations plus détaillées.** Vous trouverez des informations encore plus détaillées sur votre application, notamment des guides de démarrage rapide et bien plus encore.

Pour plus d’informations, consultez [Plateforme d’identités Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) et le billet de blog annonçant qu’une [nouvelle expérience d’inscription des applications est désormais disponible](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/). (annonce de blog).

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nouvelles fonctionnalités disponibles dans l’API Risky Users pour la protection des identités

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes heureux d’annoncer que vous pouvez maintenant utiliser l’API Risky Users pour récupérer l’historique des risques liés aux utilisateurs, ignorer les utilisateurs à risque et confirmer l’état de compromission des utilisateurs. Cette modification vous permet de mettre à jour plus efficacement l’état des risques liés à vos utilisateurs et de comprendre l’historique des risques associé.

Pour plus d’informations, consultez la [documentation de référence sur l’API Risky Users](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En mai 2019, nous avons ajouté à notre galerie d’applications les 21 applications suivantes qui prennent en charge la fédération :

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Expériences de création et de gestion des groupes améliorée dans le Portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous avons apporté des améliorations concernant la gestion des groupes dans le Portail Azure AD. Ces améliorations permettent aux administrateurs de mieux gérer les listes de groupes, les listes de membres et de fournir des options de création supplémentaires.

Les améliorations incluent :

- Filtrage de base par type d’appartenance et par type de groupe.

- Ajout de nouvelles colonnes, telles que de la source et l’adresse de messagerie.

- Possibilité de sélectionner plusieurs listes de groupes, membres et propriétaires pour faciliter la suppression.

- Possibilité de choisir une adresse de messagerie et d’ajouter des propriétaires lors de la création de groupe.

Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurer une stratégie d’affectation de noms pour les groupes Office 365 dans le Portail Azure AD (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Les administrateurs peuvent désormais configurer une stratégie d’affectation de noms pour les groupes Office 365, depuis le Portail Azure AD. Cette modification permet d’appliquer des conventions d’affectation de noms cohérentes aux groupes Office 365 créés ou modifiés par les utilisateurs de votre organisation.

Vous pouvez appliquer une stratégie de nommage aux groupes Office 365 de deux manières différentes :

- Définir des préfixes ou des suffixes, qui sont automatiquement ajoutés à un nom de groupe.

- Charger un jeu personnalisé de mots bloqués pour votre organisation, qui ne sont pas autorisés dans les noms de groupe (par exemple « PDG, paie, RH »).

Pour plus d’informations, consultez [Appliquer une stratégie de nommage pour les groupes Office 365 dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Les points de terminaison d’API Microsoft Graph sont désormais disponibles pour les journaux d’activité Azure AD (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous sommes heureux d’annoncer la disponibilité générale de la prise en charge des points de terminaison d’API Microsoft Graph pour les journaux d’activité Azure AD. Avec cette version, vous pouvez maintenant utiliser la version 1.0 des API des journaux d’audit et des journaux de connexion Azure AD.

Pour plus d’informations, consultez la [vue d’ensemble de l’API des journaux d’audit Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Les administrateurs peuvent désormais utiliser l’accès conditionnel pour le processus d’inscription combinée (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités  

Les administrateurs peuvent désormais créer des stratégies d’accès conditionnel pour une utilisation dans la page d’inscription combinée. Cela inclut l’application de stratégies pour autoriser l’inscription si :

- Les utilisateurs se trouvent sur un réseau approuvé.

- Les utilisateurs ont un niveau faible de risque à la connexion.

- Les utilisateurs utilisent un appareil géré.

- Les utilisateurs acceptent les conditions d’utilisation de l’organisation.

Pour plus d’informations sur l’accès conditionnel et la réinitialisation de mot de passe, consultez le billet de blog portant sur [l’accès conditionnel pour l’expérience d’inscription combinée Azure AD avec authentification multifacteur et réinitialisation du mot de passe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Pour plus d’informations sur les stratégies d’accès conditionnel pour le processus d’inscription combinée, consultez [Stratégies d’accès conditionnel pour l’inscription combinée](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Pour plus d’informations sur les conditions d’utilisation d’Azure AD, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
