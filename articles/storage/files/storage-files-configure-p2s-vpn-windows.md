---
title: Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6253deb53229172cd499a6aa14b8d8f19bc07b63
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629261"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files
Za pomocą połączenia sieci VPN typu punkt-lokacja (P2S) można instalować udziały plików platformy Azure za pośrednictwem protokołu SMB spoza platformy Azure bez konieczności otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja to połączenie sieci VPN między platformą Azure i pojedynczym klientem. Aby można było użyć połączenia sieci VPN P2S z Azure Files, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Jeśli masz wielu klientów, którzy muszą nawiązać połączenie z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN typu lokacja-lokacja (S2S) zamiast połączenia punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, zobacz [Konfigurowanie sieci VPN typu lokacja-lokacja do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md).

Zdecydowanie zalecamy zapoznanie się z [zagadnieniami dotyczącymi sieci w celu uzyskania bezpośredniego dostępu do udziału plików platformy Azure](storage-files-networking-overview.md) przed kontynuowaniem tego artykułu, aby zapoznać się z pełnym omówieniem opcji sieciowych dostępnych dla Azure Files.

W tym artykule szczegółowo opisano procedurę konfigurowania sieci VPN typu punkt-lokacja w systemie Windows (klienta systemu Windows i systemu Windows Server) w celu zainstalowania udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz kierować ruchem Azure File Sync przez sieć VPN, zobacz [konfigurowanie Azure File Sync serwera proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja modułu Azure PowerShell. Aby uzyskać więcej informacji na temat sposobu instalowania Azure PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps) i wybieranie systemu operacyjnego. Jeśli wolisz używać interfejsu wiersza polecenia platformy Azure w systemie Windows, możesz zapoznać się z poniższymi instrukcjami dla Azure PowerShell.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzanymi, które reprezentują udostępnioną pulę magazynów, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików platformy Azure i kont magazynu można znaleźć w temacie [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który ma zostać zainstalowany lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [konfigurowanie Azure Files punktów końcowych sieci](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure z lokalnego programu za pośrednictwem sieci VPN typu punkt-lokacja, należy utworzyć sieć wirtualną lub sieci wirtualnej. Połączenie sieci VPN P2S, które zostanie utworzone automatycznie, to most między lokalną maszyną z systemem Windows i tą siecią wirtualną platformy Azure.

Następujący program PowerShell utworzy sieć wirtualną platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana w celu uzyskania dostępu do lokalnego konta magazynu bez tworzenia routingu niestandardowego dla publicznego adresu IP konta magazynu, które może ulec zmianie, oraz jednego dla bramy sieci wirtualnej, która udostępnia usługę sieci VPN. 

Pamiętaj, aby zamienić `<region>` , `<resource-group>` i `<desired-vnet-name>` z odpowiednimi wartościami dla danego środowiska.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Utwórz certyfikat główny na potrzeby uwierzytelniania sieci VPN
Aby można było uwierzytelniać połączenia sieci VPN z lokalnych maszyn z systemem Windows w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie udostępniony bramy maszyny wirtualnej, i certyfikat klienta, który zostanie podpisany przy użyciu certyfikatu głównego. Poniższy program PowerShell tworzy certyfikat główny. certyfikat klienta zostanie utworzony po utworzeniu bramy sieci wirtualnej platformy Azure z informacjami z bramy. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Wdróż bramę sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z którą będą się łączyć lokalne maszyny z systemem Windows. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publiczny adres IP, który będzie identyfikować bramę klientom w dowolnym miejscu na świecie i certyfikat główny utworzony wcześniej, który będzie używany do uwierzytelniania klientów.

Pamiętaj, aby zamienić na `<desired-vpn-name-here>` nazwę, którą chcesz dla tych zasobów.

> [!Note]  
> Wdrożenie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Podczas wdrażania tego zasobu ten skrypt programu PowerShell zostanie zablokowany, aby wdrożenie zostało ukończone. Jest to oczekiwane zachowanie.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Utwórz certyfikat klienta
Certyfikat klienta jest tworzony przy użyciu identyfikatora URI bramy sieci wirtualnej. Ten certyfikat jest podpisany przy użyciu certyfikatu głównego utworzonego wcześniej.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracyjnymi wymaganymi do zainicjowania połączenia sieci VPN na lokalnym komputerze z systemem Windows. Skonfigurujemy połączenie sieci VPN przy użyciu funkcji [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) w systemie Windows 10/Windows Server 2016 +. Ten pakiet zawiera również pakiety wykonywalne, które spowodują skonfigurowanie starszego klienta sieci VPN systemu Windows w razie potrzeby. W tym przewodniku używa się zawsze w sieci VPN, a nie starszej wersji klienta sieci VPN systemu Windows, ponieważ klient sieci VPN zawsze włączony umożliwia użytkownikom końcowym łączenie się z siecią VPN platformy Azure lub rozłączanie się z nią bez posiadania uprawnień administratora. 

Poniższy skrypt zainstaluje certyfikat klienta wymagany do uwierzytelnienia w bramie sieci wirtualnej, pobierze i zainstaluje pakiet sieci VPN. Pamiętaj, aby zamienić `<computer1>` i na `<computer2>` żądane komputery. Ten skrypt można uruchomić na tyle maszyn, ile potrzebujesz, dodając do tablicy więcej sesji programu PowerShell `$sessions` . Twoje konto użytkowania musi być kontem administratora na każdej z tych maszyn. Jeśli jedna z tych maszyn jest maszyną lokalną, z której korzystasz ze skryptu, należy uruchomić skrypt z poziomu sesji programu PowerShell z podwyższonym poziomem uprawnień. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Zainstaluj udział plików platformy Azure
Teraz po skonfigurowaniu sieci VPN typu punkt-lokacja można użyć jej do zainstalowania udziału plików platformy Azure na komputerach, które są skonfigurowane za pomocą programu PowerShell. Poniższy przykład zainstaluje udział, wyświetli katalog główny udziału, aby potwierdzić, że udział jest w rzeczywistości zainstalowany, i odinstaluje udział. Niestety, nie jest możliwe trwałe zainstalowanie udziału za pośrednictwem komunikacji zdalnej programu PowerShell. Aby trwale zainstalować, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Zobacz też
- [Zagadnienia dotyczące sieci dla bezpośredniego dostępu do udziału plików platformy Azure](storage-files-networking-overview.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z usługą Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md)