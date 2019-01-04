---
title: Mise en cache avec Azure Front Door Service | Microsoft Docs
description: Cet article vous aide à comprendre comment Azure Front Door Service supervise l’intégrité de vos backends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 97c02726c7e359195c6bf4ea793404562f2acccf
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001930"
---
# <a name="caching-with-azure-front-door-service"></a>Mise en cache avec Azure Front Door Service
Le document suivant explique comment spécifier le comportement d’une porte d’entrée à l’aide de règles de routage ayant la mise en cache activée.

## <a name="delivery-of-large-files"></a>Distribution de fichiers volumineux
Azure Front Door Service permet de distribuer des fichiers volumineux sans aucune limite de taille de fichier. Front Door utilise une technique appelée segmentation d’objets. Quand un fichier volumineux est demandé, la porte d’entrée récupère les petits éléments du fichier auprès du backend. Après avoir reçu une demande de fichier complet ou à plage d’octets, un environnement de porte d’entrée demande le fichier au backend par blocs de 8 Mo.

</br>Dès lors qu’un bloc parvient à l’environnement de porte d’entrée, il est mis en cache et immédiatement remis à l’utilisateur. La porte d’entrée se prépare ensuite à récupérer le bloc suivant en parallèle. Cette prérécupération est l’assurance que le contenu a un bloc d’avance sur l’utilisateur, ce qui réduit la latence. Ce processus se poursuit jusqu'à ce que le fichier entier soit téléchargé (si nécessaire), que toutes les plages d’octets soient disponibles (si nécessaire), ou que le client mette fin à la connexion.

</br>Pour plus d’informations sur la demande de plage d’octets, consultez [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
La porte d’entrée met en cache les blocs à mesure qu’elle les récupère, ce qui évite d’avoir à mettre l’intégralité du fichier dans le cache Front Door. Les demandes suivantes concernant le fichier ou les plages d’octets sont traitées à partir du cache. Si certains blocs ne sont pas mis en cache, ils sont demandés au backend via une prérécupération. Cette optimisation repose sur la capacité du backend à prendre en charge les demandes de plage d’octets. À défaut, cette optimisation n’est pas efficace.

## <a name="file-compression"></a>Compression de fichiers
Front Door peut compresser dynamiquement le contenu en périphérie, ce qui a pour effet de réduire la taille et le délai de la réponse envoyée à vos clients. Tous les fichiers peuvent faire l’objet d’une compression. Cependant, un fichier doit être d’un type MIME éligible à la liste de compression. À l’heure actuelle, Front Door n’autorise pas la modification de cette liste. Voici la liste actuelle :</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Par ailleurs, la taille du fichier doit être comprise entre 1 Ko et 8 Mo (inclus).

Ces profils prennent en charge les encodages de compression suivants :
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Si une demande prend en charge la compression gzip et Brotli, la compression Brotli est prioritaire.</br>
Quand une demande portant sur un élément multimédia spécifie une compression et qu’il en résulte une absence dans le cache, Front Door compresse l’élément multimédia directement sur le serveur POP. Ensuite, le fichier compressé est servi à partir du cache. L’élément qui en résulte est retourné avec un encodage de transfert : segmenté.

## <a name="query-string-behavior"></a>Comportement des chaînes de requête
Avec Front Door, vous pouvez contrôler la manière dont les fichiers sont mis en cache pour une requête web qui contient une chaîne de requête. Dans une requête web contenant une chaîne de requête, la chaîne de requête représente la partie de la demande qui apparaît après le point d’interrogation (?). Une chaîne de requête peut contenir une ou plusieurs paires clé-valeur où le nom du champ et sa valeur sont séparés par un signe égal (=). Chaque paire clé-valeur est séparée par une esperluette (&). Par exemple : http://www.contoso.com/content.mov?field1=value1&field2=value2. S’il existe plusieurs paires clé-valeur dans la chaîne de requête d’une demande, leur ordre n’a pas d’importance.
- **Ignorer les chaînes de requête** : Mode par défaut. Dans ce mode, Front Door transmet les chaînes de requête du demandeur au backend à la première demande et met en cache l’élément multimédia. Toutes les demandes suivantes portant sur l’élément multimédia, qui sont traitées par l’environnement de porte d’entrée, ignorent les chaînes de requête tant que l’élément multimédia mis en cache n’est pas arrivé à expiration.

- **Mettre en cache chaque URL unique** : dans ce mode, chaque demande contenant une URL unique, y compris la chaîne de requête, est traitée comme une ressource unique avec son propre cache. Par exemple, la réponse du backend à une demande pour `www.example.ashx?q=test1` est mise en cache dans l’environnement de porte d’entrée et retournée pour les mises en cache suivantes avec la même chaîne de requête. Une demande pour `www.example.ashx?q=test2` est mise en cache en tant que ressource distincte avec son propre paramètre de durée de vie.

## <a name="cache-purge"></a>Vidage du cache
Front Door met en cache les éléments multimédias tant que leur durée de vie (TTL) n’est pas arrivée à expiration. Une fois que la durée de vie d’un élément multimédia est arrivée à expiration, quand un client demande cet élément multimédia, l’environnement de porte d’entrée en récupère une nouvelle copie mise à jour pour traiter la demande du client et actualiser le cache.
</br>La bonne pratique pour vous assurer que vos utilisateurs obtiennent toujours la dernière copie de vos éléments multimédia consiste à établir une version de vos ressources pour chaque mise à jour et à les publier en tant que nouvelles URL. Front Door récupère immédiatement les nouveaux éléments multimédias pour les demandes suivantes des clients. Il est parfois souhaitable de vider le contenu mis en cache sur tous les nœuds de périmètre et de tous les forcer à récupérer de nouveaux éléments multimédias mis à jour. Ce besoin peut être dû à des mises à jour de votre application web, ou à la nécessité de mettre à jour rapidement les éléments multimédias qui contiennent des informations incorrectes.

</br>Sélectionnez les éléments multimédias que vous souhaitez vider sur les nœuds de périmètre. Si vous souhaitez effacer tous les éléments multimédias, cochez la case Vider tout. Sinon, tapez le chemin de chaque élément multimédia que vous souhaitez vider dans la zone de texte Chemin d’accès. Les formats suivants sont pris en charge dans le chemin d’accès.
1. **Vidage d’URL unique** : videz un élément multimédia individuel en spécifiant l’URL complète, avec l’extension de fichier, par exemple /pictures/strasbourg.png;
2. **Vidage de caractère générique** : l’astérisque (\*) peut être utilisé comme caractère générique. Videz tous les dossiers, sous-dossiers et fichiers dans un point de terminaison avec /\* dans le chemin ou videz tous les sous-dossiers et fichiers dans un dossier spécifique en spécifiant le dossier suivi de /\*, par exemple /pictures/\*.
3. **Vidage du domaine racine** : videz la racine du point de terminaison avec « / » dans le chemin d’accès.

Les vidages du cache dans la porte d’entrée ne respectent pas la casse. Par ailleurs, ils ne prennent pas en compte les chaînes de requête, ce qui signifie que le vidage d’une URL n’a pas pour effet de vider pas toutes ses variantes constituées de chaînes de requête. 

## <a name="cache-expiration"></a>Expiration du cache
L’ordre suivant des en-têtes sert à déterminer la durée de stockage d’un élément dans le cache :</br>
1. Cache-Control: s-maxage=<seconds>
2. Cache-Control: maxage=<seconds>
3. Expires: <http-date>

Les en-têtes de réponse Cache-Control qui indiquent que la réponse n’est pas mise en cache comme Cache-Control: private, Cache-Control: no cache et Cache-Control: no-store sont honorés. Cependant, s’il existe plusieurs demandes en cours au niveau d’un point de présence pour la même URL, il se peut qu’elles partagent la réponse.


## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de demande suivants ne sont pas transférés à un backend quand la mise en cache est utilisée.
- Authorization
- Content-Length
- Transfer-Encoding

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).