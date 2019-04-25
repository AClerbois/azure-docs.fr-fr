---
title: Élément d’interface utilisateur VirtualNetworkCombo Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Network.VirtualNetworkCombo pour le Portail Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252500"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Élément d’interface utilisateur Microsoft.Network.VirtualNetworkCombo
Groupe de contrôles pour la sélection d’un réseau virtuel nouveau ou existant.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
Lorsque l’utilisateur sélectionne un nouveau réseau virtuel, il peut personnaliser le préfixe de nom et d’adresse de chaque sous-réseau. La configuration des sous-réseaux est facultative.

![Microsoft.Network.VirtualNetworkCombo nouveau](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Lorsque l’utilisateur sélectionne un réseau virtuel existant, il doit mettre en correspondance chaque sous-réseau nécessaire au modèle de déploiement avec un sous-réseau existant. La configuration des sous-réseaux dans ce cas est requise.

![Microsoft.Network.VirtualNetworkCombo existant](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- S’il est spécifié, le premier préfixe de taille d’adresse sans chevauchement `defaultValue.addressPrefixSize` est déterminé automatiquement selon les réseaux virtuels existants de l’abonnement de l’utilisateur.
- La valeur par défaut pour `defaultValue.name` et `defaultValue.addressPrefixSize` est **null**.
- `constraints.minAddressPrefixSize` doit être spécifié. Des réseaux virtuels existants dont l’espace d’adressage est plus petit que la valeur spécifiée ne sont pas disponibles à la sélection.
- `subnets` doit être spécifié, et `constraints.minAddressPrefixSize` doit être spécifié pour chaque sous-réseau.
- Lorsque vous créez un réseau virtuel, le préfixe d’adresse de chaque sous-réseau est calculé automatiquement en fonction du préfixe d’adresse du réseau virtuel et du `addressPrefixSize` respectif.
- Lorsque vous utilisez un réseau virtuel existant, tous les sous-réseaux dont la taille est inférieure à la valeur `constraints.minAddressPrefixSize` respective ne sont pas disponibles à la sélection. De plus, si cet élément est spécifié, les sous-réseaux qui ne contiennent pas au moins `minAddressCount` adresses disponibles ne sont pas disponibles à la sélection. La valeur par défaut est **0**. Pour vous assurer que les adresses disponibles sont contiguës, spécifiez **true** pour `requireContiguousAddresses`. La valeur par défaut est **true**.
- La création de sous-réseaux dans un réseau virtuel n’est pas prise en charge.
- Si `options.hideExisting` est défini sur **true**, l’utilisateur ne peut pas choisir de réseau virtuel existant. La valeur par défaut est **false**.

## <a name="sample-output"></a>Exemple de sortie

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
