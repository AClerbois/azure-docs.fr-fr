---
title: Domaines personnalisés dans le proxy d’application Azure AD | Microsoft Docs
description: Configurer et gérer des domaines personnalisés dans le Proxy d’application Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 189b8666adde0eedcb451655657a4a82dc5e4fec
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062520"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurer des domaines personnalisés dans avec le Proxy d’application Azure AD

Lorsque vous publiez une application via le Proxy d’application Azure Active Directory, vous créez une URL externe pour vos utilisateurs. Cette URL obtient le domaine par défaut *yourtenant.msappproxy.net*. Par exemple, si vous publiez une application nommée *Expenses* dans votre locataire nommé *Contoso*, l’URL externe est *https:\//dépenses-contoso.msappproxy.net*. Si vous souhaitez utiliser votre propre nom de domaine au lieu de *msappproxy.net*, configurez un domaine personnalisé pour votre application. 

## <a name="benefits-of-custom-domains"></a>Avantages des domaines personnalisés

Il est judicieux de configurer des domaines personnalisés pour vos applications chaque fois que c’est possible. Voici quelques raisons d’utiliser des domaines personnalisés :

- Les liens entre applications fonctionnent même en dehors du réseau d’entreprise. Sans domaine personnalisé, si votre application comprend des liens internes codés en dur vers des cibles situées en dehors du Proxy d’application, et que les liens ne peuvent pas être résolus à l’extérieur, ils sont rompus. Lorsque vos URL internes et externes sont identiques, vous évitez ce problème. Si vous n’êtes pas en mesure d’utiliser des domaines personnalisés, pour d’autres façons de résoudre ce problème, voir [Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD](../application-proxy-link-translation.md). 
  
- Vos utilisateurs bénéficient alors d’une expérience plus simple, car ils peuvent accéder à l’application via la même URL tant à l’intérieur qu’à l’extérieur de votre réseau. Ils n’ont pas besoin de retenir différentes URL internes et externes, ou d’effectuer le suivi de leur emplacement actuel. 

- Vous contrôlez votre marque et créez les URL de votre choix. Un domaine personnalisé peut aider à renforcer la confiance de vos utilisateurs, car ils voient et utilisent un nom familier au lieu de *msappproxy.net*.

- Certaines configurations ne fonctionnent qu’avec des domaines personnalisés. Par exemple, vous avez besoin de domaines personnalisés pour les applications qui utilisent Security Assertion Markup Language (SAML), par exemple, lorsque vous utilisez des services de fédération Active Directory (AD FS) mais ne pouvez pas utiliser WS-Federation. Pour plus d’informations, voir [Utilisation d’applications prenant en charge les revendications dans le proxy d’application](application-proxy-configure-for-claims-aware-applications.md). 

Si vous n’êtes pas en mesure de faire correspondre les URL internes et externes, ce n’est pas très important d’utiliser des domaines personnalisés, mais vous pouvez toujours profiter des autres avantages. 

## <a name="dns-configuration-options"></a>Options de configuration DNS

Il existe plusieurs options de configuration DNS, en fonction des besoins :

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Même URL interne et externe, comportements interne et externe différents 

Si vous ne souhaitez pas que vos utilisateurs internes soient dirigés via le Proxy d’application, vous pouvez configurer un *DNS Split-Brain*. Une infrastructure DNS fractionnée dirige les hôtes internes vers un serveur de noms de domaine interne et les hôtes externes vers un serveur de noms de domaine externe, pour la résolution de noms. 

![DNS Split-Brain](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URL internes et externes différentes 

Si les URL internes et externes sont différentes, vous n’avez pas besoin de configurer le comportement de Split-Brain, car le routage des utilisateurs est déterminé par l’URL. Dans ce cas, vous modifiez uniquement le DNS externe et routez l’URL externe vers le point de terminaison proxy de l’application. 

Lorsque vous sélectionnez un domaine personnalisé pour une URL externe, une barre d’informations affiche l’entrée CNAME que vous devez ajouter au fournisseur DNS externe. Vous pouvez toujours voir ces informations en accédant à la page **Proxy d’application** de l’application.

## <a name="set-up-and-use-custom-domains"></a>Configurer et utiliser des domaines personnalisés

Pour configurer une application locale afin d’utiliser un domaine personnalisé, vous devez disposer d’un domaine personnalisé Azure Active Directory vérifié, d’un certificat PFX pour le domaine personnalisé et d’une application locale à configurer. 

### <a name="create-and-verify-a-custom-domain"></a>Créer et vérifier un domaine personnalisé

Pour créer et vérifier un domaine personnalisé :

1. Dans Azure Active Directory, dans le volet de navigation de gauche, sélectionnez **Noms de domaine personnalisés**, puis **Ajouter un domaine personnalisé**. 
1. Entrez votre nom de domaine personnalisé, puis sélectionnez **Ajouter un domaine**. 
1. Dans la page du domaine, copiez les informations de l’enregistrement TXT pour votre domaine. 
1. Accédez à votre bureau d’enregistrement de domaines et créez un enregistrement TXT pour votre domaine en fonction des informations DNS que vous avez copiées.
1. Une fois le domaine inscrit, sur la page du domaine dans Azure Active Directory, sélectionnez **Vérifier**. Une fois l’état du domaine **Vérifié**, vous pouvez utiliser le domaine pour toutes vos configurations Azure AD, y compris le Proxy d’application. 

Pour des instructions plus détaillées, voir [Ajouter votre nom de domaine personnalisé à l’aide du portail Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurer une application pour utiliser un domaine personnalisé

Pour publier votre application via le Proxy d’application avec un domaine personnalisé :

1. Pour une nouvelle application, dans Azure Active Directory, dans le volet de navigation de gauche, sélectionnez **Applications d’entreprise**. Sélectionnez **Nouvelle application**. Dans la section **Applications locales**, sélectionnez **Ajouter une application locale**. 
   
   Pour une application déjà présente dans **Applications d’entreprise**, sélectionnez-la dans la liste, puis, dans le volet de navigation de gauche, sélectionnez **Proxy d’application**. 

2. Dans la page des paramètres du Proxy d’application, spécifiez un **Nom** si vous ajoutez votre propre application locale.

3.  Dans le champ **URL interne**, entrez l’URL interne de votre application.
   
4. Dans le champ **URL externe**, déroulez la liste et sélectionnez le domaine personnalisé que vous souhaitez utiliser.
   
5. Sélectionnez **Ajouter**.
   
   ![Sélectionner un domaine personnalisé](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Si le domaine a déjà un certificat, le champ **Certificat** affiche les informations du certificat. Dans le cas contraire, sélectionnez le champ **Certificat**. 
   
   ![Cliquez pour télécharger un certificat.](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Dans la page **Certificat SSL**, recherchez et sélectionnez votre fichier de certificat PFX. Entrez le mot de passe du certificat, puis sélectionnez **Charger le certificat**. Pour plus d’informations sur les certificats, voir la section [Certificats pour les domaines personnalisés](#certificates-for-custom-domains).
   
   ![Télécharger le certificat](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Un domaine personnalisé ne nécessite qu’un seul chargement de son certificat. Après cela, le certificat chargé est appliqué automatiquement lorsque vous utilisez le domaine personnalisé pour d’autres applications.
   
8. Si vous avez ajouté un certificat, dans la page **Proxy d’application**, sélectionnez **Enregistrer**. 
   
9. Dans la barre d’informations de la page **Proxy d’application**, notez l’entrée CNAME que vous devez ajouter à votre zone DNS. 
   
   ![Ajouter une entrée DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Suivez les instructions de la section [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure](../../dns/dns-operations-recordsets-portal.md) pour ajouter un enregistrement DNS qui redirige la nouvelle URL externe vers le domaine *msappproxy.net*.
   
11. Pour vérifier que l’enregistrement DNS est correctement configuré, utilisez la commande [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) afin de confirmer que votre URL externe est accessible et que le domaine *msapproxy.net* apparaît en tant qu’alias.

Votre application est maintenant configurée pour utiliser le domaine personnalisé. Veillez à affecter des utilisateurs à votre application avant de la tester ou de la publier. 

Pour modifier le domaine d’une application, sélectionnez un autre domaine dans la liste déroulante **URL externe** de la page **Proxy d’application** de l’application. Chargez un certificat pour le domaine mis à jour si nécessaire, puis mettez à jour l’enregistrement DNS. Si vous ne voyez pas le domaine personnalisé souhaité dans la liste déroulante **URL externe**, il se peut qu’il ne soit pas vérifié.

Pour obtenir des instructions plus détaillées sur le Proxy d’application, voir [Didacticiel : Ajouter une application locale pour un accès à distance via le service Proxy d’application d’Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificats pour des domaines personnalisés

Un certificat crée la connexion SSL sécurisée pour votre domaine personnalisé. 

### <a name="certificate-formats"></a>Formats de certificat

Vous devez utiliser un certificat PFX pour vous assurer que tous les certificats intermédiaires requis sont inclus. Le certificat doit comporter la clé privée.

Aucune restriction ne s’applique aux méthodes de signature de certificat. Les certificats des type chiffrement à courbe elliptique (ECC) ou autre nom de l’objet (SAN), ainsi que d’autres types de certificat courants sont pris en charge. 

Vous pouvez utiliser un certificat avec caractères génériques tant que ceux-ci correspondent à l’URL externe. Vous devez utiliser des certificats avec caractères génériques pour des [applications génériques](application-proxy-wildcard.md). Si vous souhaitez utiliser le certificat pour accéder également aux sous-domaines, vous devez ajouter les caractères génériques du sous-domaine en tant qu’autres noms d’objet dans le même certificat. Par exemple, un certificat pour *\*.adventure-works.com* ne peut fonctionner pour *\*.apps.adventure-works.com* que si vous ajoutez *\*.apps.adventure-works.com* en tant qu’autre nom de l’objet. 

Vous pouvez utiliser des certificats émis par votre propre infrastructure à clé publique (PKI) si la chaîne d’approbation est installée sur vos appareils clients. Intune peut déployer ces certificats sur des appareils gérés. Pour des appareils non gérés, vous devez installer ces certificats manuellement.

Il n’est pas judicieux d’utiliser une autorité de certification racine privée. L’autorité de certification racine privée devrait également être envoyée aux machines clientes, ce qui poserait de nombreuses difficultés. 

### <a name="certificate-management"></a>Gestion des certificats

La gestion des certificats s’effectue par le biais les pages d’application individuelles. Accédez à la page **Proxy d’application** de l’application pour accéder au champ **Certificat**.

Vous pouvez utiliser le même certificat pour plusieurs applications. Si un certificat chargé fonctionne avec une autre application, il est appliqué automatiquement. Vous n’êtes pas invité à le charger à nouveau lorsque vous ajoutez ou configurez l’application. 

Quand un certificat expire, vous recevez un message d’avertissement vous demandant de charger un autre certificat. Si le certificat est révoqué, il se peut que vos utilisateurs reçoivent un avertissement de sécurité lors de l’accès à l’application. Pour mettre à jour le certificat d’une application, accédez à la page **Proxy d’application** de l’application, sélectionnez **Certificat**, puis chargez un nouveau certificat. Si l’ancien certificat n’est pas utilisé par d’autres applications, il est automatiquement supprimé. 

## <a name="next-steps"></a>Étapes suivantes
* [Activer l’authentification unique](application-proxy-configure-single-sign-on-with-kcd.md) pour vos applications publiées avec l’authentification Azure AD.
* [Activer l’accès conditionnel](../conditional-access/technical-reference.md#cloud-apps-assignments) à vos applications publiées.

