---
title: Profiler des applications de production dans Azure avec Application Insights Profiler | Microsoft Docs
description: Identifiez le chemin réactif dans le code de votre serveur web avec un profileur de faible encombrement.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3e72c4af7139bac1cefb81d7b8fc1bbfcddabb6a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081754"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profiler des applications de production dans Azure avec Application Insights
## <a name="enable-profiler-for-your-application"></a>Activer Profiler pour votre application

Application Insights Profiler fournit des traces de performances pour les applications qui s’exécutent en production dans Azure. Il capture automatiquement les données à grande échelle sans impact négatif sur vos utilisateurs finaux. Profiler vous aide à identifier le chemin de code « réactif » qui prend le plus de temps lors du traitement d’une requête web particulière. 

Le profileur fonctionne avec les applications .Net déployées sur les services Azure suivants. Des instructions spécifiques pour activer le profileur pour chaque type de service sont accessibles via les liens ci-dessous.

* [Services d’application](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Applications Service Fabric](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles et groupes identiques de machines virtuelles](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Si vous avez activé Profiler, mais ne voyez aucune trace, consultez notre [Guide de résolution des problèmes.](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json)

L’exécution locale de Profiler n’est pas prise en charge officiellement, mais nous avons certaines [instructions que vous pouvez essayer.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Afficher les données du profileur

Pour que le profileur charge des traces, votre application doit gérer activement les demandes. Si vous effectuez une expérience, vous pouvez générer des demandes pour votre application web à l’aide des [tests de performances Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Si vous venez d’activer Profiler, vous pouvez exécuter un test de charge court. Pendant que le test de charge est en cours d’exécution, appuyez sur le bouton **Profiler maintenant** dans la [**page Paramètres de Profiler**](profiler-settings.md #profiler-settings-page). Une fois le profileur en cours d’exécution, il profile au hasard une fois toutes les heures, pendant une durée de deux minutes. Si votre application gère un flux constant de demandes, Profiler charge les traces toutes les heures.

Une fois que votre application a reçu du trafic et que le profileur a eu le temps de charger les traces, vous devez disposer de traces à visualiser. Ce processus peut prendre entre 5 et 10 minutes. Pour visualiser les traces, accédez au volet **Performances**, sélectionnez **Prendre des mesures** pour afficher les traces du profileur, puis sélectionnez le bouton **Traces du profileur**.

![Aperçu du volet de performances Application Insights - Traces du profileur][performance-blade]

Sélectionnez un exemple pour afficher, au niveau du code, une répartition du temps d’exécution de la requête.

![Explorateur de trace d’Application Insights][trace-explorer]

L’Explorateur de trace affiche les informations suivantes :

* **Afficher le chemin réactif** : ouvre le plus grand nœud feuille, ou du moins un élément qui s’en rapproche. Dans la plupart des cas, ce nœud est proche d’un goulot d’étranglement des performances.
* **Étiquette** : nom de la fonction ou de l’événement. L’arborescence affiche une combinaison des codes et des événements qui se sont produits (par exemple, des événements SQL et HTTP). L’événement supérieur représente la durée globale de la requête.
* **Écoulé** : intervalle de temps entre le début de l’opération et la fin.
* **Quand** : moment auquel la fonction ou l’événement était en cours d’exécution par rapport à d’autres fonctions.

## <a name="how-to-read-performance-data"></a>Comment lire les données de performances

Le profileur de service Microsoft utilise un ensemble de méthodes d’échantillonnage et une instrumentation pour analyser les performances de votre application. Durant la collecte détaillée, le profileur de service échantillonne le pointeur d’instruction de chaque processeur de l’ordinateur à chaque milliseconde. Chaque échantillon capture l’ensemble de la pile des appels du thread en cours d’exécution. Il fournit des informations détaillées sur l’activité de ce thread, à un niveau d’abstraction élevé et faible. Le profileur de service collecte également d’autres événements afin de suivre la corrélation et la causalité des activités, tels que les événements de changement de contexte, les événements TPL (Task Parallel Library) et les événements de pool de threads.

La pile des appels présentée dans l’affichage chronologique est le résultat de l’échantillonnage et de l’instrumentation. Étant donné que chaque échantillon capture l’ensemble de la pile des appels du thread, il inclut le code de Microsoft .NET Framework, et des autres infrastructures que vous référencez.

### <a id="jitnewobj"></a>Allocation d’objets (clr!JIT\_Nouveau ou clr!JIT\_Newarr1)

**clr!JIT\_Nouveau** et **clr!JIT\_Newarr1** sont des fonctions d’assistance du framework .NET qui allouent la mémoire à partir d’un tas managé. **clr!JIT\_Nouveau** est appelé lorsqu’un objet est alloué. **clr!JIT\_Newarr1** est appelé lorsqu’un tableau d’objet est alloué. Ces deux fonctions sont généralement rapides et s’exécutent en relativement peu de temps. Si vous constatez que **clr!JIT\_New** ou **clr!JIT\_Newarr1** prend un certain temps dans votre scénario, cela indique que le code alloue peut-être de nombreux objets et qu’il consomme une quantité importante de mémoire.

### <a id="theprestub"></a>Code de chargement (clr!ThePreStub)

**clr!ThePreStub** est une fonction d’assistance intégrée au framework .NET qui prépare le code en vue de sa première exécution. Elle implique généralement au moins une compilation JIT (juste à temps). Pour chaque méthode C#, **clr!ThePreStub** doit être appelé au maximum une fois pendant la durée de vie d’un processus.

Si **clr!ThePreStub** prend beaucoup de temps pour traiter une demande, cela indique que la demande est la première à exécuter cette méthode. Le runtime du .NET Framework a besoin de temps pour charger la première méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent, ou encore envisager d’exécuter Native Image Generator (ngen.exe) sur vos assemblys.

### <a id="lockcontention"></a>Contention de verrouillage (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indique que le thread actuel est en attente d’ouverture d’un verrou. Ce texte est souvent affiché lors de l’exécution d’une instruction **LOCK** C#, lors de l’appel de la méthode **Monitor.Enter** ou lors de l’appel d’une méthode avec l’attribut **MethodImplOptions.Synchronized**. La contention de verrouillage se produit généralement lorsque le thread _A_ acquiert un verrou et que le thread _B_ tente d’acquérir le même verrou avant qu’il ne soit libéré par le thread _A_.

### <a id="ngencold"></a>Code de chargement ([COLD])

Si le nom de la méthode contient **[COLD]**, par exemple **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, cela signifie que le runtime du framework .NET exécute pour la première fois du code qui n’utilise pas une <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimisation guidée par profil</a>. Pour chaque méthode, il doit s’afficher au maximum une fois pendant la durée de vie du processus.

Si le code de chargement prend beaucoup de temps pour traiter une requête, cela indique que la requête est la première à exécuter la partie non optimisée de la méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent.

### <a id="httpclientsend"></a>Envoyer une requête HTTP

Les méthodes telles que **HttpClient.Send** indiquent que le code attend l’exécution d’une requête HTTP.

### <a id="sqlcommand"></a>Opération de base de données

Une méthode telle que **SqlCommand.Execute** indique que le code attend l’exécution d’une opération de base de données.

### <a id="await"></a>En attente (AWAIT\_TIME)

**AWAIT\_TIME** indique que le code attend la fin de l’exécution d’une autre tâche. Cela se produit généralement avec l’instruction C# **AWAIT**. Lorsque le code exécute une instruction C# **AWAIT**, le thread se déroule et redonne le contrôle au pool de threads, et aucun thread n’est bloqué en attendant la fin de l’exécution de l’instruction **AWAIT**. Toutefois, le thread qui a exécuté l’instruction **AWAIT** est logiquement « bloqué » en attendant que l’opération se termine. L’instruction **AWAIT\_TIME** indique le temps de blocage en attendant l’exécution de la tâche.

### <a id="block"></a>Temps de blocage

**BLOCKED_TIME** indique que le code attend qu’une autre ressource soit disponible. Il peut, par exemple, attendre un objet de synchronisation, la disponibilité d’un thread ou la fin d’une requête.

### <a id="cpu"></a>Temps processeur

Le processeur est occupé à exécuter les instructions.

### <a id="disk"></a>Temps du disque

L’application effectue les opérations de disque.

### <a id="network"></a>Temps réseau

L’application effectue les opérations réseau.

### <a id="when"></a>Colonne « When » (Quand)

La colonne **When** permet de voir comment les exemples INCLUSIFS collectés pour un nœud varient au fil du temps. La plage totale de la requête est divisée en 32 périodes. Les exemples inclusifs de ce nœud sont regroupés dans ces 32 périodes. Chaque période est représentée sous forme de barre. La hauteur de la barre représente une valeur à l’échelle. Pour les nœuds marqués **CPU_TIME** ou **BLOCKED_TIME**, ou lorsqu’il existe une relation évidente de consommation d’une ressource (par exemple, processeur, disque, thread), la barre représente la consommation d’une des ressources au cours de cette période. Pour ces mesures, il est possible d’obtenir une valeur supérieure à 100 % en consommant plusieurs ressources. Par exemple, si vous utilisez en moyenne deux processeurs sur un intervalle donné, vous obtenez 200 %.

## <a name="limitations"></a>Limites

Par défaut, la période de conservation des données est de 5 jours. La quantité maximale de données ingérées par jour est de 10 Go.

L’utilisation du service Profiler est gratuite. Pour pourvoir utiliser le service Profiler, votre application web doit être hébergée au moins au niveau De base de Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Surcharge et algorithme d’échantillonnage

Profiler s’exécute aléatoirement pendant 2 minutes toutes les heures sur chaque machine virtuelle qui héberge l’application sur laquelle Profiler est activé pour capturer des traces. Lorsque Profiler est en cours d’exécution, il ajoute entre 5 et 15 % de surcharge pour les processeurs sur le serveur.

## <a name="next-steps"></a>Étapes suivantes
Activer Application Insights Profiler pour votre application Azure
* [Services d’application](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Applications Service Fabric](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles et groupes identiques de machines virtuelles](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
