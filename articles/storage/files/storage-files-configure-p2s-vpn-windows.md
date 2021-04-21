---
title: Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785275"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files
Możesz użyć połączenia sieci VPN typu punkt-lokacja (P2S), aby zainstalować udziały plików platformy Azure za pośrednictwem SMB spoza platformy Azure bez otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja to połączenie sieci VPN między platformą Azure i indywidualnym klientem. Aby można było używać połączenia sieci VPN P2S z Azure Files sieci VPN P2S, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Jeśli masz wielu klientów, którzy muszą łączyć się z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN typu lokacja-lokacja (S2S) zamiast połączenia punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, [zobacz Konfigurowanie sieci VPN typu lokacja-lokacja](storage-files-configure-s2s-vpn.md)do użycia z Azure Files .

Zdecydowanie zalecamy przeczytanie artykułu [Networking considerations for](storage-files-networking-overview.md) direct Azure file share access (Zagadnienia dotyczące sieci związane z bezpośrednim dostępem do udziału plików platformy Azure) przed kontynuowaniem pracy z tym artykułem, aby zapoznać się z pełną omówieniem opcji sieci dostępnych dla Azure Files.

Artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania sieci VPN typu punkt-lokacja w systemie Windows (klient systemu Windows i system Windows Server) w celu instalacji udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz przekierowyć ruch sieciowy Azure File Sync sieci VPN, zobacz konfigurowanie ustawień serwera [proxy Azure File Sync](../file-sync/file-sync-firewall-and-proxy.md)zapory.

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja modułu Azure PowerShell. Aby uzyskać więcej informacji na temat sposobu instalowania Azure PowerShell, zobacz Instalowanie modułu [Azure PowerShell i](/powershell/azure/install-az-ps) wybieranie systemu operacyjnego. Jeśli wolisz używać interfejsu wiersza polecenia platformy Azure w systemie Windows, możesz jednak skorzystać z poniższych instrukcji dotyczących Azure PowerShell.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Aby dowiedzieć się więcej na temat wdrażania udziałów plików i kont magazynu platformy Azure, [zobacz Tworzenie udziału plików platformy Azure.](storage-how-to-create-file-share.md)

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [Konfigurowanie Azure Files końcowych sieci](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure ze środowiska lokalnego za pośrednictwem sieci VPN typu punkt-lokacja, należy utworzyć sieć wirtualną lub sieć wirtualną. Połączenie sieci VPN typu P2S, które zostanie automatycznie tworzone, jest mostem między lokalną maszyną z systemem Windows i tą siecią wirtualną platformy Azure.

Następujący program PowerShell utworzy sieć wirtualną platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana do uzyskania dostępu do lokalnego konta magazynu bez tworzenia niestandardowego routingu dla publicznego adresu IP konta magazynu, który może ulec zmianie, oraz jedną dla bramy sieci wirtualnej, która udostępnia usługę sieci VPN. 

Pamiętaj, aby `<region>` zastąpić wartości , i odpowiednimi `<resource-group>` `<desired-vnet-name>` wartościami dla środowiska.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Tworzenie certyfikatu głównego do uwierzytelniania sieci VPN
Aby połączenia sieci VPN z lokalnych maszyn z systemem Windows zostały uwierzytelnione w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie dostarczony do bramy maszyny wirtualnej, oraz certyfikat klienta, który zostanie podpisany przy użyciu certyfikatu głównego. Następujący program PowerShell tworzy certyfikat główny; Certyfikat klienta zostanie utworzony po utworzeniu bramy sieci wirtualnej platformy Azure z informacjami z bramy. 

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

## <a name="deploy-virtual-network-gateway"></a>Wdrażanie bramy sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z która będą łączyć się lokalne maszyny z systemem Windows. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publicznego adresu IP, który będzie identyfikować bramę klientów niezależnie od tego, gdzie znajdują się na świecie, oraz utworzonego wcześniej certyfikatu głównego, który będzie używany do uwierzytelniania klientów.

Pamiętaj, `<desired-vpn-name-here>` aby zastąpić nazwą, która ma być nazwą tych zasobów.

> [!Note]  
> Wdrażanie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Podczas wdrażania tego zasobu ten skrypt programu PowerShell zablokuje możliwość ukończenia wdrożenia. Jest to oczekiwane zachowanie.

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

## <a name="create-client-certificate"></a>Tworzenie certyfikatu klienta
Certyfikat klienta jest tworzony przy użyciu URI bramy sieci wirtualnej. Ten certyfikat jest podpisany przy użyciu utworzonego wcześniej certyfikatu głównego.

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

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta sieci VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracji wymaganymi do zainicjowania połączenia sieci VPN na lokalnej maszynie z systemem Windows. Połączenie sieci VPN zostanie skonfigurowane przy użyciu [funkcji zawsze wł. sieci VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) w systemie Windows 10/Windows Server 2016+. Ten pakiet zawiera również pakiety wykonywalne, które w razie potrzeby skonfigurują starszego klienta sieci VPN systemu Windows. W tym przewodniku jest używana zawsze wł. sieć VPN, a nie starszy klient sieci VPN systemu Windows, ponieważ klient zawsze wł. sieci VPN umożliwia użytkownikom końcowym łączenie się z siecią VPN platformy Azure lub rozłączanie się z tą siecią bez uprawnień administratora do ich komputera. 

Poniższy skrypt zainstaluje certyfikat klienta wymagany do uwierzytelniania względem bramy sieci wirtualnej, pobierze i zainstaluje pakiet sieci VPN. Pamiętaj, aby zastąpić `<computer1>` `<computer2>` i żądanymi komputerami. Ten skrypt można uruchomić na większej liczby maszyn, jak chcesz, dodając kolejne sesje programu PowerShell do `$sessions` tablicy. Twoje konto użytkowania musi być administratorem na każdej z tych maszyn. Jeśli jedną z tych maszyn jest maszyna lokalna, z poziomu których jest uruchamiany skrypt, należy uruchomić skrypt w sesji programu PowerShell z podwyższonym poziomem uprawnień. 

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
Teraz, po skonfigurowaniu sieci VPN typu punkt-lokacja, możesz użyć jej do instalacji udziału plików platformy Azure na komputerach konfigurnych za pośrednictwem programu PowerShell. W poniższym przykładzie zostanie zainstalowany udział, zostanie wyświetlona lista katalogu głównego udziału, aby potwierdzić, że udział jest rzeczywiście zainstalowany, i odinstalowanie udziału. Niestety nie można zainstalować udziału w sposób trwały za pośrednictwem komunikacji zdalnej programu PowerShell. Aby zainstalować trwałe, zobacz Use an Azure file share with Windows (Używanie [udziału plików platformy Azure z systemem Windows).](storage-how-to-use-files-windows.md) 

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
- [Zagadnienia dotyczące sieci związane z bezpośrednim dostępem do udziału plików platformy Azure](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files](storage-files-configure-s2s-vpn.md)