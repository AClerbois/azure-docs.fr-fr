---
title: Résolution des problèmes d’Azure Blockchain Workbench Preview
description: Guide de dépannage d’une application Azure Blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844025"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Résolution des problèmes d’Azure Blockchain Workbench Preview

Un script PowerShell est disponible pour aider le développeur avec les activités de débogage ou de support technique. Le script génère un résumé et collecte les journaux d’activité détaillés pour le dépannage. Les journaux d’activité détaillés sont relatifs aux éléments suivants :

* Réseau Blockchain, tels qu’Ethereum
* Microservices Blockchain Workbench
* Application Insights
* Surveillance Azure (journaux Azure Monitor)

Vous pouvez utiliser les informations pour identifier la procédure à suivre et déterminer la cause racine des problèmes.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Script de dépannage

Le script de dépannage PowerShell est disponible sur GitHub. [Téléchargez un fichier zip ](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Exécutez le script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Exécutez le script `collectBlockchainWorkbenchTroubleshooting.ps1` pour collecter des journaux d’activité et créer un fichier ZIP contenant un dossier des données de dépannage. Par exemple :

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Le script accepte les paramètres suivants :

| Paramètre  | Description | Obligatoire |
|---------|---------|----|
| SubscriptionID | ID d’abonnement associé à la création ou l’emplacement de l’ensemble des ressources. | OUI |
| ResourceGroupName | Nom du groupe de ressources Azure dans lequel a été déployé Blockchain Workbench. | OUI |
| OutputDirectory | Chemin d’accès utilisé pour la création du fichier ZIP de sortie. S’il n’est pas spécifié, il est défini par défaut sur le répertoire actuel. | Non |
| LookbackHours | Nombre d’heures à utiliser lors de l’extraction des données de télémétrie. La valeur par défaut est de 24 heures. La valeur maximale est de 90 heures. | Non |
| OmsSubscriptionId | ID de l’abonnement dans lequel les journaux Azure Monitor sont déployés. Transmettez ce paramètre uniquement si les journaux Azure Monitor associés au réseau blockchain sont déployés à l’extérieur du groupe de ressources Blockchain Workbench.| Non |
| OmsResourceGroup |Groupe de ressources dans lequel les journaux Azure Monitor sont déployés. Transmettez ce paramètre uniquement si les journaux Azure Monitor associés au réseau blockchain sont déployés à l’extérieur du groupe de ressources Blockchain Workbench.| Non |
| OmsWorkspaceName | Nom de l’espace de travail Log Analytics. Transmettez ce paramètre uniquement si les journaux Azure Monitor associés au réseau blockchain sont déployés à l’extérieur du groupe de ressources Blockchain Workbench | Non |

## <a name="what-is-collected"></a>Quels sont les éléments collectés ?

Le fichier ZIP de sortie contient la structure de dossier suivante :

| Dossier ou fichier | Description  |
|---------|---------|
| \Summary.txt | Résumé du système |
| \Metrics\blockchain | Mesures relatives à l’application blockchain |
| \Metrics\Workbench | Mesures relatives à l’application Workbench |
| \Details\Blockchain | Journaux d’activité détaillés relatifs à l’application blockchain |
| \Details\Workbench | Journaux d’activité détaillés relatifs à l’application workbench |

Le fichier de résumé vous octroie un aperçu sur l’état global de l’application et sur l’intégrité de l’application. Le résumé communique les actions recommandées, met en évidence les erreurs principales et les métadonnées sur les services en cours d’exécution.

Le dossier **Métriques** contient les métriques des différents composants système, collectées jusqu’à présent. Par exemple, le fichier de sortie `\Details\Workbench\apiMetrics.txt` contient un récapitulatif des différents codes de réponse et temps de réponse collectés au cours d’une période donnée. Le dossier **Détails** contient des journaux d’activité détaillés permettant de résoudre les problèmes liés à Workbench ou au réseau blockchain sous-jacent. Par exemple, `\Details\Workbench\Exceptions.csv` contient la liste des exceptions qui se sont produites dans le système dernièrement, ce qui est utile pour résoudre les erreurs liées aux contrats intelligents ou aux interactions avec le blockchain. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide de dépannage Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
