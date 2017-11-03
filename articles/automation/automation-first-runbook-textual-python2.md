---
title: "Mon premier Runbook Python dans Azure Automation | Microsoft Docs"
description: "Ce didacticiel vous familiarise avec la création, le test et la publication d’un Runbook Python simple."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: eslesar
ms.openlocfilehash: 4e7b3049fff76c86956e08d71b22a0f8dbf55b0e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="my-first-python-runbook"></a>Mon premier runbook Python

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ce didacticiel vous guide dans la création d’un [Runbook Python](automation-runbook-types.md#python-runbooks) dans Azure Automation. Nous commençons par un simple runbook que nous testons et publions tout en expliquant comment suivre l’état du travail du runbook. Nous modifions ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, nous renforçons le runbook en ajoutant des paramètres de runbook.

## <a name="prerequisites"></a>Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Nous arrêtons et démarrons cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="create-a-new-runbook"></a>Créer un Runbook

Nous commençons par créer un Runbook simple qui renvoie le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.
   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
1. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Créez un runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
1. Nommez le runbook *MyFirstRunbook-Python*.
1. Dans ce cas précis, nous allons créer un [Runbook Python](automation-runbook-types.md#python-runbooks). Par conséquent, sélectionnez **Python2** comme **Type de Runbook**.
1. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="add-code-to-the-runbook"></a>Ajouter du code au Runbook

Nous ajoutons une simple commande pour imprimer le texte « Hello World » :

```python
print("Hello World!")
```

Cliquez sur **Enregistrer** pour enregistrer le runbook.

## <a name="test-the-runbook"></a>Tester le Runbook

Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
   ![Test Pane](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
1. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.
   L’état initial du travail est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.
1. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.
1. Fermez le volet de test pour revenir au canevas.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que nous avons créé est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production.
Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon.
Dans notre cas, nous n’avons pas encore de version publiée car nous venons de créer le Runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
   ![Bouton Publier](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Si vous faites défiler la page vers la gauche pour visualiser le volet **Runbooks**, celui-ci affichera **l’État de création** **Publié**.
1. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Python**.
   Les options de la partie supérieure nous permettent de démarrer le runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
1. Notre objectif étant de démarrer le runbook, cliquons sur **Démarrer**, puis sur **OK** à l’ouverture du panneau Démarrer le Runbook.
1. Un volet de travail est ouvert pour le travail du runbook que nous avons créé. Nous pouvons fermer ce volet mais, dans ce cas, nous le laissons ouvert afin de suivre la progression du travail.
1. L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.
1. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.
1. Fermez le volet Sortie.
1. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de Runbook, notamment Mode détaillé et Erreur si le Runbook consigne ces informations.
1. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook-Python.
1. Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.
1. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que nous avons consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="add-authentication-to-manage-azure-resources"></a>Ajouter une authentification pour gérer les ressources Azure

Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure.
Pour gérer les ressources Azure, le script doit s’authentifier à l’aide des informations d’identification de votre [compte Automation](automation-offering-get-started.md).

> [!NOTE]
> Le compte Automation doit avoir été créé avec la fonctionnalité de principal de service pour qu’un certificat puisse être exécuté.
> Si vous n’avez pas créé votre compte Automation avec cette fonctionnalité, vous pouvez vous authentifier à l’aide de la méthode décrite dans [S’authentifier avec les bibliothèques de gestion Azure pour Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Python.
1. Ajoutez le code suivant pour vous authentifier dans Azure :
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Ajouter du code pour créer le client Compute Python et démarrer la machine virtuelle

Pour utiliser des machines virtuelles Azure, créez une instance du [client Compute Azure pour Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

Utilisez le client Compute pour démarrer la machine virtuelle. Ajoutez le code suivant au Runbook :

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

_MyResourceGroup_ étant le nom du groupe de ressource qui contient la machine virtuelle, et _TestVM_ le nom de la machine virtuelle que vous souhaitez démarrer. 

Testez et exécutez une nouvelle fois le Runbook pour vérifier qu’il démarre la machine virtuelle.

## <a name="use-input-parameters"></a>Utilisez les paramètres d’entrée

Actuellement, le Runbook utilise des valeurs codées en dur pour le nom du groupe de ressources et celui de la machine virtuelle.
Nous ajoutons maintenant du code pour obtenir ces valeurs des paramètres d’entrée.

Nous utilisons la variable `sys.argv` pour obtenir les valeurs du paramètre.
Ajoutez le code suivant au Runbook à la suite des autres instructions `import` :

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Cette action importe le module `sys` et crée deux variables pour stocker le nom du groupe de ressources et celui de la machine virtuelle.
Remarque : l’élément `sys.argv[0]` dans la liste des arguments correspond au nom du script et n’est pas entré par l’utilisateur.

Nous pouvons maintenant modifier les deux dernières lignes du Runbook pour utiliser les valeurs du paramètre d’entrée à la place des valeurs codées en dur :

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Lorsque vous redémarrez le Runbook Python (dans le panneau **Test** ou en tant que Runbook publié), vous pouvez entrer les valeurs des paramètres dans le panneau **Démarrer Runbook** dans **Paramètres**.

![Boîte Valeur du paramètre](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Après avoir entré une valeur dans la première boîte, une deuxième apparaît, et ainsi de suite. Vous pouvez entrer autant de valeurs de paramètres que nécessaire.

Les valeurs sont disponibles pour le script, tout comme le tableau `sys.argv`, dans le code que nous venons d’ajouter.

Entrez le nom du groupe de ressources comme valeur du premier paramètre, et le nom de la machine virtuelle à démarrer comme valeur du deuxième paramètre.

![Entrer des valeurs de paramètres](media/automation-first-runbook-textual-python/runbook-python-params.png)

Cliquez sur **OK** pour démarrer le Runbook. Le Runbook s’exécute et démarre la machine virtuelle que vous avez spécifiée.

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour en savoir plus sur le développement pour Azure avec Python, consultez [Azure pour les développeurs Python](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
* Pour afficher des exemples de runbooks Python 2, consultez [GitHub Azure Automation](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
