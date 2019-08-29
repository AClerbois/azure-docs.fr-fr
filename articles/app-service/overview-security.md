---
title: Vue d’ensemble de la sécurité - Azure App Service | Microsoft Docs
description: Découvrez comment App Service vous aide à sécuriser votre application et comment vous pouvez renforcer la protection de votre application contre les menaces.
keywords: azure app service, application web, application mobile, application d’api, application de fonction, sécurité, sécuriser, sécurisé, conformité, conforme, certificat, certificats, https, ftps, tls, approbation, chiffrement, chiffrer, chiffré, restriction d’adresse ip, authentification, autorisation, authn, autho, msi, identité de service managée, identité managée, secrets, secret, mise à jour corrective, correctif, correctifs, version, isolation, isolement réseau, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b6f122abff1ac75bb1cb836f3389c96dfcdf60e0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074119"
---
# <a name="security-in-azure-app-service"></a>Sécurité dans Azure App Service

Cet article vous montre comment [Azure App Service](overview.md) vous aide à sécuriser votre application web, backend d’application mobile, application API et [application de fonction](/azure/azure-functions/). Il vous montre également comment renforcer la sécurité de votre application avec les fonctionnalités App Service intégrées.

Les composants de plateforme d’App Service, notamment les machines virtuelles Azure, le stockage, les connexions réseau, les frameworks web, les fonctionnalités de gestion et d’intégration, sont activement sécurisés et renforcés. App Service fait régulièrement l’objet de vérifications de conformité strictes pour garantir les points suivants :

- Les ressources de votre application sont [protégées](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) des ressources Azure des autres clients.
- Les [instances de machine virtuelle et les logiciels de runtime sont régulièrement mis à jour](overview-patch-os-runtime.md) pour que soient traitées les vulnérabilités récemment découvertes. 
- La communication de secrets (tels que des chaînes de connexion) entre votre application et d’autres ressources Azure (telles que [SQL Database](https://azure.microsoft.com/services/sql-database/)) reste dans Azure et ne franchit pas les limites du réseau. Les secrets sont toujours chiffrés quand ils sont stockés.
- Toutes les communications via les fonctionnalités de connectivité d’App Service, telles que la [connexion hybride](app-service-hybrid-connections.md), sont chiffrées. 
- Toutes les connexions avec les outils de gestion à distance, tels qu’Azure PowerShell, Azure CLI, les SDK Azure et les API REST, sont chiffrées.
- La gestion continue des menaces protège l’infrastructure et la plateforme contre les programmes malveillants, le déni de service distribué (DDoS), les attaques de l’intercepteur (man-in-the-middle, MITM) et bien d’autres menaces.

Pour plus d’informations sur la sécurité de l’infrastructure et de la plateforme dans Azure, consultez [Centre de confidentialité Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Les sections suivantes vous montrent comment renforcer la protection de votre application App Service contre les menaces.

## <a name="https-and-certificates"></a>HTTPS et certificats

App Service vous permet de sécuriser vos applications avec [HTTPS](https://wikipedia.org/wiki/HTTPS). Quand votre application est créée, son nom de domaine par défaut (\<nom_application>. azurewebsites.net) est déjà accessible à l’aide de HTTPS. Si vous [configurez un domaine personnalisé pour votre application](app-service-web-tutorial-custom-domain.md), vous devez également le [sécuriser avec un certificat personnalisé](app-service-web-tutorial-custom-ssl.md) afin que les navigateurs clients puissent établir des connexions HTTPS sécurisées à votre domaine personnalisé. Vous pouvez effectuer cette opération de deux façons :

- **Certificat App Service**  : créez un certificat directement dans Azure. Le certificat est sécurisé dans [Azure Key Vault](/azure/key-vault/) et peut être importé dans votre application App Service. Pour plus d’informations, consultez [Acheter et configurer un certificat SSL pour votre service Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certificat tiers** : chargez un certificat SSL personnalisé que vous avez acheté auprès d’une autorité de certification de confiance et liez-le à votre application App Service. App Service prend en charge les certificats de domaine unique et les certificats avec caractères génériques. Il prend également en charge les certificats auto-signés à des fins de test. Pour plus d’informations, consultez [Lier un certificat SSL personnalisé existant à Azure App Service](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocoles non sécurisés (HTTP, TLS 1.0, FTP)

Dans App Service, vous pouvez appliquer le protocole HTTPS d’un seul clic pour sécuriser votre application vis-à-vis de toutes les connexions (HTTP) non chiffrées. Les demandes non sécurisées sont écartées avant même qu’elles n’atteignent votre code d’application. Pour plus d’informations, consultez [Appliquer le protocole HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 n’est plus considéré comme sécurisé par les normes du secteur telles que [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service vous permet de désactiver les protocoles obsolètes en [appliquant le protocole TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service prend en charge FTP et FTPS pour le déploiement de vos fichiers. Toutefois, FTPS doit être utilisé à la place de FTP, dans la mesure du possible. Quand un de ces protocoles, ou les deux, ne sont pas utilisés, vous devez [les désactiver](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrictions d’adresse IP statique

Par défaut, votre application App Service accepte les demandes émanant de toutes les adresses IP d’internet, mais vous pouvez limiter cet accès à un petit sous-ensemble d’adresses IP. App Service sur Windows permet de définir une liste d’adresses IP pouvant accéder à votre application. La liste autorisée peut inclure des adresses IP individuelles ou une plage d’adresses IP définie par un masque de sous-réseau. Pour plus d’informations, consultez [Restrictions d’adresse IP statique avec Azure App Service](app-service-ip-restrictions.md).

Pour App Service sur Windows, vous pouvez également limiter les adresses IP dynamiquement en configurant _web.config_. Pour plus d’informations, consultez [Sécurité IP dynamique \<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Authentification et autorisation clientes

Azure App Service fournit l’authentification et l’autorisation clé en main des utilisateurs ou des applications clientes. Quand cette configuration est activée, les utilisateurs et les applications clientes peuvent se connecter avec peu ou aucun code d’application. Vous pouvez implémenter votre propre solution d’authentification et d’autorisation ou autoriser App Service à la gérer pour vous. Le module d’authentification et d’autorisation gère les demandes web avant de les remettre à votre code d’application, et il refuse les demandes non autorisées avant qu’elles n’atteignent votre code.

Le module d’autorisation et d’authentification App Service prend en charge plusieurs fournisseurs d’authentification, notamment Azure Active Directory, les comptes Microsoft, Facebook, Google et Twitter. Pour plus d’informations, consultez la page [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Authentification de service à service

Quand l’authentification est effectuée par rapport à un service backend, App Service fournit deux mécanismes différents selon vos besoins :

- **Identité de service** : se connecter à la ressource distante à l’aide de l’identité de l’application proprement dite. App Service vous permet de créer facilement une [identité managée](overview-managed-identity.md) que vous pouvez utiliser pour vous authentifier auprès d’autres services, par exemple [Azure SQL Database](/azure/sql-database/) ou [Azure Key Vault](/azure/key-vault/). Pour un tutoriel de bout en bout sur cette approche, voir [Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité managée](app-service-web-tutorial-connect-msi.md).
- **Pour le compte de** : effectuer l’accès délégué aux ressources distantes pour le compte de l’utilisateur. Avec Azure Active Directory comme fournisseur d’authentification, votre application App Service peut effectuer une connexion déléguée à un service distant, tel que [l’API Azure Active Directory Graph](../active-directory/develop/active-directory-graph-api.md) ou à une application API distante dans App Service. Pour suivre un tutoriel de bout en bout sur cette approche, consultez [Authentifier et autoriser des utilisateurs de bout en bout dans Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Connectivité aux ressources distantes

Il existe trois types de ressources distantes auxquelles votre application peut avoir besoin d’accéder : 

- [Ressources Azure](#azure-resources)
- [Ressources à l’intérieur d’un réseau virtuel Azure](#resources-inside-an-azure-virtual-network)
- [Ressources locales](#on-premises-resources)

Dans chacun de ces cas, App Service fournit un moyen d’établir des connexions sécurisées, mais vous devez toujours observer des bonnes pratiques de sécurité. Par exemple, utilisez toujours des connexions chiffrées même si la ressource backend autorise les connexions non chiffrées. En outre, vérifiez que votre service Azure backend autorise l’ensemble minimal d’adresses IP. Pour trouver les adresses IP sortantes pour votre application, consultez [Adresses IP entrantes et sortantes dans Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Ressources Azure

Quand votre application se connecte à des ressources Azure, telles que [SQL Database](https://azure.microsoft.com/services/sql-database/) et [Stockage Azure](/azure/storage/), la connexion reste dans Azure et ne franchit pas les limites du réseau. Toutefois, comme la connexion emprunte le dispositif réseau partagé dans Azure, assurez-vous systématiquement qu’elle est chiffrée. 

Si votre application est hébergée dans un [environnement App Service](environment/intro.md), vous devez [vous connecter à des services Azure pris en charge à l’aide de points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Ressources à l’intérieur d’un réseau virtuel Azure

Votre application peut accéder aux ressources dans un [réseau virtuel Azure](/azure/virtual-network/) par le biais d’une [intégration à un réseau virtuel](web-sites-integrate-with-vnet.md). L’intégration à un réseau virtuel est établie à l’aide d’un VPN de point à site. L’application peut ensuite accéder aux ressources du réseau virtuel à l’aide de leurs adresses IP privées. Toutefois, la connexion de point à site, emprunte toujours les réseaux partagés dans Azure. 

Pour isoler complètement la connectivité de vos ressources des réseaux partagés dans Azure, créez votre application dans un [environnement App Service](environment/intro.md). Dans la mesure où un environnement App Service est toujours déployé sur un réseau virtuel dédié, la connectivité entre votre application et vos ressources dans le réseau virtuel est complètement isolée. Pour plus d’informations sur d’autres aspects de la sécurité réseau dans un environnement App Service, consultez [Isolement réseau](#network-isolation).

### <a name="on-premises-resources"></a>Ressources locales

Vous pouvez accéder de manière sécurisée à des ressources locales, telles que des bases de données, de trois façons : 

- [Connexions hybrides](app-service-hybrid-connections.md) : établit une connexion de point à point à votre ressource distante via un tunnel TCP. Le tunnel TCP est établi à l’aide de TLS 1.2 avec des clés de signature d’accès partagé (SAP).
- [Intégration à un réseau virtuel](web-sites-integrate-with-vnet.md) avec VPN de site à site : comme décrit dans [Ressources à l’intérieur d’un réseau virtuel Azure](#resources-inside-an-azure-virtual-network), mais le réseau virtuel peut être connecté à votre réseau local par le biais d’un [VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Dans cette topologie de réseau, votre application peut se connecter à des ressources locales telles que d’autres ressources dans le réseau virtuel.
- [Environnement App Service](environment/intro.md) avec VPN de site à site : comme décrit dans [Ressources à l’intérieur d’un réseau virtuel Azure](#resources-inside-an-azure-virtual-network), mais le réseau virtuel peut être connecté à votre réseau local par le biais d’un [VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). Dans cette topologie de réseau, votre application peut se connecter à des ressources locales telles que d’autres ressources dans le réseau virtuel.

## <a name="application-secrets"></a>Secrets de l’application

Ne stockez pas les secrets de l’application, tels que les informations d’identification de la base de données, les jetons d’API et les clés privées, dans vos fichiers de code ou de configuration. L’approche couramment acceptée consiste à y accéder sous forme de [variables d’environnement](https://wikipedia.org/wiki/Environment_variable) à l’aide du modèle standard dans la langue de votre choix. Dans App Service, la définition des variables d’environnement passe par l’utilisation de [paramètres d’application](configure-common.md#configure-app-settings) (et, en particulier pour les applications .NET, de [chaînes de connexion](configure-common.md#configure-connection-strings)). Les paramètres d’application et les chaînes de connexion sont stockés dans Azure, et ils sont déchiffrés uniquement avant d’être injectés dans la mémoire de processus de votre application au démarrage de celle-ci. Les clés de chiffrement sont régulièrement permutées.

Une autre approche consiste à intégrer votre application App Service à [Azure Key Vault](/azure/key-vault/) pour bénéficier d’une gestion avancée des secrets. En [accédant à Key Vault avec une identité managée](../key-vault/tutorial-web-application-keyvault.md), votre application App Service peut accéder de manière sécurisée aux secrets dont vous avez besoin.

## <a name="network-isolation"></a>Isolement réseau

À la différence du niveau tarifaire **Isolé**, tous les niveaux exécutent vos applications sur l’infrastructure réseau partagée dans App Service. Par exemple, les adresses IP publiques et les équilibreurs de charge frontaux sont partagés avec d’autres locataires. Le niveau **Isolé** vous procure un isolement réseau complet en exécutant vos applications à l’intérieur d’un [environnement App Service](environment/intro.md) dédié. Un environnement App Service s’exécute dans votre propre instance de [Réseau virtuel Azure](/azure/virtual-network/). Il vous permet d’effectuer les opérations suivantes : 

- Servir vos applications par le biais d’un point de terminaison public dédié, avec des serveurs frontaux dédiés.
- Servir une application interne à l’aide d’un équilibreur de charge interne (ILB), l’accès n’étant alors autorisé qu’à partir de votre réseau virtuel Azure. L’équilibreur de charge interne possède une adresse IP appartenant à votre sous-réseau privé, ce qui isole totalement vos applications d’internet.
- [Utiliser un équilibreur de charge interne derrière un pare-feu d’applications web (WAF)](environment/integrate-with-application-gateway.md). Le WAF offre une protection de niveau entreprise à vos applications publiques, telle que la protection DDoS, le filtrage des URI et la prévention de l’injection SQL.

Pour plus d’informations, consultez [Présentation des environnements Azure App Service](environment/intro.md). 
