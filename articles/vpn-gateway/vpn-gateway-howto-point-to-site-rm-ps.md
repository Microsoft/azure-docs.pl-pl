---
title: 'Nawiązywanie połączenia z siecią wirtualną z poziomu komputera — P2S VPN i natywnego uwierzytelniania certyfikatu platformy Azure: PowerShell'
description: Bezpieczne łączenie klientów systemu Windows i macOS z usługą Azure Virtual Network przy użyciu P2S i wystawionych certyfikatów urzędu certyfikacji. W tym artykule używany jest program PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660410"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurowanie połączenia sieci VPN typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure: PowerShell

W tym artykule opisano sposób bezpiecznego łączenia poszczególnych klientów z systemem Windows, Linux lub macOS do sieci wirtualnej platformy Azure. Połączenia sieci VPN typu punkt-lokacja są przydatne, gdy chcesz nawiązać połączenie z siecią wirtualną z lokalizacji zdalnej, na przykład podczas komunikacji z domu lub z konferencji. Możesz również użyć połączenia typu punkt-lokacja zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenia punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Łączenie się z komputera z diagramem połączeń sieci wirtualnej platformy Azure":::

Więcej informacji o sieci VPN typu punkt-lokacja znajduje się w temacie [Informacje o sieci VPN typu punkt-lokacja](point-to-site-about.md). Aby utworzyć tę konfigurację przy użyciu Azure Portal, zobacz [Konfigurowanie sieci VPN typu punkt-lokacja przy użyciu Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Wiele kroków opisanych w tym artykule może używać Azure Cloud Shell. Nie można jednak używać Cloud Shell do generowania certyfikatów. Ponadto w celu przekazania klucza publicznego certyfikatu głównego należy użyć Azure PowerShell lokalnego lub Azure Portal.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Zaloguj się

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. Deklarowanie zmiennych

W tym artykule są używane zmienne, dzięki czemu można łatwo zmienić wartości mające zastosowanie do własnego środowiska bez konieczności zmiany przykładów. Zadeklaruj zmienne, których chcesz użyć. Możesz użyć poniższego przykładu, podstawiając własne wartości w razie potrzeby. Jeśli zamkniesz sesję programu PowerShell/Cloud Shell w dowolnym momencie podczas wykonywania, po prostu skopiuj i wklej ponownie wartości, aby ponownie zadeklarować zmienne.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. Konfigurowanie sieci wirtualnej

1. Utwórz grupę zasobów.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Utwórz konfiguracje podsieci dla sieci wirtualnej, nadając im nazwę *frontony* i *GatewaySubnet*. Prefiksy te muszą być częścią zadeklarowanej przestrzeni adresowej sieci wirtualnej.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Utwórz sieć wirtualną.

   W tym przykładzie parametr serwera -DnsServer jest opcjonalny. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie z Twojej sieci wirtualnej. W tym przykładzie użyto prywatnego adresu IP, ale prawdopodobnie nie jest to adres IP Twojego serwera DNS. Pamiętaj, aby użyć własnych wartości. Wartość, którą określasz, jest używana przez zasoby wdrażane w sieci wirtualnej, nie przez połączenie typu punkt-lokacja czy klienta sieci VPN.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Określ zmienne dla utworzonej sieci wirtualnej.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

   Zażądaj dynamicznie przydzielanego publicznego adresu IP.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. Tworzenie bramy sieci VPN

W tym kroku należy skonfigurować i utworzyć bramę sieci wirtualnej dla sieci wirtualnej.

* Zmienna - GatewayType musi przyjąć wartość **Vpn**, a zmienna - VpnType musi przyjąć wartość **RouteBased**.
* Parametr -VpnClientProtocol służy do określania typów tuneli, które mają zostać włączone. Dostępne są opcje tunelu **OpenVPN, SSTP** i **IKEv2**. Możesz włączyć jedną z nich lub dowolną obsługiwaną kombinację. Jeśli chcesz włączyć wiele typów, określ nazwy oddzielone przecinkami. Nie można jednocześnie włączyć OpenVPN i SSTP. Klient strongSwan w systemach Android i Linux oraz natywny klient sieci VPN IKEv2 w systemach iOS i OSX będą używać do łączenia się tylko tuneli IKEv2. Klienci w systemie Windows będą najpierw próbowali użyć protokołu IKEv2, a jeśli połączenie nie zostanie nawiązane, użyją protokołu SSTP. Klienta OpenVPN można użyć do nawiązania połączenia z typem tunelu OpenVPN.
* Jednostka SKU bramy sieci wirtualnej "podstawowa" nie obsługuje uwierzytelniania IKEv2, OpenVPN ani usługi RADIUS. Jeśli planujesz, aby klienci z systemem Mac mogli łączyć się z siecią wirtualną, nie używaj podstawowej jednostki SKU.
* Tworzenie bramy sieci VPN może zająć do 45 minut, zależnie od wybranej [jednostki sku bramy](vpn-gateway-about-vpn-gateway-settings.md). W tym przykładzie użyto protokołu IKEv2.

1. Skonfiguruj i utwórz bramę sieci wirtualnej dla sieci wirtualnej. Utworzenie bramy trwa około 45 minut.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Po utworzeniu bramy możesz ją wyświetlić, korzystając z poniższego przykładu. W przypadku zamknięcia programu PowerShell lub przekroczenia limitu czasu podczas tworzenia bramy możesz ponownie [zadeklarować zmienne](#declare) .

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. Dodawanie puli adresów klienta sieci VPN

Po zakończeniu tworzenia bramy VPN Gateway możesz dodać pulę adresów klienta sieci VPN. Pula adresów klienta sieci VPN to zakres, z którego klienci sieci VPN otrzymują adres IP podczas nawiązywania połączenia. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć.

W tym przykładzie Pula adresów klienta sieci VPN jest zadeklarowana jako [zmienna](#declare) we wcześniejszym kroku.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. generowanie certyfikatów

>[!IMPORTANT]
> Nie można generować certyfikatów przy użyciu Azure Cloud Shell. Należy użyć jednej z metod opisanych w tej sekcji. Jeśli chcesz użyć programu PowerShell, musisz zainstalować go lokalnie.
>

Certyfikaty są używane przez platformę Azure do uwierzytelniania klientów sieci VPN dla połączeń VPN typu punkt-lokacja. Informacje o kluczu publicznym certyfikatu głównego należy przekazać na platformę Azure. Klucz publiczny jest wtedy uważany za „zaufany”. Certyfikaty klienta muszą być generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim w magazynie certyfikatów Certificates-Current User/Personal. Certyfikat jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną. 

Jeśli używasz certyfikatów z podpisem własnym, należy je utworzyć przy użyciu określonych parametrów. Certyfikat z podpisem własnym można utworzyć przy użyciu instrukcji dotyczących [środowiska PowerShell i systemu Windows 10](vpn-gateway-certificates-point-to-site.md) lub, jeśli nie masz systemu Windows 10, możesz użyć polecenia [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Istotne jest, aby podczas generowania certyfikatów głównych z podpisem własnym i certyfikatów klientów wykonać kroki opisane w instrukcjach. W przeciwnym razie wygenerowane przez Ciebie certyfikaty nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd połączenia.

### <a name="root-certificate"></a><a name="cer"></a>Certyfikat główny

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Po utworzeniu certyfikatu głównego [wyeksportuj](vpn-gateway-certificates-point-to-site.md#cer) dane certyfikatu publicznego (nie klucz prywatny) jako plik. cer szyfrowany X. 509.

### <a name="client-certificate"></a><a name="generate"></a>Certyfikat klienta

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Po utworzeniu certyfikatu klienta [wyeksportuj](vpn-gateway-certificates-point-to-site.md#clientexport) go. Certyfikat klienta będzie dystrybuowany do komputerów klienckich, które będą się łączyć.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Przekaż informacje o kluczu publicznym certyfikatu głównego

Upewnij się, że zakończono tworzenie bramy VPN Gateway. Po ukończeniu możesz przekazać plik cer (który zawiera informacje o kluczu publicznym) dla zaufanego certyfikatu głównego do platformy Azure. Po przekazaniu pliku cer platforma Azure może używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego. Jeśli okaże się to konieczne, dodatkowe pliki zaufanego certyfikatu głównego możesz przekazać później — maksymalnie może ich być 20.

>[!NOTE]
> Nie można przekazać pliku CER przy użyciu Azure Cloud Shell. Możesz użyć programu PowerShell lokalnie na komputerze lub można użyć [Azure Portal kroków](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Zadeklaruj zmienną dla nazwy certyfikatu, zastępując wartość swoją własną.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Zastąp ścieżkę, podając własną, a następnie uruchom polecenia cmdlet.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Przekaż informacje o kluczu publicznym do platformy Azure. Po przekazaniu informacji o certyfikacie platforma Azure traktuje ją jako zaufany certyfikat główny. Podczas przekazywania upewnij się, że używasz programu PowerShell lokalnie na komputerze, lub zamiast tego możesz użyć [Azure Portal kroków](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Nie można przekazać za pomocą Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. Instalowanie wyeksportowanego certyfikatu klienta

Poniższe kroki ułatwiają zainstalowanie programu na kliencie systemu Windows. Aby uzyskać dodatkowych klientów i uzyskać więcej informacji, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Upewnij się, że certyfikat klienta został wyeksportowany jako plik pfx wraz z całym łańcuchem certyfikatów (jest to ustawienie domyślne). W przeciwnym razie informacje o certyfikacie głównym nie będą dostępne na komputerze klienckim i klient nie będzie mógł się poprawnie uwierzytelnić.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. Konfigurowanie klienta sieci VPN

W tej sekcji skonfigurujesz klienta natywnego na potrzeby łączenia się z bramą sieci wirtualnej. Na przykład po przejściu do ustawień sieci VPN na komputerze z systemem Windows można dodać połączenia sieci VPN. Połączenie typu punkt-lokacja wymaga określonych ustawień konfiguracji. Te kroki ułatwiają utworzenie pakietu z określonymi ustawieniami, których natywny klient sieci VPN musi mieć możliwość łączenia się z siecią wirtualną za pośrednictwem połączenia typu punkt-lokacja.

Aby wygenerować i zainstalować pakiet konfiguracji klienta, można użyć następujących szybkich przykładów. Aby uzyskać więcej informacji na temat zawartości pakietu i dodatkowych instrukcji dotyczących generowania i instalowania plików konfiguracji klienta sieci VPN, zobacz [Tworzenie i Instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md).

Jeśli musisz ponownie zadeklarować zmienne, możesz je znaleźć [tutaj](#declare).

### <a name="to-generate-configuration-files"></a>Aby wygenerować pliki konfiguracji

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Aby zainstalować pakiet konfiguracji klienta

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Łączenie z platformą Azure

### <a name="windows-vpn-client"></a>Klient sieci VPN systemu Windows

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Klient sieci VPN dla komputerów Mac

W oknie dialogowym Sieć znajdź profil klienta, którego chcesz użyć, a następnie kliknij polecenie **Połącz**.
Aby uzyskać szczegółowe instrukcje, sprawdź [Install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Jeśli występują problemy z połączeniem, sprawdź, czy Brama sieci wirtualnej nie używa podstawowej jednostki SKU. Podstawowa jednostka SKU nie jest obsługiwana w przypadku klientów na komputery Mac.

  ![Połączenie z komputerem Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Aby zweryfikować połączenie

Te instrukcje dotyczą klientów w systemie Windows.

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę, że otrzymany adres IP jest jednym z adresów w puli adresów klienta sieci VPN typu punkt-lokacja określonego w konfiguracji. Wyniki są podobne, jak w następującym przykładzie:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

Te instrukcje dotyczą klientów w systemie Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Sprawdź, czy pakiet konfiguracji klienta sieci VPN został wygenerowany po określeniu adresów IP serwera DNS dla sieci wirtualnej. Jeśli adresy IP serwera DNS zostały zaktualizowane, wygeneruj i zainstaluj nowy pakiet konfiguracji klienta sieci VPN.

* Użyj narzędzia „ipconfig”, aby sprawdzić adres IPv4 przypisany do karty Ethernet na komputerze, z którego jest nawiązywane połączenie. Jeśli adres IP znajduje się w zakresie adresów sieci wirtualnej, z którą jest nawiązywane połączenie, lub w zakresie adresów puli VPNClientAddressPool, jest to określane jako nakładająca się przestrzeń adresowa. Kiedy przestrzeń adresowa nakłada się w ten sposób, ruch sieciowy nie dociera do platformy Azure, tylko pozostaje w sieci lokalnej.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Dodawanie lub usuwanie certyfikatu głównego

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure (przekazany na tę platformę). Te kroki wymagają Azure PowerShell poleceń cmdlet zainstalowanych lokalnie na komputerze (nie Azure Cloud Shell). Możesz również użyć Azure Portal, aby dodać certyfikaty główne.

**Aby dodać:**

Na platformie Azure można dodać maksymalnie 20 plików cer certyfikatu głównego. Poniższe kroki ułatwiają dodanie certyfikatu głównego. 

1. Przygotuj plik cer do przekazania:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Przekaż plik. Jednocześnie możesz przekazać tylko jeden plik.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Aby sprawdzić, czy plik certyfikatu został przekazany:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Aby usunąć:**

1. Zadeklaruj zmienne. Zmodyfikuj zmienne w przykładzie w celu dopasowania do certyfikatu, który chcesz usunąć.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Usuń certyfikat.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Użyj następującego przykładu, aby zweryfikować, czy certyfikat został pomyślnie usunięty.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Aby odwołać lub przywrócić certyfikat klienta

Certyfikaty klienta można odwołać. Lista odwołania certyfikatów umożliwia selektywne odmowa połączenia punkt-lokacja na podstawie poszczególnych certyfikatów klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

**Aby odwołać:**

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [Jak pobrać odcisk palca certyfikatu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).

1. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów. Ten ciąg jest deklarowany jako zmienna w następnym kroku.

1. Zadeklaruj zmienne. Pamiętaj, aby zadeklarować odcisk palca pobrany w poprzednim kroku.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Dodaj odcisk palca do listy odwołanych certyfikatów. Po dodaniu odcisku palca zostanie wyświetlony komunikat „Powodzenie”.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Sprawdź, czy odcisk palca został dodany do listy odwołania certyfikatów.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Po dodaniu odcisku palca nie będzie można używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

**Aby przywrócić:**

Certyfikat klienta można przywrócić przez usunięcie odcisku palca z listy odwołanych certyfikatów klienta.

1. Zadeklaruj zmienne. Pamiętaj, aby zadeklarować poprawny odcisk palca dla certyfikatu, który chcesz przywrócić.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Usuń odcisk palca certyfikatu z listy odwołania certyfikatów.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Sprawdź, czy odcisk palca został usunięty z listy odwołania.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń punkt-lokacja

Aby uzyskać dodatkowe informacje punkt-lokacja, zobacz [często zadawane pytania dotyczące VPN Gateway punkt-lokacja](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](../index.yml) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting: Azure point-to-site connections problems (Rozwiązywanie problemów: problemy z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).