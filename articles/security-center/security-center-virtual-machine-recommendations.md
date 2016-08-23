<properties
   pageTitle="Protection de vos machines virtuelles dans Azure Security Center | Microsoft Azure"
   description="Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos machines virtuelles et à rester en conformité avec les stratégies de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protection de vos machines virtuelles dans Azure Security Center

Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles, mise en réseau, applications et SQL.

Cet article traite des recommandations qui s’appliquent aux machines virtuelles. Les recommandations relatives aux machines virtuelles se concentrent autour de la collecte de données, de l’application des mises à jour du système, de la configuration du logiciel anti-programme malveillant, du chiffrement de vos disques de machines virtuelles, et bien plus encore. Utilisez le tableau ci-dessous pour mieux comprendre les recommandations disponibles pour les machines virtuelles et leurs effets.

## Recommandations disponibles pour les machines virtuelles

|Recommandation|Description|
|-----|-----|
|[Activer la collecte des données pour des abonnements](security-center-enable-data-collection.md)|Recommande l’activation de la collecte des données dans la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles de vos abonnements.|
|[Corriger des vulnérabilités du système d’exploitation](security-center-remediate-os-vulnerabilities.md)|Recommande d’aligner les configurations de votre système d’exploitation sur les règles de configuration recommandées, comme le fait de ne pas permettre l’enregistrement des mots de passe.|
|[Appliquer des mises à jour système](security-center-apply-system-updates.md)|Recommande le déploiement des mises à jour de sécurité du système et des mises à jour critiques manquantes sur les machines virtuelles.|
|[Redémarrage après des mises à jour système](security-center-apply-system-updates.md#reboot-after-system-updates)|Recommande de redémarrer une machine virtuelle pour terminer le processus de mise à jour du système.|
|[Installer Endpoint Protection](security-center-install-endpoint-protection.md)|Recommande l’approvisionnement de logiciels anti-programme malveillant sur les machines virtuelles (Windows uniquement).|
|[Résoudre les alertes d’intégrité Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recommande la résolution des défaillances de protection de point de terminaison.|
|[Activer l’agent de machine virtuelle](security-center-enable-vm-agent.md)|Vous permet de connaître les machines virtuelles qui nécessitent l’agent de machine virtuelle. L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle.|
| [Apply disk encryption (Appliquer le chiffrement de disque Azure Disk Encryption)](security-center-apply-disk-encryption.md) |Recommande le chiffrement des disques des machines virtuelles à l’aide d’Azure Disk Encryption (Windows et Linux). Le chiffrement est recommandé pour les systèmes d’exploitation et les volumes de données de votre machine virtuelle.|
| [Mettre à jour la version du système d’exploitation](security-center-update-os-version.md) | Recommande de mettre à jour la version du système d’exploitation de votre service Cloud vers la version la plus récente disponible pour votre famille de systèmes d’exploitation. Pour en savoir plus sur Cloud Services, consultez [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md). |

## Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

- [Protection de vos applications dans Azure Security Center](security-center-application-recommendations.md)
- [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
- [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

<!---HONumber=AcomDC_0810_2016-->