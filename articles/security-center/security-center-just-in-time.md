---
title: Accès Juste à temps à la machine virtuelle dans Azure Security Center | Microsoft Docs
description: Ce document montre comment l’accès juste-à-temps à la machine virtuelle dans Azure Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fb092a3c6b473680480c3bba0ad6f437176833de
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576398"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gérer l’accès juste-à-temps à la machine virtuelle

L’accès juste-à-temps (JIT) à la machine virtuelle peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, réduire l’exposition aux attaques et faciliter la connexion aux machines virtuelles si nécessaire.

> [!NOTE]
> La fonctionnalité juste-à-temps est disponible pour le niveau Standard de Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.


> [!NOTE]
> L’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Pour en savoir plus sur les modèles de déploiement de type Classic et Resource Manager, consultez [Déploiement Azure Resource Manager et déploiement Classic](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Scénario d’attaque

Les attaques par force brute ciblent généralement les ports de gestion cible pour accéder à une machine virtuelle. S’il réussit, un attaquant peut prendre le contrôle sur la machine virtuelle et créer une brèche dans votre environnement.

Pour réduire l’exposition aux attaques par force brute, vous pouvez limiter la durée d’ouverture d’un port. Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Lorsque la fonctionnalité juste-à-temps est activée, Security Center utilise des règles de [groupe de sécurité réseau](../virtual-network/security-overview.md#security-rules) (NSG) et de pare-feu Azure qui limitent l’accès aux ports de gestion, pour qu’ils ne soient pas la cible d’attaquants.

![Scénario juste-à-temps](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Fonctionnement de l’accès JIT

Lorsque la fonctionnalité juste-à-temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau (NSG). Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant sera verrouillé. Ces ports sont contrôlés par la solution juste-à-temps.

Quand un utilisateur demande l’accès à une machine virtuelle, Security Center vérifie que cet utilisateur dispose des autorisations [Contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) sur cette machine virtuelle. Si la requête est approuvée, Security Center configure automatiquement les groupes de sécurité réseau (NSG) et le pare-feu Azure afin d’autoriser le trafic entrant vers les ports sélectionnés et vers les plages ou adresses IP source demandées, pendant la durée spécifiée. Après expiration du délai, Security Center restaure les groupes de sécurité réseau à leur état précédent. Ces connexions déjà établies ne sont cependant pas interrompues.

 > [!NOTE]
 > Si une demande d’accès juste-à-temps est approuvée pour une machine virtuelle derrière un pare-feu Azure, Security Center modifie automatiquement les règles de stratégie du groupe de sécurité réseau et du pare-feu. Pendant la durée qui a été spécifiée, les règles autorisent le trafic entrant vers les ports sélectionnés et les plages ou adresses IP sources demandées. Une fois la durée écoulée, Security Center restaure les règles du groupe de sécurité réseau et du pare-feu à leur état précédent.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorisations nécessaires pour configurer et utiliser JIT

| Pour permettre à un utilisateur de : | Autorisations à définir|
| --- | --- |
| Configurer ou modifier une stratégie juste-à-temps pour une machine virtuelle | *Attribuez ces actions au rôle :*  <ul><li>Sur l’étendue d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> Sur l’étendue d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle : <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|Demander l’accès JIT à une machine virtuelle | *Attribuez ces actions à l’utilisateur :*  <ul><li>Sur l’étendue d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  Sur l’étendue d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>Configurer une stratégie juste-à-temps sur une machine virtuelle

Trois méthodes s’offrent à vous pour configurer une stratégie juste-à-temps sur une machine virtuelle :

- [Configurer un accès juste-à-temps dans Azure Security Center](#jit-asc)
- [Configurer un accès juste-à-temps dans un panneau de machine virtuelle Azure](#jit-vm)
- [Configurer une stratégie juste-à-temps sur une machine virtuelle par programme](#jit-program)

## <a name="configure-jit-in-security-center"></a>Configurer JIT dans Security Center

Dans Security Center, il est possible de configurer une stratégie JIT pour demander l’accès à une machine virtuelle.


### Configurer un accès JIT sur une machine virtuelle dans Security Center <a name="jit-asc"></a>

1. Ouvrez le tableau de bord **Security Center**.

2. Dans le volet gauche, sélectionnez **Accès Juste à temps à la machine virtuelle**.

    ![Vignette Accès juste-à-temps à la machine virtuelle](./media/security-center-just-in-time/just-in-time.png)

    La fenêtre **Accès juste-à-temps à la machine virtuelle** s’ouvre.

      ![Activer l’accès juste-à-temps](./media/security-center-just-in-time/enable-just-in-time.png)

    La section **Accès juste-à-temps à la machine virtuelle** fournit des informations sur l’état de vos machines virtuelles :

    - **Configuré** : machines virtuelles configurées pour prendre en charge l’accès juste-à-temps à la machine virtuelle. Les données présentées concernent la semaine qui vient de s’écouler et incluent, pour chaque machine virtuelle le nombre de requêtes approuvées, la date et l’heure du dernier accès, mais aussi le dernier utilisateur.
    - **Recommandé** : machines virtuelles qui peuvent prendre en charge l’accès juste-à-temps à la machine virtuelle, mais qui n’ont pas été configurées dans cette optique. Nous vous recommandons d’activer le contrôle d’accès juste-à-temps à la machine virtuelle pour ces machines virtuelles.
    - **Aucune recommandation** : voici les raisons pour lesquelles une machine virtuelle peut ne pas être recommandée :
      - Groupe de sécurité réseau manquant : la solution juste-à-temps nécessite la présence d’un groupe de sécurité réseau.
      - Machine virtuelle classique : l’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Le déploiement classique n’est pas pris en charge par la solution juste-à-temps. 
      - Autre : catégorie d’une machine virtuelle si la solution juste-à-temps est désactivée dans la stratégie de sécurité de l’abonnement ou du groupe de ressources, ou si la machine virtuelle ne dispose pas d’une adresse IP publique ni d’un groupe de sécurité réseau.

3. Sélectionnez l’onglet **Recommandé**.

4. Sous **MACHINE VIRTUELLE**, cliquez sur les machines virtuelles à activer. Une coche est alors placée en regard des machines virtuelles concernées.

5. Cliquez sur **Activer JIT sur les machines virtuelles**.
   -. Ce panneau affiche les ports par défaut recommandés par Azure Security Center :
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. Vous pouvez également configurer des ports personnalisés :

      1. Cliquez sur **Add**. La fenêtre **Ajouter la configuration du port** s’ouvre.
      2. Pour chaque port que vous choisissez de configurer (par défaut et personnalisé), vous pouvez personnaliser les paramètres suivants :

    - **Type de protocole** : le protocole qui est autorisé sur ce port lorsqu’une demande est approuvée.
    - **Adresses IP source autorisées** : les plages d’adresses IP qui sont autorisées sur ce port lorsqu’une demande est approuvée.
    - **Durée de demande maximale** : la fenêtre de temps maximale pendant laquelle un port spécifique peut être ouvert.

     3. Cliquez sur **OK**.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
>Lorsque l’accès juste-à-temps aux machines virtuelles est activé pour une machine virtuelle, Azure Security Center crée des règles de « refus de tout le trafic entrant » pour les ports sélectionnés dans les groupes de sécurité réseau associés et le pare-feu Azure. Si d’autres règles avaient été créées pour les ports sélectionnés, les règles existantes sont prioritaires sur les nouvelles règles de « refus de tout le trafic entrant ». S’il n’y a aucune règle sur les ports sélectionnés, alors les nouvelles règles de « refus de tout le trafic entrant » sont prioritaires sur les groupes de sécurité réseau et le pare-feu Azure.


## <a name="request-jit-access-via-security-center"></a>Demander un accès JIT dans Security Center

Pour demander l’accès à une machine virtuelle avec Security Center :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.

2. Sous **Machine virtuelle**, cliquez sur les machines virtuelles pour lesquelles vous souhaitez demander l’accès. Une coche est alors placée en regard de la machine virtuelle concernée.


    - L’icône dans la colonne **Détails de la connexion** indique si l’accès juste-à-temps est activé sur le groupe de sécurité réseau ou le pare-feu. S’il est activé sur les deux, seule l’icône de pare-feu s’affiche.

    - La colonne **Détails de la connexion** fournit les informations nécessaires pour se connecter à la machine virtuelle et à ses ports ouverts.

      ![Demander l’accès juste-à-temps](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Cliquez sur **Demander l’accès**. La fenêtre **Demander l’accès** s’ouvre.

      ![Détails de l’accès juste-à-temps](./media/security-center-just-in-time/just-in-time-details.png)

4. Sous **Demander l’accès**, configurez pour chaque machine virtuelle les ports que vous voulez ouvrir, les adresses IP sources sur lesquelles le port est ouvert, ainsi que la fenêtre de temps pendant laquelle le port est ouvert. La demande d’accès ne sera possible que pour les ports configurés dans la stratégie juste-à-temps. Chaque port dispose d’un délai maximal autorisé issu de la stratégie juste-à-temps.

5. Cliquez sur **Ports ouverts**.

> [!NOTE]
> Si un utilisateur demande l’accès alors qu’il se trouve derrière un proxy, l’option **Mon IP** risque de ne pas fonctionner. Il peut se révéler nécessaire de définir la plage complète d’adresses IP de l’organisation.

## <a name="edit-a-jit-access-policy-via-security-center"></a>Modifier une stratégie d’accès JIT dans Security Center

Vous pouvez modifier la stratégie juste-à-temps d’une machine virtuelle en ajoutant et en configurant un port à protéger pour cette machine virtuelle, ou en modifiant tout autre paramètre lié à un port déjà protégé.

Pour modifier une stratégie juste-à-temps existante d’une machine virtuelle :
1. Dans l’onglet **Configuré**, sous **Machines virtuelles**, sélectionnez la machine virtuelle à laquelle ajouter un port en cliquant sur les trois points qui se trouvent sur la ligne de cette machine virtuelle. 

1. Sélectionnez **Modifier**.
1. Sous **JIT VM access configuration** (Configuration de l’accès juste-à-temps à la machine virtuelle), vous pouvez soit modifier les paramètres existants d’un port déjà protégé, soit ajouter un nouveau port personnalisé. 
  ![accès JIT à la machine virtuelle](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>Auditer l’activité d’accès JIT dans Security Center

Vous pouvez obtenir des informations sur les activités des machines virtuelles à l’aide de la recherche dans les journaux. Pour consulter les journaux d’activité :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2. Sous **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez consulter les informations en cliquant sur l’ellipse qui se trouve sur la ligne de cette machine virtuelle, puis sélectionnez **Journal d’activité** dans le menu. Le **journal d’activité** s’ouvre.

   ![Sélectionner un journal d’activité](./media/security-center-just-in-time/select-activity-log.png)

   **Journal d’activité** fournit une vue filtrée des opérations précédentes pour cette machine virtuelle, ainsi que l’abonnement, la date et l’heure.

Vous pouvez télécharger les informations du journal en sélectionnant **Cliquez ici pour télécharger tous les éléments au format CSV**.

Modifiez les filtres et sélectionnez **Appliquer** pour créer une recherche et un journal.



## Configurer un accès JIT sur la page d’une machine virtuelle Azure <a name="jit-vm"></a>

Dans un souci de simplicité, il est possible de se connecter à une machine virtuelle en accès JIT, directement sur la page de cette machine virtuelle dans Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configurer un accès JIT sur une machine virtuelle sur la page de la machine virtuelle Azure

Pour faciliter le déploiement de l’accès juste-à-temps entre vos machines virtuelles, vous pouvez configurer une machine virtuelle pour autoriser uniquement l’accès juste-à-temps directement à partir de la machine virtuelle.

1. Dans le portail Azure, sélectionnez **Machines virtuelles**.
2. Cliquez sur la machine virtuelle pour laquelle vous voulez limiter l’accès juste-à-temps.
3. Dans le menu, cliquez sur **Configuration**.
4. Sous **Accès juste-à-temps**, cliquez sur **Activer une stratégie juste-à-temps**. 

Cela permet un accès juste-à-temps pour la machine virtuelle en utilisant les paramètres suivants :

- Serveurs Windows :
    - Port RDP 3389
    - Trois heures d’accès autorisé maximum
    - L’option Adresses IP sources autorisées est définie sur Toutes
- Serveurs Linux :
    - Port SSH 22
    - Trois heures d’accès autorisé maximum
    - L’option Adresses IP sources autorisées est définie sur Toutes
     
Si l’accès juste-à-temps est déjà activé sur une machine virtuelle, vous pouvez le voir quand vous accédez à sa page de configuration, et vous pouvez utiliser le lien pour ouvrir la stratégie dans Azure Security Center afin d’afficher et de changer les paramètres.

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Demander un accès juste-à-temps sur une machine virtuelle via le panneau de machine virtuelle Azure

Dans le portail Azure, lorsque vous essayez de vous connecter à une machine virtuelle, Azure vérifie que vous disposez d’une stratégie d’accès juste-à-temps configurée sur cette machine virtuelle.

- Si vous ne disposez pas d’une stratégie JIT configurée sur la machine virtuelle, vous pouvez cliquer sur **Demander l’accès** pour vous permettre d’avoir accès conformément à la stratégie JIT définie pour la machine virtuelle. 

  >![Demande d’accès juste-à-temps](./media/security-center-just-in-time/jit-request.png)

  L’accès est demandé avec les paramètres par défaut suivants :

  - **IP source** : « Toutes » (*) (cette valeur n’est pas modifiable)
  - **Intervalle de temps** : Trois heures (cette valeur n’est pas modifiable) <!--Isn't this set in the policy-->
  - **Numéro de port** : port RDP 3389 pour Windows/port 22 pour Linux (cette valeur est modifiable)

    > [!NOTE]
    > Une fois la demande approuvée pour une machine virtuelle protégée par le pare-feu Azure, Security Center fournit à l’utilisateur les informations de connexion appropriées (mappage de ports provenant de la table DNAT) à utiliser pour se connecter à la machine virtuelle.

- Si vous ne disposez pas d’accès JIT configuré sur une machine virtuelle, vous êtes invité à configurer une stratégie JIT.

  ![invite jit](./media/security-center-just-in-time/jit-prompt.png)

## Configurer une stratégie juste-à-temps sur une machine virtuelle par programme <a name="jit-program"></a>

Vous pouvez configurer et utiliser la fonctionnalité juste-à-temps via les API REST et via PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Accès juste-à-temps aux machines virtuelles via les API REST

La fonctionnalité d’accès aux machines virtuelles juste-à-temps peut être utilisée via l’API Azure Security Center. Vous pouvez obtenir des informations sur les machines virtuelles configurées, en ajouter de nouvelles, demander l’accès à une machine virtuelle ou effectuer d’autres actions via cette API. Pour plus d’informations sur l’API REST juste-à-temps, consultez [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

## <a name="jit-vm-access-via-powershell"></a>Accès juste-à-temps aux machines virtuelles via PowerShell

Pour utiliser la solution d’accès juste-à-temps aux machines virtuelles via PowerShell, utilisez les cmdlets officiels d’Azure Security Center PowerShell, et plus spécifiquement `Set-AzJitNetworkAccessPolicy`.

L’exemple suivant définit une stratégie d’accès juste-à-temps aux machines virtuelles pour une machine virtuelle spécifique et définit les éléments suivants :

1.  Fermez les ports 22 et 3389.

2.  Définissez une fenêtre de temps maximale de 3 heures pour chaque afin de pouvoir être ouverts pour chaque demande approuvée.
3.  Cela permet à l’utilisateur demandant un accès de contrôler les adresses IP source et cela lui permet d’établir une session avec succès après une demande d’accès juste-à-temps approuvée.

Pour ce faire, exécutez la commande suivante dans PowerShell :

1.  Attribuez une variable qui conserve la stratégie d’accès juste-à-temps à une machine virtuelle pour une machine virtuelle :

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Insérez la stratégie d’accès juste-à-temps à une machine virtuelle d’une machine virtuelle dans le tableau :
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurez la stratégie d’accès juste-à-temps à une machine virtuelle de la machine virtuelle sélectionnée :
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Demander l’accès à une machine virtuelle via PowerShell

Dans l’exemple suivant, vous pouvez voir une demande d’accès juste-à-temps à une machine virtuelle pour une machine virtuelle spécifique. Dans cette dernière, l’ouverture du port 22 est requise pour une adresse IP et une durée spécifiques :

Exécutez la commande suivante dans PowerShell :
1.  Configurez les propriétés de la demande d’accès à une machine virtuelle

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insérez les paramètres de la demande d’accès à la machine virtuelle dans un tableau :

        $JitPolicyArr=@($JitPolicyVm1)
3.  Envoyez la demande d’accès (utilisez l’ID de ressource obtenu à l’étape 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Pour plus d’informations, consultez la documentation sur la cmdlet PowerShell.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a fait découvrir en quoi l’accès juste-à-temps à la machine virtuelle dans Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaire](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
- [Blog sur la sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

