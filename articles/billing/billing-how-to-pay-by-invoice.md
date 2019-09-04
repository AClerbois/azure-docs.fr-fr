---
title: Paiement des abonnements Azure par facture
description: Décrit comment payer les abonnements Azure par facture.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012595"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Paiement de vos abonnements Azure par facture

Si vous passez au paiement par facture, cela signifie que vous devez payer votre facture dans les 30 jours à compter de la date de facture par chèque/virement bancaire. Afin de pouvoir payer votre abonnement Azure par facture, envoyez une requête au support Azure. Une fois votre requête approuvée, vous pouvez passer au paiement par facture (chèque/virement bancaire) dans le [Portail Azure](https://portal.azure.com).

> [!IMPORTANT]
> * Le paiement par facture (chèque/virement bancaire) est uniquement disponible pour les comptes professionnels.
> * Payez tous les frais en attente avant de passer au paiement par facture.
> * Actuellement, le paiement par facture n’est pas pris en charge par Azure international en Chine.

## <a name="request-to-pay-by-invoice"></a>Demander un paiement par facture

1. Connectez-vous au [Portail Azure](https://portal.azure.com/). Sélectionnez **Aide + support** > **Nouvelle demande de support**.

    ![Aide et lien du support](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Sélectionnez **Facturation** comme **Type de problème**. Le *type de problème* est la catégorie de demande de support. Sélectionnez l’abonnement que vous souhaitez payer par facture, choisissez un plan de support, puis cliquez sur **Suivant**.

3. Sélectionnez **Paiement** comme **Type de problème**. Le *type de problème* est la sous-catégorie de demande de support.

4. Sélectionnez **Passer au paiement par facture** comme **Sous-type de problème**

5. Entrez les informations suivantes dans la zone **Détails** puis sélectionnez **Suivant**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - Le **nom de la société** et l’**adresse de la société** doivent correspondre aux informations que vous avez fournies pour le compte Azure. Pour afficher ou mettre à jour les informations, consultez [Modifier les informations de profil de votre compte Azure](billing-how-to-change-azure-account-profile.md).
    - Ajoutez vos informations de contact de facturation dans le portail Azure pour que la limite de crédit puisse être approuvée. Les détails du contact doivent être liés au service financier et comptabilité fournisseurs de l’entreprise. Pour mettre à jour les informations de contact de facturation, accédez au [Centre des comptes Azure](https://account.azure.com/Profile).

6. Vérifiez vos coordonnées et votre méthode de contact préférée, puis cliquez sur **Créer**.

Si nous avons besoin de vérifier votre solvabilité en raison du montant du crédit dont vous avez besoin, nous vous enverrons une demande de contrôle de solvabilité.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Passer au paiement par facture (chèque/virement bancaire)

Une fois autorisé à payer par facture, vous pouvez passer au paiement par facture (chèque/virement bancaire) dans le portail Azure.

Si vous avez un compte de programme Microsoft Online Services, vous pouvez modifier votre abonnement Azure afin de payer par chèque/virement bancaire. Avec un Contrat client Microsoft, vous pouvez modifier votre profil de facturation pour payer par chèque/virement bancaire. [Découvrez comment vérifier votre type de compte](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Modifier l’abonnement Azure pour payer par chèque/virement bancaire

Suivez les étapes ci-dessous pour modifier votre abonnement Azure afin de payer par facture (chèque/virement bancaire). **Une fois le paiement par facture (chèque/virement bancaire) adopté, vous ne pouvez pas repasser au paiement par carte de crédit**.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d'écran illustrant la recherche](./media/billing-how-to-pay-by-invoice/search.png)

1. Sélectionnez l’abonnement que vous souhaitez faire passer au paiement par facture.
1. Sélectionnez **Modes de paiement**.
1. Dans la barre de commandes, cliquez sur le bouton **Payer par facture**.

    ![Capture d’écran montrant le bouton Payer par facture](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Modifier le profil de facturation pour passer au paiement par chèque/virement bancaire

Suivez les étapes ci-dessous pour modifier le profil de facturation pour passer au paiement par chèque/virement bancaire. Seule la personne qui s’est inscrite à Azure peut modifier le mode de paiement par défaut d’un profil de facturation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, cliquez sur **Profils de facturation**.

    ![capture d’écran montrant le profil de facturation dans le menu](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.

   ![Capture d’écran montrant les modes de paiement dans le menu](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Cliquez sur la bannière bleue indiquant que vous êtes éligible au paiement par chèque/virement bancaire.

    ![Capture d’écran montrant la bannière bleue pour passer au paiement par chèque/virement bancaire](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Si nécessaire, mettez à jour les informations de contact de facturation dans le [Centre des comptes Azure](https://account.azure.com/Profile).
