---
title: Modèles d’intégration de contrat intelligent dans Azure Blockchain Workbench
description: Vue d’ensemble de modèles d’intégration de contrat intelligent dans Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0fe4816dbafc28974796c7d9cd307b04fdb9d6d4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="smart-contract-integration-patterns"></a>Modèles d’intégration de contrat intelligent

Les contrats intelligents représentent généralement un flux de travail métier qui doit être intégré à des systèmes et appareils externes.

Les exigences de ces flux de travail incluent la nécessité d’initier des transactions sur un registre distribué qui contient des données provenant d’un système, service ou appareil externe. Ils requièrent également des systèmes externes qui réagissent à des événements provenant de contrats intelligents sur un registre distribué.

L’intégration d’une API REST et d’une messagerie permet d’envoyer des transactions de systèmes externes vers des contrats intelligents inclus dans une application Azure Blockchain Workbench et d’envoyer des notifications d’événements à des systèmes externes en fonction des modifications apportées dans une application.

Pour les scénarios d’intégration de données, Azure Blockchain Workbench inclut un ensemble de vues de base de données permettant de fusionner une combinaison de données transactionnelles provenant de blockchain et de métadonnées relatives aux applications et aux contrats intelligents.

Dans certains scénarios, tels que ceux liés à la chaîne logistique ou aux médias, l’intégration de documents peut également être nécessaire. Bien qu’Azure Blockchain Workbench ne permette pas d’appeler une API pour gérer les documents directement, des documents peuvent être intégrés dans une application Azure Blockchain. Cette section inclut également ce modèle.

Cette section inclut les modèles identifiés pour l’implémentation de chacun de ces types d’intégration dans vos solutions de bout en bout.

## <a name="rest-api-based-integration"></a>Intégration basée sur l’API REST

Les fonctionnalités de l’application web générée par Azure Blockchain Workbench sont exposées via l’API REST. Ces fonctionnalités Azure Blockchain Workbench incluent le téléchargement, la configuration et l’administration des applications, l’envoi de transactions à un registre distribué et l’interrogation des métadonnées de l’application et des données du registre.

L’API REST est principalement utilisée pour les clients interactifs tels que les applications web, mobiles et bot.

Cette section présente les modèles en se concentrant sur les API REST qui envoient des transactions à un registre distribué et sur celles qui interrogent des données sur les transactions dans la base de données SQL *off chain* d’Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Envoi de transactions à un registre distribué à partir d’un système externe

L’API REST Azure Blockchain Workbench permet d’envoyer des requêtes authentifiées afin d’exécuter des transactions sur un registre distribué.

![Envoi de transactions à un registre distribué](media/blockchain-workbench-integration-patterns/send-transactions-ledger.png)

Cela se produit selon le processus décrit ci-dessus, dans lequel :

-   L’application externe s’authentifie auprès d’Azure Active Directory configuré dans le cadre du déploiement d’Azure Blockchain Workbench.
-   Les utilisateurs autorisés reçoivent un jeton du porteur qui peut être envoyé avec des requêtes à l’API.
-   Les applications externes appellent l’API REST à l’aide du jeton du porteur.
-   L’API REST prépare la requête sous forme de message et l’envoie au Service Bus. Elle est alors récupérée, signée et envoyée au registre distribué approprié.
-   L’API REST formule une requête vers la base de données SQL Azure Blockchain Workbench afin d’enregistrer la requête et de déterminer l’état d’approvisionnement actuel.
-   La base de données SQL renvoie l’état d’approvisionnement et l’appel d’API renvoie l’ID de l’application externe qui l’a appelée.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Interrogation des métadonnées Blockchain Workbench et transactions de registre distribué

L’API REST Azure Blockchain Workbench permet d’envoyer des requêtes authentifiées afin de rechercher des informations liées à l’exécution de contrat intelligent sur un registre distribué.

![Interrogation des métadonnées](media/blockchain-workbench-integration-patterns/querying-metadata.png)

Cela se produit selon le processus décrit ci-dessus, dans lequel :

1. L’application externe s’authentifie auprès d’Azure Active Directory configuré dans le cadre du déploiement d’Azure Blockchain Workbench.
2. Les utilisateurs autorisés reçoivent un jeton du porteur qui peut être envoyé avec des requêtes à l’API.
3. Les applications externes appellent l’API REST à l’aide du jeton du porteur.
4. L’API REST interroge les données pour la requête dans la base de données SQL et les retourne au client.

## <a name="messaging-integration"></a>Intégration d’une messagerie

L’intégration d’une messagerie facilite l’interaction avec les systèmes, services et appareils pour lesquels une connexion interactive n’est pas possible ou n’est pas souhaitable. L’intégration d’une messagerie vise deux types de messages : ceux demandant que des transactions soient exécutées sur un registre distribué et les événements qui sont exposés par ce registre lors de l’exécution de transactions.

L’intégration d’une messagerie vise l’exécution et la surveillance de transactions liées à la création d’un utilisateur, la création d’un contrat et l’exécution de transactions sur des contrats, et est principalement utilisée par des systèmes principaux *décentralisés*.

Cette section présente les modèles en se concentrant sur les API basées sur des messages qui envoient des transactions à un registre distribué et sur celles qui représentent des messages d’événement exposés par le registre distribué sous-jacent.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Remise d’événement unidirectionnelle d’un contrat intelligent à un consommateur d’événements 

Dans ce scénario, un événement se produit dans un contrat intelligent, par exemple, un changement d’état ou l’exécution d’un type spécifique de transaction. Cet événement est diffusé via un Event Grid aux consommateurs en aval, et ces consommateurs effectuent alors les actions appropriées.

Un exemple de ce scénario est que, lorsqu’une transaction se produit, un consommateur est averti et peut effectuer une action comme l’enregistrement des informations dans une base de données SQL ou le Common Data Service. Workbench suit ce même modèle pour remplir la base de données SQL *off chain*.

Un autre exemple est celui d’un contrat intelligent passant à un état particulier, par exemple, lorsqu’un contrat devient *OutOfCompliance*. Lorsque ce changement d’état se produit, une alerte peut être déclenchée et envoyée au téléphone mobile d’un administrateur.

![Remise d’événement unidirectionnelle](media/blockchain-workbench-integration-patterns/one-way-event-delivery.png)

Cela se produit selon le processus décrit ci-dessus, dans lequel :

-   Le contrat intelligent passe à un nouvel état et envoie un événement au registre.
-   Le registre reçoit et remet l’événement à Azure Blockchain Workbench.
-   Azure Blockchain Workbench est abonné aux événements du registre et reçoit l’événement.
-   Azure Blockchain Workbench publie l’événement aux abonnés sur l’Event Grid.
-   Les systèmes externes sont abonnés à l’Event Grid, exploitent le message et effectuent la ou les actions appropriées.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Remise d’événement unidirectionnelle d’un message d’un système externe à un contrat intelligent

Il existe également un scénario dans le sens opposé. Dans ce cas, un événement est généré par un capteur ou un système externe et les données de cet événement doivent être envoyées à un contrat intelligent.

Un exemple courant réside dans la remise de données de marchés financiers, par exemple, les prix de marchandises, d’actions ou obligations, à un contrat intelligent.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Remise directe d’un Azure Blockchain Workbench dans le format attendu

Certaines applications sont conçues pour s’intégrer à Azure Blockchain Workbench et génèrent et envoient directement des messages dans les formats attendus.

![Remise directe](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Cela se produit selon le processus décrit ci-dessus, dans lequel :

-   Un événement se produit dans un système externe qui entraîne la création d’un message destiné à Azure Blockchain Workbench.
-   Le système externe inclut du code écrit pour créer ce message dans un format connu et l’envoie directement au Service Bus.
-   Azure Blockchain Workbench est abonné aux événements du Service Bus et récupère le message.
-   Azure Blockchain Workbench appelle le registre, en envoyant des données du système externe à un contrat spécifique.
-   À la réception du message, le contrat passe à un nouvel état.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Remise d’un message dans un format inconnu à Azure Blockchain Workbench

Certains systèmes ne peuvent pas être modifiés pour remettre des messages dans les formats standards utilisés par Azure Blockchain Workbench. Dans ces cas, les mécanismes existants et les formats de message de ces systèmes peuvent généralement être utilisés. Plus précisément, les types de messages natifs de ces systèmes peuvent être transformés à l’aide de Logic Apps, d’Azure Functions ou de tout autre code personnalisé pour correspondre à l’un des formats de messagerie standards attendus.

![Format de message inconnu](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Cela se produit selon le processus décrit ci-dessus, dans lequel :

-   Un événement se produit dans un système externe qui entraîne la création d’un message.
-   Une application logique ou un code personnalisé est utilisé pour recevoir ce message et le transformer en un message au format Azure Blockchain Workbench standard.
-   L’application logique envoie le message transformé directement au Service Bus.
-   Azure Blockchain Workbench est abonné aux événements du Service Bus et récupère le message.
-   Azure Blockchain Workbench appelle le registre, en envoyant des données du système externe à une fonction spécifique du contrat.
-   La fonction s’exécute et modifie généralement l’état. Le changement d’état fait avancer le flux de travail métier correspondant dans le contrat intelligent, en activant l’exécution des autres fonctions prévues.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Transition de contrôle vers un processus externe et attente de l’achèvement

Dans certains scénarios, l’exécution d’un contrat intelligent doit être arrêtée et transférée à un processus externe. Ce processus doit alors la terminer, envoyer un message au contrat intelligent, et l’exécution doit se poursuivre dans le contrat intelligent.

#### <a name="transition-to-the-external-process"></a>Transition vers le processus externe

Ce modèle est généralement implémenté selon l’approche suivante :

-   Le contrat intelligent passe à un état spécifique. Dans cet état, aucune ou un nombre limité de fonctions peut être exécuté jusqu’à ce qu’un système externe effectue l’action souhaitée.
-   Le changement d’état est présenté au consommateur en aval sous la forme d’un événement.
-   Le consommateur en aval reçoit l’événement et déclenche l’exécution de code externe.

![Transition de contrôle vers le processus externe](media/blockchain-workbench-integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retour de contrôle du contrat intelligent

En fonction des possibilités de personnalisation du système externe, il peut ou non être en mesure de remettre des messages dans l’un des formats standards attendus par Azure Blockchain Workbench. La capacité des systèmes externes à générer l’un de ces messages déterminera lequel des deux chemins de retour suivants utiliser.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Remise directe d’un Azure Blockchain Workbench dans le format attendu

![](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Dans ce modèle, la communication avec le contrat et le changement d’état qui s’en suit se produisent selon le processus ci-dessus, dans lequel :

-   À l’achèvement ou lorsqu’un jalon spécifique de l’exécution du code externe est atteint, un événement est envoyé au Service Bus connecté à Azure Blockchain Workbench.

-   Pour les systèmes qui ne sont pas directement adaptés pour écrire un message conforme aux attentes de l’API, celui-ci est transformé.

-   Le contenu du message est préparé et envoyé à une fonction spécifique du contrat intelligent. Cette opération est effectuée sous le nom de l’utilisateur associé au système externe.

-   La fonction s’exécute et entraîne généralement un changement d’état. Le changement d’état fait avancer le flux de travail métier correspondant dans le contrat intelligent, en activant l’exécution des autres fonctions prévues.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Remise d’un message dans un format inconnu à Azure Blockchain Workbench

![Format de message inconnu](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Dans ce modèle, dans lequel un message au format standard ne peut pas être envoyé directement, la communication avec le contrat et le changement d’état qui s’en suit se produisent selon le processus ci-dessus, dans lequel :

1.  À l’achèvement ou lorsqu’un jalon spécifique de l’exécution du code externe est atteint, un événement est envoyé au Service Bus connecté à Azure Blockchain Workbench.
2.  Une application logique ou un code personnalisé est utilisé pour recevoir ce message et le transformer en un message au format Azure Blockchain Workbench standard.
3.  L’application logique envoie le message transformé directement au Service Bus.
4.  Azure Blockchain Workbench est abonné aux événements du Service Bus et récupère le message.
5.  Azure Blockchain Workbench appelle le registre, en envoyant des données du système externe à un contrat spécifique.
6. Le contenu du message est préparé et envoyé à une fonction spécifique du contrat intelligent. Cette opération est effectuée sous le nom de l’utilisateur associé au système externe.
7.  La fonction s’exécute et entraîne généralement un changement d’état. Le changement d’état fait avancer le flux de travail métier correspondant dans le contrat intelligent, en activant l’exécution des autres fonctions prévues.

## <a name="iot-integration"></a>Intégration d’IoT

Un scénario d’intégration courant consiste à inclure dans un contrat intelligent des données de télémétrie récupérées de capteurs. En fonction des données fournies par les capteurs, les contrats intelligents peuvent effectuer des actions et modifier l’état du contrat.

Par exemple, si un camion de livraison de médicaments voit sa température atteindre 110 degrés, l’efficacité des médicaments peut être affectée et entraîner un problème de santé publique s’il n’est pas détecté et retiré de la chaîne logistique. Si un conducteur pousse son véhicule à 160 kilomètres/heure, les informations du capteur risquent d’entraîner une résiliation du contrat d’assurance par son assureur. S’il s’agit d’un véhicule de location, les données GPS peuvent indiquer quand le conducteur est sorti du périmètre couvert par son contrat de location et entraîner la facturation de pénalités.

Le défi réside dans le fait que ces capteurs peuvent fournir des données en continu et qu’il n’est pas approprié d’envoyer toutes ces données à un contrat intelligent. Une approche classique consiste à limiter le nombre de messages envoyés au blockchain mais à remettre tous les messages à un magasin secondaire. Par exemple, remettre les messages reçus à intervalle fixe uniquement, une fois par heure par exemple, et lorsqu’une valeur est en dehors de la plage convenue pour un contrat intelligent. La vérification des valeurs en dehors des tolérances garantit la bonne réception et exécution des données pertinentes pour la logique métier des contrats. La vérification de la valeur selon un intervalle garantit le bon fonctionnement du capteur. Toutes les données sont envoyées à un magasin de rapports secondaire pour permettre des rapports, des analyses et un Machine Learning plus approfondis. Par exemple, même si des résultats de capteurs GPS ne soient pas nécessaires toutes les minutes pour un contrat intelligent, ils peuvent fournir des données intéressantes à utiliser dans des rapports ou des itinéraires de mappage.

Sur la plateforme Azure, l’intégration à des appareils se fait généralement avec IoT Hub. IoT Hub permet d’acheminer des messages en fonction du contenu et d’utiliser le type de fonctionnalités décrit ci-dessus.

![Messages IoT](media/blockchain-workbench-integration-patterns/iot.png)

Le processus ci-dessus présente un modèle d’implémentation :

-   Un appareil communique directement ou via une passerelle locale avec IoT Hub.
-   IoT Hub reçoit les messages et les évalue par rapport aux itinéraires établis qui vérifient le contenu du message, par exemple. *Le capteur signale-t-il une température supérieure à 50 degrés ?*
-   L’IoT Hub envoie des messages qui répondent aux critères à un Service Bus défini pour l’itinéraire.
-   Une application logique ou un autre code écoute le Service Bus défini par IoT Hub pour l’itinéraire.
-   L’application logique ou autre code récupère et transforme le message dans un format connu.
-   Le message transformé, désormais dans un format standard, est envoyé au Service Bus pour Azure Blockchain Workbench.
-   Azure Blockchain Workbench est abonné aux événements du Service Bus et récupère le message.
-   Azure Blockchain Workbench appelle le registre, en envoyant des données du système externe à un contrat spécifique.
-   À la réception du message, le contrat évalue les données et peut changer d’état en fonction du résultat de cette évaluation, par exemple, pour une température élevée, passer à l’état *Out of Compliance*.

## <a name="data-integration"></a>Intégration des données

En plus des API REST et basées sur des messages, Azure Blockchain Workbench permet également d’accéder à une base de données SQL remplie avec des métadonnées d’application et de contrat, ainsi que des données transactionnelles de registres distribués.

![Intégration des données](media/blockchain-workbench-integration-patterns/data-integration.png)

L’intégration de données est bien connue :

-   Selon son comportement normal, Azure Blockchain Workbench stocke des métadonnées sur des applications, flux de travail, contrats et transactions.
-   Les systèmes ou outils externes présentent une ou plusieurs boîtes de dialogue pour faciliter la collecte d’informations sur la base de données, comme le nom du serveur de base de données, le nom de la base de données, le type d’authentification, les informations d’identification de connexion et les vues de base de données à utiliser.
-   Les requêtes sont écrites sur des vues de base de données SQL afin de faciliter la consommation en aval par les systèmes externes, services, rapports, outils de développement et outils de productivité d’entreprise.

## <a name="storage-integration"></a>Intégration d’un stockage

Plusieurs scénarios peuvent nécessiter l’intégration de fichiers attestables. Pour diverses raisons, il ne sera pas inapproprié de placer des fichiers sur un blockchain. Au lieu de cela, une approche courante consiste à effectuer un hachage unidirectionnel par rapport à un fichier et à partager ce hachage sur un registre distribué. Une réexécution du hachage à tout moment doit retourner le même résultat. Si le fichier est modifié, même s’il ne s’agit que d’un seul pixel dans une image, le hachage retournera une valeur différente.

![Intégration d’un stockage](media/blockchain-workbench-integration-patterns/storage-integration.png)

Le modèle peut être implémenté, dans lequel :

-   Un système externe conserve un fichier dans un mécanisme de stockage tel que le Stockage Azure.
-   Un hachage est généré avec le fichier, ou le fichier et les métadonnées associées, comme un identificateur du propriétaire, l’URL où se trouve le fichier, etc.
-   Le hachage et les métadonnées sont envoyés à une fonction d’un contrat intelligent comme *FileAdded*
-   Ultérieurement, le fichier et les métadonnées peuvent être de nouveau hachés et comparés aux valeurs stockées dans le registre.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Conditions préalables à l’implémentation de modèles d’intégration à l’aide des API REST et de messages

Les éléments suivants sont nécessaires pour faciliter l’interaction d’un système ou appareil externe avec le contrat intelligent via l’API REST ou de messages :

1. Dans Azure Active Directory pour le consortium, un compte représentant le système ou appareil externe est créé.
2. Des fonctions sont définies dans le ou les contrats intelligents appropriés pour votre application Azure Blockchain Workbench pour accepter les événements de votre système ou appareil externe.
3. Le fichier de configuration de l’application de votre contrat intelligent inclut le rôle qui sera affecté au système ou appareil.
4. Le fichier de configuration de l’application de votre contrat intelligent identifie dans quels états cette fonction peut être appelée par le rôle défini.
5. Le fichier de configuration de l’application et ses contrats intelligents sont chargés dans Azure Blockchain Workbench.

Une fois que l’application est chargée, le compte Azure Active Directory du système externe est affecté au contrat et au rôle associé.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Test des flux d’intégration de système externe avant d’écrire le code d’intégration 

La capacité d’intégration à des systèmes externes est une condition essentielle de nombreux scénarios. Il est préférable de pouvoir valider la conception d’un contrat intelligent avant ou parallèlement au développement du code d’intégration à des systèmes externes.

L’utilisation d’Azure Active Directory (Azure AD) peut optimiser considérablement de la productivité du développeur et le retour sur investissement. Plus précisément, la durée d’intégration de code à un système externe peut être énorme. Azure AD et la génération automatique de l’UX par Azure Blockchain Workbench peuvent permettre aux développeurs de se connecter à Workbench en tant que système externe et de remplir les valeurs attendues de ce système externe via l’UX. Cela permet de développer et de valider rapidement des idées dans un environnement de preuve de concept avant ou parallèlement à l’écriture du code d’intégration des systèmes externes.
