<properties
   pageTitle="Utilisation de DNS dynamique pour inscrire les noms d’hôte"
   description="Cette page fournit des détails sur la configuration de DNS dynamique pour inscrire les noms d’hôte sur vos propres serveurs DNS."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Utilisation de DNS dynamique pour inscrire les noms d’hôte sur votre propre serveur DNS
[Azure fournit la résolution de noms](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour les machines virtuelles et les instances de rôle. Cependant, lorsque votre résolution de noms doit aller au-delà de ceux fournis par Azure, vous pouvez fournir vos propres serveurs DNS. Cela vous permet de personnaliser votre solution DNS pour l’adapter à vos propres besoins. Par exemple, vous pouvez avoir besoin d’accéder à des ressources locales avec votre contrôleur de domaine Active Directory.

Lorsque vos serveurs DNS personnalisés sont hébergés en tant que machines virtuelles Azure, vous pouvez transférer les requêtes de nom d'hôte (pour le même réseau virtuel) vers Azure pour résoudre les adresses IP internes. Si vous ne souhaitez pas utiliser cet itinéraire, vous pouvez enregistrer vos machines virtuelles dans votre serveur DNS à l'aide de Dynamic DNS. Comme Azure n’a pas la possibilité (informations d’identification, par exemple) de créer directement des enregistrements dans vos serveurs DNS, d’autres mécanismes sont souvent nécessaires. Voici quelques scénarios courants avec des solutions alternatives.

## Clients Windows
Les clients Windows non joints à un domaine tentent des mises à jour DNS dynamiques non sécurisées lors du démarrage ou de la modifications de leurs adresses IP. Le nom DNS se compose du nom d’hôte et du suffixe DNS principal. Azure laisse le suffixe DNS principal vide, mais ce dernier peut être remplacé dans la machine virtuelle, via l’[interface utilisateur](https://technet.microsoft.com/library/cc794784.aspx) ou [avec Automation](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Les clients Windows joints à un domaine inscrivent leurs adresses IP auprès du contrôleur de domaine à l’aide de la mise à jour DNS dynamique sécurisée. Le processus de jonction de domaine définit le suffixe DNS principal sur le client et gère la relation d’approbation.

## Clients Linux
Généralement, les clients Linux ne s’inscrivent pas eux-mêmes auprès du serveur DNS au démarrage et présument que le serveur DHCP le fait à leur place. Les serveurs DHCP d'Azure n'ont pas la capacité d’inscrire des enregistrements dans votre serveur DNS. Vous pouvez utiliser un outil appelé *nsupdate*, qui est inclus dans le package Bind, pour envoyer des mises à jour DNS dynamiques. Comme le protocole DNS dynamique est normalisé, vous pouvez utiliser *nsupdate* même quand vous n’utilisez pas Bind sur le serveur DNS.

Vous pouvez utiliser les raccordements fournis par le client DHCP pour inscrire le nom d’hôte sur le serveur DNS. Au cours du cycle DHCP, le client exécute les scripts dans */etc/dhcp/dhclient-exit-hooks.d/*. Vous pouvez l’utiliser pour inscrire la nouvelle adresse IP à l’aide de *nsupdate*. Par exemple :

    	#!/bin/sh
    	requireddomain=mydomain.local

    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi

		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

Vous pouvez également utiliser la commande *nsupdate* pour effectuer des mises à jour DNS dynamiques sécurisées. Par exemple, quand vous utilisez un serveur DNS Bind, une paire de clés publique/privée est [générée](http://linux.yyz.us/nsupdate/). Le serveur DNS est [configuré](http://linux.yyz.us/dns/ddns-server.html) avec la partie publique de la clé, ce qui lui permet de vérifier la signature de la demande. Vous devez utiliser l’option *-k* pour fournir la paire de clés à *nsupdate* afin de signer la demande de mise à jour DNS dynamique.

Quand vous utilisez un serveur DNS Windows, l’authentification Kerberos peut être utilisée avec le paramètre *-g* dans *nsupdate* (non disponible dans la version Windows de *nsupdate*). Pour ce faire, utilisez *kinit* pour charger les informations d’identification (par exemple, depuis un [fichier keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). *nsupdate -g* sélectionnera ensuite les informations d’identification à partir du cache.

Si nécessaire, vous pouvez ajouter un suffixe de recherche DNS à vos machines virtuelles. Le suffixe DNS est spécifié dans le fichier */etc/resolv.conf*. Comme la plupart des distributions Linux gèrent automatiquement le contenu de ce fichier, il ne peut généralement pas être modifié. Toutefois, vous pouvez remplacer le suffixe à l’aide de la commande *supersede* du client DHCP. Pour ce faire, dans */etc/dhcp/dhclient.conf*, ajoutez :

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0224_2016-->