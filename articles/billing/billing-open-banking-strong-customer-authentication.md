---
title: Système bancaire ouvert et authentification forte du client pour les clients Azure
description: Cet article explique pourquoi l’authentification multifacteur est requise pour certains achats Azure et comment effectuer l’authentification.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: db6b78986ae8abfe2c8ae3296d07c327cc041726
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223690"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Système bancaire ouvert et authentification forte du client pour les clients Azure

Depuis le 14 septembre 2019, les banques des 31 pays de l’[Espace économique européen](https://en.wikipedia.org/wiki/European_Economic_Area) sont tenues de vérifier l’identité de la personne qui effectue un achat en ligne avant le traitement du paiement. Cette vérification nécessite l’authentification multifacteur pour garantir la sécurité et la protection de vos achats en ligne. La date de cette exigence de vérification sera différée pour certains pays. Pour plus d’informations, consultez [Forum aux questions Microsoft sur le système bancaire ouvert](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Signification du système bancaire ouvert pour les clients Azure

Si vous effectuez un paiement pour Azure avec une carte de crédit émise par une banque de l’[Espace économique européen](https://en.wikipedia.org/wiki/European_Economic_Area), vous devrez peut-être effectuer une authentification multifacteur pour le mode de paiement de votre compte. Vous pouvez être invité à répondre à la demande d’authentification multifacteur lors de l’inscription ou de la mise à niveau de votre compte Azure, même si vous n’effectuez pas d’achat à ce moment-là. Vous pouvez également être invité à fournir une authentification multifacteur quand vous modifiez le mode de paiement de votre compte Azure, supprimez votre plafond de dépenses ou effectuez un paiement immédiat à partir du portail Azure, par exemple le règlement de soldes impayés ou l’achat de crédits Azure.

Si votre banque refuse vos frais Azure mensuels, vous recevrez un e-mail de solde dû de la part d’Azure avec des instructions pour y remédier. Vous pouvez répondre à la demande d’authentification multifacteur et régler les frais en souffrance dans le portail Azure.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Effectuer une authentification multifacteur dans le portail Azure

Les sections suivantes décrivent comment effectuer l’authentification multifacteur dans le portail Azure. Utilisez les informations qui vous concernent.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Modifier le mode de paiement actif de votre compte Azure

Vous pouvez modifier le mode de paiement actif de votre compte Azure en procédant comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte et accédez à **Gestion des coûts + facturation**.
2. Dans la page **Vue d’ensemble**, sélectionnez l’abonnement correspondant dans la grille **Mes abonnements**.
3. Sous Facturation, sélectionnez **Modes de paiement**. Vous pouvez ajouter une nouvelle carte de crédit ou définir une carte existante comme mode de paiement actif pour l’abonnement. Si votre banque requiert l’authentification multifacteur, vous êtes invité à répondre à une demande d’authentification au cours du processus.

Pour plus d’informations, consultez [Ajouter, mettre à jour ou supprimer une carte de crédit pour Azure](billing-how-to-change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Régler les frais en souffrance pour les services Azure

Si votre banque refuse les frais, l’état de votre compte Azure affiche **En retard** dans le portail Azure. Vous pouvez vérifier l’état de votre compte en procédant comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur de compte.
2. Effectuez une recherche sur **Gestion des coûts + facturation**.
3. Dans la page **Vue d’ensemble** **Gestion des coûts + facturation**, passez en revue la colonne d’état dans la grille **Mes abonnements**.
4. Si votre abonnement est étiqueté **En retard**, cliquez sur **Régler le solde**. Vous êtes invité à effectuer l’authentification multifacteur pendant le processus.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Régler les frais en souffrance pour les achats sur la Place de marché et de réservation

Les achats sur la Place de marché et de réservation sont facturés séparément des services Azure. Si votre banque refuse les frais de Place de marché ou de réservation, l’état de votre facture affiche **En retard** dans le portail Azure. Vous pouvez vérifier l’état de vos factures de Place de marché et de réservation en procédant comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur de compte.
2. Effectuez une recherche sur **Gestion des coûts + facturation**.
3. Sous Facturation, sélectionnez **Factures**.
4. Cliquez sur l’onglet **Place de marché et réservations Azure** sur la droite.
5. Sélectionnez l’abonnement correspondant.
6. Dans la grille des factures, passez en revue la colonne d’état. Si la facture indique **Échéance** ou **En retard**, cliquez sur **Payer maintenant**. Vous êtes invité à effectuer l’authentification multifacteur pendant le processus.

## <a name="next-steps"></a>Étapes suivantes
- Si vous devez payer une facture Azure, consultez [Résoudre un solde impayé pour votre abonnement Azure](billing-azure-subscription-past-due-balance.md).
