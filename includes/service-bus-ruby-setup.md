---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111213"
---
## <a name="create-a-ruby-application"></a>Création d'une application Ruby
Pour obtenir des instructions, consultez le guide [Création d’une application Ruby sur Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l'utilisation de Service Bus
Pour utiliser Service Bus, téléchargez et utilisez le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilisation de RubyGems pour obtenir le package
1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### <a name="import-the-package"></a>Importation du package
À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configuration d’une connexion Service Bus
Utilisez le code suivant pour définir les valeurs d’espace de noms, de nom de la clé, de la clé, du signataire et de l’hôte :

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Attribuez à l'espace de noms la valeur que vous avez créée plutôt que l'URL entière. Par exemple, utilisez **« votreespacedenomsexemple »** et non « votreespacedenomsexemple.servicebus.windows.net ».
