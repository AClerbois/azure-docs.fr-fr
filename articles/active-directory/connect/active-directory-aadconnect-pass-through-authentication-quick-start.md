---
title: "Authentification directe Azure AD : Démarrage rapide | Microsoft Docs"
description: "Cet article explique comment commencer à utiliser l’authentification directe d’Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 4f4fa884694dc8dad6349e3835e7c7ba2c4d2bdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Authentification directe Azure Active Directory : Démarrage rapide

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Comment déployer l’authentification directe Azure AD ?

L’authentification directe Azure Active Directory (Azure AD) permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Elle connecte les utilisateurs en validant leurs mots de passe directement par rapport à votre annuaire Active Directory local.

>[!IMPORTANT]
>Si vous utilisiez jusqu’à maintenant cette fonctionnalité dans la préversion, vous devez mettre à niveau la préversion des agents d’authentification en suivant [ces instructions](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Procédez comme suit pour déployer l’authentification directe :

## <a name="step-1-check-prerequisites"></a>Étape 1 : Vérifier les prérequis

Vérifiez que les prérequis suivants sont remplis :

### <a name="on-the-azure-active-directory-admin-center"></a>Dans le Centre d’administration Azure Active Directory

1. Créez un compte d’administrateur général « cloud uniquement » dans votre locataire Azure AD. De cette façon, vous pouvez gérer la configuration de votre locataire si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle si vous voulez éviter de vous retrouver en dehors de votre locataire.
2. Ajoutez un ou plusieurs [noms de domaine personnalisés](../active-directory-add-domain.md) à votre locataire Azure AD. Vos utilisateurs se connectent à l’aide de l’un de ces noms de domaine.

### <a name="in-your-on-premises-environment"></a>Dans votre environnement local

1. Identifiez un serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter Azure AD Connect. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont les mots de passe doivent être validés.
2. Installez la [version la plus récente d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) sur le serveur identifié à l’étape précédente. Si Azure AD Connect est déjà en cours d’exécution, vérifiez que la version est 1.1.557.0 ou ultérieure.
3. Identifiez un autre serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter l’agent d’authentification autonome. La version de l’agent d’authentification doit être 1.5.193.0 ou ultérieure. Ce serveur est nécessaire pour garantir une haute disponibilité des demandes de connexion. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont les mots de passe doivent être validés.
4. S’il existe un pare-feu entre vos serveurs et Azure AD, vous devez configurer les éléments suivants :
   - Assurez-vous que les agents d’authentification peuvent effectuer des requêtes **sortantes** vers Azure AD sur les ports suivants :
   
   | Numéro de port | Utilisation |
   | --- | --- |
   | **80** | Téléchargement de la liste de révocation de certificats lors de la validation du certificat SSL |
   | **443** | Toutes les communications sortantes avec notre service |
   
   Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
   - Si votre pare-feu ou votre proxy autorise la mise en liste verte de DNS, vous pouvez y placer les connexions vers **\*.msappproxy.net** et **\*.servicebus.windows.net**. Dans le cas contraire, autorisez l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.
   - Vos agents d’authentification doivent accéder à **login.windows.net** et à **login.microsoftonline.net** pour l’inscription initiale. Par conséquent, ouvrez votre pare-feu pour ces URL.
   - Pour valider le certificat, débloquez les URL suivantes : **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** et **www.microsoft.com:80**. Ces URL sont utilisées pour la validation de certificat avec d’autres produits Microsoft : elles seront peut-être déjà débloquées.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Étape 2 : Activer la prise en charge d’Exchange ActiveSync (facultatif)

Suivez ces instructions pour activer la prise en charge d’Exchange ActiveSync :

1. Utilisez [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) pour exécuter la commande suivante :
```
Get-OrganizationConfig | fl per*
```

2. Vérifiez la valeur du paramètre `PerTenantSwitchToESTSEnabled`. Si la valeur est **true**, votre locataire est correctement configuré, ce qui est généralement le cas pour la plupart des clients. Si la valeur est **false**, exécutez la commande suivante :
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Vérifiez que la valeur du paramètre `PerTenantSwitchToESTSEnabled` est désormais définie sur **true**. Patientez une heure avant de passer à l’étape suivante.

Si vous rencontrez des problèmes au cours de cette étape, consultez notre [guide de résolution des problèmes](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) pour plus d’informations.

## <a name="step-3-enable-the-feature"></a>Étape 3 : Activer la fonctionnalité

Vous pouvez activer l’authentification directe par l’intermédiaire d’[Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Vous pouvez activer l’authentification directe sur le serveur principal ou sur un serveur intermédiaire Azure AD Connect. Nous vous recommandons vivement de l’activer à partir du serveur principal.

Si vous installez Azure AD Connect pour la première fois, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Dans la page **Connexion utilisateur**, choisissez **Authentification directe** comme méthode d’authentification. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect. La fonctionnalité d’authentification directe est également activée sur votre locataire.

![Azure AD Connect - Connexion de l’utilisateur](./media/active-directory-aadconnect-sso/sso3.png)

Si vous avez déjà installé Azure AD Connect (à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md)), sélectionnez **Modifier la connexion utilisateur** sur Azure AD Connect, puis cliquez sur **Suivant**. Ensuite, sélectionnez **Authentification directe** comme mode d’authentification. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect et la fonctionnalité est activée sur votre locataire.

![Azure AD Connect - Changer la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L’authentification directe est une fonctionnalité au niveau du locataire. Son activation a un impact sur la connexion des utilisateurs dans _tous_ les domaines gérés dans votre locataire. Si vous passez d’AD FS à l’authentification directe, nous vous recommandons d’attendre au moins 12 heures avant d’arrêter votre infrastructure AD FS. Ce délai d’attente garantit que les utilisateurs peuvent conserver leur connexion à Exchange ActiveSync lors de la transition.

## <a name="step-4-test-the-feature"></a>Étape 4 : Tester la fonctionnalité

Suivez ces instructions pour vérifier que vous avez activé correctement l’authentification directe :

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) à l’aide des informations d’identification d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche.
3. Sélectionnez ensuite **Azure AD Connect**.
4. Vérifiez que la fonctionnalité **Authentification directe** est **activée**.
5. Sélectionnez **Authentification directe**. Ce panneau répertorie les serveurs sur lesquels vos agents d’authentification sont installés.

![Centre d’administration Azure Active Directory - panneau Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centre d’administration Azure Active Directory - panneau Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

À ce stade, les utilisateurs de tous les domaines gérés de votre locataire peuvent se connecter à l’aide de l’authentification directe. Toutefois, les utilisateurs des domaines fédérés continuent à se connecter à l’aide des services de fédération Active Directory (ADFS) ou d’un autre fournisseur de fédération précédemment configuré. Si vous convertissez un domaine fédéré en domaine géré, tous les utilisateurs de ce domaine se connectent alors automatiquement à l’aide de l’authentification directe. La fonctionnalité d’authentification directe n’a pas d’incidence sur les utilisateurs cloud uniquement.

## <a name="step-5-ensure-high-availability"></a>Étape 5 : Garantir une haute disponibilité

Si vous envisagez de déployer l’authentification directe dans un environnement de production, vous devez installer un agent d’authentification autonome. Installez ce deuxième agent d’authentification sur un serveur _autre_ qu’un serveur exécutant Azure AD Connect et le premier agent d’authentification. Cette installation procure une haute disponibilité des demandes de connexion. Pour déployer un agent d’authentification autonome, effectuez les étapes suivantes :

1. **Téléchargez la dernière version de l’agent d’authentification (version 1.5.193.0 ou ultérieure)** : connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) avec les droits d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche.
3. Sélectionnez **Azure AD Connect**, puis **Authentification directe**. Sélectionnez **Télécharger l’agent**.
4. Cliquez sur le bouton **Accepter les conditions et télécharger**.
5. **Installez la dernière version de l’agent d’authentification** : exécutez le fichier exécutable téléchargé à l’étape précédente. Fournissez les informations d’identification de l’administrateur général de votre locataire lorsque vous y êtes invité.

![Centre d’administration Azure Active Directory - Bouton Télécharger Agent d’authentification](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centre d’administration Azure Active Directory - Panneau Télécharger l’agent](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Vous pouvez également télécharger l’agent d’authentification [ici](https://aka.ms/getauthagent). Veillez à consulter et à accepter les [conditions de service](https://aka.ms/authagenteula) de l’agent d’authentification _avant_ de l’installer.

## <a name="next-steps"></a>Étapes suivantes
- [**Verrouillage intelligent**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : configurez la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Immersion technique**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Forum aux questions**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Immersion dans la sécurité**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : informations techniques supplémentaires sur la fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour formuler des demandes de nouvelles fonctionnalités.
