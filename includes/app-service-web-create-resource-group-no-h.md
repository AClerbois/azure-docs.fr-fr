Dans Cloud Shell, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *Europe de l’Ouest*.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. Pour afficher tous les emplacements pris en charge pour les plans App Service, exécutez la commande `az appservice list-locations`.