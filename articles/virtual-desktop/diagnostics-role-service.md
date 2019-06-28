---
title: Identifier les problèmes relatifs à la fonctionnalité de diagnostics en préversion de Windows Virtual Desktop - Azure
description: Décrit la fonctionnalité de diagnostics en préversion de Windows Virtual Desktop et comment s’en servir.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 747e177b0fbbfb9049959c3194ee39c3234bba50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234033"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifier les problèmes relatifs à la fonctionnalité de diagnostic

La version préliminaire de Windows Virtual Desktop offre une fonctionnalité de diagnostics qui permet à l’administrateur d’identifier les problèmes via une interface unique. Les rôles Windows Virtual Desktop journalisent une activité de diagnostic à chaque fois qu’un utilisateur interagit avec le système. Chaque journal contient des informations importantes telles que les rôles Windows Virtual Desktop impliqués dans la transaction, les messages d’erreur, les informations d’abonnés, et les informations d’utilisateurs. Les activités de diagnostics sont créées par des actions de l’administrateur et de l’utilisateur, et peuvent être divisées en trois catégories principales :

* Activités d’ajout de flux : l’utilisateur déclenche ces activités à chaque fois qu’il essaie de se connecter à son flux via des applications Bureau à distance Microsoft.
* Activités de connexion : l’utilisateur déclenche ces activités à chaque fois qu’il essaie de se connecter à un bureau ou RemoteApp via des applications Bureau à distance Microsoft.
* Activités de gestion : l’administrateur déclenche ces activités à chaque fois qu’il effectue des opérations de gestion sur le système, telles que la création de pools d’hôte, l’attribution d’utilisateurs à des groupes d’applications et la création d’attributions de rôles.
  
Les connexions qui n’atteignent pas Windows Virtual Desktop n’apparaîtront pas dans les résultats de diagnostic, car le service de rôle de diagnostics lui-même fait partie de Windows Virtual Desktop. Des problèmes de connexion à Windows Virtual Desktop peuvent survenir lorsque l’utilisateur rencontre des problèmes de connectivité au réseau.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

## <a name="diagnose-issues-with-powershell"></a>Diagnostiquer les problèmes avec PowerShell

Windows Virtual Desktop Diagnostics utilise une seule cmdlet PowerShell mais contient de nombreux paramètres facultatifs pour aider à limiter et isoler les problèmes. Les sections suivantes répertorient les cmdlets que vous pouvez exécuter pour diagnostiquer les problèmes. La plupart des filtres peuvent être appliqués ensemble. Les valeurs figurant entre crochets, comme `<tenantName>`, doivent être remplacées par les valeurs qui s’appliquent à votre situation.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Récupérer les activités de diagnostic dans votre locataire

Vous pouvez récupérer les activités de diagnostic en exécutant la cmdlet **Get-RdsDiagnosticActivities**. L’exemple de cmdlet suivant retourne une liste d’activités de diagnostic, triées de la plus récente à la plus ancienne.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Comme pour les autres cmdlets PowerShell Windows Virtual Desktop, vous devez utiliser le paramètre **-TenantName** pour spécifier le nom de l’abonné que vous voulez utiliser pour votre requête. Le nom du locataire est applicable à presque toutes les requêtes d’activité de diagnostic.

### <a name="retrieve-detailed-diagnostic-activities"></a>Récupérer des activités de diagnostics détaillées

Le paramètre **-Detailed** fournit des informations supplémentaires pour chaque activité de diagnostic retournée. Le format de chaque activité varie en fonction de son type d’activité. Le paramètre **-Detailed** peut être ajouté à n’importe quelle requête **Get-RdsDiagnosticActivities**, comme montré dans l’exemple suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Récupérer une activité de diagnostic spécifique par ID d’activité

Le paramètre **-ActivityId** retourne une activité de diagnostic spécifique (s’il y en a une), comme montré dans l’exemple de cmdlet suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrer les activités de diagnostic par utilisateur

Le paramètre **-UserName** retourne une liste d’activités de diagnostic initiées par l’utilisateur spécifié, comme montré dans l’exemple de cmdlet suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Le paramètre **-UserName** peut aussi être combiné à d’autres paramètres de filtrage facultatifs.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrer les activités de diagnostic par période

Vous pouvez filtrer la liste des activités de diagnostic avec les paramètres **-StartTime** et **-EndTime**. Le paramètre **-StartTime** retourne une liste des activités de diagnostic à partir d’une date spécifique, comme montré dans l’exemple suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Le paramètre **-EndTime** peut être ajouté à une cmdlet avec le paramètre **-StartTime** pour spécifier une période pour laquelle vous souhaitez recevoir des résultats. L’exemple de cmdlet suivant retourne une liste d’activités de diagnostic entre le 1er août et le 10 août.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Les paramètres **-StartTime** et **-EndTime** peuvent aussi être combinés à d’autres paramètres de filtrage facultatifs.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrer les activités de diagnostic par type d’activité

Vous pouvez aussi filtrer les activités de diagnostic par type d’activité avec le paramètre **-ActivityType**. La cmdlet suivante retourne une liste de connexions d’utilisateur :

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

La cmdlet suivante retourne une liste de tâches de gestion d’administrateur :

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

La cmdlet **Get-RdsDiagnosticActivities** ne prend actuellement pas en charge Feed (Flux) comme ActivityType (type d’activité).

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrer les activités de diagnostic par résultat

Vous pouvez filtrer la liste des activités de diagnostic par résultat avec le paramètre **-Outcome**. L’exemple de cmdlet suivant retourne une liste d’activités de diagnostic réussies.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

L’exemple de cmdlet suivant retourne une liste d’activités de diagnostic échouées.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Le paramètre **-Outcome** peut aussi être combiné à d’autres paramètres de filtrage facultatifs.

## <a name="common-error-scenarios"></a>Scénarios d’erreur courants

Les scénarios d’erreur sont classés en interne au service et en externe à Windows Virtual Desktop.

* Problème interne : spécifie les scénarios qui ne peuvent pas être atténués par l’administrateur client et qui doivent être résolus en tant que problème de support. Lorsque vous fournissez des commentaires via [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), incluez l’ID d’activité, puis spécifiez une période approximative pour l’erreur.
* Problème externe : fait référence aux scénarios qui peuvent être atténués par l’administrateur système. Il s’agit de problèmes externes à Windows Virtual Desktop.

Le tableau suivant répertorie les erreurs courantes que pourraient rencontrer vos administrateurs.

>[!NOTE]
>Cette version préliminaire n’inclut pas une catégorisation complète des erreurs et sera mise à jour régulièrement. Pour être sûr de disposer des informations les plus à jour, veillez à consulter cet article au moins une fois par mois.

### <a name="external-management-error-codes"></a>Codes d’erreur de gestion externe

|Code numérique|Code d'erreur|Solution suggérée|
|---|---|---|
|3|UnauthorizedAccess|L’utilisateur qui a tenté d’exécuter la cmdlet PowerShell d’administration n’est pas autorisé à le faire ou a mal tapé son nom d’utilisateur.|
|1 000|TenantNotFound|Le nom de l’abonné que vous avez saisi ne correspond à aucun abonné existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Vous ne pouvez pas supprimer un abonné tant qu’il contient des objets. Supprimez d’abord les pools d’hôte de la session, puis réessayez.|
|2000|HostPoolNotFound|Le nom du pool d’hôte que vous avez saisi ne correspond à aucun pool d’hôte existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Vous ne pouvez pas supprimer un pool d’hôte tant qu’il contient des objets. Supprimez d’abord tous les groupes d’applications dans le pool d’hôte.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Supprimez tous les hôtes de la session avant de supprimer le pool d’hôte de la session.|
|5001|SessionHostNotFound|L’hôte de la session interrogé est peut-être hors ligne. Vérifiez l’état du pool d’hôte.|
|5008|SessionHostUserSessionsExist |Vous devez déconnecter tous les utilisateurs de l’hôte de la session avant d’exécuter l’activité de gestion souhaitée.|
|6000|AppGroupNotFound|Le nom du groupe d’applications que vous avez saisi ne correspond à aucun groupe d’applications existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|6022|RemoteAppNotFound|Le nom de l’application RemoteApp que vous avez saisi ne correspond à aucune application RemoteApp. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|6010|PublishedItemsExist|Le nom de la ressource que vous tentez de publier est identique à une ressource existante. Changez le nom de la ressource, puis réessayez.|
|7002|NameNotValidWhiteSpace|N’utilisez pas d’espace blanc dans le nom.|
|8000|InvalidAuthorizationRoleScope|Le nom du rôle que vous avez saisi ne correspond à aucun nom de rôle existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez. |
|8001|UserNotFound |Le nom d’utilisateur que vous avez saisi ne correspond à aucun nom d’utilisateur existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|8005|UserNotFoundInAAD |Le nom d’utilisateur que vous avez saisi ne correspond à aucun nom d’utilisateur existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|8008|TenantConsentRequired|Suivez les instructions [ici](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) pour donner votre consentement à votre abonné.|

### <a name="external-connection-error-codes"></a>Codes d’erreur de connexion externe

|Code numérique|Code d'erreur|Solution suggérée|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L’hôte de la session n’est pas correctement joint à Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Les connexions ont échoué car l’hôte de la session est indisponible. Vérifiez l’intégrité de l’hôte de la session.|
|-2146233088|ConnectionFailedClientDisconnect|Si vous rencontrez souvent cette erreur, assurez-vous que l’ordinateur est connecté au réseau.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La session à laquelle l’utilisateur hôte a tenté de se connecter n’est pas viable. Déboguez la machine virtuelle.|
|-2146233088|ConnectionFailedUserNotAuthorized|L’utilisateur n’est pas autorisé à accéder à l’application publiée ou au bureau. Cette erreur peut apparaître après la suppression de ressources publiées par l’administrateur. Demandez à l’utilisateur d’actualiser le flux dans l’application de bureau à distance.|
|2|FileNotFound|L’application à laquelle l’utilisateur tente d’accéder est incorrectement installée ou son chemin d’accès est invalide.|
|3|InvalidCredentials|Le nom d’utilisateur ou le mot de passe saisi par l’utilisateur ne correspond à aucun nom d’utilisateur ou mot de passe existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|8|ConnectionBroken|La connexion entre le client et passerelle ou serveur a été interrompue. Aucune action n’est nécessaire, sauf si cela se produit de manière inattendue.|
|14|UnexpectedNetworkDisconnect|La connexion au réseau a été perdue. Demandez à l’utilisateur de se reconnecter.|
|24|ReverseConnectFailed|La machine virtuelle hôte n’a aucune une ligne directe vers la passerelle Bureau à distance. Vérifiez que l’adresse IP de la passerelle peut être résolue.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les rôles au sein de Windows Virtual Desktop, consultez [Environnement Windows Virtual Desktop](environment-setup.md).

Pour obtenir une liste des cmdlets PowerShell disponibles pour Windows Virtual Desktop, consultez le document de [référence PowerShell](/powershell/windows-virtual-desktop/overview).
