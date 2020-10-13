---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 594ad352d5fd8431ffaf6d681c891c967cf9d32a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606893"
---
Aby można było pomyślnie ustanowić tunel urządzenia, muszą zostać spełnione następujące wymagania:

* Urządzenie musi być komputerem przyłączonym do domeny z systemem Windows 10 Enterprise lub Education w wersji 1809 lub nowszej.
* Tunel można skonfigurować tylko dla wbudowanego rozwiązania sieci VPN systemu Windows i jest ustanawiany przy użyciu protokołu IKEv2 z uwierzytelnianiem certyfikatu komputera.
* Na urządzenie można skonfigurować tylko jeden tunel urządzenia.

1. Zainstaluj certyfikaty klienta na kliencie systemu Windows 10 przy użyciu artykułu [Klient sieci VPN typu punkt-lokacja](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) . Certyfikat musi znajdować się w magazynie komputera lokalnego.
1. Utwórz profil sieci VPN i skonfiguruj tunel urządzenia w kontekście lokalnego konta SYSTEMowego, korzystając z [tych instrukcji](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Przykład konfiguracji dla tunelu urządzenia

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowaniu certyfikatu klienta w magazynie komputera lokalnego na kliencie systemu Windows 10 Użyj następujących przykładów, aby skonfigurować tunel urządzenia klienckiego:

1. Skopiuj poniższy tekst i Zapisz go jako ***devicecert.ps1***.

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
1. Skopiuj poniższy tekst i Zapisz go jako ***VPNProfile.xml*** w tym samym folderze co **devicecert.ps1**. Edytuj następujący tekst, aby dopasować go do środowiska.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
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
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Pobierz **PsExec** z programu [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) i Wyodrębnij pliki do **C:\PSTools**.
1. W wierszu polecenia administratora uruchom program PowerShell, uruchamiając polecenie:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Zrzut ekranu przedstawia okno wiersza polecenia z poleceniem umożliwiającym uruchomienie 64-bitowej wersji programu PowerShell.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. W programie PowerShell przejdź do folderu, w którym znajdują się **devicecert.ps1** i **VPNProfile.xml** , i uruchom następujące polecenie:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Zrzut ekranu przedstawia okno programu PowerShell z uruchomioną MachineCertTest za pomocą skryptu devicesert.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Uruchom **Rasphone**.

   ![Zrzut ekranu przedstawia okno dialogowe uruchamiania z wybranym programem Rasphone.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Wyszukaj wpis **MachineCertTest** , a następnie kliknij przycisk **Połącz**.

   ![Zrzut ekranu przedstawia okno dialogowe połączenia sieciowe z wybranym MachineCertTest i przycisk Połącz.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Jeśli połączenie zakończy się pomyślnie, należy ponownie uruchomić komputer. Tunel zostanie automatycznie nawiązane.
