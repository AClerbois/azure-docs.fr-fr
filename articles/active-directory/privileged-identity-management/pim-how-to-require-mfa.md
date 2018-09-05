---
title: Exiger une authentification multifacteur pour les rôles d’annuaire Azure AD dans PIM | Microsoft Docs
description: Découvrez comment exiger l’authentification multifacteur pour les rôles d’annuaire Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9777a7d2ed07ea2edf4bea661a5065c808de9f2b
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188351"
---
# <a name="require-multi-factor-authentication-for-azure-ad-directory-roles-in-pim"></a>Exiger une authentification multifacteur pour les rôles d’annuaire Azure AD dans PIM
Il est vivement recommandé d’exiger l’application de la solution Multi-Factor Authentication (MFA) pour tous vos administrateurs. Cela réduit le risque d'attaque en raison d'un mot de passe compromis.

Vous pouvez exiger que les utilisateurs se soumettent à une authentification MFA lors de leur connexion. Le billet de blog [MFA pour Office 365 et MFA pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compare ce qui est inclus dans Office et les abonnements Azure avec les fonctionnalités contenues dans l'offre Microsoft Azure Multi-Factor Authentication.

Vous pouvez également exiger que les utilisateurs effectuent une demande d'authentification MFA lorsqu’ils activent un rôle dans Azure AD PIM. De cette façon, si l'utilisateur n'a pas terminé une demande d'authentification MFA lors de sa connexion, il sera invité à le faire par PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigence d’application de la solution MFA dans Azure AD Privileged Identity Management
Quand vous gérez des identités dans PIM en tant qu’administrateur de rôle privilégié, vous pouvez voir des alertes qui recommandent l’authentification MFA pour des comptes privilégiés. Cliquez sur l’alerte de sécurité dans le tableau de bord PIM. Un nouveau panneau s’ouvre, affichant la liste des comptes administrateur qui doivent exiger l’authentification MFA.  Vous pouvez exiger l’authentification MFA en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Corriger**. Vous pouvez également cliquer sur les points de suspension en regard d’un rôle, puis sur le bouton **Corriger**.

> [!IMPORTANT]
> L’authentification multifacteur Azure ne fonctionne actuellement qu’avec des comptes professionnels ou scolaires et pas avec des comptes Microsoft (généralement un compte personnel utilisé pour se connecter à des services Microsoft tels que Skype, Xbox, Outlook.com, etc.). Pour cette raison, toute personne disposant d’un compte Microsoft ne peut pas être un administrateur éligible car elle ne peut pas utiliser l’authentification multifacteur pour activer leur rôle. Si ces utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, convertissez-les en administrateurs permanents pour l’instant.
> 
> 

En outre, vous pouvez modifier l'exigence d’authentification MFA pour un rôle spécifique en cliquant dessus dans la section Rôles du tableau de bord PIM. Cliquez ensuite sur **Paramètres** dans le panneau de rôle, puis sélectionnez **Activer** sous l’authentification multifacteur.

## <a name="how-azure-ad-pim-validates-mfa"></a>Validation de Multi-Factor Authentication (MFA) par Azure AD PIM
Il existe deux options pour valider l'authentification multifacteur lorsqu’un utilisateur active un rôle.

Le plus simple consiste à s’appuyer sur Azure MFA pour les utilisateurs qui activent un rôle privilégié. Pour ce faire, vérifiez tout d’abord que ces utilisateurs bénéficient d’une licence si nécessaire, et qu’il se sont enregistrés pour l’authentification Azure MFA. Pour plus d’informations, consultez [Prise en main d’Azure Multi-Factor Authentication dans le cloud](../authentication/howto-mfa-getstarted.md). Il est recommandé, mais pas obligatoire, de configurer Azure AD pour appliquer l’authentification MFA à ces utilisateurs lorsqu’ils se connectent. En effet, les vérifications de l’authentification multifacteur sont effectuées par Azure AD PIM directement.

Si les utilisateurs s’authentifient en local, vous pouvez également faire en sorte que votre fournisseur d’identité soit responsable de l’authentification MFA. Par exemple, si vous avez configuré des services ADFS pour exiger l’authentification par carte à puce avant d’accéder à Azure AD, la section [Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et ADFS](../authentication/howto-mfa-adfs.md) inclut des instructions pour configurer ADFS afin d’envoyer les revendications à Azure AD. Lorsqu’un utilisateur tente d’activer un rôle, Azure AD PIM accepte cette authentification MFA déjà validée pour l’utilisateur dès réception des revendications appropriées.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](pim-how-to-change-default-settings.md)
- [Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM](pim-how-to-configure-security-alerts.md)
