---
title: Tutoriel - Ajouter un domaine personnalisé à votre point de terminaison Azure CDN | Microsoft Docs
description: Dans ce tutoriel, vous mappez le contenu du point de terminaison Azure CDN à un domaine personnalisé.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: f66aaa23a631bd71494587683aab87a74a5aef20
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261268"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Tutoriel : Ajouter un domaine personnalisé à votre point de terminaison Azure CDN
Ce didacticiel montre comment ajouter un domaine personnalisé à un point de terminaison de réseau de distribution de contenu (CDN) Azure. Lorsque vous utilisez un point de terminaison CDN pour diffuser du contenu, un domaine personnalisé est nécessaire si vous souhaitez que votre nom de domaine soit visible dans l’URL du CDN. Un nom de domaine visible peut être pratique pour vos clients et utile à des fins de personnalisation. 

Après avoir créé un point de terminaison CDN dans votre profil, le nom de point de terminaison, qui est un sous-domaine de azureedge.net, est inclus dans l’URL de diffusion de contenu CDN par défaut (par exemple, https :\//contoso.azureedge.net/photo.png). Par commodité, Azure CDN offre la possibilité d’associer un domaine personnalisé à un point de terminaison. Avec cette option, vous distribuez votre contenu avec un domaine personnalisé dans votre URL au lieu d’un nom de point de terminaison (par exemple, https :\//www.contoso.com/photo.png). 

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> - Créez un enregistrement CNAME DNS.
> - Associez le domaine personnalisé à votre point de terminaison CDN.
> - Vérifiez le domaine personnalisé.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant d’effectuer les étapes de ce tutoriel, vous devez d’abord créer un profil CDN et au moins un point de terminaison CDN. Pour plus d’informations, consultez la page[Prise en main d’Azure CDN](cdn-create-new-endpoint.md).

Si vous ne disposez pas déjà d’un domaine personnalisé, vous devez tout d’abord en acheter un auprès d’un fournisseur de domaine. Par exemple, consultez [Acheter un nom de domaine personnalisé](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).

Si vous utilisez Azure pour héberger vos [domaines DNS](https://docs.microsoft.com/azure/dns/dns-overview), vous devez déléguer le DNS (Domain Name System) du fournisseur de domaine à un DNS Azure. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Sinon, si vous utilisez un fournisseur de domaine pour gérer votre domaine DNS, passez à [Créer un enregistrement CNAME DNS](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Créer un enregistrement CNAME DNS

Avant de pouvoir utiliser un domaine personnalisé avec un point de terminaison Azure CDN, vous devez créer un enregistrement de nom canonique (CNAME) avec votre fournisseur de domaine pointant vers votre point de terminaison CDN. Un enregistrement CNAME est un type de fonctionnalité DNS (Domain Name System) qui mappe un nom de domaine source à un nom de domaine de destination. Pour Azure CDN, le domaine source est votre domaine personnalisé et le domaine de destination est votre point de terminaison CDN. Une fois qu’Azure CDN a vérifié l’enregistrement CNAME que vous avez créé, le trafic adressé au domaine source personnalisé (tel que www.contoso.com) est acheminé vers le nom d’hôte du point de terminaison CDN de la destination spécifiée (par exemple, contoso.azureedge.net). 

Un domaine personnalisé et son sous-domaine peuvent être associés à un seul point de terminaison à la fois. Toutefois, vous pouvez utiliser différents sous-domaines du même domaine personnalisé pour différents points de terminaison de service Azure en utilisant plusieurs enregistrements CNAME. Vous pouvez également mapper un domaine personnalisé ayant différents sous-domaines au même point de terminaison CDN.


## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapper le sous-domaine temporaire cdnverify

Lorsque vous mappez un domaine existant qui est en production, plusieurs points demandent une attention particulière. Lorsque vous inscrivez votre domaine personnalisé dans le Portail Azure, il se peut que le domaine connaisse un court temps d’arrêt. Pour éviter l’interruption du trafic web, vous devez tout d’abord mapper votre domaine personnalisé à votre nom d’hôte du point de terminaison CDN avec le sous-domaine cdnverify Azure afin de créer un mappage CNAME temporaire. Avec cette méthode, les utilisateurs peuvent accéder à votre domaine sans interruption pendant que le mappage DNS est en cours. 

Autrement, si vous utilisez votre domaine personnalisé pour la première fois et qu’aucun trafic de production n’est en cours d’exécution sur ce dernier, vous pouvez mapper directement votre domaine personnalisé à votre point de terminaison CDN. Passez à [Mapper le domaine personnalisé permanent](#map-permanent-custom-domain).

Pour créer un enregistrement CNAME avec le sous-domaine cdnverify :

1. Connectez-vous au site web du fournisseur de domaine pour votre domaine personnalisé.

2. Recherchez la page permettant de gérer les enregistrements DNS en consultant la documentation du fournisseur ou en recherchant les sections du site web intitulées **Nom de domaine**, **DNS** ou **Gestion des noms de serveur**. 

3. Créez une entrée d’enregistrement CNAME pour votre domaine personnalisé et renseignez les champs comme indiqué dans le tableau suivant (les noms de champs peuvent varier) :

    | Source                    | type  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.NET |

    - Source : entrez votre nom de domaine personnalisé, y compris le sous-domaine cdnverify, au format suivant : cdnverify. _&lt;nom de domaine personnalisé&gt;. Par exemple, cdnverify.www.contoso.com.

    - Type : entrez *CNAME*.

    - Destination : entrez votre nom d’hôte du point de terminaison CDN, y compris le sous-domaine cdnverify, au format suivant : cdnverify. _&lt;nom de point de terminaison&gt;_.azureedge.net. Par exemple, cdnverify.contoso.azureedge.NET.

4. Enregistrez vos modifications.

Par exemple, la procédure pour le bureau d’enregistrement de domaines GoDaddy est la suivante :

1. Connectez-vous et sélectionnez le domaine personnalisé que vous souhaitez utiliser.

2. Dans la section Domaines, sélectionnez **Gérer tous**, puis sélectionnez **DNS** | **Gérer les zones**.

3. Pour le **Nom de domaine**, entrez votre domaine personnalisé, puis sélectionnez **Rechercher**.

4. À partir de la page **Gestion DNS**, sélectionnez **Ajouter**, puis sélectionnez **CNAME** dans la liste **Type**.

5. Renseignez les champs suivants de l’entrée CNAME :

    ![Entrée CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Type : laissez *CNAME* sélectionné.

    - Hôte : entrez le sous-domaine de votre domaine personnalisé à utiliser, y compris le nom de sous-domaine cdnverify. Par exemple, cdnverify.www.

    - Pointe vers : entrez le nom d’hôte de votre point de terminaison CDN, y compris le nom de sous-domaine cdnverify. Par exemple, cdnverify.contoso.azureedge.NET. 

    - Durée de vie (TTL) : laissez *1 heure* sélectionné.

6. Sélectionnez **Enregistrer**.
 
    L’entrée de l’enregistrement CNAME est ajoutée à la table des enregistrements DNS.

    ![Table des enregistrements DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associer le domaine personnalisé à votre point de terminaison CDN

Une fois que vous avez enregistré votre domaine personnalisé, vous pouvez l’ajouter à votre point de terminaison CDN. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/), puis accédez au profil CDN contenant le point de terminaison que vous souhaitez mapper à un domaine personnalisé.
    
2. Sur la page **Profil CDN**, sélectionnez le point de terminaison CDN à associer avec le domaine personnalisé.

   La page **Point de terminaison** s’ouvre.
    
3. Sélectionnez **Noms de domaine personnalisés**. 

   ![Bouton Domaine CDN personnalisé](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   La page **Ajouter un domaine personnalisé** s’ouvre.

4. Pour **Nom d’hôte du point de terminaison**, le nom d’hôte du point de terminaison à utiliser comme domaine de destination de votre enregistrement CNAME est prérempli et dérivé de l’URL du point de terminaison CDN : *&lt;nom d’hôte du point de terminaison&gt;*.azureedge.net. Cette valeur n’est pas modifiable.

5. Pour **Nom d’hôte personnalisé**, entrez votre domaine personnalisé, y compris le sous-domaine, à utiliser comme domaine source de votre enregistrement CNAME. Par exemple, www.contoso.com ou cdn.contoso.com. N’utilisez pas le nom de sous-domaine cdnverify.

   ![Boîte de dialogue Domaine CDN personnalisé](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Sélectionnez **Ajouter**.

   Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine personnalisé que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé sera validé. 

   La propagation des nouveaux paramètres de domaine personnalisé sur tous les nœuds de périphérie CDN peut prendre un certain temps : 
    - Pour les profils du **CDN Azure Standard fourni par Microsoft**, la propagation s’effectue généralement dans un délai de 10 minutes. 
    - Pour les profils du **CDN Azure Standard fourni par Akamai**, la propagation s’effectue généralement dans un délai d’une minute. 
    - Dans le cas des profils du **CDN Azure Standard fourni par Verizon** et du **CDN Azure Premium fourni par Verizon**, la propagation s’effectue généralement dans un délai de 10 minutes.   


## <a name="verify-the-custom-domain"></a>Vérifier le domaine personnalisé

Une fois que vous avez terminé l’inscription de votre domaine personnalisé, vérifiez qu’il référence votre point de terminaison CDN.
 
1. Assurez-vous d’avoir un contenu public qui est mis en cache au point de terminaison. Par exemple, si votre point de terminaison CDN est associé à un compte de stockage, Azure CDN met en cache le contenu dans un conteneur public. Pour tester le domaine personnalisé, assurez-vous que votre conteneur est configuré pour autoriser l’accès public et qu’il contient au moins un fichier.

2. Dans votre navigateur, accédez à l’adresse du fichier à l’aide du domaine personnalisé. Par exemple, si votre domaine personnalisé est cdn.contoso.com, l’URL vers l’objet mis en cache sera similaire à l’URL suivante : http:\//cdn.contoso.com/my-public-container/my-file.jpg. Vérifiez que le résultat est identique au résultat que vous obtenez lorsque vous accédez au point de terminaison CDN directement à *&lt;nom d’hôte du point de terminaison&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mapper le domaine personnalisé permanent

Si vous avez vérifié que le sous-domaine cdnverify a été correctement mappé à votre point de terminaison (ou si vous utilisez un nouveau domaine personnalisé qui n’est pas en production), vous pouvez ensuite mapper le domaine personnalisé directement à votre nom d’hôte du point de terminaison CDN.

Pour créer un enregistrement CNAME pour votre domaine personnalisé :

1. Connectez-vous au site web du fournisseur de domaine pour votre domaine personnalisé.

2. Recherchez la page permettant de gérer les enregistrements DNS en consultant la documentation du fournisseur ou en recherchant les sections du site web intitulées **Nom de domaine**, **DNS** ou **Gestion des noms de serveur**. 

3. Créez une entrée d’enregistrement CNAME pour votre domaine personnalisé et renseignez les champs comme indiqué dans le tableau suivant (les noms de champs peuvent varier) :

    | Source          | type  | Destination           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Source : entrez votre nom de domaine personnalisé (par exemple, www.contoso.com).

    - Type : entrez *CNAME*.

    - Destination : entrez votre nom d’hôte du point de terminaison CDN. Il doit être au format suivant :_&lt;nom de point de terminaison&gt;_. azureedge.net. Par exemple, contoso.azureedge.net.

4. Enregistrez vos modifications.

5. Si vous avez déjà créé un enregistrement temporaire CNAME du sous-domaine cdnverify, supprimez-le. 

6. Si vous utilisez ce domaine personnalisé en production pour la première fois, suivez les étapes pour [Associer le domaine personnalisé à votre point de terminaison CDN](#associate-the-custom-domain-with-your-cdn-endpoint) et [Vérifier le domaine personnalisé](#verify-the-custom-domain).

Par exemple, la procédure pour le bureau d’enregistrement de domaines GoDaddy est la suivante :

1. Connectez-vous et sélectionnez le domaine personnalisé que vous souhaitez utiliser.

2. Dans la section Domaines, sélectionnez **Gérer tous**, puis sélectionnez **DNS** | **Gérer les zones**.

3. Pour le **Nom de domaine**, entrez votre domaine personnalisé, puis sélectionnez **Rechercher**.

4. À partir de la page **Gestion DNS**, sélectionnez **Ajouter**, puis sélectionnez **CNAME** dans la liste **Type**.

5. Renseignez les champs de l’entrée CNAME :

    ![Entrée CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Type : laissez *CNAME* sélectionné.

    - Hôte : Entrez le sous-domaine de votre domaine personnalisé à utiliser. Par exemple, www ou cdn.

    - Pointe vers : entrez le nom d’hôte de votre point de terminaison CDN. Par exemple, contoso.azureedge.net. 

    - Durée de vie (TTL) : laissez *1 heure* sélectionné.

6. Sélectionnez **Enregistrer**.
 
    L’entrée de l’enregistrement CNAME est ajoutée à la table des enregistrements DNS.

    ![Table des enregistrements DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Si vous avez un enregistrement CNAME cdnverify, sélectionnez l’icône crayon en regard de celui-ci, puis sélectionnez l’icône corbeille.

8. Sélectionnez **Supprimer** pour supprimer l’enregistrement CNAME.


## <a name="clean-up-resources"></a>Supprimer des ressources

Dans les étapes précédentes, vous avez ajouté un domaine personnalisé à un point de terminaison CDN. Si vous ne voulez plus associer votre point de terminaison avec un domaine personnalisé, vous pouvez supprimer le domaine personnalisé en procédant comme suit :
 
1. Dans votre profil CDN, sélectionnez le point de terminaison avec le domaine personnalisé que vous souhaitez supprimer.

2. À partir de la page **Point de terminaison**, sous Domaines personnalisés, cliquez sur le domaine personnalisé que vous souhaitez supprimer, puis sélectionnez **Supprimer** dans le menu contextuel.  

   Le domaine personnalisé est dissocié de votre point de terminaison.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> - Créez un enregistrement CNAME DNS.
> - Associez le domaine personnalisé à votre point de terminaison CDN.
> - Vérifiez le domaine personnalisé.

Passer au tutoriel suivant pour apprendre à configurer HTTPS sur un domaine personnalisé Azure CDN.

> [!div class="nextstepaction"]
> [Didacticiel : configurer HTTPS sur un domaine personnalisé Azure CDN](cdn-custom-ssl.md)


