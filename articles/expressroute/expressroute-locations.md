---
title: 'Fournisseurs de connectivité et emplacements : Azure ExpressRoute | Microsoft Docs'
description: Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure. Trié par fournisseur de connectivité.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2019
ms.author: pareshmu
ms.openlocfilehash: 0dcf1ab67368fa07eadaae261d19dec573574541
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002151"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partenaires ExpressRoute et emplacements d’homologation

> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md)


Les tables de cet article offrent des informations sur les fournisseurs de connectivité ExpressRoute, la couverture géographique ExpressRoute, les services cloud Microsoft pris en charge via ExpressRoute et les intégrateurs système ExpressRoute.

## <a name="partners"></a>Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.

![Carte des emplacements][0]

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.
Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

| **Région géopolitique** | **Zone** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- | --- |
| **Secteur public australien** | 1 | Australie Centre, Australie Centre 2 |Canberra, Canberra2 |
| **Europe** | 1 |France Centre, France Sud, Europe Nord, Europe Ouest, Royaume-Uni Ouest, Royaume-Uni Sud |Amsterdam, Amsterdam2, Dublin, London, London2, Marseille, Newport(Wales), Paris, Zurich |
| **Amérique du Nord** | 1 |USA Est, USA Ouest, USA Est 2, USA Ouest 2, USA Centre, USA Centre Sud, USA Centre Nord, USA Centre-Ouest, Canada Centre, Canada Est |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montréal, Québec, Toronto |
| **Asie** | 2 |Asie Est, Asie Sud-Est |Hong Kong (r.a.s.), Kuala Lumpur, Singapour, singapour2, Taipei |
| **Australie** | 2 |Australie Sud-Est | Australie Est |Melbourne, Perth, Sydney | 
| **Inde** | 2 |Inde Ouest, Inde Centre, Inde Sud |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japon** | 2 |Japon Ouest, Japon Est |Osaka, Tokyo |
| **Corée du Sud** | 2 |Centre de la Corée, Corée du Sud |Busan, Séoul|
| **ÉMIRATS ARABES UNIS** | 2 | Émirats Arabes Unis, États-Unis, Nord des Émirats Arabes Unis | Dubaï |
| **Afrique du Sud** | 3 |Afrique du Sud Ouest, Nord de l’Afrique du Sud |Le Cap, Johannesburg |
| **Amérique du Sud** | 3 |Brésil Sud |Sao Paulo |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Régions et limites géopolitiques pour les clouds nationaux
Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques et des clouds nationaux.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Cloud du gouvernement des États-Unis** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginie, US DoD Centre, US DoD Est  |Chicago, Dallas, New York, Seattle, Phoenix, San Antonio, Silicon Valley, Washington DC |
| **Chine Est** |Chine Est, Chine Est2 |Shanghai, Shanghai2 |
| **Chine Nord** |Chine Nord, Chine Nord2 |Beijing, Beijing2 |
| **Allemagne** |Allemagne centrale, Allemagne de l’est |Berlin, Francfort |

La connectivité entre les régions géopolitiques n’est pas prise en charge dans la référence ExpressRoute Standard. Vous devez activer le module complémentaire ExpressRoute Premium pour prendre en charge la connectivité globale. La connectivité à des environnements de cloud nationaux n’est pas prise en charge. En cas de besoin, vous pouvez collaborer avec votre fournisseur de connectivité.

## <a name="locations"></a>Emplacements de fournisseur de connectivité

Le tableau suivant présente les emplacements, en fonction de chaque fournisseur de services. Si vous souhaitez afficher les fournisseurs disponibles par emplacement, consultez la liste des [fournisseurs de services par emplacement](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Production Azure

| **Fournisseur de services** | **Microsoft Azure** | **Office 365 et Dynamics 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Pris en charge |Pris en charge |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | Pris en charge | Pris en charge | Chennai2, Mumbai2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Hong Kong (r.a.s.), Sao Paulo, Seattle, Silicon Valley, Singapour, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Pris en charge |Pris en charge |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Pris en charge |Pris en charge |Montréal, Toronto, Québec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong (r.a.s.), Londres, Newport(Wales), Sao Paulo, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **C3ntro** |Bientôt disponible |Bientôt disponible |Miami |
| **CDC** | Pris en charge | Pris en charge | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Pris en charge |Pris en charge |Las Vegas, New York, San Antonio, Silicon Valley, Tokyo, Toronto |
| **Chief Telecom** |Pris en charge |Pris en charge |Taipei |
| **China Telecom Global** |Pris en charge |Non pris en charge |Hong Kong (R.A.S.) |
| **[Cologix](https://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Pris en charge |Pris en charge |Chicago, Dallas, Montréal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Pris en charge |Pris en charge |Amsterdam, Dublin, Londres, Paris, Singapour2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Pris en charge |Pris en charge |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Pris en charge |Pris en charge |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **DE-CIX** | Pris en charge |Pris en charge |Amsterdam2|
| **eir** |Pris en charge |Pris en charge |Dublin|
| **Communications globales EPSILON** |Pris en charge |Pris en charge |Singapour, Singapour2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Pris en charge |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hong Kong (r.a.s.), Londres, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **Émirats arabes unis Etisalat** |Pris en charge |Pris en charge |Dubaï|
| **euNetworks** |Pris en charge |Pris en charge |Amsterdam, Dublin, Londres |
| **GÉANT** |Pris en charge |Pris en charge |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Pris en charge| Pris en charge | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Londres, New York, Paris, Silicon Valley, Singapour, Washington DC |
| **Internet2** |Pris en charge |Pris en charge |Chicago, Dallas, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Pris en charge |Pris en charge |Osaka, Tokyo |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Pris en charge |Pris en charge |Le Cap, Johannesburg, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Pris en charge |Pris en charge |Amsterdam, Amsterdam2, Dublin, Londres, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Pris en charge |Pris en charge | Amsterdam, London2, Silicon Valley, Toronto |
| **Jisc** |Pris en charge |Pris en charge |Londres |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Pris en charge |Pris en charge |Séoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Pris en charge |Pris en charge |Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Pris en charge | Pris en charge | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Londres, Newport (Pays de Galles), Sao Paulo, Seattle, Silicon Valley, Singapour, Washington DC |
| **LG CNS** |Pris en charge |Pris en charge |Busan, Séoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Pris en charge |Pris en charge |Le Cap, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Pris en charge |Pris en charge |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, RAS de Hong Kong, Las Vegas, Londres, Los Angeles, Melbourne, Miami, New York, Perth, Québec, San Antonio, Seattle, Silicon Valley, Singapour, singapour2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Pris en charge |Pris en charge |Londres |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Pris en charge |Pris en charge |Dallas, Los Angeles, Miami, Sao Paulo |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Pris en charge |Pris en charge |Newport (Nouvelle-Galles du Sud) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Pris en charge |Pris en charge |Melbourne, Perth, Sydney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong (r.a.s.), Londres, Los Angeles, Osaka, Singapour, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Pris en charge |Pris en charge |Tokyo |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Pris en charge |Pris en charge |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Pris en charge |Pris en charge |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong (r.a.s.), Londres, Paris, Sao Paulo, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Pris en charge |Pris en charge |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Pris en charge |Pris en charge |Chicago, RAS de Hong Kong, Londres |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Pris en charge |Pris en charge |Séoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Pris en charge |Pris en charge |Chennai, Mumbai2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Pris en charge |Pris en charge |Singapour, Singapour2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Pris en charge |Pris en charge |Osaka, Tokyo |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Pris en charge |Pris en charge |Chicago, Silicon Valley, Washington DC |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Pris en charge |Pris en charge |Amsterdam, Chennai, Hong Kong (r.a.s.), Londres, Mumbai, Sao Paulo, Silicon Valley, Singapour, Washington DC |
| **Telecity Group** |Pris en charge |Pris en charge |Amsterdam |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Pris en charge |Pris en charge |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Pris en charge |Pris en charge |Londres |
| **Telenor** |Pris en charge |Pris en charge |Amsterdam, Londres |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Pris en charge | Pris en charge |Amsterdam, Chicago, Dallas, Londres, Washington DC |
| **Telmex Uninet**| Pris en charge | Pris en charge | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Pris en charge |Pris en charge |Melbourne, Singapour, Sydney |
| **[Telus](https://www.telus.com)** |Pris en charge |Pris en charge |Montréal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Pris en charge |Pris en charge |Le Cap, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Pris en charge | Pris en charge | Kuala Lumpur |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Pris en charge |Pris en charge |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Hong Kong (r.a.s.), Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Pris en charge |Non pris en charge |Londres, Singapour |
| **Vodafone Idea** | Pris en charge | Pris en charge | Mumbai, Mumbai2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Denver, Londres, Los Angeles, Montréal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC |

 **+** = bientôt disponible

### <a name="national-cloud-environment"></a>Environnement de cloud national

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Pris en charge |Pris en charge |Chicago, Phoenix, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Pris en charge |Pris en charge |New York, Phoenix |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Pris en charge |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Pris en charge |Pris en charge |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Pris en charge | Pris en charge | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Pris en charge |Pris en charge |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Chine

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **China Telecom** |Pris en charge |Non pris en charge |Beijing, Shanghai |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Pris en charge |Non pris en charge |Beijing2, Shanghai2 |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Allemagne

| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Pris en charge |Non pris en charge |Francfort |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Non pris en charge |Francfort |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Pris en charge |Non pris en charge |Berlin |
| **Interxion** |Pris en charge |Non pris en charge |Francfort |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Pris en charge  | Non pris en charge | Berlin |
| **T-Systems** |Pris en charge |Non pris en charge |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Connectivité via des fournisseurs Exchange

Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-additional-service-providers"></a>Connectivité via d’autres fournisseurs de services

| **Fournisseur de connectivité** | **Microsoft Exchange** | **Emplacements** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapour |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montréal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Francfort, Londres, Singapour, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hong Kong (R.A.S.) |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfort, Hambourg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montréal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | Londres |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londres, Singapour, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Québec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapour|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong (R.A.S.) |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Le Cap, Johannesburg |
| **[NextGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfort |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londres, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Francfort |  
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londres | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapour |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |Londres|
| **[Zertia](https://www.zertia.es)**| Niveau 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectivité via des fournisseurs de centres de données

| **Fournisseur** | **Microsoft Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivité via les réseaux nationaux de la recherche et de l’enseignement (NREN)

| **Fournisseur**|
| --- |
| **AARNET**| 
| **DeIC, via GÉANT**|
| **GARR, via GÉANT**|
| **GÉANT**|
| **HEAnet, via GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, via GÉANT**|
| **SINET**|
| **Surfnet, via GÉANT**|

* Si votre fournisseur de connectivité n’est pas répertorié ici, vérifiez s’il est en lien avec l’un des partenaires Exchange ExpressRoute répertoriés ci-dessus.

## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Intégrateur système** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europe |
| **[Avanade Inc.](https://www.avanade.com/)** | Asie, Europe, Amérique du Nord, Amérique du Sud |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europe
| **[Ensyst](https://www.ensyst.com.au)** | Asie
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Amérique du Nord |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Amérique du Nord |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Amérique du Nord |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australie |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europe (Allemagne) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europe |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europe |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asie |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europe |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Amérique du Nord |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Amérique du Nord |
| **[sol-tec](https://www.sol-tec.com/services)** | Europe |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Amérique du Sud |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australie |

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"
