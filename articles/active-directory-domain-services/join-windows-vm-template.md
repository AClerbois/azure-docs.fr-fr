---
title: Utiliser un modèle pour joindre une machine virtuelle Windows à Azure AD DS | Microsoft Docs
description: Découvrez comment utiliser des modèles Azure Resource Manager pour joindre une machine virtuelle Windows Server nouvelle ou existante à un domaine managé Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: 9f35c9c3c6c974c79dc849425fc91b532aed4d22
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755703"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Joindre une machine virtuelle Windows Server à un domaine managé Azure Active Directory Domain Services à l’aide d’un modèle Resource Manager

Pour automatiser le déploiement et la configuration de machines virtuelles Azure, vous pouvez utiliser un modèle Resource Manager. Ces modèles vous permettent de créer des déploiements cohérents à chaque fois. Des extensions peuvent aussi être incluses dans les modèles pour configurer automatiquement une machine virtuelle pendant le déploiement. Une extension utile joint des machines virtuelles à un domaine et peut être utilisée avec des domaines managés Azure Active Directory Domain Services (Azure AD DS).

Cet article explique comment créer une machine virtuelle Windows Server et comment la joindre à un domaine managé Azure AD DS à l’aide de modèles Resource Manager. Vous allez aussi découvrir comment joindre une machine virtuelle Windows Server existante à un domaine Azure AD DS.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, le premier tutoriel [crée et configure une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="azure-resource-manager-template-overview"></a>Vue d’ensemble des modèles Azure Resource Manager

Les modèles Resource Manager permettent de définir une infrastructure Azure dans du code. Les ressources nécessaires, les connexions réseau ou la configuration des machines virtuelles peuvent toutes être définies dans un modèle. Ces modèles vous permettent de créer des déploiements cohérents et reproductibles à chaque fois, et une nouvelle version peut être créée à mesure que vous y apportez des modifications. Pour plus d’informations, consultez [Vue d’ensemble des modèles Azure Resource Manager][template-overview].

Chaque ressource est définie dans un modèle au format JSON. L’exemple JSON suivant utilise le type de ressource *Microsoft. Compute/virtualMachines/extensions* pour installer l’extension de jonction de domaine Active Directory. Il comporte des paramètres qui sont spécifiés au moment du déploiement. Quand l’extension est déployée, la machine virtuelle est jointe au domaine managé Azure AD DS spécifié.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Cette extension de machine virtuelle peut être déployée même si vous ne créez pas de machine virtuelle dans le même modèle. Les exemples de cet article illustrent les deux approches suivantes :

* [Créer une machine virtuelle Windows Server et la joindre à un domaine managé](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Joindre une machine virtuelle Windows Server existante à un domaine managé](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Créer une machine virtuelle Windows Server et la joindre à un domaine managé

Si vous avez besoin d’une machine virtuelle Windows Server, vous pouvez en créer et configurer une à l’aide d’un modèle Resource Manager. Quand la machine virtuelle est déployée, une extension est alors installée pour joindre la machine virtuelle à un domaine managé Azure AD DS. Si vous disposez déjà d’une machine virtuelle et que vous voulez la joindre à un domaine managé Azure AD DS, passez à l’étape [Joindre une machine virtuelle Windows Server existante à un domaine managé](#join-an-existing-windows-server-vm-to-a-managed-domain).

Pour créer une machine virtuelle Windows Server et la joindre ensuite à un domaine managé Azure AD DS, effectuez les étapes suivantes :

1. Accédez au [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Sélectionnez l’option **Déployer dans Azure**.
1. Dans la page **Déploiement personnalisé**, entrez les informations suivantes pour créer une machine virtuelle Windows Server et la joindre au domaine managé Azure AD DS :

    | Paramètre                   | Valeur |
    |---------------------------|-------|
    | Subscription              | Choisissez le même abonnement Azure que celui dans lequel vous avez activé Azure AD Domain Services. |
    | Resource group            | Choisissez le groupe de ressources de votre machine virtuelle. |
    | Location                  | Sélectionnez l’emplacement de votre machine virtuelle. |
    | Existing VNET Name        | Nom du réseau virtuel existant auquel connecter la machine virtuelle, par exemple *myVnet*. |
    | Existing Subnet Name      | Nom du sous-réseau du réseau virtuel existant, par exemple *Workloads*. |
    | DNS Label Prefix          | Entrez le nom DNS à utiliser pour la machine virtuelle, par exemple *myvm*. |
    | Taille de la machine virtuelle                   | Spécifiez une taille de machine virtuelle, par exemple *Standard_DS2_v2*. |
    | Domain To Join            | Nom DNS du domaine managé Azure AD DS, par exemple *contoso.com*. |
    | Domain Username           | Compte d’utilisateur dans le domaine managé Azure AD DS qui doit être utilisé pour joindre la machine virtuelle au domaine managé. Ce compte doit être membre du groupe *Administrateurs Azure AD DC*. |
    | Domain Password           | Mot de passe du compte d’utilisateur spécifié dans le paramètre précédent. |
    | Optional OU Path          | UO personnalisée dans laquelle ajouter la machine virtuelle. Si vous ne spécifiez pas de valeur pour ce paramètre, la machine virtuelle est ajoutée à l’unité d’organisation *AAD DC Computers*. |
    | VM Admin Username         | Spécifiez le compte administrateur local à créer sur la machine virtuelle. |
    | VM Admin Password         | Spécifiez un mot de passe d’administrateur local pour la machine virtuelle. Créez un mot de passe d’administrateur local fort pour être protégé contre les attaques de mot de passe par force brute. |

1. Passez en revue les conditions générales, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**. Quand vous êtes prêt, sélectionnez **Purchase** (Acheter) pour créer et joindre la machine virtuelle au domaine managé Azure AD DS.

> [!WARNING]
> **Gérez les mots de passe avec prudence.**
> Le fichier de paramètres du modèle demande le mot de passe d’un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC*. N’entrez pas manuellement de valeurs dans ce fichier et laissez-le accessible sur les partages de fichiers ou à d’autres emplacements partagés.

Le déploiement prend quelques minutes. À l’issue de l’opération, la machine virtuelle Windows est créée et jointe au domaine managé Azure AD DS. La machine virtuelle peut être managée ou connectée à l’aide de comptes de domaine.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Joindre une machine virtuelle Windows Server existante à un domaine géré

Si vous disposez d’une machine virtuelle ou d’un groupe de machines virtuelles que vous voulez joindre à un domaine managé Azure AD DS, vous pouvez utiliser un modèle Resource Manager pour déployer simplement l’extension de machine virtuelle.

Pour joindre une machine virtuelle Windows Server existante à un domaine managé Azure AD DS, effectuez les étapes suivantes :

1. Accédez au [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Sélectionnez l’option **Déployer dans Azure**.
1. Dans la page **Déploiement personnalisé**, entrez les informations suivantes pour joindre la machine virtuelle au domaine managé Azure AD DS :

    | Paramètre                   | Valeur |
    |---------------------------|-------|
    | Subscription              | Choisissez le même abonnement Azure que celui dans lequel vous avez activé Azure AD Domain Services. |
    | Resource group            | Choisissez le groupe de ressources avec votre machine virtuelle existante. |
    | Location                  | Sélectionnez l’emplacement de votre machine virtuelle existante. |
    | VM list                   | Entrez la liste séparée par des virgules de la ou des machines virtuelles existantes à joindre au domaine managé Azure AD DS, par exemple *myVM1,myVM2*. |
    | Domain Join User Name     | Compte d’utilisateur dans le domaine managé Azure AD DS qui doit être utilisé pour joindre la machine virtuelle au domaine managé. Ce compte doit être membre du groupe *Administrateurs Azure AD DC*. |
    | Domain Join User Password | Mot de passe du compte d’utilisateur spécifié dans le paramètre précédent. |
    | Optional OU Path          | UO personnalisée dans laquelle ajouter la machine virtuelle. Si vous ne spécifiez pas de valeur pour ce paramètre, la machine virtuelle est ajoutée à l’unité d’organisation *AAD DC Computers*. |

1. Passez en revue les conditions générales, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**. Quand vous êtes prêt, sélectionnez **Purchase** (Acheter) pour joindre la machine virtuelle au domaine managé Azure AD DS.

> [!WARNING]
> **Gérez les mots de passe avec prudence.**
> Le fichier de paramètres du modèle demande le mot de passe d’un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC*. N’entrez pas manuellement de valeurs dans ce fichier et laissez-le accessible sur les partages de fichiers ou à d’autres emplacements partagés.

Le déploiement prend quelques instants. Quand vous avez terminé, les machines virtuelles Windows spécifiées sont jointes au domaine managé Azure AD DS et peuvent être managées ou connectées en utilisant des comptes du domaine.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez utilisé la portail Azure pour configurer et déployer des ressources à l’aide de modèles. Vous pouvez aussi déployer des ressources avec des modèles Resource Manager à partir d’[Azure PowerShell][deploy-powershell] ou [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/template-deployment-overview.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
