---
title: Certifier votre image de machine virtuelle pour Azure Marketplace
description: Cet article explique comment tester et soumettre une image de machine virtuelle pour la certification Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/26/2018
ms.author: pabutler
ms.openlocfilehash: 0dbf1abbb91f9e5c3bd2d042c57f87591d52c9cd
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938493"
---
# <a name="certify-your-vm-image"></a>Certifier votre image de machine virtuelle

Une fois que vous avez créé et déployé votre machine virtuelle, vous devez tester et soumettre l’image de machine virtuelle pour la certification Place de marché Microsoft Azure. Cet article vous explique où obtenir l’outil *Certification Test Tool for Azure Certified*, comment l’utiliser pour certifier votre image de machine virtuelle et comment charger les résultats de la vérification dans le conteneur Azure où résident vos disques durs virtuels (VHD). 


## <a name="download-and-run-the-certification-test-tool"></a>Télécharger et exécuter l’outil Certification Test

L’outil Certification Test Tool for Azure Certified s’exécute sur une machine Windows locale, mais teste une machine virtuelle Windows ou Linux basée sur Azure.  Il vérifie que votre image de machine virtuelle utilisateur est compatible avec Microsoft Azure, autrement dit que les recommandations et exigences concernant la préparation de votre VHD ont été satisfaites. La sortie de l’outil est un rapport de compatibilité, que vous chargerez dans le [Portail Microsoft Cloud Partner](https://cloudpartner.azure.com) pour demander la certification de la machine virtuelle.

1. Téléchargez et installez la version la plus récente de l’outil [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Ouvrez l’outil de certification, puis cliquez sur le bouton **Start New Test** (Commencer un nouveau test).
3. Dans l’écran **Test Information** (Informations sur le test), entrez un nom de test dans le champ **Test Name** (Nom du test) pour la série de tests.
4. Dans le champ **Platform** (Plateforme), sélectionnez la plateforme pour votre machine virtuelle, soit `Windows Server`, soit `Linux`. La plateforme que vous choisissez a une incidence sur le reste des options.
5. Si votre machine virtuelle utilise ce service de base de données, cochez la case **Test for Azure SQL Database** (Test pour Azure SQL Database).

   ![Page initiale de l’outil de test de certification](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle

  L’outil se connecte aux machines virtuelles Windows avec [PowerShell](https://docs.microsoft.com/powershell/), et se connecte aux machines virtuelles Linux par le biais de [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle Linux

1. Sélectionnez le mode **SSH Authentication** (Authentification SSH) : `Password Authentication` ou `key File Authentication`.
2. Si vous utilisez l’authentification basée sur un mot de passe, renseignez les champs **VM DNS Name** (Nom du DNS de machine virtuelle), **User Name** (Nom d’utilisateur) et **Password** (Mot de passe).  Si vous le souhaitez, vous pouvez modifier la valeur par défaut du champ **SSH Port** (Port SSH).

     ![Authentification de l’image de machine virtuelle Linux par mot de passe](./media/publishvm_026.png)

3. Si vous utilisez l’authentification par fichier de clé, renseignez les champs **VM DNS Name** (Nom du DNS de machine virtuelle), **User Name** (Nom d’utilisateur) et **Private key** (Clé privée).  Si vous le souhaitez, vous pouvez renseigner le champ **Passphrase** (Phrase secrète) ou modifier la valeur par défaut du champ **SSH Port** (Port SSH).

     ![Authentification de l’image de machine virtuelle Linux par fichier](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connecter l’outil de certification à une image de machine virtuelle sous Windows**
1. Dans le champ **VM DNS Name** (Nom du DNS de machine virtuelle), entrez un nom complet (par exemple, `MyVMName.Cloudapp.net`).
2. Renseignez les champs **User Name** (Nom d’utilisateur) et **Password** (Mot de passe).

   ![Authentification de l’image de machine virtuelle Windows par mot de passe](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Exécuter un test de certification

Après avoir fourni les valeurs de paramètre pour votre image de machine virtuelle dans l’outil de certification, sélectionnez **Test Connection** (Tester la connexion) pour garantir la validité de la connexion à votre machine virtuelle. Une fois la connexion vérifiée, sélectionnez **Next** (Suivant) pour démarrer le test.  Une fois le test terminé, un tableau s’affiche avec les résultats du test : Pass (Réussite)/Fail (Échec)/Warning (Avertissement).  L’exemple ci-après présente les résultats d’un test de machine virtuelle Linux. 

![Résultats du test de certification d’une image de machine virtuelle Linux](./media/publishvm_029.png)

Si l’un des tests échoue, votre image n’est *pas* certifiée. Dans ce cas, examinez les exigences et les messages d’échec, apportez les modifications indiquées, puis réexécutez le test. 

Après le test automatisé, vous êtes invité à fournir des informations supplémentaires concernant votre image de machine virtuelle dans l’écran **Questionnaire** (Questionnaire).  Cet écran comporte deux onglets que vous devez remplir.  L’onglet **General Assessment** (Évaluation générale) contient des questions de type **True/False** (Vrai/Faux), tandis que l’onglet **Kernel Customization** (Personnalisation du noyau) comporte des questions à choix multiple et en forme libre.  Répondez aux questions sur les deux onglets, puis sélectionnez **Next** (Suivant).

![Questionnaire de l’outil de certification](./media/publishvm_030.png)

Le dernier écran vous permet de fournir des informations supplémentaires, telles que les informations d’accès SSH pour une image de machine virtuelle Linux, ainsi qu’une explication des éventuels échecs d’évaluation si vous recherchez les exceptions. 

Enfin, cliquez sur **Generate Report** (Générer le rapport) afin de télécharger les résultats de test et les fichiers journaux pour les cas de test exécutés, ainsi que vos réponses au questionnaire. Enregistrez les résultats dans le même conteneur que vos VHD.

![Enregistrer les résultats du test de certification](./media/publishvm_031.png)


## <a name="next-steps"></a>Étapes suivantes

Vous allez ensuite [générer un identificateur Uniform Resource Identifier (URI) pour chaque VHD](./cpp-get-sas-uri.md) que vous soumettez à la place de marché. 
