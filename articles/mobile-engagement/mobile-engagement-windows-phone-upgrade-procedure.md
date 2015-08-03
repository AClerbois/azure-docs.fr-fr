<properties 
	pageTitle="Procédures de mise à niveau du Kit de développement Windows Phone Silverlight" 
	description="Procédures de mise à niveau du SDK Windows Phone Silverlight pour Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="piyushjo" />

#Procédures de mise à niveau du Kit de développement Windows Phone Silverlight

Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

##Migration de 1.1.1 vers 2.0.0

La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement.

> [Azure.IMPORTANT]Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Engagement Mobile.

Si vous migrez à partir d'une version antérieure, consultez le site web de Capptain pour migrer tout d'abord vers 1.1.1, puis appliquez la procédure suivante.

### Package NuGet

Remplacez **Capptain.WindowsPhone** par le package Nuget **MicrosoftAzure.MobileEngagement**.

### Application d'Engagement Mobile

Le SDK utilise le terme `Engagement`. Vous devez mettre à jour votre projet pour qu'il corresponde à cette modification.

Vous devez désinstaller votre package nuget Capptain actuel. Considérez que toutes vos modifications dans le dossier de ressources Capptain seront supprimées. Si vous souhaitez conserver ces fichiers, effectuez-en une copie.

Après cela, installez le nouveau package nuget Microsoft Azure Engagement sur votre projet. Vous le trouverez directement sur [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Cette action remplace tous les fichiers de ressources utilisés par Engagement et ajoute la nouvelle DLL Engagement à vos références de projet.

Vous devez nettoyer vos références de projet en supprimant les références à la DLL Capptain. Si vous ne le faites pas, la version de Capptain génère un conflit et une erreur se produit.

Si vous avez personnalisé des ressources Capptain, copiez le contenu de vos anciens fichiers et collez-le dans les nouveaux fichiers Engagement. Notez que les fichiers xaml et cs doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références Capptain par les nouvelles références Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.

	Avant la migration :
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Après la migration :
	
		using Microsoft.Azure.Engagement;

2. Toutes les classes Capptain qui contiennent « Capptain » doivent contenir « Engagement ».

	Avant la migration :
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Après la migration :
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. Pour les fichiers xaml, les attributs et les espaces de noms Capptain changent également.

	Avant la migration :
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Après la migration :
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. Notez que les autres ressources, comme les images Capptain, ont aussi été renommées afin d'utiliser « Engagement ».

### ID de l'application / clé SDK

Engagement utilise une chaîne de connexion. Il est inutile de spécifier un ID d'application et une clé SDK avec Mobile Engagement. Il suffit de spécifier une chaîne de connexion. Vous pouvez la configurer dans votre fichier EngagementConfiguration.

La configuration d'Engagement peut être définie dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

-   Votre chaîne de connexion d'application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous souhaitez plutôt la spécifier au moment de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Initialize Engagement angent with above configuration. */
		EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion de votre application est affichée sur le portail de gestion Azure.

### Changement de noms d'éléments

Tous les éléments nommés *capptain* ont été renommés *engagement*. De même pour *Capptain* (renommés *Engagement*).

Exemples d'éléments Capptain couramment utilisés :

-   CapptainConfiguration se nomme maintenant EngagementConfiguration
-   CapptainAgent se nomme maintenant EngagementAgent
-   CapptainReach se nomme maintenant EngagementReach
-   CapptainHttpConfig se nomme maintenant EngagementHttpConfig
-   GetCapptainPageName se nomme maintenant GetEngagementPageName

Notez que ce changement affecte également les méthodes substituées.



 

<!---HONumber=July15_HO4-->