---
title: 'Azure VPN Gateway: łączenie bram z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach'
description: Skonfiguruj bramę sieci VPN opartą na trasach platformy Azure dla wielu urządzeń sieci VPN opartych na zasadach przy użyciu Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89418885"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Łączenie bram sieci VPN platformy Azure z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell

Ten artykuł ułatwia skonfigurowanie bramy sieci VPN opartej na trasach platformy Azure w celu połączenia z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach wykorzystującymi niestandardowe zasady protokołu IPsec/IKE dla połączeń sieci VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Informacje o bramach sieci VPN opartych na zasadach i trasach

Urządzenia sieci VPN *oparte na zasadach i trasie* różnią się w zależności od tego, jak selektory ruchu protokołu IPSec są ustawiane dla połączenia:

* **Oparte na zasadach** Urządzenia sieci VPN używają kombinacji prefiksów z obu sieci do definiowania sposobu szyfrowania i odszyfrowywania ruchu za pośrednictwem tuneli IPsec. Jest zazwyczaj oparty na urządzeniach zapory, które wykonują filtrowanie pakietów. Szyfrowanie i odszyfrowywanie tunelu IPsec jest dodawane do aparatu filtrowania i przetwarzania pakietów.
* **Oparta na trasach** Urządzenia sieci VPN używają selektorów ruchu dowolnego do dowolnego (symbol wieloznaczny) i umożliwiają kierowanie tabel routingu/przekazywania do różnych tuneli IPsec. Jest zazwyczaj oparty na platformach routera, w których każdy tunel IPsec jest modelowany jako interfejs sieciowy lub VTI (interfejs tunelu wirtualnego).

Na poniższych diagramach są wyróżniane dwa modele:

### <a name="policy-based-vpn-example"></a>Przykład sieci VPN opartej na zasadach
![oparte na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Przykład sieci VPN opartej na trasach
![oparta na trasach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Pomoc techniczna platformy Azure dla sieci VPN opartej na zasadach
Obecnie platforma Azure obsługuje oba tryby bram sieci VPN: bramy sieci VPN oparte na trasach i bramy sieci VPN oparte na zasadach. Są one oparte na różnych platformach wewnętrznych, co skutkuje różnymi specyfikacjami:

| Kategoria | PolicyBased VPN Gateway | RouteBased VPN Gateway | RouteBased VPN Gateway |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **Jednostka SKU bramy platformy Azure**    | Podstawowa                       | Podstawowa                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Wersja IKE**          | IKEv1                       | IKEv2                            | Protokoły IKEv1 i IKEv2                         |
| **Maksymalna liczba połączeń S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Za pomocą niestandardowych zasad protokołu IPsec/IKE można teraz skonfigurować bramy sieci VPN oparte na trasach na podstawie prefiksów z opcją "**PolicyBasedTrafficSelectors**", aby połączyć się z lokalnymi urządzeniami sieci VPN opartymi na zasadach. Ta funkcja umożliwia łączenie z sieci wirtualnej platformy Azure i bramy sieci VPN z wieloma lokalnymi urządzeniami sieci VPN/zapory opartymi na zasadach, usuwając limit pojedynczego połączenia z bieżących bram sieci VPN opartych na zasadach platformy Azure.

> [!IMPORTANT]
> 1. Aby włączyć tę łączność, lokalne urządzenia sieci VPN oparte na zasadach muszą obsługiwać protokół **IKEv2** do łączenia się z bramami sieci VPN opartymi na trasach platformy Azure. Sprawdź specyfikacje urządzeń sieci VPN.
> 2. Sieci lokalne łączące się za pośrednictwem urządzeń sieci VPN opartych na zasadach z tym mechanizmem mogą łączyć się tylko z siecią wirtualną platformy Azure. **nie mogą oni przetransferować do innych sieci lokalnych lub sieci wirtualnych za pośrednictwem tej samej bramy sieci VPN platformy Azure**.
> 3. Opcja konfiguracji jest częścią niestandardowych zasad połączenia protokołu IPsec/IKE. W przypadku włączenia opcji selektora ruchu opartego na zasadach należy określić kompletną zasadę (algorytmy szyfrowania IPsec/IKE i integralności, mocne klucze i okresy istnienia skojarzeń zabezpieczeń).

Na poniższym diagramie przedstawiono, dlaczego Routing tranzytowy za pośrednictwem usługi Azure VPN Gateway nie działa z opcją opartą na zasadach:

![tranzyt oparty na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak pokazano na diagramie, Brama sieci VPN platformy Azure ma selektory ruchu z sieci wirtualnej do każdego z prefiksów sieci lokalnych, ale nie prefiksów między połączeniami. Na przykład lokacja lokalna 2, lokacja 3 i witryna 4 mogą komunikować się odpowiednio VNet1, ale nie może łączyć się za pośrednictwem bramy sieci VPN platformy Azure ze sobą. Na diagramie przedstawiono selektory ruchu między połączeniami, które nie są dostępne w bramie sieci VPN platformy Azure w ramach tej konfiguracji.

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Instrukcje zawarte w tym artykule są zgodne z tym samym przykładem, zgodnie z opisem w artykule [Konfigurowanie zasad protokołu IPSec/IKE dla połączeń S2S lub Sieć wirtualna-sieć](vpn-gateway-ipsecikepolicy-rm-powershell.md) wirtualna w celu nawiązania połączenia sieci VPN S2S. Jest to pokazane na poniższym diagramie:

![S2S — zasady](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Przepływ pracy umożliwiający włączenie tej łączności:
1. Utwórz sieć wirtualną, bramę sieci VPN i bramę sieci lokalnej dla połączenia między lokalizacjami.
2. Utwórz zasady protokołu IPsec/IKE.
3. Zastosuj zasady podczas tworzenia połączenia S2S lub VNet-to-VNet i **włączania selektorów ruchu opartych na zasadach** w połączeniu.
4. Jeśli połączenie zostało już utworzone, możesz zastosować lub zaktualizować zasady do istniejącego połączenia.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Włączanie selektorów ruchu opartych na zasadach

W tej sekcji pokazano, jak włączyć selektory ruchu opartego na zasadach w ramach połączenia. Upewnij się, że wykonano [część 3 artykułu Konfigurowanie zasad IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). W procedurach przedstawionych w tym artykule są używane te same parametry.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Nawiązywanie połączenia z subskrypcją i deklarowanie zmiennych

1. Jeśli używasz programu PowerShell lokalnie na komputerze, zaloguj się przy użyciu polecenia cmdlet *Connect-AzAccount* . Zamiast tego należy użyć Azure Cloud Shell w przeglądarce.

2. Zadeklaruj swoje zmienne. W tym ćwiczeniu używane są następujące zmienne:

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

1. Utwórz grupę zasobów.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Użyj poniższego przykładu, aby utworzyć sieć wirtualną sieci testvnet1 z trzema podsieciami i bramą sieci VPN. Jeśli chcesz zastąpić wartości, pamiętaj, że zawsze należy nazwać podsieć bramy, szczególnie "GatewaySubnet". W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2. Tworzenie połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

1. Utwórz zasady protokołu IPsec/IKE.

   > [!IMPORTANT]
   > Aby włączyć opcję "UsePolicyBasedTrafficSelectors" dla połączenia, należy utworzyć zasady protokołu IPsec/IKE.

   Poniższy przykład tworzy zasady protokołu IPsec/IKE z tymi algorytmami i parametrami:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, okres istnienia skojarzenia zabezpieczeń 14400 s & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Utwórz połączenie S2S VPN za pomocą selektorów ruchu opartego na zasadach oraz zasad protokołu IPsec/IKE i Zastosuj zasady protokołu IPsec/IKE utworzone w poprzednim kroku. Należy pamiętać o dodatkowym parametrze "-UsePolicyBasedTrafficSelectors $True", który umożliwia wybór ruchu oparty na zasadach w połączeniu.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Po wykonaniu tych kroków połączenie sieci VPN S2S będzie używać zdefiniowanych zasad protokołu IPsec/IKE i włącza selektory ruchu opartego na zasadach w połączeniu. Możesz powtórzyć te same czynności, aby dodać więcej połączeń do dodatkowych lokalnych urządzeń sieci VPN opartych na zasadach z tej samej bramy sieci VPN platformy Azure.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Aby zaktualizować selektory ruchu opartego na zasadach
W tej sekcji pokazano, jak zaktualizować opcję selektorów ruchu opartych na zasadach dla istniejącego połączenia sieci VPN S2S.

1. Pobierz zasób połączenia.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Wyświetl opcję selektorów ruchu opartych na zasadach.
W poniższym wierszu przedstawiono, czy dla połączenia są używane selektory ruchu opartego na zasadach:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Jeśli wiersz zwraca wartość "**true**", to w ramach połączenia są konfigurowane selektory ruchu opartego na zasadach. w przeciwnym razie zwraca wartość "**false**".
1. Po uzyskaniu zasobu połączenia można włączyć lub wyłączyć selektory ruchu opartego na zasadach w ramach połączenia.

   - Aby włączyć

      Poniższy przykład włącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady protokołu IPsec/IKE bez zmian:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Aby wyłączyć

      Poniższy przykład wyłącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady protokołu IPsec/IKE bez zmian:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).

Zapoznaj się również z tematem [Konfigurowanie zasad protokołu IPSec/IKE dla połączeń sieci VPN S2S lub połączenia Sieć wirtualna-sieć wirtualna,](vpn-gateway-ipsecikepolicy-rm-powershell.md) Aby uzyskać szczegółowe informacje na temat niestandardowych zasad protokołu IPSec/IKE.
