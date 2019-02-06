---
title: Contenu du rôle d’administrateur de charges de travail Microsoft 365 - Azure AD | Microsoft Docs
description: Rechercher du contenu et des références d’API pour des rôles d’administrateur de charges de travail Microsoft 365 dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9ef400a5c7b42f6782fefa28e2351b09d8667861
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470244"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Rôles d’administrateur de charges de travail Microsoft 365

Tous les produits de Microsoft 365 peuvent être gérés avec des rôles d’administration dans Azure AD. Certains produits fournissent également des rôles supplémentaires qui sont spécifiques de ce produit. Pour plus d’informations sur les rôles pris en charge par chaque produit, consultez le tableau ci-dessous. Vous trouverez des discussions générales de problèmes de délégation dans [Planification de la délégation de rôle dans Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Où trouver du contenu

Charge de travail Microsoft 365 | Contenu de rôle | Contenu d’API
---------------------- | ------------------ | -----------------
Rôles d’administrateur dans les plans commerciaux Office 365 et Microsoft 365 | [Rôles d’administration Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Non disponible
Azure Active Directory (Azure AD) et Azure AD Identity Protection| [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Contrôle d’accès en fonction du rôle Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell pour Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md)<br>Également [À propos du rôle d’administrateur SharePoint dans Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype Entreprise | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centre de sécurité et de conformité (Protection avancée contre les menaces Office 365, Exchange Online Protection, Information Protection) | [Rôles d’administration Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Degré de sécurisation | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Rôles Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Non disponible
Azure Information Protection | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Contrôle d’accès en fonction du rôle](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Informations de référence sur l'API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure - Protection avancée contre les menaces | [Groupes de rôles Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Non disponible
Protection avancée contre les menaces Windows Defender | [Contrôle d’accès en fonction du rôle Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Non disponible
Privileged Identity Management | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Contrôle d’accès en fonction du rôle Intune](https://docs.microsoft.com/intune/role-based-access-control) | [API Graph](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Ordinateur de bureau managé | [Rôles d’administrateur Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Récupérer des attributions de rôles](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour attribuer ou supprimer des rôles d’administrateur Azure AD](directory-manage-roles-portal.md)
* [Informations de référence sur les rôles d’administrateur Azure AD](directory-assign-admin-roles.md)
