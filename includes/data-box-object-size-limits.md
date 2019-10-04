---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244660"
---
Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~4.75 Tio                                                 |
| Objet blob de pages         | 8 Tio <br> Chaque fichier chargé dans le format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |
| Azure Files        | 1 Tio                                                      |
| Disques managés     | 4 Tio <br> Pour plus d’informations sur la taille et les limites, consultez : <li>[Objectifs d’évolutivité des disques SSD Standard](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Objectifs d’évolutivité des disques SSD Premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Objectifs d’évolutivité des disques HDD Standard](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Tarification et facturation des disques managés](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
