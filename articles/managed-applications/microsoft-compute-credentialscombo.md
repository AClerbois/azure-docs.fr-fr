---
title: Élément d’interface utilisateur CredentialsCombo Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Compute.CredentialsCombo pour le Portail Azure.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 914e354265754a05476e96411d35e6cb04183213
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261052"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Élément d’interface utilisateur Microsoft.Compute.CredentialsCombo
Un groupe de contrôles avec validation intégrée pour les clés publiques SSH et les mots de passe Windows et Linux.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Schéma
Si `osPlatform` est **Windows**, le schéma suivant est utilisé :
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Si `osPlatform` est **Linux**, le schéma suivant est utilisé :
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- `osPlatform` doit être spécifié. Il peut s’agir de **Windows** ou de **Linux**.
- Si `constraints.required` a **true**, puis le mot de passe ou les zones de texte de la clé publique SSH doivent contenir des valeurs à valider correctement. La valeur par défaut est **true**.
- Si `options.hideConfirmation` est défini sur **true**, la deuxième zone de texte de confirmation du mot de passe utilisateur est masquée. La valeur par défaut est **false**.
- Si `options.hidePassword` est défini sur **true**, l’option permettant d’utiliser l’authentification par mot de passe est masquée. Elle peut être utilisée uniquement lorsque `osPlatform` est **Linux**. La valeur par défaut est **false**.
- Il est possible d’implémenter des contraintes supplémentaires sur les mots de passe autorisés à l’aide de la propriété `customPasswordRegex`. La chaîne de `customValidationMessage` s’affiche lorsque la validation personnalisée du mot de passe échoue. La valeur par défaut pour les deux propriétés est **null**.

## <a name="sample-output"></a>Exemple de sortie
Si `osPlatform` est **Windows**, ou si l’utilisateur a fourni un mot de passe au lieu d’une clé publique SSH, la sortie suivante est attendue :

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Si l’utilisateur a fourni une clé publique SSH, la sortie suivante est attendue :
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
