---
title: Mettre à jour les cibles de stockage Azure HPC Cache
description: Comment modifier les cibles de stockage Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092454"
---
# <a name="edit-storage-targets"></a>Modifier les cibles de stockage

Vous pouvez supprimer ou modifier une cible de stockage à partir de la page de portail **Cibles de stockage** du cache ou à l’aide de l’interface de ligne de commande Azure.

> [!TIP]
> La [vidéo Gestion d’Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) montre comment modifier une cible de stockage dans le portail Azure.

## <a name="remove-a-storage-target"></a>Supprimer une cible de stockage

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour supprimer une cible de stockage, sélectionnez-la dans la liste et cliquez sur le bouton **Supprimer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilisez [az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) pour supprimer une cible de stockage du cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Cette action supprime l’association de la cible de stockage avec ce système Azure HPC Cache, mais elle ne modifie pas le système de stockage back-end. Par exemple, si vous avez utilisé un conteneur Stockage Blob Azure, le conteneur et son contenu existent toujours une fois que vous l’avez supprimé du cache. Vous pouvez ajouter le conteneur à un autre Azure HPC Cache, le rajouter à ce cache ou le supprimer avec le portail Azure.

Toutes les modifications de fichiers stockées dans le cache sont écrites dans le système de stockage back-end avant la suppression de la cible de stockage. Ce processus peut prendre une heure ou plus si un grand nombre de données modifiées se trouvent dans le cache.

## <a name="update-storage-targets"></a>Mettre à jour des cibles de stockage

Vous pouvez modifier les cibles de stockage, afin de changer la valeur de certaines de leurs propriétés. Différentes propriétés peuvent être modifiées, en fonction du type de stockage :

* Pour les cibles de stockage d’objets blob, vous pouvez modifier le chemin d’accès à l’espace de noms.

* Pour les cibles de stockage NFS, vous pouvez modifier les propriétés suivantes :

  * Chemin de l’espace de noms
  * Modèle d’utilisation
  * Exporter
  * Exporter un sous-répertoire

Vous ne pouvez pas modifier le nom, le type ou le système de stockage back-end d’une cible de stockage (conteneur d’objets blob ou nom d’hôte/adresse IP NFS). Si vous avez besoin de modifier ces propriétés, supprimez la cible de stockage et créez-en une autre, avec la nouvelle valeur.

Dans le portail Azure, vous pouvez voir quels champs sont modifiables en cliquant sur le nom de la cible de stockage et en ouvrant la page de détails correspondante. Vous pouvez également modifier les cibles de stockage à l’aide de l’interface de ligne de commande Azure.

![Capture d’écran de la page de modification d’une cible de stockage NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Mettre à jour cible de stockage NFS

Pour une cible de stockage NFS, vous pouvez mettre à jour plusieurs propriétés. (Reportez-vous à la capture d’écran ci-dessus pour obtenir un exemple des modifications possibles.)

* **Modèle d’utilisation** : le modèle d’utilisation influe sur le mode de conservation des données par le cache. Pour en savoir plus, voir [Choisir un modèle d’utilisation](hpc-cache-add-storage.md#choose-a-usage-model).
* **Chemin de l’espace de noms virtuels** : chemin d’accès utilisé par les clients pour monter cette cible de stockage. Pour en savoir plus, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
* **Chemin d’exportation NFS** : chemin d’exportation du système de stockage à utiliser pour ce chemin d’accès à l’espace de noms.
* **Chemin de sous-répertoire** : sous-répertoire (sous l’exportation) à associer à ce chemin d’accès à l’espace de noms. Laissez ce champ vide si vous n’avez pas besoin de spécifier un sous-répertoire.

Chaque chemin d’accès à l’espace de noms a besoin d’une combinaison unique de chemin d’exportation et de sous-répertoire. Autrement dit, vous ne pouvez pas créer deux chemins d’accès côté client différents dans le même répertoire sur le système de stockage back-end.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Après avoir apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilisez la commande [az nfs-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) pour modifier le modèle d’utilisation, le chemin de l’espace de noms virtuel et les valeurs d’exportation ou de sous-répertoire NFS d’une cible de stockage.

* Pour modifier le modèle d’utilisation, utilisez l’option ``--nfs3-usage-model``. Exemple : ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Pour modifier le chemin de l’espace de noms, exporter ou exporter le sous-répertoire, utilisez l’option ``--junction``.

  Le paramètre ``--junction`` utilise les valeurs suivantes :

  * ``namespace-path`` : chemin d’accès au fichier virtuel orienté client
  * ``nfs-export`` : exportation du système de stockage à associer au chemin d’accès orienté client
  * ``target-path`` (facultatif) : sous-répertoire de l’exportation, si nécessaire

  Exemple : ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Le nom du cache, le nom de la cible de stockage et le groupe de ressources sont requis dans toutes les commandes de mise à jour.

Exemple de commande : <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Si le cache est arrêté ou n’est pas sain, la mise à jour s’applique une fois que le cache est sain.

---

## <a name="update-an-azure-blob-storage-target"></a>Mettre à jour une cible de stockage d’objets blob Azure

Pour une cible de stockage de blobs, vous pouvez modifier le chemin de l’espace de noms virtuel.

### <a name="portal"></a>[Portail](#tab/azure-portal)

La page de détails d’une cible de stockage d’objets blob vous permet de modifier le chemin d’accès à l’espace de noms virtuels.

![Capture d’écran de la page de modification d’une cible de stockage d’objets blob](media/hpc-cache-edit-storage-blob.png)

Lorsque vous avez terminé, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilisez [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) pour mettre à jour le chemin de l’espace de noms d’une cible.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Si le cache est arrêté ou n’est pas sain, la mise à jour s’applique une fois que le cache est sain.

---

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur ces options, voir [Ajouter des cibles de stockage](hpc-cache-add-storage.md).
* Pour plus d’informations sur l’utilisation des chemins d’accès virtuels, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
