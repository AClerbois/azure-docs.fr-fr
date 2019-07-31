---
title: Liste de contrôle de création d’offre – Place de marché Azure commerciale pour Azure
description: Détails que vous pouvez fournir dans le cadre du processus de création d’offre – Place de marché commerciale pour Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701153"
---
# <a name="offer-creation-checklist"></a>Liste de vérification de la création d’offre

Le processus de création d’offre vous amène à visiter plusieurs pages. Voici les détails que vous pouvez fournir sur chacune d’elles, avec des liens pour en apprendre davantage plus sur chaque élément.

Les éléments que vous devez fournir ou spécifier sont indiqués ci-dessous. Certaines zones sont facultatives ou comportent des valeurs par défaut que vous pouvez modifier à votre guise. Vous n’êtes pas obligé de travailler sur ces sections dans l’ordre indiqué ici.

| **Item**    | **Objectif**  |
| :---------- | :-------------------|
| [**Modal de nouvelle offre**](#new-offer-modal) | Collecte des informations d’identité liées à l’offre.  |
| [Page Configuration de l’offre](#offer-setup-page) | Optez pour l’utilisation des fonctionnalités clés et choisissez comment vendre votre offre via Microsoft.  |
| [Page Propriétés](#properties-page) | Définissez les catégories et secteurs utilisés pour grouper votre offre en fonction des places de marché, des contrats légaux associés, et de la version de votre application. |
| [Page Référencement de l’offre](#offer-listing-page) | Définissez les détails de l’offre à afficher sur la Place de marché, dont des descriptions de votre offre et de ses composants marketing. |
| [Page Préversion](#preview-page) | Définissez un Public de préversion limité avant de publier votre offre pour public plus important. |
| [Page Configuration technique de l’offre](#technical-configuration-page)  | Disponible uniquement si vous choisissez de vendre l’offre via Microsoft. Définissez les détails techniques (chemin d’URL, Webhook, ID de locataire et ID d’application) utilisés pour la connexion à votre offre. |
| [**Modal de nouveau plan**](#plan-identity-modal) | Collecte des informations sur l’identité du plan.  |
| [Page Référencement du plan](#plan-listing-page)  | Disponible uniquement si vous choisissez de vendre l’offre via Microsoft. Définissez les détails utilisés pour référencer le plan sur la place de marché.  |
| [Page Tarification et disponibilité du plan](#plan-pricing--availability-page)  | Disponible uniquement si vous choisissez de vendre l’offre via Microsoft.  Recueille les caractéristiques de l’entreprise (modèle de tarification), l’audience et la disponibilité du marché pour chaque forfait (version) de votre offre.  |
| [Page Référencement de version d’évaluation](#test-drive-listing-page)  | Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails utilisés pour référencer la version d’évaluation sur la Place de marché.  |
| Page Configuration technique de la version d’évaluation  | Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails techniques de la démonstration (ou « version d’évaluation ») qui permettra à des clients d’essayer votre offre avant de s’engager à l’acheter.  |
| [Page Passer en revue et publier](#review-and-publish-page)  | Sélectionnez les modifications que vous souhaitez publier, affichez l’état de chaque page et ajoutez des notes à l’adresse de l’équipe de certification.  |


## <a name="new-offer-modal"></a>Modal de nouvelle offre 

Les premiers éléments d’informations que vous serez invité à fournir sont un ID et un alias pour votre offre. 

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID de l’offre  | Obligatoire, ne peut pas être modifié après la création. Maximum 50 caractères alphanumériques minuscules, tirets ou traits de soulignement. |
| Alias de l’offre  | Requis. |

## <a name="offer-setup-page"></a>Page de configuration de l’offre

La page de configuration de l’offre vous permet de choisir différents canaux et propositions de vente, ainsi que de déclarer l’utilisation de fonctionnalités clés, telles que des versions d’évaluation et des prospects. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Voulez-vous vendre via Microsoft ?  | Requis. Valeur par défaut : OUI |
| Comment voulez-vous que des clients potentiels interagissent avec le référencement de l’offre ? (Invite à l’action)  | Obligatoire en cas de vente sans passer par Microsoft. Valeur par défaut : Essai gratuit, Options : « Télécharger maintenant », « Essai gratuit », « Me contacter ». |
| URL de la version d’essai  | Obligatoire si « Essai gratuit » est sélectionné comme façon dont les clients doivent interagir avec le référencement de l’offre. |
| URL de l’offre  | Obligatoire si « Obtenir maintenant » est sélectionné comme façon dont les clients doivent interagir avec le référencement de l’offre. |
| Canaux  | facultatif. Valeur par défaut : Pas activé dans le canal CSP (revendeur).  |
| Version d’évaluation | facultatif. Valeur par défaut : Aucune version d’évaluation activée.  |
| Type de version d’évaluation | Obligatoire version d’évaluation activée. Valeur par défaut : Non sélectionné. Options : Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic Apps, Power BI.  |
| Gestion des prospects – Se connecter à un système CRM | Obligatoire en cas de vente via Microsoft ou de référencement des offres via un lien « Me contacter ». Par défaut : aucun système CRM connecté. Options CRM : Azure table, Azure blob, Dynamics CRM Online, Point de terminaison HTTPS, Marketo, Salesforce  |

## <a name="properties-page"></a>Page Propriétés

La page de propriétés est l’emplacement où vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les places de marché, les contrats légaux associés, et la version de votre application. Veillez à fournir des détails complets et précis sur votre offre dans cette page, afin qu’elle soit correctement affichée et proposée aux clients appropriés. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Catégorie et sous-catégorie | Obligatoire 1 et maximum 3. Valeur par défaut : Aucune sélectionnée. |
| Secteurs et sous-secteurs | facultatif. 2 secteurs L1 et au maximum 2 sous-secteurs dans chaque secteur L1. Valeur par défaut : Aucune sélectionnée |
| Version de l’application  | facultatif. Valeur par défaut : Aucune. |
| Utiliser le contrat Standard  | facultatif. Par défaut : non sélectionné.  | |
| Conditions d’utilisation  | Obligatoire si le contrat Standard n’est pas sélectionné.  |

## <a name="offer-listing-page"></a>Page de référencement de l’offre

La page de référencement vous permet de fournir le texte et les images que les clients voient quand ils affichent le référencement de votre offre sur la Place de marché. 

| **Nom du champ**    | **Remarques**   |
| :---------------- | :-----------| 
| Nom  | Obligatoire, maximum 50 caractères. |
| Résumé  | Obligatoire, maximum 100 caractères. | 
| Description  | Obligatoire, maximum 3 000 caractères. |
| Instructions de prise en main  | Obligatoire, maximum 3 000 caractères. |
| Instructions de prise en main  | Obligatoire, maximum 3 000 caractères. |
| Mots clés de recherche  | Facultatif, recommandé, maximum 3 mots clés. |
| URL de la politique de confidentialité  | Requis. |
| URL des Ressources Marketing du programme CSP  | facultatif. |
| Titre + URL de liens utiles  | facultatif. |
| Titre + Fichier de documents associés  | Obligatoire, min. 1 et max. 3. Doit être au format de fichier PDF. |
| Captures d’écran.  | Obligatoire, min. 1 et max. 5 captures d’écran ; min. quatre recommandées. Doit être au format PNG 1280 X 720. |
| Logos du Store (Petit, Moyen, Grand, Large, Bannière)  | Petit (48 X 48) et Grand (216 X 216) obligatoires. Les autres tailles sont facultatives mais recommandées : Moyen (90 x 90), Large (255 x 115), Bannière (815 x 290). Le logo doit être au format PNG |
| Nom + URL + miniature des vidéos  | Facultatif, recommandé, max. 4 vidéos. La miniature doit être au format PNG 1280 x 720. La vidéo doit être hébergée sur YouTube ou Vimeo. |
| Contacts (Programme Fournisseur de solutions Microsoft Cloud (CSP), Ingénierie, Support)  | Contacts Ingénierie et Support obligatoires (nom, e-mail et le numéro de téléphone) ; Contact Programme CSP facultatif mais recommandé. |
| URL du support technique  | Requis. |

## <a name="preview-page"></a>Page de préversion

La page de préversion vous permet de spécifier le public ayant accès à la préversion de votre offre afin de vérifier que celle-ci répond à toutes vos exigences avant sa mise en ligne. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| E-mail + description AAD/MSA | Obligatoire, min. 1 et max. 10 en cas d’entrée manuelle, ou jusqu’à 20 e cas de chargement de fichier CSV. |

## <a name="technical-configuration-page"></a>Page de configuration technique 

La page de configuration technique est celle où vous spécifiez les détails techniques utilisés par Microsoft pour se connecter à votre offre. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| URL de la page d’accueil | Obligatoire en cas de vente via Microsoft. |
| Webhook de connexion | Obligatoire en cas de vente via Microsoft. |
| ID de locataire Azure AD | Obligatoire en cas de vente via Microsoft. |
| ID de l’application Azure AD | Obligatoire en cas de vente via Microsoft. |

## <a name="plan-identity-modal"></a>Modal d’identité de plan

Les premiers éléments d’informations que vous êtes invité à fournir sont un nom et un identifiant pour votre plan. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID du plan  | Obligatoire en cas de vente via Microsoft. Ne peut pas être modifié une fois créé. Maximum 50 caractères alphanumériques minuscules, tirets ou traits de soulignement. |
| Nom du plan  | Obligatoire en cas de vente via Microsoft. Doit être unique pour tous les plans dans l’offre. 50 caractères maximum. |

## <a name="plan-listing-page"></a>Page de référencement du plan

La page de référencement du plan est celle dans laquelle vous fournissez le texte que les clients peuvent voir quand ils consultent le plan sur la Place de marché. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| Description du plan   | Obligatoire en cas de vente via Microsoft. Maximum 500 caractères. | |

## <a name="plan-pricing--availability-page"></a>Page de tarification et disponibilité du plan

La page de tarification et disponibilité de plan est celle dans laquelle vous définissez les caractéristiques de l’entreprise, l’audience et la disponibilité du marché pour chaque forfait (version) de votre offre. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Disponibilité sur le marché  | Obligatoire, min. 1 et max. 141. |
| Modèle de tarification  | Requis. Valeur par défaut : Taux fixe. Options : Tarification fixe, par utilisateur. |
| Minimum et maximum de sièges  | Facultatif, disponible uniquement si le modèle de tarification basée sur ls sièges et sélectionné. |
| Fréquence de facturation  | Requis. Valeur par défaut : Mensuelle: Options : Mensuelle, Annuelle. |
| Prix  | Obligatoire, USD par mois, si la fréquence de facturation sélectionnée est mensuelle, ou USD par an si la fréquence de facturation sélectionnée est annuelle. |
| Public du plan  | facultatif. Valeur par défaut : Plan public. Options : Public, privé, par ID de locataire |
| Public du plan restreint (ID de locataire + description)  | Obligatoire si le plan sélectionné est privé. Min. 1 et max. 10 ID de locataire en cas d’entrée manuelle. Max. 20 000 en cas d’importation de fichier CSV. |

## <a name="test-drive-listing-page"></a>Page Référencement de version d’évaluation

Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails utilisés pour référencer la version d’évaluation sur la Place de marché.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Description  | Requis. |
| Nom + fichier de manuel utilisateur  | Obligatoire, max. 1 doc. Doit être au format PDF. |
| Nom de la vidéo, URL + miniature  | Facultatif, recommandé. La miniature doit être 533 x 324 au format JPGP ou PNG. La vidéo doit être hébergée sur YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Page Passer en revue et publier

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Notes pour la certification  | facultatif. |

## <a name="next-steps"></a>Étapes suivantes

- [Créer une nouvelle offre SaaS](./create-new-saas-offer.md)
