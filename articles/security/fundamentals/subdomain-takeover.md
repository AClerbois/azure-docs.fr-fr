---
title: Prévention des acquisitions de sous-domaines avec les enregistrements d’alias Azure DNS et la vérification du domaine personnalisé Azure App Service
description: Découvrez comment éviter la menace courante et de gravité élevée que représente l’acquisition de sous-domaine.
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890822"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Prévention des entrées DNS non résolues et de l’acquisition de sous-domaine

Cet article décrit la menace de sécurité courante que représente l’acquisition de sous-domaine et la procédure à suivre pour y remédier.


## <a name="what-is-subdomain-takeover"></a>Qu’est-ce que l’acquisition de sous-domaine ?

Les acquisitions de sous-domaines constituent une menace courante et de gravité élevée pour les organisations qui créent et suppriment régulièrement de nombreuses ressources. Elles peuvent se produire lorsqu’un enregistrement DNS pointe vers une ressource Azure déprovisionnée. Ces enregistrements DNS sont également appelés entrées « DNS non résolues ». Les enregistrements CNAME sont particulièrement vulnérables à cette menace.

Voici un cas de figure courant d’acquisition de sous-domaine :

1. Un site web est créé. 

    Dans cet exemple : `app-contogreat-dev-001.azurewebsites.net`.

1. Une entrée CNAME est ajoutée au nom DNS qui pointe vers le site web. 

    Dans cet exemple, le nom convivial suivant a été créé : `greatapp.contoso.com`.

1. Au bout de quelques mois, le site n’est plus nécessaire. Il est donc supprimé **sans** que l’entrée DNS correspondante soit supprimée. 

    L’entrée DNS CNAME est désormais « non résolue ».

1. Presque tout de suite après la suppression du site, un auteur de menace découvre le site manquant et crée son propre site web à l’adresse `app-contogreat-dev-001.azurewebsites.net`.

    Le trafic destiné à `greatapp.contoso.com` est maintenant dirigé vers le site Azure de l’auteur de la menace, qui contrôle ainsi le contenu affiché. 

    Le nom DNS non résolu a été exploité et le sous-domaine « GreatApp » de Contoso a été victime d’une acquisition de sous-domaine. 

![Acquisition de sous-domaine à partir d’un site web déprovisionné](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Les risques de l’acquisition de sous-domaine

Un enregistrement DNS qui pointe vers une ressource non disponible aurait dû être supprimé de la zone DNS. Dans le cas contraire, il s’agit d’un enregistrement « DNS non résolu », ce qui laisse la possibilité d’une acquisition de sous-domaine.

Les entrées DNS non résolues permettent aux auteurs de menaces de prendre le contrôle du nom DNS associé pour héberger un service ou un site web malveillant. La présence de pages et de services malveillants sur le sous-domaine d’une organisation peut avoir les répercussions suivantes :

- **Perte de contrôle sur le contenu du sous-domaine** : mauvaise presse sur l’incapacité de votre organisation à sécuriser son contenu, ainsi que préjudice à la marque et perte de confiance.

- **Collecte de cookies à l’insu des visiteurs** : il est courant que les applications web exposent les cookies de session aux sous-domaines (*.contoso.com) ; par conséquent, n’importe quel sous-domaine peut y accéder. Les auteurs de menaces peuvent utiliser l’acquisition de sous-domaine pour créer une page d’apparence authentique, inciter les utilisateurs non avertis à la visiter et collecter leurs cookies (même sécurisés). Selon une idée reçue, le recours à des certificats SSL protègerait votre site, ainsi que les cookies de vos utilisateurs, de l’acquisition. Or, un auteur de menace peut utiliser le sous-domaine détourné pour demander et recevoir un certificat SSL valide. Celui-ci lui accorde l’accès aux cookies sécurisés et peut augmenter encore la légitimité perçue du site malveillant.

- **Campagnes d’hameçonnage** : des sous-domaines d’apparence authentique peuvent être utilisés dans des campagnes d’hameçonnage. C’est vrai pour les sites malveillants, ainsi que pour les enregistrements MX qui permettent à l’auteur de menace de recevoir des e-mails adressés à un sous-domaine légitime d’une marque fiable connue.

- **Autres risques** : les sites malveillants peuvent servir de base à d’autres attaques classiques (XSS, CSRF, contournement CORS, etc.).



## <a name="preventing-dangling-dns-entries"></a>Prévention des entrées DNS non résolues

Il est crucial, dans le cadre du programme de sécurité d’une organisation, d’implémenter des processus visant à empêcher les entrées DNS non résolues et les acquisitions de sous-domaines résultantes.

Voici les mesures préventives disponibles à ce jour.


### <a name="use-azure-dns-alias-records"></a>Enregistrements d’alias Azure DNS

Les [enregistrements d’alias](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) Azure DNS empêchent les références non résolues en associant le cycle de vie d’un enregistrement DNS à une ressource Azure. Prenons l'exemple d'un enregistrement DNS qualifié en tant qu'enregistrement d'alias et pointant vers une adresse IP publique ou un profil Traffic Manager. Si vous supprimez ces ressources sous-jacentes, l’enregistrement d’alias DNS devient un jeu d’enregistrements vide. Il ne fait plus référence à la ressource supprimée. Il est important de noter qu’il existe des limites à la protection offerte par les enregistrements d’alias. La liste se limite actuellement aux éléments suivants :

- Azure Front Door
- Profils Traffic Manager
- Points de terminaison Azure Content Delivery Network (CDN)
- Adresses IP publiques

Malgré les offres de service limitées à l’heure actuelle, nous vous recommandons d’utiliser des enregistrements d’alias pour vous défendre dans la mesure du possible contre les acquisitions de sous-domaines.

[En savoir plus](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) sur les fonctionnalités des enregistrements d’alias Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Vérification du domaine personnalisé Azure App Service

Lors de la création d’entrées DNS pour Azure App Service, créez un enregistrement TXT asuid.{sous-domaine} avec l’ID de vérification du domaine. Lorsqu’il existe un enregistrement TXT de ce type, aucun autre abonnement Azure ne peut valider le domaine personnalisé, ni donc l’acquérir. 

Ces enregistrements n’empêchent pas de créer le service Azure App Service du même nom que celui qui se trouve dans votre entrée CNAME. Sans moyen de prouver la propriété du nom de domaine, les auteurs de menaces ne peuvent pas recevoir de trafic ni contrôler le contenu.

[En savoir plus](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) sur le mappage d’un nom DNS personnalisé existant à Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Création et automatisation de processus pour atténuer la menace

Il incombe souvent aux développeurs et aux équipes d’exploitation d’exécuter des processus de nettoyage pour éviter les menaces liées aux noms DNS non résolus. Les pratiques ci-dessous vous aideront à faire en sorte que votre organisation ne subisse pas cette menace. 

- **Créer des procédures de prévention :**

    - Apprenez à vos développeurs d’applications à réacheminer les adresses chaque fois qu’ils suppriment des ressources.

    - Placez « Supprimer l’entrée DNS » dans la liste des vérifications requises lors de la désactivation d’un service.

    - Placez des [verrous de suppression](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) sur les ressources qui comportent une entrée DNS personnalisée. Un verrou de suppression indique que le mappage doit être supprimé avant le déprovisionnement de la ressource. Les mesures comme celles-ci ne peuvent fonctionner qu’associées à des programmes de formation internes.

- **Créer des procédures de découverte :**

    - Vérifiez régulièrement vos enregistrements DNS pour contrôler que vos sous-domaines sont tous mappés à des ressources Azure qui remplissent les conditions suivantes :

        - Les ressources existent : interrogez vos zones DNS pour connaître les ressources pointant vers des sous-domaines Azure tels que *.azurewebsites.net ou *.cloudapp.azure.com (voir [cette liste de références](azure-domains.md)).
        - Vous en êtes le propriétaire : vérifiez que vous possédez toutes les ressources que vos sous-domaines DNS ciblent.

    - Gérez un catalogue de services de vos points de terminaison de nom de domaine complet (FQDN) Azure et des propriétaires d’applications. Pour créer votre catalogue de services, exécutez le script de requête Azure Resource Graph suivant. Ce script projette les informations des points de terminaison de FQDN des ressources auxquelles vous avez accès et les génère dans un fichier CSV. Si vous avez accès à tous les abonnements de votre locataire, le script les prend tous en compte, comme indiqué dans l’exemple de script suivant. Pour restreindre les résultats à un ensemble spécifique d’abonnements, modifiez le script comme indiqué.

        >[!IMPORTANT]
        > **Autorisations** : exécutez la requête en tant qu’utilisateur ayant accès à tous vos abonnements Azure. 
        >
        > **Limitations** : Azure Resource Graph présente des limitations et des limites de pagination que vous devez prendre en compte si vous disposez d’un environnement Azure volumineux. [En savoir plus](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) sur la gestion de gros jeux de données de ressources Azure. L’exemple de script suivant utilise le traitement d’abonnements par lot pour éviter ces limitations.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Liste des types et de leurs valeurs `FQDNProperty`, comme spécifié dans la requête Resource Graph précédente :

        |Nom de la ressource  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|properties.cName|
        |Stockage Blob Azure|microsoft.storage/storageaccounts|properties.primaryEndpoints.blob|
        |Azure CDN|microsoft.cdn/profiles/endpoints|properties.hostName|
        |Adresses IP publiques|microsoft.network/publicipaddresses|properties.dnsSettings.fqdn|
        |Azure Traffic Manager|microsoft.network/trafficmanagerprofiles|properties.dnsConfig.fqdn|
        |Azure Container Instance|microsoft.containerinstance/containergroups|properties.ipAddress.fqdn|
        |Gestion des API Azure|microsoft.apimanagement/service|properties.hostnameConfigurations.hostName|
        |Azure App Service|microsoft.web/sites|properties.defaultHostName|
        |Azure App Service – Emplacements|microsoft.web/sites/slots|properties.defaultHostName|


- **Créer des procédures de correction :**
    - Lorsque des entrées DNS non résolues sont trouvées, votre équipe doit déterminer si une compromission s’est produite.
    - Recherchez la raison pour laquelle l’adresse n’a pas été redirigée lorsque la ressource a été retirée.
    - Supprimez l’enregistrement DNS s’il n’est plus utilisé ou faites-le pointer vers la bonne ressource (FQDN) Azure détenue par votre organisation.
 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les services connexes et les fonctionnalités Azure que vous pouvez utiliser pour vous défendre contre l’acquisition de sous-domaine, consultez les pages suivantes.

- [Azure DNS prend en charge l’utilisation d’enregistrements d’alias pour les domaines personnalisés](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Recours à l’ID de vérification du domaine lors de l’ajout de domaines personnalisés dans Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Démarrage rapide : Exécution d’une requête Resource Graph à l’aide d’Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
