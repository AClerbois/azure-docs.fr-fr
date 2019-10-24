---
title: azcopy login | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 058506110a8ac4b11f272406a854f72062a1c90d
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514705"
---
# <a name="azcopy-login"></a>azcopy login

Se connecte à Azure Active Directory pour accéder aux ressources du stockage Azure.

## <a name="synopsis"></a>Synopsis

Connectez-vous à Azure Active Directory pour accéder aux ressources du stockage Azure.

Pour être autorisé à utiliser votre compte de stockage Azure, vous devez affecter le rôle **Contributeur aux données Blob du stockage** à votre compte d’utilisateur, dans le contexte du compte de stockage, du groupe de ressources parent ou de l’abonnement parent.

Cette commande met en cache les informations de connexion chiffrées pour l’utilisateur actuel à l’aide de mécanismes intégrés au système d’exploitation.

Pour plus d’informations, consultez les exemples.

> [!IMPORTANT]
> Si vous définissez une variable d’environnement à l’aide de la ligne de commande, la variable sera lisible dans votre historique de ligne de commande. Vous pouvez supprimer de l’historique de la ligne de commande les variables qui contiennent des informations d’identification. Pour empêcher l’affichage des variables dans votre historique, vous pouvez utiliser un script qui invite l’utilisateur à entrer ses informations d’identification et qui définit la variable d’environnement.

```azcopy
azcopy login [flags]
```

## <a name="examples"></a>Exemples

Se connecter de manière interactive avec l’ID de locataire AAD par défaut qui est défini sur « common » :

```azcopy
azcopy login
```

Se connecter de manière interactive avec un ID de locataire spécifié :

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Se connecter à l’aide de l’identité attribuée par le système d’une machine virtuelle :

```azcopy
azcopy login --identity
```

Se connecter à l’aide de l’identité attribuée par l’utilisateur d’une machine virtuelle et un ID de client de l’identité du service :

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Se connecter à l’aide de l’identité attribuée par l’utilisateur d’une machine virtuelle et un ID d’objet de l’identité du service :

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Se connecter à l’aide de l’identité attribuée par l’utilisateur d’une machine virtuelle et un ID de ressource de l’identité du service :

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Connectez-vous en tant que principal de service à l’aide d’un secret client. Définissez la variable d’environnement AZCOPY_SPA_CLIENT_SECRET sur le secret client pour une authentification du principal de service basée sur les secrets.

```azcopy
azcopy login --service-principal
```

Connectez-vous en tant que principal de service à l’aide d’un certificat et d’un mot de passe. Définissez la variable d’environnement AZCOPY_SPA_CERT_PASSWORD sur le mot de passe du certificat pour une autorisation du principal de service basée sur les certificats.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Veillez à traiter /path/to/my/cert comme le chemin d’un fichier PEM ou PKCS12. AzCopy n’accède pas au magasin de certificats du système pour obtenir votre certificat.

--certificate-path est obligatoire lors d’une authentification du principal de service basée sur les certificats.

## <a name="options"></a>Options

|Option|Description|
|--|--|
|--application-id (chaîne)|ID d’application de l’identité affectée par l’utilisateur. Obligatoire pour l’authentification du principal de service.|
|--certificate-path (chaîne)|Chemin du certificat pour l’authentification SPN. Obligatoire pour l’authentification du principal de service basée sur les certificats.|
|-h, --help|Affiche l’aide de la commande login.|
|--identity|Connectez-vous à l’aide de l’identité de la machine virtuelle, également appelée « Managed Service Identity » (MSI).|
|--identity-client-id (chaîne)|ID client de l’identité affectée par l’utilisateur.|
|--identity-object-id (chaîne)|ID objet de l’identité affectée par l’utilisateur.|
|--identity-resource-id (chaîne)|ID ressource de l’identité affectée par l’utilisateur.|
|--service-principal|Connectez-vous via le nom du principal de service (SPN) à l’aide d’un certificat ou d’un secret. Le secret client ou le mot de passe du certificat doit être placé dans la variable d’environnement appropriée. Tapez `AzCopy env` pour afficher les noms et les descriptions des variables d’environnement.|
|--tenant-id (chaîne)| ID de locataire Azure Active Directory à utiliser pour la connexion interactive de l’appareil OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
