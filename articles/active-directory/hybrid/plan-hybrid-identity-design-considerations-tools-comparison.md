---
title: 'Identité hybride : Comparaison des outils d’intégration d’annuaire | Microsoft Docs'
description: Cette page contient un tableau comparatif complet des différents outils d’intégration de répertoire pouvant être utilisés pour l’intégration de répertoire.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456915"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Identité hybride : Comparaison des outils d’intégration d’annuaire
Au fil des années, les outils d’intégration de répertoire ont évolué et se sont améliorés.  Ce document procure une vue d’ensemble consolidée de ces outils et compare les fonctions disponibles dans chacun d’entre eux.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect comprend les composants et les fonctionnalités précédemment publiés sous le nom de synchronisation d’annuaires et AAD Sync. Ces outils ne sont plus commercialisés individuellement et toutes les améliorations à venir figureront dans les mises à jour d’Azure AD Connect, afin que vous sachiez toujours où obtenir les dernières fonctionnalités.
> 
> DirSync et Azure AD Sync sont déconseillés. Des informations supplémentaires sont disponibles [ici](reference-connect-dirsync-deprecated.md).
> 
> 

Le code suivant s’applique aux tableaux ci-dessous.

● = Disponible maintenant  
FR = Version à venir  
PP = Version préliminaire publique  

## <a name="on-premises-to-cloud-synchronization"></a>Synchronisation des ressources locales avec le cloud
| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) - N’EST PLUS PRIS EN CHARGE | Services de synchronisation Azure Active Directory (DirSync) - N’EST PLUS PRIS EN CHARGE | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Connexion à une forêt AD locale |● |● |● |● |● |
| Connexion à plusieurs forêts AD locales |● |● | |● |● |
| Connexion à plusieurs organisations Exchange locales |● | | | | |
| Connexion à un répertoire LDAP local |●* | | |● |● | 
| Connexion à plusieurs répertoires LDAP |●*  | | |● |● | 
| Connexion à des répertoires locaux AD et LDAP |●* | | |● |● | 
| Connexion à des systèmes personnalisés (comme SQL, Oracle, MySQL, etc.) |FR | | |● |● |
| Synchronisation des attributs définis par le client (extensions de répertoire) |● | | | | |
| Connexion à un système de ressources humaines local (par exemple, SAP, Oracle eBusiness, PeopleSoft) |FR | | |● |● |
| Prise en charge des connecteurs et des règles de synchronisation FIM pour le déploiement sur des systèmes locaux. | | | |● |● |

 
&#42; Actuellement, deux options sont prises en charge pour cela.  Il s'agit de : 

   1. Vous pouvez utiliser le connecteur LDAP générique et l’activer en dehors d’Azure AD Connect.  C’est une opération complexe demandant un partenaire pour l’intégration et un contrat de support Premier pour le tenir à jour.  Cette option peut gérer les répertoires LDAP uniques et multiples. 

   2. Vous pouvez développer votre propre solution pour déplacer des objets depuis LDAP vers Active Directory.  Synchronisez ensuite les objets avec Azure AD Connect.  MIM ou FIM peut être utilisé comme solution pour déplacer les objets. 

## <a name="cloud-to-on-premises-synchronization"></a>Synchronisation du cloud avec les ressources locales
| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) - N’EST PLUS PRIS EN CHARGE  | Services de synchronisation Azure Active Directory (DirSync) - N’EST PLUS PRIS EN CHARGE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Écriture différée des appareils |● | |● | | |
| Écriture différée des attributs (pour un déploiement hybride Exchange) |● |● |● |● |● |
| Écriture différée des objets groupes |● | | | | |
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) |● |● | | | |

## <a name="authentication-feature-support"></a>Prise en charge des fonctionnalités d’authentification
| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) - N’EST PLUS PRIS EN CHARGE  | Services de synchronisation Azure Active Directory (DirSync) - N’EST PLUS PRIS EN CHARGE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Synchronisation du hachage de mot de passe pour une forêt AD locale |●|●|● | | |
| Synchronisation du hachage de mot de passe pour plusieurs forêts AD locales |●|● | | | |
| Authentification directe pour forêt AD locale |●| | | | |
| Authentification unique à l’aide de la fédération |● |● |● |● |● |
| Authentification unique transparente|● |||||
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) |● |● | | | |

## <a name="set-up-and-installation"></a>Configuration et installation
| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) - N’EST PLUS PRIS EN CHARGE  | Services de synchronisation Azure Active Directory (DirSync) - N’EST PLUS PRIS EN CHARGE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Installation des supports sur un contrôleur de domaine |● |● |● | |
| Installation des supports à l’aide de SQL Express |● |● |● | |
| Mise à niveau facile à partir de DirSync |● | | | |
| Localisation de l’expérience utilisateur admin en langues Windows Server |● |● |● | |
| Localisation de l’expérience utilisateur final en langues Windows Server | | | |● |
| Prise en charge pour Windows Server 2008 et Windows Server 2008 R2 |● pour la synchronisation, pas pour la fédération |● |● |● |
| Prise en charge pour Windows Server 2012 et Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrage et configuration
| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) - N’EST PLUS PRIS EN CHARGE  | Services de synchronisation Azure Active Directory (DirSync) - N’EST PLUS PRIS EN CHARGE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrage sur les domaines et les unités organisationnelles |● |● |● |● |● |
| Filtrage sur les valeurs d’attributs des objets |● |● |● |● |● |
| Autorisation d’un ensemble minimal d’attributs à synchroniser (MinSync) |● |● | | | |
| Autorisation de différents modèles de service à appliquer pour les flux d’attributs |● |● | | | |
| Autorisation du blocage des attributs circulant d’AD vers Azure AD |● |● | | | |
| Autorisation de la personnalisation avancée pour les flux d’attributs |● |● | |● |● |

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).

