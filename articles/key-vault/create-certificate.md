---
title: Méthodes de création de certificats
description: Méthodes de création d’un certificat dans Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="certificate-creation-methods"></a>Méthodes de création de certificats

 Un certificat Key Vault (KV) peut être créé ou importé dans un coffre de clés. Lors de la création d’un certificat KV, la clé privée est créée dans le coffre de clés et n’est jamais exposée au propriétaire du certificat. Vous trouverez ci-dessous des méthodes de création d’un certificat dans Key Vault :  

-   **Créer un certificat auto-signé :** créer une paire de clés publique-privée et l’associer à un certificat. Le certificat est signé par sa propre clé.  

-    **Créer un nouveau certificat manuellement :** créer une paire de clés publique-privée et générer une demande de signature de certificat X.509. La demande de signature peut être signée par votre autorité de certification ou autorité d’inscription. Le certificat x509 signé peut être fusionné avec la paire de clés en attente afin de compléter le certificat KV dans Key Vault. Bien que cette méthode nécessite davantage d’étapes, elle offre une plus grande sécurité, car la clé privée est créée dans Key Vault et restreinte à ce dernier. Ceci est expliqué dans le diagramme ci-dessous.  

![Créer un certificat avec votre propre autorité de certification](media/certificate-authority-1.png)  

Les descriptions suivantes correspondent aux étapes écrites en vert dans le diagramme précédent.

1. Dans le diagramme ci-dessus, votre application crée un certificat qui commence en interne par la création d’une clé dans votre coffre de clés.
2. Key Vault retourne une demande de signature de certificat (CSR) à votre application
3. Votre application la demande CSR à votre autorité de certification choisie.
4. Votre autorité de certification choisie répond avec un certificat X509.
5. Votre application termine la création du nouveau certificat par le biais d’une fusion avec le certificat X509 provenant de votre autorité de certification.

-   **Créer un certificat avec un fournisseur émetteur connu :** cette méthode requiert une tâche unique consistant à créer un objet émetteur. Une fois qu’un objet émetteur est créé dans votre coffre de clé, son nom peut être référencé dans la stratégie du certificat KV. La demande de création d’un tel certificat KV crée une paire de clés dans le coffre et communique avec le service de fournisseur émetteur à l’aide des informations dans l’objet émetteur référencé pour obtenir un certificat x509. Le certificat x509 est récupéré à partir du service de l’émetteur et est fusionné avec la paire de clés pour terminer la création du certificat KV.  

![Créer un certificat avec une autorité de certification associée à Key Vault](media/certificate-authority-2.png)  

Les descriptions suivantes correspondent aux étapes écrites en vert dans le diagramme précédent.

1. Dans le diagramme ci-dessus, votre application crée un certificat qui commence en interne par la création d’une clé dans votre coffre de clés.
2. Key Vault envoie et authentifie par SSL la demande de certificat à l’autorité de certification.
3. Votre application interroge, dans un processus boucle et attente, votre Key Vault pour savoir si la création du certificat est terminée. La création du certificat est terminée lorsque Key Vault reçoit la réponse de l’autorité de certification avec un certificat x509.
4. L’autorité de certification répond à la demande de certificat SSL de Key Vault avec un certificat SSL X509.
5. La création de votre nouveau certificat se termine avec la fusion du certificat X509 pour l’autorité de certification.

## <a name="asynchronous-process"></a>Processus asynchrone
La création du certificat KV est un processus asynchrone. Cette opération crée une demande de certificat KV et retourne un code d’état http 202 (Accepté). L’état de la demande peut être suivi par l’interrogation de l’objet en attente créé par cette opération. L’URI complet de l’objet en attente est retourné dans l’en-tête LOCATION.  

Quand une demande de création d’un certificat KV est terminée, l’état de l’objet en attente passe de « inprogress » (En cours) à « completed » (Terminé), et une nouvelle version du certificat KV est créée. Elle devient la version actuelle.  

## <a name="first-creation"></a>Première création
 Lorsqu’un certificat KV est créé pour la première fois, un secret et une clé adressables sont également créés avec le même nom que celui du certificat. Si le nom est déjà utilisé, l’opération échoue avec un code d’état http 409 (Conflit).
Le secret et la clé adressables obtiennent leurs attributs à partir des attributs du certificat KV. Le secret et la clé adressables ainsi créés sont marqués en tant que secrets et clés managés, dont durée de vie est gérée par Key Vault. Les secrets et les clés managés sont en lecture seule. Remarque : si un certificat KV expire ou est désactivé, le secret et la clé correspondants deviennent inutilisables.  

 S’il s’agit de la première création d’un certificat KV, alors une stratégie est requise.  Une stratégie peut également être fournie avec les opérations de création suivantes pour remplacer la ressource de la stratégie. Si aucune stratégie n’est fournie, alors la ressource de la stratégie sur le service est utilisée pour créer une prochaine version du certificat KV. Notez que lorsqu’une demande de création d’une prochaine version est en cours, le certificat KV actuel, ainsi que le secret et la clé adressables correspondants, restent inchangés.  

## <a name="self-issued-certificate"></a>Certificat auto-émis
 Pour créer un certificat auto-émis, définissez le nom de l’émetteur sur « Self » (Auto) dans la stratégie du certificat comme indiqué dans l’extrait de code suivant issu de la stratégie de certificat.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Si le nom de l’émetteur n’est pas spécifié, il est défini sur « Unknown » (Inconnu). Lorsque l’émetteur est « Inconnu », le propriétaire du certificat doit obtenir manuellement un certificat x509 auprès de l’émetteur de son choix, puis fusionner le certificat x509 public avec l’objet en attente de certificat du coffre de clés afin de terminer la création du certificat.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Fournisseurs d’autorité de certification associés
La création du certificat peut être effectuée manuellement ou à l’aide un émetteur « Self ». Key Vault s’associe également à certains fournisseurs émetteurs pour simplifier la création de certificats. Les types de certificats suivants peuvent être commandés pour Key Vault avec ces fournisseurs émetteurs partenaires.  

|Fournisseur|Type de certificat|  
|--------------|----------------------|  
|DigiCert|Key Vault propose des certificats SSL OV ou EV avec DigiCert|
|GlobalCert|Key Vault propose des certificats SSL OV ou EV avec GlobalSign|

 Pour plus d’informations, notamment sur la disponibilité géographique de ces fournisseurs émetteurs, consultez [Émetteurs de certificats](/rest/api/keyvault/certificate-issuers.md).

Notez que lorsqu’une commande est passée auprès du fournisseur émetteur, elle peut respecter ou remplacer les extensions du certificat x509 et la période de validité du certificat en fonction du type de certificat.  

 Autorisation : autorisation certificates/create requise.

 ## <a name="see-also"></a>Voir aussi
 - [Présentation des clés, des secrets et des certificats](about-keys-secrets-and-certificates.md)
 - [Surveiller et gérer la création de certificats](create-certificate-scenarios.md)
