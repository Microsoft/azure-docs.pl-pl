---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3c10ca35ee2f085d4ce41e862a895ff17ff63a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84317455"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Ile punktów końcowych klienta sieci VPN może obejmować konfiguracja punkt-lokacja?

Zależy od jednostki SKU bramy. Aby uzyskać więcej informacji o liczbie obsługiwanych połączeń, zobacz [jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Których systemów operacyjnych klienta można używać z punktem do lokacji?

Obsługiwane są następujące systemy operacyjne klientów:

* Windows 7 (32-bitowy i 64-bitowy)
* Windows Server 2008 R2 (tylko 64-bitowy)
* Windows 8.1 (32-bitowy i 64-bitowy)
* Windows Server 2012 (tylko 64-bitowy)
* Windows Server 2012 R2 (tylko 64-bitowy)
* Windows Server 2016 (tylko 64-bitowy)
* Windows Server 2019 (tylko wersja 64-bitowa)
* Windows 10
* Mac OS X w wersji 10,11 lub nowszej
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Czy można pominąć serwery proxy i zapory, korzystając z funkcji punkt-lokacja?

Platforma Azure obsługuje trzy typy opcji sieci VPN typu punkt-lokacja:

* Protokół Secure Socket Tunneling Protocol (SSTP). SSTP to firmowe rozwiązanie oparte na protokole SSL firmy Microsoft, które może przeniknąć zapory od momentu, gdy większość zapór otworzy wychodzący port TCP, którego używa protokół SSL 443.

* OpenVPN. OpenVPN jest rozwiązaniem opartym na protokole SSL, które może przeniknąć zapory od czasu, gdy większość zapór otwiera wychodzący port TCP, którego używa protokół SSL 443.

* Sieć VPN z protokołem IKEv2. Sieci VPN IKEv2 to oparte na standardach rozwiązanie sieci VPN IPsec, które korzysta z wychodzących portów UDP 500 i 4500 oraz protokołu IP nr. 50. Zapory nie zawsze otwierają te porty, więc istnieje możliwość, że sieć VPN z protokołem IKEv2 nie będzie mogła nawiązywać połączeń przez serwery proxy i zapory.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Czy w przypadku ponownego uruchomienia komputera klienckiego skonfigurowanego pod kątem połączenia typu punkt-lokacja połączenie z siecią VPN zostanie nawiązane automatycznie?

Domyślnie komputer kliencki nie przywraca automatycznie połączenia z siecią VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Czy w przypadku połączeń punkt-lokacja jest obsługiwane automatyczne ponowne nawiązywanie połączenia i DDNS na klientach sieci VPN?

Automatyczne ponowne nawiązywanie połączenia i DDNS nie są obecnie obsługiwane w przypadku połączeń VPN typu punkt-lokacja.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Czy z konfiguracji typu lokacja-lokacja i punkt-lokacja można korzystać równolegle w ramach tej samej sieci wirtualnej?

Tak. W przypadku modelu wdrażania przy użyciu usługi Resource Manager dla bramy trzeba mieć sieć VPN opartą na trasach. W przypadku klasycznego modelu wdrażania należy użyć bramy dynamicznej. Nie obsługujemy połączeń typu punkt-lokacja dla bram VPN Gateway z routingiem statycznym lub bram VPN Gateway opartych na zasadach.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Czy można skonfigurować klienta punkt-lokacja do łączenia się z wieloma bramami sieci wirtualnych w tym samym czasie?

W zależności od używanego oprogramowania klienta sieci VPN może być możliwe nawiązanie połączenia z wieloma bramami Virtual Network, pod warunkiem, że sieci wirtualne, z którymi nawiązano połączenie, nie mają przestrzeni adresowej powodujących konflikt między nimi lub z siecią z klientem, z którego nawiązywane jest połączenie.  Klient sieci VPN platformy Azure obsługuje wiele połączeń sieci VPN, ale tylko jedno połączenie może być połączone w danym momencie.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Czy można skonfigurować klienta typu punkt-lokacja pod kątem jednoczesnego nawiązywania połączenia z wieloma sieciami wirtualnymi?

Tak, połączenia punkt-lokacja z bramą Virtual Network wdrożone w sieci wirtualnej, która jest połączona z innym sieci wirtualnychem, może mieć dostęp do innych komunikacji równorzędnych sieci wirtualnych.  Pod warunkiem, że Komunikacja równorzędna sieci wirtualnych korzysta z funkcji UseRemoteGateway/AllowGatewayTransit, klient punkt-lokacja będzie mógł nawiązać połączenie z tymi równorzędnymi sieci wirtualnych.  Aby uzyskać więcej informacji, zapoznaj się z [tym](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) artykułem.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakiej przepływności można oczekiwać w przypadku połączeń typu lokacja-lokacja lub punkt-lokacja?

Trudno jest utrzymać dokładną przepływność tuneli VPN. Protokoły IPsec i SSTP należą do niejawnie ciężkich protokołów sieci VPN. Przepływność ograniczają również opóźnienia i przepustowość między lokalizacjami lokalnymi i Internetem. W przypadku bramy VPN Gateway z samymi połączeniami typu punkt-lokacja IKEv2 przez sieć VPN całkowita przepływność, której można oczekiwać, zależy od jednostki SKU bramy. Aby uzyskać więcej informacji o przepływności, zobacz [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) (Jednostki SKU bramy).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Czy mogę używać dowolnego programowego klienta sieci VPN dla połączeń typu punkt-lokacja obsługujących protokoły SSTP i/lub IKEv2?

Nie. Możesz używać wyłącznie natywnego klienta sieci VPN w systemie Windows dla protokołu SSTP i natywnego klienta sieci VPN na komputerach Mac dla protokołu IKEv2. Można jednak użyć klienta OpenVPN na wszystkich platformach, aby nawiązać połączenie za pośrednictwem protokołu OpenVPN. Zapoznaj się z listą obsługiwanych systemów operacyjnych klienta.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Czy platforma Azure obsługuje sieć VPN z protokołem IKEv2 w systemie Windows?

Protokół IKEv2 jest obsługiwany w systemach Windows 10 i Server 2016. Jednak aby można było używać protokołu IKEv2, należy zainstalować aktualizacje i lokalnie ustawić wartość klucza rejestru. Wersje systemu operacyjnego starsze niż Windows 10 nie są obsługiwane i mogą korzystać tylko z **protokołu SSTP lub OpenVPN®**.

Aby przygotowywać system Windows 10 lub Server 2016 pod kątem protokołu IKEv2:

1. Zainstaluj aktualizację.

   | Wersja systemu operacyjnego | Date (Data) | Numer/link |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 w wersji 1607 | 17 stycznia 2018 r. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 w wersji 1703 | 17 stycznia 2018 r. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 w wersji 1709 | 22 marca, 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Ustaw wartość klucza rejestru. Utwórz lub ustaw klucz rejestru REG_DWORD „HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload”na wartość 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Co się stanie po jednoczesnym skonfigurowaniu protokołów SSTP i IKEv2 dla połączeń sieci VPN P2S?

Po jednoczesnym skonfigurowaniu protokołów SSTP i IKEv2 w środowisku mieszanym (składającym się z urządzeń z systemem Windows i komputerów Mac) klient sieci VPN z systemem Windows będzie zawsze próbował najpierw użyć tunelu IKEv2, a gdy to się nie uda, wróci do protokołu SSTP. Klienci z systemem MacOSX będą łączyć się tylko za pomocą protokołu IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Jakie platformy, oprócz systemów Windows i Mac, platforma Azure obsługuje dla sieci VPN P2S?

Dla sieci VPN P2S platforma Azure obsługuje systemy Windows, Mac i Linux.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Mam już wdrożoną usługę Azure VPN Gateway. Czy mogę w niej włączyć sieć VPN z protokołem RADIUS i/lub IKEv2?

Tak, te nowe funkcje możesz włączyć w już wdrożonych bramach, używając do tego programu PowerShell lub witryny Azure Portal. Warunkiem jest, aby używane przez Ciebie jednostki SKU bramy obsługiwały protokół RADIUS i/lub protokół IKEv2. Na przykład podstawowa jednostka SKU bramy sieci VPN nie obsługuje protokołu RADIUS ani protokołu IKEv2.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Jak mogę usunąć konfiguracji połączenia P2S?

Konfigurację P2S można usunąć za pomocą interfejsu wiersza polecenia platformy Azure i programu PowerShell przy użyciu następujących poleceń:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
