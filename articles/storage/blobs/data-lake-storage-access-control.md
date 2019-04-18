---
title: Vue d’ensemble du contrôle d’accès dans Azure Data Lake Storage Gen2 | Microsoft Docs
description: Comprendre le fonctionnement du contrôle d’accès dans Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 4ba8977180e33256bfdc6652811495a02a9ef19c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58802951"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Contrôle d’accès dans Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implémente un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) Azure et les listes de contrôle d’accès (ACL) POSIX. Cet article présente les notions de base du modèle de contrôle d’accès pour Data Lake Storage Gen2. 

## <a name="azure-role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle Azure (RBAC)

Le contrôle d’accès en fonction du rôle (RBAC) Azure utilise des attributions de rôles pour appliquer efficacement des ensembles d’autorisations sur les utilisateurs, groupes et principaux de service des ressources Azure. En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (*par exemple,* comptes de Stockage Azure). Avec le Stockage Azure, et par conséquent Azure Data Lake Storage Gen2, ce mécanisme a été étendu à la ressource du système de fichiers.

Même si l’affectation de rôle RBAC est un mécanisme puissant pour contrôler les autorisations utilisateur, il s’agit d’un mécanisme grossier par rapport aux listes de contrôle d’accès. La granularité la plus fine que gère la fonction RBAC se trouve au niveau du système de fichiers, et cela sera évalué en priorité par rapport aux ACL. Par conséquent, si vous affectez des autorisations RBAC sur un système de fichiers, l’utilisateur ou le principal de service concerné disposera d’une autorisation pour TOUS les répertoires et fichiers de ce système de fichiers, indépendamment des attributions ACL.

Le Stockage Azure propose trois rôles RBAC intégrés pour le stockage Blob : 

- [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)

Quand un utilisateur ou un principal de service reçoit des autorisations RBAC d’accès aux données via l’un de ces rôles prédéfinis, ou via un rôle personnalisé, ces autorisations sont évaluées en premier lors de l’autorisation d’une demande. Si l’opération demandée est autorisée par les attributions RBAC de l’appelant, l’autorisation est immédiatement résolue et aucune vérification supplémentaire n’est réalisée au niveau des ACL. Si l’appelant n’a pas de rôle RBAC attribué ou si l’exécution de la demande ne correspond pas à l’autorisation accordée, des vérifications ACL sont effectuées pour déterminer si l’appelant est autorisé à exécuter l’opération demandée.

Note spéciale sur le rôle prédéfini de propriétaire des données Blob du stockage. Si ce rôle RBAC est attribué à l’appelant, l’utilisateur est considéré comme un *super utilisateur* et il bénéficie d’un accès complet à toutes les opérations de mutation, notamment la définition du propriétaire d’un répertoire ou d’un fichier ainsi que des ACL pour les répertoires et fichiers dont ils ne sont pas propriétaires. L’accès de super utilisateur constitue la seule manière autorisée de modifier le propriétaire d’une ressource.

## <a name="shared-key-and-shared-access-signature-authentication"></a>Authentification avec une clé partagée et une signature d’accès partagé

Azure Data Lake Storage Gen2 prend en charge les méthodes d’authentification par clé partagée et par signature d’accès partagé. Une caractéristique de ces méthodes d’authentification est qu’aucune identité n’est associée à l’appelant. Par conséquent aucune permission basée sur une autorisation utilisateur ne peut pas être accordée.

Dans le cas des clés partagées, l’appelant bénéficie effectivement d’un accès de super utilisateur, autrement dit d’un accès complet à toutes les opérations sur toutes les ressources, y compris la définition du propriétaire et la modification des ACL.

Les jetons SAP incluent les autorisations accordées dans le jeton. Les autorisations incluses dans le jeton SAP sont appliquées à toutes les décisions d’autorisation, mais aucune vérification ACL supplémentaire n’est effectuée.

## <a name="access-control-lists-on-files-and-directories"></a>Listes de contrôle d’accès sur les fichiers et répertoires

Il existe deux types de listes de contrôle d’accès (ACL) : les ACL d’accès et les ACL par défaut.

* **ACL d’accès** : Les ACL d’accès contrôlent l’accès à un objet. Les fichiers et les répertoires ont tous des ACL d’accès.

* **ACL par défaut** : Un modèle d’ACL associées à un répertoire, qui détermine les ACL d’accès pour tous les éléments enfants créés dans ce répertoire. Les fichiers n’ont pas d’ACL par défaut.

Les ACL d’accès et les ACL par défaut ont la même structure.

> [!NOTE]
> La modification de l’ACL par défaut d’un parent n’affecte pas l’ACL d’accès ni l’ACL par défaut des éléments enfants qui existent déjà.

## <a name="permissions"></a>Autorisations

Les autorisations sur un objet de système de fichiers sont **Lecture**, **Écriture** et **Exécution**. Elles peuvent être utilisées sur les fichiers et les répertoires comme l’indique le tableau ci-dessous :

|            |    Fichier     |   Répertoire |
|------------|-------------|----------|
| **Lecture (R)** | Permet de lire le contenu d’un fichier | Requiert les autorisations **Lecture** et **Exécution** pour répertorier le contenu du répertoire |
| **Écriture (W)** | Permet d’écrire ou d’ajouter du contenu dans un fichier | Requiert les autorisations **Écriture** et **Exécution** pour créer des éléments enfants dans un répertoire |
| **Exécution (X)** | Cela ne signifie rien dans le contexte de Data Lake Storage Gen2 | Requise pour parcourir les éléments enfants d’un répertoire |

### <a name="short-forms-for-permissions"></a>Formes abrégées des autorisations

**RWX** correspond à **Lecture + Écriture + Exécution**. Il existe une forme numérique plus condensée dans laquelle **Lecture = 4**, **Écriture = 2** et **Exécution = 1**. Les autorisations sont représentées par la somme de ces chiffres. Voici quelques exemples.

| Forme numérique | Forme abrégée |      Signification     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lecture + Écriture + Exécution |
| 5.            | `R-X`        | Lecture + Exécution         |
| 4            | `R--`        | Lire                   |
| 0            | `---`        | | Aucune autorisation         |

### <a name="permissions-inheritance"></a>Héritage des autorisations

Dans le modèle POSIX utilisé par Data Lake Storage Gen2, les autorisations d’un élément sont stockées sur l’élément lui-même. En d’autres termes, les autorisations sur un élément ne peuvent pas être héritées à partir des éléments parents si les autorisations sont définies après la création de l’élément enfant. Les autorisations sont héritées uniquement si les autorisations par défaut ont été définies sur les éléments parents avant la création des éléments enfants.

## <a name="common-scenarios-related-to-permissions"></a>Scénarios courants liés aux autorisations

Le tableau suivant répertorie quelques scénarios courants pour vous aider à comprendre les autorisations nécessaires pour effectuer certaines opérations sur un compte Data Lake Storage Gen2.

|    Opération             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Lire Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Ajouter à Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Supprimer Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Créer Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Répertorier /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Répertorier /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Répertorier /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Les autorisations d’écriture ne sont pas nécessaires pour supprimer le fichier, tant que les deux conditions précédentes sont remplies.
>
>

## <a name="users-and-identities"></a>Utilisateurs et identités

Chaque fichier et répertoire dispose d’autorisations distinctes pour ces identités :

- L’utilisateur propriétaire
- Le groupe propriétaire
- Les utilisateurs nommés
- Les groupes nommés
- Les principaux de service nommés
- Tous les autres utilisateurs

Les identités des utilisateurs et des groupes sont des identités Azure Active Directory (Azure AD). Sauf mention contraire, dans le contexte de Data Lake Storage Gen2, un *utilisateur* peut désigner un utilisateur, un principal de service ou un groupe de sécurité Azure AD.

### <a name="the-owning-user"></a>L’utilisateur propriétaire

L’utilisateur qui a créé l’élément est automatiquement l’utilisateur propriétaire de l’élément. Les utilisateurs propriétaires peuvent :

* modifier les autorisations de leurs fichiers ;
* modifier le groupe propriétaire d’un fichier détenu, tant que l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> L’utilisateur propriétaire *ne peut pas* modifier l’utilisateur propriétaire d’un fichier ou d’un répertoire. Seuls les super utilisateurs peuvent modifier l’utilisateur propriétaire d’un fichier ou d’un répertoire.

### <a name="the-owning-group"></a>Le groupe propriétaire

Dans les ACL POSIX, chaque utilisateur est associé à un *groupe principal*. Par exemple, l’utilisateur « Alice » peut appartenir au groupe « Finance ». Alice peut appartenir à plusieurs groupes, mais un groupe est toujours désigné comme son groupe principal. Dans POSIX, lorsqu’Alice crée un fichier, son groupe principal est défini comme groupe propriétaire de ce fichier (en l’occurrence, « finance »). Sinon, le groupe propriétaire se comporte comme pour les autorisations assignées à d’autres utilisateurs/groupes.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Affectation du groupe propriétaire pour un nouveau fichier ou répertoire

* **Cas n° 1** : Répertoire racine "/". Ce répertoire est créé lors de la création d’un système de fichiers Data Lake Storage Gen2. Dans ce cas, le groupe propriétaire est celui de l’utilisateur qui a créé le système de fichiers si l’opération est réalisée avec OAuth. Si le système de fichiers est créé à l’aide d’une clé partagée, d’une SAP de compte ou d’une SAP de service, le propriétaire et le groupe propriétaire sont définis sur **$superuser**.
* **Cas 2** (tous les autres cas) : lorsqu’un nouvel élément est créé, le groupe propriétaire est copié à partir du répertoire parent.

#### <a name="changing-the-owning-group"></a>Modification du groupe propriétaire

Le groupe propriétaire peut être modifié par :
* un super utilisateur ;
* l’utilisateur propriétaire, si l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> Le groupe propriétaire ne peut pas modifier les ACL d’un fichier ou d’un répertoire.  Alors que le groupe d’appartenance est défini sur l’utilisateur qui a créé le compte dans le cas du répertoire racine, **Cas 1** ci-dessus, un seul compte d’utilisateur n’est pas valide pour accorder des autorisations via le groupe d’appartenance. Si applicable, vous pouvez assigner cette autorisation à un groupe d’utilisateurs valide.

## <a name="access-check-algorithm"></a>Algorithme de vérification des accès

Le pseudocode suivant représente l’algorithme de vérification des accès pour les comptes Data Lake Storage Gen2.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask IS NOT used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Le masque

Comme illustré dans l’algorithme de vérification des accès, le masque limite l’accès pour les utilisateurs nommés, le groupe propriétaire et les groupes nommés.  

> [!NOTE]
> Pour un nouveau système de fichiers Data Lake Storage Gen2, la valeur par défaut du masque d’ACL du répertoire racine ("/") est de 750 pour les répertoires et 640 pour les fichiers. Les fichiers ne reçoivent pas le bit X, car il est sans importance pour les fichiers dans un système de stockage uniquement.
>
> Le masque peut être spécifié par appel. Ainsi, différents systèmes de consommation, tels que les clusters, peuvent être associés à différents masques plus efficaces pour leurs opérations de fichier. Si un masque est spécifié sur une requête donnée, il remplace complètement le masque par défaut.

### <a name="the-sticky-bit"></a>Le sticky bit

Le sticky bit est une fonctionnalité avancée d’un système de fichiers POSIX. Dans le contexte de Data Lake Storage Gen2, il est peu probable que le sticky bit soit nécessaire. En résumé, si le sticky bit est activé sur un répertoire, un élément enfant peut uniquement être supprimé ou renommé par l’utilisateur propriétaire de l’élément enfant.

Le sticky bit n’est pas affiché dans le portail Azure.

## <a name="default-permissions-on-new-files-and-directories"></a>Autorisations par défaut sur les nouveaux fichiers et répertoires

Lorsqu’un nouveau fichier ou répertoire est créé dans un dossier existant, l’ACL par défaut sur le répertoire parent détermine :

- L’ACL par défaut et l’ACL d’accès d’un répertoire enfant.
- L’ACL d’accès d’un fichier enfant (les fichiers n’ont pas d’ACL par défaut).

### <a name="umask"></a>umask

Quand vous créez un fichier ou répertoire, l’umask est utilisé pour modifier la façon dont les ACL par défaut sont définies sur l’élément enfant. L’umask est une valeur 9 bits sur les répertoires parents qui contient une valeur RWX pour **l’utilisateur propriétaire**, le **groupe propriétaire** et **d’autres rôles**.

L’umask pour Azure Data Lake Storage Gen2 est une valeur constante définie sur 007. Cette valeur se traduit par :

| Composant umask     | Forme numérique | Forme abrégée | Signification |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pour l’utilisateur propriétaire, copiez l’ACL par défaut du parent dans l’ACL d’accès de l’enfant | 
| umask.owning_group  |    0         |   `---`      | Pour le groupe propriétaire, copiez l’ACL par défaut du parent dans l’ACL d’accès de l’enfant | 
| umask.other         |    7         |   `RWX`      | Pour d’autres rôles, supprimez toutes les autorisations de l’ACL d’accès de l’enfant |

La valeur umask utilisée par Azure Data Lake Storage Gen2 signifie effectivement que la valeur pour d’**autres** rôles n’est jamais transmise par défaut sur les nouveaux enfants, quelle que soit l’indication de l’ACL par défaut. 

Le pseudocode suivant montre comment l’umask est appliqué lors de la création des ACL pour un élément enfant.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Questions les plus fréquentes sur les ACL dans Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Dois-je activer la prise en charge des ACL ?

Non. Le contrôle d’accès via ACL est activé pour un compte Data Lake Storage Gen2 tant que la fonctionnalité d’espace de noms hiérarchique est activée.

Si cette fonctionnalité est désactivée, les règles d’autorisation Azure RBAC s’appliquent toujours.

### <a name="what-is-the-best-way-to-apply-acls"></a>Quelle est la meilleure façon d’appliquer des ACL ?

Utilisez toujours les groupes de sécurité Azure AD comme principal affecté dans les listes ACL. Résistez à la possibilité d’attribuer directement des utilisateurs ou des principaux de service individuels. L’utilisation de cette structure vous permettra d’ajouter et de supprimer des utilisateurs ou des principaux de service sans devoir réappliquer les ACL sur la structure de répertoires dans son ensemble. Au lieu de cela, vous devez simplement les ajouter ou les supprimer du groupe de sécurité Azure AD approprié. N’oubliez pas que les ACL ne sont pas héritées. Par conséquent, la réapplication des ACL requiert la mise à jour de ces dernières sur chaque fichier et sous-répertoire. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quelles sont les autorisations nécessaires pour supprimer de manière récursive un répertoire et son contenu ?

- L’appelant dispose des autorisations de « super utilisateur »,

Ou

- L’utilisateur doit disposer des autorisations Écriture + Exécution sur le répertoire parent.
- L’utilisateur doit disposer des autorisations Lecture + Écriture + Exécution sur le répertoire à supprimer et sur tous ses sous-répertoires.

> [!NOTE]
> L’autorisation Écriture n’est pas nécessaire pour supprimer des fichiers situés dans des répertoires. En outre, le répertoire racine « / » ne peut jamais être supprimé.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Qui est le propriétaire d’un fichier ou d’un répertoire ?

Le créateur d’un fichier ou d’un répertoire en devient le propriétaire. Dans le cas du répertoire racine, il s’agit de l’identité de l’utilisateur ayant créé le système de fichiers.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Quel groupe est propriétaire d’un fichier ou d’un répertoire lors de sa création ?

Le groupe propriétaire est copié à partir du groupe propriétaire du répertoire dans lequel le fichier ou le répertoire est créé.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Je suis l’utilisateur propriétaire d’un fichier, mais je n’ai pas les autorisations RWX dont j’ai besoin. Que faire ?

L’utilisateur propriétaire peut modifier les autorisations du fichier pour s’accorder les autorisations RWX dont il a besoin.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Pourquoi vois-je parfois des GUID dans les ACL ?

Un GUID s’affiche si l’entrée représente un utilisateur et que ce dernier n’existe plus dans Azure AD. Cela se produit généralement lorsque l’utilisateur a quitté l’entreprise ou que son compte a été supprimé dans Azure AD. En outre, les principaux de service et les groupes de sécurité ne sont identifiés par aucun UPN. Par conséquent, ils sont représentés par leur attribut OID (un GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Comment définir ACL correctement pour un service principal ?

Lorsque vous définissez des ACL pour les principaux de service, il est important d’utiliser l’ID d’objet (OID) de la *principal du service* pour l’inscription d’application que vous avez créé. Il est important de noter que les applications inscrites ont un principal de service distinct dans spécifique au locataire Azure AD. Applications inscrites ont un OID qui est visible dans le portail Azure, mais la *principal du service* a un autre OID (différent).

Pour obtenir l’OID du principal du service qui correspond à une inscription d’application, vous pouvez utiliser la `az ad sp show` commande. Spécifiez l’ID d’Application comme paramètre. Voici un exemple sur l’obtention de l’OID du principal du service qui correspond à une inscription d’application avec l’Id d’application = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Dans Azure CLI, exécutez la commande suivante :

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Si vous avez l’OID correct pour le principal du service, accédez à l’Explorateur de stockage **gérer l’accès** page pour ajouter l’OID et attribuer des autorisations appropriées pour l’OID. Veillez à sélectionner **enregistrer**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 prend-il en charge l’héritage des ACL ?

Les affectations RBAC Azure peuvent être héritées. Les affectations passent de l’abonnement, du groupe de ressources et des ressources du compte de stockage à la ressource du système de fichiers.

Les ACL ne peuvent pas être héritées. Cependant, les ACL par défaut peuvent être utilisées pour définir les ACL des sous-répertoires et fichiers enfants nouvellement créés sous le répertoire parent. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Comment en savoir plus sur le modèle de contrôle d’accès POSIX ?

* [Listes de contrôle d’accès (ACL) POSIX sur Linux](https://www.linux.com/news/posix-acls-linux)
* [HDFS Permission Guide (Guide des autorisations HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Forum aux questions POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [ACL POSIX sous Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL using access control lists on Linux (ACL utilisant des listes de contrôle d’accès sous Linux)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Voir aussi

* [Présentation d’Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
