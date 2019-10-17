---
title: Configurer un tunnel VPN Always On pour la passerelle VPN
description: Étapes de configuration d’un tunnel VPN utilisateur Always On pour la passerelle VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846373"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurer un tunnel utilisateur VPN Always On

L’une des nouvelles fonctionnalités du client de réseau privé virtuel (VPN) Windows 10 est la capacité de gérer une connexion VPN. Always On est une fonctionnalité de Windows 10 qui permet au profil VPN actif de se connecter automatiquement et rester connecté en fonction de déclencheurs, à savoir connexion de l’utilisateur, changement de l’état du réseau ou écran de l’appareil actif.

Les passerelles de réseau virtuel Azure peuvent être utilisées avec Windows 10 Always On pour établir des tunnels d’utilisateur persistants ainsi que des tunnels d’appareil vers Azure. Cet article vous aidera à configurer un tunnel utilisateur VPN Always On.

Les connexions VPN Always On incluent deux types de tunnels :

* Le **tunnel d’appareil** se connecte à des serveurs VPN spécifiés avant que les utilisateurs ne se connectent à l’appareil. Les scénarios de connectivité de préconnexion et la gestion des appareils utilisent un tunnel d’appareil.

* Le **tunnel d’utilisateur** se connecte uniquement après la connexion d’un utilisateur à l’appareil. Le tunnel d’utilisateur permet aux utilisateurs d’accéder aux ressources de l’organisation via les serveurs VPN.

Le tunnel d’appareil et le tunnel d’utilisateur fonctionnent tous deux de façon indépendante avec leurs profils VPN. Ils peuvent être connectés en même temps et utiliser différentes méthodes d’authentification et d’autres paramètres de configuration VPN selon les besoins.

## <a name="1-configure-the-gateway"></a>1. Configurer la passerelle

Configurez la passerelle VPN pour utiliser IKEv2 et l’authentification basée sur les certificats à l’aide de cet [article sur la connexion point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurer le tunnel d’utilisateur

1. Installez des certificats clients sur le client Windows 10 comme indiqué dans cet [article sur un client VPN point à site](point-to-site-how-to-vpn-client-install-azure-cert.md). Le certificat doit se trouver dans le magasin de l’utilisateur actuel
2. Configurez le client VPN Always On via PowerShell, SCCM ou Intune en suivant [ces instructions](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Exemple de configuration de tunnel utilisateur

Après avoir configuré la passerelle de réseau virtuel et installé le certificat client dans le magasin de l’ordinateur local sur le client Windows 10, utilisez les exemples suivants pour configurer un tunnel d’appareil client.

1. Copiez le texte suivant et enregistrez-le sous le nom ***usercert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Copiez le texte suivant et enregistrez-le sous le nom ***VPNProfile.xml*** dans le même dossier que **usercert.ps1**. Modifiez le texte suivant pour correspondre à votre environnement.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
    </Authentication>  
    <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
     <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
    <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
      </NativeProfile> 
      <!-- use host routes(/32) to prevent routing conflicts -->  
      <Route>  
    <Address>192.168.3.5</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
      <Route>  
    <Address>192.168.3.4</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Exécutez PowerShell en tant qu’administrateur.

1. Dans PowerShell, accédez au dossier où **usercert.ps1** et **VPNProfile.xml** se trouvent, et exécutez la commande suivante :

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Regardez sous Paramètres VPN.

1. Recherchez l’entrée **UserTest**, puis cliquez sur **Se connecter**.

1. Si la connexion réussit, vous avez correctement configuré un tunnel utilisateur Always On.

## <a name="cleanup"></a>Nettoyage

Pour supprimer le profil, exécutez la commande suivante :

1. Arrêtez la connexion et désactivez la case à cocher « Se connecter automatiquement ».

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Nettoyage](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes, consultez [Problèmes de connexion point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
