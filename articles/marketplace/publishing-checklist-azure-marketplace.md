---
title: Check-list de publication pour la Place de marché Azure | Azure
description: Check-list de publication pour la Place de marché Azure à l’aide du Portail Cloud Partner.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 26041bbf392f9b2afaaf5af38f758deb7708b651
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734981"
---
# <a name="publishing-checklist-for-azure-marketplace"></a>Check-list de publication pour la Place de marché Azure    
Passez en revue les composants requis avant de commencer le processus de publication.  

Les artefacts suivants sont nécessaires au workflow de création d’une offre dans le Portail Cloud Partner.  

## <a name="checklist"></a>Liste de contrôle  

| Type de liste | Type d’offre | Publication d’artefact |   
|:--- |:--- |:--- |  
| Tous | Tous | <table> <tr><th>Informations sur les vitrines</th></tr> <tr><td>Nom de l’offre (200 caractères)</td></tr> <tr><td>Description (2 000 caractères)</td></tr> <tr><td>ID MPN</td></tr> <tr><td>Disponibilité par pays/région</td></tr> <tr><td>Secteurs, catégories et mots clés de recherche applicables</td></tr> <tr><td>Captures d’écran (1 280 x 720, 5 maximum)</td></tr> <tr><td>Documentation marketing (3 maximum)</td></tr> <tr><td>Destination du prospect</td></tr> <tr><td>Vidéo de présentation du produit (facultatif)</td></tr> </table> <table> <tr><th>Contacts</th></tr> <tr><td>Coordonnées (support, ingénieurs, commerciaux)</td></tr> </table> <table> <tr><th>Informations techniques</th></tr> <tr><td>URL des conditions d’utilisation et de la politique de confidentialité</td></tr> </table> <table> <tr><th>Version d’évaluation</th></tr> <tr><td>Nom du groupe de ressources Azure</td></tr> </table> |  
| Tous | Machine virtuelle | <table> <tr><th>Informations techniques</th></tr> <tr><td>URL du support technique</td></tr> </table> |
| Liste | Service de conseil | <table> <tr><th>Informations sur les vitrines</th></tr> <tr><td>Durée de l’engagement</td></tr> <tr><td>Logos d’entreprise (48 x 48, 216 x 216)</td></tr> </table> |  
| Version d’évaluation | Tous | <table> <tr><th>Informations techniques</th></tr> <tr><td>URL de la version d’essai</td></tr> <tr><td>Langues prises en charge</td></tr> <tr><td>Numéro de version de l’application</td></tr> <tr><td>Date de sortie de l’application</td></tr> <tr><td>URL du support technique</td></tr> </table> |  
| Version d’évaluation | Test drive | <table> <tr><th>Version d’évaluation</th></tr> <tr><td>Description</td></tr> <tr><td>Duration</td></tr> <tr><td>Manuel de l’utilisateur</td></tr> <tr><td>Vidéo de test drive (1 maximum)</td></tr> <tr><td>Disponibilité par pays/région du test drive</td></tr> <tr><td>ID d’abonnement Azure</td></tr> <tr><td>ID de locataire Azure AD</td></tr> <tr><td>ID de l’application Azure AD</td></tr> <tr><td>Clé d’application Azure AD</td></tr> </table> |  
| Transaction | Machine virtuelle | <table> <tr><th>Vitrines</th></tr> <tr><td>Titre (50 caractères)</td></tr> <tr><td>Résumé (200 caractères)</td></tr> <tr><td>Résumé long (256 caractères)</td></tr> <tr><td>Description HTML (3 000 caractères)</td></tr> <tr><td>Logos d’entreprise (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Informations sur le système d’exploitation</td></tr> <tr><td>Ports utilisés</td></tr> <tr><td>Protocoles utilisés</td></tr> <tr><td>Version de chaque disque dur virtuel utilisé</td></tr> <tr><td>URL SAS de chaque disque dur virtuel utilisé</td></tr> </table> |  
| Transaction | Applications Azure : Modèle de solution | <table> <tr><th>Vitrines</th></tr> <tr><td>Titre (50 caractères)</td></tr> <tr><td>Résumé (200 caractères)</td></tr> <tr><td>Résumé long (256 caractères)</td></tr> <tr><td>Description HTML (3 000 caractères)</td></tr> <tr><td>Logos d’entreprise (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Numéro de version</td></tr> <tr><td>Fichier de package qui contient<ul> <li>tous les fichiers du modèle</li> <li>le fichier createUIDefinition</li> </ul> </td></tr> </table> |  
| Transaction | Applications Azure : application gérée | <table> <tr><th>Vitrines</th></tr> <tr><td>Titre (50 caractères)</td></tr> <tr><td>Résumé (200 caractères)</td></tr> <tr><td>Résumé long (256 caractères)</td></tr> <tr><td>Description HTML (3 000 caractères)</td></tr> <tr><td>Logos d’entreprise (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Numéro de version</td></tr> <tr><td>Fichier de package qui contient<ul> <li>tous les fichiers du modèle</li> <li>le fichier createUIDefinition</li> </ul> </td></tr> </table> |  
| Transaction | Conteneur | <table> <tr><th>Vitrines</th></tr> <tr><td>Titre (50 caractères)</td></tr> <tr><td>Résumé (200 caractères)</td></tr> <tr><td>Résumé long (256 caractères)</td></tr> <tr><td>Description HTML (3 000 caractères)</td></tr> <tr><td>Logos d’entreprise (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Détails du référentiel Azure Container Registry (ACR) Image : Identifiant d’abonnement</td></tr> <tr><td>Détails du référentiel ACR Image : Nom de groupe ressources</td></tr> <tr><td>Détails du référentiel ACR Image : Nom du registre</td></tr> <tr><td>Détails du référentiel ACR Image : Nom du dépôt</td></tr> <tr><td>Détails du référentiel ACR Image : Nom d'utilisateur</td></tr> <tr><td>Détails du référentiel ACR Image : Mot de passe</td></tr> <tr><td>Détails du référentiel ACR Image : Balises d’image (facultatifs)</td></tr> </table> |  
| Transaction | Application SaaS | <table> <tr><th>Vitrines</th></tr> <tr><td>Titre (50 caractères)</td></tr> <tr><td>Résumé (200 caractères)</td></tr> <tr><td>Résumé long (256 caractères)</td></tr> <tr><td>Description HTML (3 000 caractères)</td></tr> <tr><td>Logos d’entreprise (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)</td></tr> </table> |  

## <a name="next-steps"></a>Étapes suivantes
*   Consultez la page [Guide de l’éditeur Place de marché Azure et AppSource](./marketplace-publishers-guide.md).  
 
---
