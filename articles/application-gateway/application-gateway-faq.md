---
title: Forum aux questions sur Azure Application Gateway
description: Trouvez des réponses aux questions les plus fréquentes sur Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 5bfb3a093cd101f30daf4439dc8f58b5b4f693ca
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68740884"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Forum aux questions sur Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous trouverez ci-dessous les questions fréquentes sur Azure Application Gateway.

## <a name="general"></a>Généralités

### <a name="what-is-application-gateway"></a>Présentation de Application Gateway

Le service Azure Application Gateway fournit un contrôleur de livraison d'applications (ADC) sous forme de service. Il offre diverses fonctionnalités d’équilibrage de charge de couche 7 pour vos applications. Ce service est hautement disponible, évolutif et complètement managé par Azure.

### <a name="what-features-does-application-gateway-support"></a>Quelles sont les fonctionnalités prises en charge par Application Gateway ?

Application Gateway prend en charge la mise à l’échelle automatique, le déchargement SSL et SSL de bout en bout, un pare-feu d’application web (WAF), l’affinité de session basée sur les cookies, le routage basé sur le chemin d’accès de l’URL, l’hébergement de plusieurs sites, et bien plus encore. Pour obtenir une liste complète des fonctionnalités prises en charge, voir [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>En quoi Application Gateway et Azure Load Balancer diffère-t-il ?

Application Gateway est un équilibreur de charge de couche 7, ce qui signifie qu’il fonctionne uniquement avec le trafic web (HTTP/HTTPS/WebSocket etHTTP/2). Il prend en charge des fonctionnalités telles que la terminaison SSL, l’affinité de session basée sur les cookies et le tourniquet (round robin) pour le trafic d’équilibrage de charge. Load Balancer équilibre la charge du trafic au niveau de la couche 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quels sont les protocoles pris en charge par Application Gateway ?

Application Gateway prend en charge les protocoles HTTP, HTTPS, HTTP/2 et WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway prend-il en charge le protocole HTTP/2 ?

Consultez [HTTP/2 support](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quelles sont les ressources prises en charge dans un pool backend ?

Consultez [Ressources principales prises en charge](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Dans quelles régions Application Gateway est-il disponible ?

Application Gateway est disponible dans toutes les régions de la version globale d’Azure. Il est également disponible dans [Azure - Chine 21Vianet](https://www.azure.cn/) et [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>S’agit-il d’un déploiement dédié à mon abonnement ou est-il partagé entre les clients ?

Application Gateway est un déploiement dédié dans votre réseau virtuel.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway prend-il en charge la redirection HTTP vers HTTPS ?

La redirection est prise en charge. Consultez [Vue d’ensemble de la redirection Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Dans quel ordre les écouteurs sont-ils traités ?

Consultez l'[ordre de traitement des écouteurs](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Où puis-je trouver l’adresse IP et le DNS d’Application Gateway ?

Si vous utilisez une adresse IP publique en tant que point de terminaison, vous trouverez les informations d’adresse IP et de DNS dans la ressource d’adresse IP publique. Elles sont également disponibles sur la page Vue d'ensemble de la passerelle d’application. Si vous utilisez des adresses IP internes, ces informations se trouvent dans la page Vue d'ensemble.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quels sont les paramètres du délai de maintien de connexion et du délai d’inactivité TCP ?

 Dans la référence SKU Application Gateway v1, le délai de maintien de connexion est de 120 secondes. Le délai de maintien de connexion de la référence SKU v2 est de 75 secondes. Par défaut, le délai d'inactivité TCP est de 4 minutes sur l'adresse IP virtuelle du serveur frontal d'Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L’adresse IP ou le nom DNS changent-ils pendant la durée de vie d’Application Gateway ?

L’adresse IP virtuelle peut changer si vous arrêtez et démarrez la passerelle d’application. Le nom DNS associé à la passerelle d’application, quant à lui, ne change pas pendant toute la durée de vie de la passerelle. Le nom DNS ne changeant pas, vous devez utiliser un alias CNAME et le faire pointer vers l’adresse DNS de la passerelle d’application.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway prend-il en charge les adresses IP statiques ?

Oui. La référence SKU v2 d’Application Gateway prend en charge les adresses IP publiques statiques. La référence SKU v1 quant à elle prend en charge les adresses IP internes statiques.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway prend-il en charge plusieurs adresses IP publiques sur la passerelle ?

Une seule adresse IP publique est prise en charge sur une passerelle d’application.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quelle taille mon sous-réseau pour Application Gateway doit-il avoir ?

Consultez [Considérations relatives à la taille du sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Puis-je déployer plusieurs ressources Application Gateway dans un seul sous-réseau ?

Oui. En plus de plusieurs instances d’un déploiement Application Gateway donné, vous pouvez configurer une autre ressource Application Gateway unique dans un sous-réseau existant qui contient une autre ressource Application Gateway.

À lui seul, un sous-réseau n'est pas en mesure de prendre en charge Standard_v2 et Standard Application Gateway.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway prend-il en charge les en-têtes x-forwarded-for ?

Oui. Consultez [Modifications apportées à une requête](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Combien de temps faut-il pour déployer une instance Application Gateway ? Mon instance Application Gateway continue-t-elle de fonctionner lorsqu’elle est mise à jour ?

La configuration des déploiements de nouvelles références SKU v1 d’Application Gateway peut prendre jusqu’à 20 minutes. Les modifications apportées à la taille ou au nombre des instances n’entraînent pas d’interruption, et la passerelle reste active pendant ce temps.

Pour la plupart des déploiements qui utilisent la référence SKU v2, le provisionnement prend environ 6 minutes. Toutefois, cette opération peut prendre plus de temps en fonction du type de déploiement. Par exemple, les déploiements sur plusieurs zones de disponibilité avec de nombreuses instances peuvent prendre plus de 6 minutes. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Puis-je utiliser Exchange Server en tant que serveur principal avec Application Gateway ?

Non. Application Gateway ne prend pas en charge les protocoles de messagerie tels que SMTP, IMAP et POP3. 

## <a name="performance"></a>Performances

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Comment Application Gateway prend-il en charge la haute disponibilité et la scalabilité ?

La référence SKU v1 d’Application Gateway prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux instances ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. La référence SKU v1 prend en charge la scalabilité en ajoutant plusieurs instances de la même passerelle pour partager la charge.

La référence SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Comment puis-je obtenir un scénario DR dans les centres de données à l'aide d'Application Gateway ?

Utilisez Traffic Manager pour répartir le trafic entre plusieurs instances Application Gateway dans différents centres de données.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway prend-il en charge la mise à l'échelle automatique ?

Oui. La référence SKU v2 d’Application Gateway prend en charge la mise à l’échelle automatique. Pour plus d’informations, consultez [Application Gateway avec mise à l’échelle automatique et redondance interzone](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Les opérations de montée/descente en puissance manuelles entraînent-elles des temps d’arrêt ?

Non. Aucun temps d’arrêt n’a lieu, les instances sont réparties entre les domaines de mise à niveau et les domaines d’erreur.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway prend-il en charge le drainage de connexion ?

Oui. Vous pouvez configurer le drainage de connexion afin de modifier des membres au sein d’un pool principal sans interrompre le service. Vous pouvez ainsi continuer d'envoyer les connexions existantes à leur destination précédente jusqu'à ce que cette connexion soit fermée ou qu’un délai configurable expire. Ce drainage de connexion attend uniquement la fin des connexions actuellement en transit. Application Gateway ne connaît pas l’état de la session d’application.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Puis-je passer d’une taille moyenne à une grande taille d’instance sans interruption de service ?

Oui. Azure distribue des instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. Application Gateway prend en charge la mise à l’échelle en ajoutant plusieurs instances de la même passerelle pour partager la charge.

## <a name="configuration"></a>Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway est-il toujours déployé dans un réseau virtuel ?

Oui. Application Gateway est toujours déployé dans un sous-réseau de réseau virtuel. Ce sous-réseau ne peut contenir que des instances Application Gateway. Pour plus d’informations, consultez [Exigences de réseau virtuel et de sous-réseau](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway peut-il communiquer avec des instances en dehors de son réseau virtuel ou de son abonnement ?

Sous réserve de disposer d'une connectivité IP, Application Gateway peut communiquer avec des instances en dehors du réseau virtuel dans lequel il se trouve. Application Gateway peut également communiquer avec des instances extérieures à l'abonnement dans lequel il se trouve. Si vous envisagez d'utiliser des adresses IP internes en tant que membres de pool de back-ends, utilisez le [Peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) ou la [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Puis-je déployer autre chose dans le sous-réseau de la passerelle d’application ?

Non. Mais vous pouvez déployer d’autres passerelles d’application dans le sous-réseau.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Les groupes de sécurité réseau sont-ils pris en charge dans le sous-réseau de la passerelle d’application ?

Consultez [Groupes de sécurité réseau dans le sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Le sous-réseau de la passerelle d'application prend-il en charge les routes définies par l’utilisateur ?

Consultez [Les routes définies par l’utilisateur prises en charge dans le sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quelles sont les limites d’Application Gateway ? Puis-je augmenter ces limites ?

Consultez [Limites d’Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Puis-je utiliser simultanément Application Gateway pour le trafic externe et interne ?

Oui. Application Gateway peut avoir une adresse IP interne et une adresse IP externe par passerelle d’application.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway prend-il en charge le peering de réseaux virtuels ?

Oui. Le peering de réseaux virtuels permet d'équilibrer la charge du trafic dans d'autres réseaux virtuels.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Puis-je communiquer avec les serveurs locaux lorsqu’ils sont connectés via ExpressRoute ou des tunnels VPN ?

Oui, tant que le trafic est autorisé.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool back-end peut-il servir plusieurs applications sur des ports différents ?

L’architecture orientée microservices est prise en charge. Pour sonder différents ports, vous devez configurer plusieurs paramètres HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Les sondes personnalisées prennent-elles en charge les caractères génériques ou les expressions régulières sur les données de réponse ?

Non. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Comment les règles d'acheminement sont-elles traitées dans Application Gateway ?

Consultez [Ordre de traitement des règles](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>À quoi correspond le champ Hôte pour les sondes personnalisées ?

Le champ hôte Spécifie le nom auquel envoyer la sonde lorsque vous avez configuré plusieurs sites sur Application Gateway. Sinon, utilisez « 127.0.0.1 ». Cette valeur est différente du nom d’hôte de la machine virtuelle. Elle se présente au format \<protocole\>://\<hôte\>:\<port\>\<chemin\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Puis-je autoriser l’accès d’Application Gateway à quelques adresses IP sources uniquement ?

Oui. Consultez [Restreindre l’accès à des adresses IP sources spécifiques](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Puis-je utiliser le même port pour les écouteurs publics et privés ?

Non.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe-t-il des instructions pour migrer de la référence SKU v1 vers la référence SKU v2 ?

Oui. Pour plus d'informations, consultez [Migrer Azure Application Gateway et le pare-feu d’applications web de v1 à v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Configuration - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Quels sont les certificats pris en charge par Application Gateway ?

Application Gateway prend en charge les certificats auto-signés, les certificats d’autorité de certification (CA), les certificats de validation étendue (EV) et les certificats génériques.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quelles sont les suites de chiffrement prises en charge par Application Gateway ?

Application Gateway prend en charge les suites de chiffrement ci-dessous. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Pour savoir comment personnaliser les options SSL, consultez [Configurer les versions de stratégie SSL et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway prend-il en charge le nouveau chiffrement du trafic sur le back-end ?

Oui. Application Gateway prend en charge le déchargement SSL et SSL de bout en bout, qui chiffre à nouveau le trafic vers le serveur principal.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Puis-je configurer la stratégie SSL pour gérer les versions du protocole SSL ?

Oui. Vous pouvez configurer Application Gateway pour refuser TLS1.0, TLS1.1 et TLS1.2. Par défaut, SSL 2.0 et 3.0 sont déjà désactivés et ne sont pas configurables.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Puis-je configurer les suites de chiffrement et l’ordre de la stratégie ?

Oui. Dans Application Gateway, vous pouvez [configurer des suites de chiffrement](application-gateway-ssl-policy-overview.md). Pour définir une stratégie personnalisée, activez au moins une des suites de chiffrement suivantes. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway utilise SHA256 pour la gestion des serveurs principaux.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Combien de certificats SSL Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu'à 100 certificats SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Combien de certificats d’authentification pour le nouveau chiffrement du back-end Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu'à 10 certificats d'authentification. La valeur par défaut est 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway s’intègre-t-il en mode natif à Azure Key Vault ?

La référence SKU v2 d’Application Gateway prend en charge Key Vault. Pour plus d'informations, consultez [Arrêt de SSL avec des certificats Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Comment configurer des écouteurs HTTPS pour les sites .com et .net ? 

Pour un acheminement (basé sur l'hôte) sur plusieurs domaines, vous pouvez créer des écouteurs multisites, configurer des écouteurs utilisant le protocole HTTPS et associer les écouteurs aux règles d'acheminement. Pour plus d’informations, consultez [Hébergement de plusieurs sites à l'aide d'Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuration - pare-feu d’applications web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>La référence SKU de pare-feu d’applications web propose-t-elle toutes les fonctionnalités disponibles dans la référence SKU Standard ?

Oui. Le pare-feu d’applications web prend en charge toutes les fonctionnalités de la référence Standard.

### <a name="which-crs-versions-does-application-gateway-support"></a>Quelles sont les versions CRS prises en charge par Application Gateway ?

Application Gateway prend en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Comment puis-je surveiller le pare-feu d’application web ?

Surveillez le pare-feu d'applications web via la journalisation des diagnostics. Pour plus d'informations, consultez [Journalisation des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Est-ce que le mode de détection bloque le trafic ?

Non. Le mode de détection journalise uniquement le trafic qui déclenche une règle de pare-feu d’applications web.

### <a name="can-i-customize-waf-rules"></a>Puis-je personnaliser les règles de pare-feu d’application web ?

Oui. Pour plus d’informations, consultez [Personnaliser les règles et groupes de règles WAF](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quelles sont les règles actuellement disponibles pour WAF ?

WAF prend actuellement en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Ces règles constituent un rempart contre la plupart des 10 principales vulnérabilités identifiées par l'OWASP (Open Web Application Security Project) : 

* Protection contre les injections de code SQL
* Protection contre les scripts intersites
* Protection contre les attaques web courantes comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)
* Protection contre les violations de protocole HTTP
* Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept
* Protection contre les robots, les crawlers et les scanneurs
* Détection des erreurs de configuration d’application courantes (par exemple, Apache, IIS, etc.)

Pour plus d’informations, consultez [Les 10 principales vulnérabilités identifiées par l'OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>WAF prend-il en charge la protection DDoS ?

Oui. Vous pouvez activer le service de protection DDoS sur le réseau virtuel sur lequel la passerelle d’application est déployée. Ainsi, le service Azure DDoS Protection protège aussi l’adresse IP virtuelle de la passerelle d’application.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe-t-il des instructions pour migrer de la référence SKU v1 vers la référence SKU v2 ?

Oui. Pour plus d'informations, consultez [Migrer Azure Application Gateway et le pare-feu d’applications web de v1 à v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quels types de journaux Application Gateway fournit-il ?

Application Gateway fournit trois types de journaux : 

* **ApplicationGatewayAccessLog** : le journal d’accès contient toutes les requêtes envoyées au serveur frontal de la passerelle d’application. Les données incluent l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, et les octets d’entrée et de sortie. Un journal d’accès est collecté toutes les 300 secondes. Il contient un enregistrement par instance Application Gateway.
* **ApplicationGatewayPerformanceLog** : Le journal des performances capture les informations de performances de chaque instance d'Application Gateway. Parmi les informations consignées figurent notamment le débit en octets, le nombre total de requêtes traitées, le nombre de requêtes ayant échoué, ainsi que le nombre d'instances de serveur principal saines ou non saines.
* **ApplicationGatewayFirewallLog** : Pour les passerelles d’application que vous configurez avec le pare-feu d'applications web, le journal du pare-feu contient les requêtes consignées via le mode de détection ou le mode de prévention.

Pour plus d’informations, consultez [Intégrité du serveur principal, journaux de diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Comment savoir si les membres de mon pool back-end sont intègres ?

Pour vérifier leur intégrité, utilisez la cmdlet PowerShell `Get-AzApplicationGatewayBackendHealth` ou le portail. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Quelle est la stratégie de rétention des journaux de diagnostic ?

Les journaux de diagnostic sont envoyés au compte de stockage du client. Les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un Event Hub ou à des journaux d’activité Azure Monitor. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Comment puis-je obtenir des journaux d’audit pour Application Gateway ?

Dans le portail, dans le panneau du menu de la passerelle d’application, sélectionnez **Journal d’activité** pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Puis-je définir des alertes avec Application Gateway ?

Oui. Dans Application Gateway, les alertes sont configurées sur les métriques. Pour plus d’informations, consultez [Métriques Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) et [Recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Comment analyser les statistiques de trafic pour Application Gateway ?

Vous pouvez afficher et analyser les journaux d’accès de plusieurs façons. Utilisez les journaux d'activité Azure Monitor, Excel, Power BI, etc.

Vous pouvez également utiliser un modèle Resource Manager qui installe et exécute le célèbre analyseur de journal d’activité [GoAccess](https://goaccess.io/) pour les journaux d’accès Application Gateway. GoAccess fournit des statistiques de trafic HTTP précieuses telles que les visiteurs uniques, les fichiers demandés, les hôtes, les systèmes d’exploitation, les navigateurs ou les codes d’état HTTP. Pour plus d’informations, consultez le [fichier Lisez-moi dans le dossier de modèles Resource Manager dans GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Dans quelles circonstances, l'intégrité d'un serveur principal peut-elle indiquer un état inconnu ?

En règle générale, vous constatez un état inconnu lorsque l'accès au serveur principal est bloqué par un groupe de sécurité réseau (NSG), un DNS personnalisé ou un routage défini par l'utilisateur (UDR) sur le sous-réseau d'Application Gateway. Pour plus d’informations, consultez [Intégrité du serveur principal, journalisation des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Application Gateway, consultez [Qu’est-ce qu’Azure Application Gateway ?](overview.md)
