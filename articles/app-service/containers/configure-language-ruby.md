---
title: Configurer des applications Ruby - Azure App Service
description: Ce tutoriel décrit les options relatives à la création et à la configuration d’une application Ruby pour Azure App Service sur Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 402c85e7902c8c2f612ad6c777d8f6773a4d0ca3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549552"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurer une application Ruby Linux pour Azure App Service

Cet article explique la façon dont [Azure App Service](app-service-linux-intro.md) exécute des applications Ruby, et comment vous pouvez personnaliser le comportement d’App Service si nécessaire. Les applications Ruby doivent être déployées avec tous les modules [pip](https://pypi.org/project/pip/) nécessaires.

Ce guide fournit des concepts et des instructions clés aux développeurs Ruby qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, vous devez d’abord suivre le [guide de démarrage rapide Ruby](quickstart-ruby.md) et le [tutoriel sur l’utilisation de Ruby avec PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Afficher la version de Ruby

Pour afficher la version actuelle de Ruby, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher toutes les versions prises en charge de Ruby, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Vous pouvez exécuter une version non prise en charge de Ruby en créant votre propre image conteneur. Pour plus d’informations, consultez [Utiliser une image Docker personnalisée](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Définir la version de Ruby

Exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) pour définir la version 2.3 de Ruby :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Si vous voyez des erreurs semblables à celles-ci au moment du déploiement :
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> or
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Cela signifie que la version de Ruby configurée dans votre projet est différente de celle installée dans le conteneur que vous exécutez (`2.3.3`, dans l’exemple ci-dessus). Dans l’exemple ci-dessus, regardez *Gemfile* et *.ruby-version* pour voir si la version de Ruby n’est pas définie ou si elle est définie sur la version qui est installée dans le conteneur que vous exécutez (`2.3.3`, dans l’exemple ci-dessus).

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle standard [ENV['<nom-chemin>']](https://ruby-doc.org/core-2.3.3/ENV.html). Par exemple, pour accéder à un paramètre d’application nommé `WEBSITE_SITE_NAME`, utilisez le code suivant :

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personnaliser le déploiement

Si vous déployez un [dépôt Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ou un [package Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) lorsque les processus de génération sont activés, le moteur de déploiement (Kudu) exécute automatiquement les étapes de post-déploiement suivantes par défaut :

1. Vérifiez qu’un *Gemfile* existe.
1. Exécutez `bundle clean`. 
1. Exécutez `bundle install --path "vendor/bundle"`.
1. Exécutez `bundle package` pour empaqueter les gemmes dans le dossier du fournisseur ou du cache.

### <a name="use---without-flag"></a>Utiliser l’indicateur --without

Pour exécuter `bundle install` avec l’indicateur [--without](https://bundler.io/man/bundle-install.1.html), définissez le [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) `BUNDLE_WITHOUT` sur une liste de groupes séparés par des virgules. Par exemple, la commande suivante le définit sur `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Si ce paramètre est défini, le moteur de déploiement exécute `bundle install` avec `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Précompiler les ressources

Par défaut, les étapes de post-déploiement ne précompilent pas les ressources. Pour activer la précompilation des ressources, définissez le [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) `ASSETS_PRECOMPILE` sur `true`. Ensuite, la commande `bundle exec rake --trace assets:precompile` est exécutée à la fin des étapes de post-déploiement. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Pour plus d’informations, consultez [Traiter les ressources statiques](#serve-static-assets).

## <a name="customize-start-up"></a>Personnaliser le démarrage

Par défaut, le conteneur Ruby démarre le serveur Rails en suivant la procédure ci-dessous (pour plus d’informations, consultez le [script de démarrage](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)) :

1. Il génère une valeur [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), si elle n’existe pas déjà. Cette valeur est nécessaire pour que l’application puisse s’exécuter en mode production.
1. Définissez la variable d’environnement `RAILS_ENV` sur `production`.
1. Supprimez le fichier *.pid* qui se trouve dans le répertoire *tmp/pids* depuis une précédente exécution d’un serveur Rails.
1. Vérifiez que toutes les dépendances sont installées. Si ce n’est pas le cas, essayez d’installer les gemmes à partir du *répertoire du fournisseur ou du cache* local.
1. Exécutez `rails server -e $RAILS_ENV`.

Vous pouvez personnaliser le processus de démarrage de différentes manières :

- [En traitant les ressources statiques](#serve-static-assets)
- [En exécutant en mode hors production](#run-in-non-production-mode)
- [En définissant manuellement secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Traiter les ressources statiques

Le serveur Rails du conteneur Ruby s’exécute en mode production par défaut, et [part du principe que les ressources sont précompilées et traitées par votre serveur web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Pour traiter les ressources statiques à partir du serveur Rails, vous devez faire deux choses :

- **Précompiler les ressources** - [Précompilez les ressources statiques localement](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) et déployez-les manuellement. Vous pouvez aussi laisser le moteur de déploiement se charger de la précompilation (consultez [Précompiler les ressources](#precompile-assets)).
- **Activer le traitement des fichiers statiques** - Pour traiter les ressources statiques du conteneur Ruby, [définissez le paramètre d’application `RAILS_SERVE_STATIC_FILES`](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) sur `true`. Par exemple : 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Exécuter en mode hors production

Par défaut, le serveur Rails s’exécute en mode production. Pour exécuter en mode développement, par exemple, définissez le [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) `RAILS_ENV` sur `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Toutefois, ce paramètre seul entraîne le démarrage du serveur Rails en mode développement, qui accepte uniquement les requêtes localhost et qui n’est pas accessible en dehors du conteneur. Pour accepter les requêtes des clients distants, définissez le [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) `APP_COMMAND_LINE` sur `rails server -b 0.0.0.0`. Ce paramètre d’application vous permet d’exécuter une commande personnalisée dans le conteneur Ruby. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>Définir manuellement secret_key_base

Pour utiliser votre propre valeur `secret_key_base` au lieu de laisser App Service en générer une pour vous, définissez le [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) `SECRET_KEY_BASE` sur la valeur souhaitée. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application Rails avec PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)