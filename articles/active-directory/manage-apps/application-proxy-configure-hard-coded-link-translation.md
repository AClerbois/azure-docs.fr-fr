---
title: Traduire les liens et Proxy d’application d’URL Azure AD | Documents Microsoft
description: Couvre les bases sur les connecteurs de proxy d’application Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 618df9b4bcc4a1b6f44d9cabc29c797a2cabcc80
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366790"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD

Le Proxy d’application Azure AD rend vos applications locales disponibles pour les utilisateurs distants ou sur leurs propres appareils. Toutefois, certaines applications, ont été développées avec des liens locaux incorporés dans le code HTML. Ces liens ne fonctionnent pas correctement lorsque l’application est utilisée à distance. Lorsque vous disposez de plusieurs applications locales en lien l’une avec l’autre, vos utilisateurs attendent que les liens continuent à fonctionner lorsqu’ils ne sont pas au bureau. 

La meilleure façon de vous assurer que des liens fonctionnent de la même manière à l’intérieur et à l’extérieur de votre réseau d’entreprise est de configurer les URL externes de vos applications comme les URL internes. Utilisez [des domaines personnalisés](application-proxy-configure-custom-domain.md) pour configurer vos URL externes et utiliser votre nom de domaine d’entreprise plutôt que le domaine de proxy d’application par défaut.


Si vous ne pouvez pas utiliser des domaines personnalisés dans votre locataire, il existe plusieurs autres options pour fournir cette fonctionnalité. Toutes ces options sont également compatibles les unes avec les autres et avec les domaines personnalisés. Vous pouvez donc configurer des domaines personnalisés et d’autres solutions si nécessaire. 

**Option 1 : Utiliser Managed Browser** – Cette solution s’applique uniquement si vous envisagez de recommander ou d’exiger que les utilisateurs accèdent à l’application par le biais d’Intune Managed Browser. Elle gère toutes les URL publiées. 

**Option 2 : Utiliser l’extension MyApps** – Cette solution exige des utilisateurs qu’ils installent une extension de navigateur côté client, mais elle gère les URL publiées et fonctionne avec les navigateurs les plus populaires. 

**Option 3 : Utiliser le paramètre de traduction de lien** – Il s’agit d’un paramètre côté administrateur qui est invisible pour les utilisateurs. Toutefois, il gère uniquement les URL en HTML et CSS. Les URL internes codées en dur et générées par le biais de Javascript (par exemple) ne fonctionnent pas.  

Ces trois fonctionnalités garantissent le fonctionnement de vos liens quel que soit l’emplacement des utilisateurs. Lorsque vous avez des applications qui indiquent directement les points de terminaison internes ou des ports, vous pouvez mapper ces URL internes aux URL de Proxy d’application externes publiées. 

 
> [!NOTE]
> La dernière option est destinée uniquement aux locataires qui, pour une raison ou une autre, ne peuvent pas utiliser de domaines personnalisés avec les mêmes URL internes et externes pour leurs applications. Avant d’activer cette fonctionnalité, consultez si [des domaines personnalisés dans le Proxy d’application Azure AD](application-proxy-configure-custom-domain.md) peuvent vous être utiles. 

>Ou, si l’application que vous souhaitez configurer avec une traduction de liens est SharePoint, consultez [Configurer les mappages d’accès de substitution pour SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) pour une autre approche des liens de mappage. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Option 1 : Intégration d’Intune Managed Browser 

Vous pouvez utiliser Intune Managed Browser afin de mieux protéger votre application et votre contenu. Pour utiliser cette solution, vous devez exiger/recommander que les utilisateurs accèdent à l’application par le biais d’Intune Managed Browser. Toutes les URL internes publiées avec Proxy d’application seront reconnues par Managed Browser et redirigées vers l’URL externe correspondante. Cela garantit le fonctionnement de toutes les URL internes codées en dur, et si un utilisateur accède au navigateur et tape directement l’URL interne, elle fonctionnera même si l’utilisateur est distant.  

Pour plus d’informations, notamment sur la façon de configurer cette option, consultez la documentation de [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser).  

### <a name="option-2-myapps-browser-extension"></a>Option 2 : Extension de navigateur MyApps 

Avec l’extension de navigateur MyApps, toutes les URL internes publiées avec Proxy d’application sont reconnues par l’extension et redirigées vers l’URL externe correspondante. Cela garantit le fonctionnement de toutes les URL internes codées en dur, et si un utilisateur accède à la barre d’adresse du navigateur et tape directement l’URL interne, elle fonctionnera même si l’utilisateur est distant.  

Pour utiliser cette fonctionnalité, l’utilisateur doit télécharger l’extension et être connecté. Aucune autre configuration n’est nécessaire pour les administrateurs ou les utilisateurs. 

 

### <a name="option-3-link-translation-setting"></a>Option 3 : Paramètre de traduction de lien 

Quand la traduction de lien est activée, le service Proxy d’application effectue des recherches dans le code HTML et CSS afin de trouver les liens internes publiés, puis traduit ces liens afin que vos utilisateurs bénéficient d’une expérience sans interruption. 



## <a name="how-link-translation-works"></a>Comment fonctionnement la traduction de lien

Après l’authentification, lorsque le serveur proxy transmet les données d’application à l’utilisateur, le Proxy d’application analyse l’application pour obtenir des liens codés en dur et les remplace par leurs URL externes publiées respectives.

Le Proxy d’application part du principe que les applications sont encodées en UTF-8. Si tel n’est pas le cas, spécifiez le type de codage dans un en-tête de réponse http, tel que `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Quels sont les liens qui sont affectés ?

La fonctionnalité de traduction de lien recherche uniquement les liens qui se trouvent dans les balises de code dans le corps d’une application. Le Proxy d’application dispose d’une fonctionnalité distincte pour traduire les URL ou les cookies dans les en-têtes. 

Il existe deux types de liens internes communs dans des applications locales :

- **Les liens internes relatifs** qui indiquent une ressource partagée dans une structure de fichiers locaux, tels que `/claims/claims.html`. Ces liens fonctionnent automatiquement dans les applications publiées via le Proxy d’application et continuent de fonctionner avec ou sans traduction de liens. 
- **Les liens internes codés en dur** vers d’autres applications locales comme `http://expenses` ou des fichiers publiés comme `http://expenses/logo.jpg`. La fonctionnalité de traduction de lien fonctionne sur les liens internes codés en dur et les modifie pour pointer vers les URL externes par lesquelles les utilisateurs distants doivent passer.

### <a name="how-do-apps-link-to-each-other"></a>Comment les applications sont-elles liées l’une à l’autre ?

La traduction de lien est activée pour chaque application, afin que vous contrôliez l’expérience utilisateur pour chaque application. Activez la traduction de lien pour une application lorsque vous souhaitez que les liens *depuis* cette application soient traduits et pas les liens *vers* cette application. 

Par exemple, supposons que vous disposez de trois applications publiées via le Proxy d’application qui sont toutes liées l’une à l’autre : Profits, Dépenses et Transports. Il existe une quatrième application, Commentaires, qui n’est pas publiée via le Proxy d’application.

Lorsque vous activez la traduction de lien pour l’application Profits, les liens vers Dépenses et Transports sont redirigés vers les URL externes pour ces applications, mais le lien vers Commentaires n’est pas redirigé, car il n’existe aucune URL externe. Les liens à partir de Dépenses et Transports de retour vers Profits ne fonctionnent pas, car la traduction de lien n’a pas été activée pour ces deux applications.

![Liens à partir de Profit vers d’autres applications lorsque la traduction de lien est activée](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quels sont les liens qui ne sont pas traduits ?

Pour améliorer les performances et la sécurité, certains liens ne sont pas traduits :

- Les liens non inclus dans des balises de code. 
- Liens dans un code autre que HTML ou CSS. 
- Liens internes ouverts à partir d’autres programmes. Les liens envoyés par e-mail ou message instantané ou inclus dans d’autres documents, ne sont pas traduits. Les utilisateurs doivent savoir comment accéder à l’URL externe.

Si vous avez besoin de prendre en charge un de ces deux scénarios, vous pouvez utiliser les mêmes URL internes et externes, ce qui élimine le besoin de traduction de liens.  

## <a name="enable-link-translation"></a>Activer la traduction de lien

Bien démarrer avec la traduction de lien est aussi simple que cliquer sur un bouton :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **toutes les applications** > sélectionnez l’application que vous souhaitez gérer > **Proxy d’application**.
3. Définissez **Traduire les URL dans le corps de l’application** sur **Oui**.

   ![Sélectionnez Oui pour traduire les URL dans le corps de l’application](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png).
4. Sélectionnez **Enregistrer** pour enregistrer vos modifications.

Maintenant, lorsque les utilisateurs accèdent à cette application, le proxy recherche automatiquement les URL internes qui ont été publiées via le Proxy d’application sur votre client.

## <a name="send-feedback"></a>Envoyer des commentaires

Nous souhaitons votre aide pour que cette fonctionnalité fonctionne pour toutes vos applications. Nous recherchons plus de 30 balises dans le code HTML et CSS. Si vous disposez d’un exemple de liens générés qui ne sont pas en cours de traduction, envoyez un extrait de code aux [Commentaires du Proxy d’application](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Étapes suivantes
[Utilisez des domaines personnalisés avec le Proxy d’application Azure AD](application-proxy-configure-custom-domain.md) pour que les URL interne et externe soit identiques

[Configurer les mappages d’accès alternatifs dans SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
