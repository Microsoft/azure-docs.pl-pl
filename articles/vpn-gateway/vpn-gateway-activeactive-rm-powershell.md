---
title: Konfigurowanie aktywnych i aktywnych połączeń S2S platformy Azure VPN Gateway
description: W tym artykule opisano konfigurowanie połączeń aktywnych i aktywnych za pomocą bram sieci VPN platformy Azure przy użyciu Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89440783"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurowanie aktywnych i aktywnych połączeń sieci VPN S2S z bramami sieci VPN platformy Azure

W tym artykule przedstawiono kroki umożliwiające utworzenie aktywnych — aktywnych połączeń między różnymi lokalizacjami i między sieciami wirtualnymi przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell. W Azure Portal można również skonfigurować bramę Active-Active.

## <a name="about-highly-available-cross-premises-connections"></a>Informacje o połączeniach między lokalizacjami o wysokiej dostępności
Aby zapewnić wysoką dostępność połączeń między różnymi lokalizacjami i między sieciami wirtualnymi, należy wdrożyć wiele bram sieci VPN i ustanowić wiele połączeń równoległych między firmami i platformą Azure. Aby zapoznać się z omówieniem opcji łączności i topologii, zobacz [wiele lokalizacji i łączność między sieciami wirtualnymi o wysokiej](vpn-gateway-highlyavailable.md) dostępności.

Ten artykuł zawiera instrukcje dotyczące konfigurowania aktywnego i aktywnego połączenia sieci VPN obejmującego wiele lokalizacji oraz połączenia aktywnego między dwiema sieciami wirtualnymi.

* [Część 1 — Tworzenie i Konfigurowanie bramy sieci VPN platformy Azure w trybie aktywny-aktywny](#aagateway)
* [Część 2 — ustanawianie połączeń między lokalizacjami aktywnymi](#aacrossprem)
* [Część 3 — nawiązywanie połączeń między sieciami wirtualnymi w sieci wirtualnej](#aav2v)

Jeśli masz już bramę sieci VPN, możesz:
* [Aktualizowanie istniejącej bramy sieci VPN z trybu aktywny-gotowość do aktywnego-aktywnego lub odwrotnie](#aaupdate)

Można połączyć je razem w celu utworzenia bardziej złożonej topologii sieci o wysokiej dostępności, która spełnia Twoje potrzeby.

> [!IMPORTANT]
> Tryb aktywny-aktywny jest dostępny dla wszystkich jednostek SKU z wyjątkiem warstwy Podstawowa.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Część 1 — Tworzenie i Konfigurowanie bram sieci VPN aktywnych-aktywnych
Poniższe kroki spowodują skonfigurowanie bramy sieci VPN platformy Azure w trybie aktywny-aktywny. Kluczowe różnice między bramami Active-Active i Active-Standby:

* Należy utworzyć dwie konfiguracje IP bramy z dwoma publicznymi adresami IP
* Należy ustawić flagę EnableActiveActiveFeature
* Jednostka SKU bramy musi mieć wartość VpnGw1, VpnGw2, VpnGw3 lub HighPerformance (starsza wersja SKU).

Inne właściwości są takie same, jak bramy nieaktywne-aktywne. 

### <a name="before-you-begin"></a>Zanim rozpoczniesz
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Jeśli nie chcesz używać CloudShell w przeglądarce, musisz zainstalować polecenia cmdlet programu PowerShell Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [omówienie Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 — Tworzenie i Konfigurowanie VNet1
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

To ćwiczenie rozpoczniemy od zadeklarowania zmiennych. Jeśli używasz "try it" Cloud Shell, nastąpi automatyczne połączenie z kontem. Jeśli używasz programu PowerShell lokalnie, Skorzystaj z następującego przykładu, aby pomóc w nawiązaniu połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

W poniższym przykładzie deklarujemy zmienne przy użyciu wartości podanych dla tego ćwiczenia. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami. Tych zmiennych można użyć, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```azurepowershell-interactive
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-create-a-new-resource-group"></a>2. Utwórz nową grupę zasobów

Użyj poniższego przykładu, aby utworzyć nową grupę zasobów:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utwórz sieci testvnet1
Poniższy przykład pozwala utworzyć sieć wirtualną o nazwie TestVNet1 oraz trzy podsieci noszące kolejno nazwy GatewaySubnet, FrontEnd i BackEnd. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2. Tworzenie bramy sieci VPN dla usługi sieci testvnet1 z trybem aktywny-aktywny
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Utwórz publiczne adresy IP i konfiguracje protokołu IP bramy
Zażądaj przydzielenia dwóch publicznych adresów IP do bramy, która zostanie utworzona dla sieci wirtualnej. Zdefiniowano również wymagane konfiguracje podsieci i adresów IP.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Utwórz bramę sieci VPN z konfiguracją Active-Active
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Należy zauważyć, że istnieją dwa wpisy GatewayIpConfig i ustawiono flagę EnableActiveActiveFeature. Tworzenie bramy może potrwać co najmniej 45 minut.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Uzyskaj publiczne adresy IP bramy i adres IP elementu równorzędnego BGP
Po utworzeniu bramy należy uzyskać adres IP elementu równorzędnego BGP na VPN Gateway platformy Azure. Ten adres jest wymagany do skonfigurowania VPN Gateway platformy Azure jako elementu równorzędnego protokołu BGP dla lokalnych urządzeń sieci VPN.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Użyj następujących poleceń cmdlet, aby wyświetlić dwa publiczne adresy IP przydzieloną dla bramy sieci VPN, i odpowiadające im adresy IP elementów równorzędnych BGP dla każdego wystąpienia bramy:

```azurepowershell-interactive
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Kolejność publicznych adresów IP dla wystąpień bramy i odpowiednich adresów komunikacji równorzędnej BGP jest taka sama. W tym przykładzie maszyna wirtualna bramy z publicznym adresem IP 40.112.190.5 będzie używać 10.12.255.4 jako adresu komunikacji równorzędnej BGP, a brama z 138.91.156.129 będzie używać 10.12.255.5. Te informacje są potrzebne podczas konfigurowania lokalnych urządzeń sieci VPN nawiązujących połączenie z bramą Active-Active. Brama jest pokazywana na poniższym diagramie ze wszystkimi adresami:

![Brama Active-Active](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po utworzeniu bramy można używać tej bramy do nawiązywania połączenia między różnymi lokalizacjami lub między sieciami wirtualnymi. W poniższych sekcjach opisano kroki, które należy wykonać w celu ukończenia ćwiczenia.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Część 2 — Nawiązywanie połączenia między lokalizacjami aktywnymi i aktywnymi
Aby nawiązać połączenie między różnymi lokalizacjami, należy utworzyć bramę sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN oraz połączenie z bramą sieci VPN platformy Azure z bramą sieć lokalną. W tym przykładzie Brama sieci VPN platformy Azure jest w trybie aktywny-aktywny. W związku z tym, mimo że istnieje tylko jedno lokalne urządzenie sieci VPN (Brama sieci lokalnej) i jeden zasób połączenia, wystąpienia bramy sieci VPN platformy Azure ustanawiają tunele sieci VPN S2S z urządzeniem lokalnym.

Przed kontynuowaniem upewnij się, że wykonano [część 1](#aagateway) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 — Tworzenie i Konfigurowanie bramy sieci lokalnej
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne
W tym ćwiczeniu będzie nadal można utworzyć konfigurację pokazaną na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Kilka rzeczy, które należy zwrócić uwagę na parametry bramy sieci lokalnej:

* Brama sieci lokalnej może znajdować się w tej samej lub innej lokalizacji i grupie zasobów co Brama sieci VPN. Ten przykład pokazuje je w różnych grupach zasobów, ale w tej samej lokalizacji platformy Azure.
* Jeśli istnieje tylko jedno lokalne urządzenie sieci VPN, jak pokazano powyżej, połączenie aktywne-aktywne może współdziałać z protokołem BGP lub bez niego. W tym przykładzie zastosowano protokół BGP dla połączenia obejmującego wiele lokalizacji.
* W przypadku włączenia protokołu BGP prefiks, który należy zadeklarować dla bramy sieci lokalnej, jest adresem hosta adresu IP elementu równorzędnego BGP na urządzeniu sieci VPN. W tym przypadku jest to prefiks klasy/32 "10.52.255.253/32".
* W razie konieczności należy używać różnych numerów ASN protokołu BGP między sieciami lokalnymi i siecią wirtualną platformy Azure. Jeśli są one takie same, należy zmienić numer ASN sieci wirtualnej, jeśli lokalne urządzenie sieci VPN już używa numeru ASN do komunikacji równorzędnej z innymi sąsiadami BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Utwórz bramę sieci lokalnej dla usługi Site5
Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1. Utwórz grupę zasobów, jeśli nie została jeszcze utworzona.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2. Łączenie bramy sieci wirtualnej i bramy sieć lokalna
#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwie bramy

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz sieci testvnet1 do połączenia site5
W tym kroku utworzysz połączenie z sieci testvnet1, aby Site5_1 z opcją "EnableBGP" ustawioną na $True.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. parametry sieci VPN i protokołu BGP dla lokalnego urządzenia sieci VPN
W poniższym przykładzie przedstawiono parametry, które należy wprowadzić do sekcji konfiguracyjnej protokołu BGP na lokalnym urządzeniu sieci VPN, w tym ćwiczeniu:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Połączenie powinno być nawiązywane po kilku minutach, a sesja komunikacji równorzędnej BGP rozpocznie się po nawiązaniu połączenia IPsec. W tym przykładzie skonfigurowano tylko jedno lokalne urządzenie sieci VPN, co prowadzi do poniższej ilustracji:

![Active-Active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3. Łączenie dwóch lokalnych urządzeń sieci VPN z aktywną i aktywną bramą sieci VPN
W przypadku dwóch urządzeń sieci VPN w tej samej sieci lokalnej można osiągnąć podwójną nadmiarowość, łącząc bramę sieci VPN platformy Azure z drugim urządzeniem sieci VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Utwórz drugą bramę sieci lokalnej dla usługi Site5
Adres IP bramy, prefiks adresu i adres komunikacji równorzędnej BGP dla drugiej bramy sieci lokalnej nie mogą nakładać się na poprzednią bramę sieci lokalnej dla tej samej sieci lokalnych.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Nawiąż połączenie z bramą sieci wirtualnej i drugą bramą sieć lokalną
Utwórz połączenie z sieci testvnet1, aby Site5_2 z opcją "EnableBGP" ustawioną na $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. parametry sieci VPN i protokołu BGP dla drugiego lokalnego urządzenia sieci VPN
Podobnie, poniżej przedstawiono listę parametrów, które należy wprowadzić w drugim urządzeniu sieci VPN:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po nawiązaniu połączenia (tunele) będziesz mieć dwa nadmiarowe urządzenia sieci VPN i tunele, które łączą się z siecią lokalną i platformą Azure:

![Podwójna nadmiarowość — crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Część 3 — nawiązywanie połączenia sieci wirtualnej między sieciami wirtualnymi
W tej sekcji jest tworzone połączenie między sieciami wirtualnymi w sieci wirtualnej z protokołem BGP. 

Poniższe instrukcje stanowią ciąg dalszy poprzedzających je kroków wymienionych powyżej. Należy ukończyć [część 1](#aagateway) , aby utworzyć i skonfigurować sieci testvnet1 oraz VPN Gateway przy użyciu protokołu BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1. Tworzenie TestVNet2 i bramy sieci VPN
Ważne jest, aby upewnić się, że przestrzeń adresów IP nowej sieci wirtualnej (TestVNet2) nie pokrywa się z żadnym z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Połączenia między sieciami wirtualnymi można skonfigurować między różnymi subskrypcjami; Aby uzyskać więcej informacji, zapoznaj się z tematem [Konfigurowanie połączenia Sieć wirtualna-sieć wirtualna](vpn-gateway-vnet-vnet-rm-ps.md) . Pamiętaj o dodaniu "-EnableBgp $True" podczas tworzenia połączeń, aby włączyć protokół BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne
Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```azurepowershell-interactive
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Utwórz bramę sieci VPN typu Active-Active dla usługi TestVNet2
Zażądaj przydzielenia dwóch publicznych adresów IP do bramy, która zostanie utworzona dla sieci wirtualnej. Zdefiniowano również wymagane konfiguracje podsieci i adresów IP.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Utwórz bramę sieci VPN z numerem AS i flagą "EnableActiveActiveFeature". Należy pamiętać, że należy zastąpić domyślny numer ASN na bramach sieci VPN platformy Azure. Zawiadomienie ASN dla połączonej sieci wirtualnych musi być inne, aby można było włączyć routing BGP i transport.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2. Łączenie bram sieci testvnet1 i TestVNet2
W tym przykładzie obie bramy znajdują się w tej samej subskrypcji. Ten krok można wykonać w tej samej sesji programu PowerShell.

#### <a name="1-get-both-gateways"></a>1. Pobierz obie bramy
Pamiętaj o zalogowaniu się i połączeniu z subskrypcją 1.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Utwórz oba połączenia
W tym kroku utworzysz połączenie od sieci testvnet1 do TestVNet2, a połączenie z TestVNet2 do sieci testvnet1.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Należy pamiętać o włączeniu protokołu BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut, a sesja komunikacji równorzędnej BGP zostanie zakończona po zakończeniu połączenia między sieciami wirtualnymi przy użyciu podwójnej nadmiarowości:

![Active-Active-V2V](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Aktualizowanie istniejącej bramy sieci VPN

W przypadku zmiany bramy aktywnego stanu gotowości na aktywny-aktywny należy utworzyć inny publiczny adres IP, a następnie dodać drugą konfigurację protokołu IP bramy. Ta sekcja ułatwia zmianę istniejącej bramy sieci VPN platformy Azure z trybu aktywny-gotowość w trybie aktywny-aktywny lub odwrotnie przy użyciu programu PowerShell. Możesz również zmienić bramę w Azure Portal na stronie **konfiguracji** bramy sieci wirtualnej.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Zmiana bramy aktywnego stanu gotowości na bramę Active-Active

Poniższy przykład konwertuje bramę Active-Standby do bramy Active-Active. 

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

Zastąp następujące parametry używane dla przykładów z ustawieniami, które są wymagane dla własnej konfiguracji, a następnie zadeklaruj te zmienne.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po zadeklarowaniu zmiennych można skopiować i wkleić ten przykład do konsoli programu PowerShell.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Utwórz publiczny adres IP, a następnie Dodaj drugą konfigurację protokołu IP bramy

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Włącz tryb aktywny-aktywny i zaktualizuj bramę

W tym kroku włączasz tryb aktywny-aktywny i aktualizujesz bramę. W tym przykładzie Brama sieci VPN używa obecnie starszej standardowej jednostki SKU. Jednak usługa Active-Active nie obsługuje standardowej jednostki SKU. Aby zmienić rozmiar starszej wersji SKU na obsługiwaną przez program (w tym przypadku HighPerformance), wystarczy określić obsługiwaną starszą jednostkę SKU, która ma być używana.

* W tym kroku nie można zmienić starszej wersji SKU na jedną z nowych jednostek SKU. Można zmienić rozmiar starszej jednostki SKU na inną obsługiwaną starszą jednostkę SKU. Nie można na przykład zmienić jednostki SKU z warstwy Standardowa na VpnGw1 (mimo że VpnGw1 jest obsługiwana w przypadku usługi Active-Active), ponieważ Standard jest starszą wersją SKU, a VpnGw1 jest bieżącą jednostką SKU. Aby uzyskać więcej informacji na temat zmiany rozmiarów i migracji jednostek SKU, zobacz [jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

* Jeśli chcesz zmienić rozmiar bieżącej jednostki SKU, na przykład VpnGw1 do VpnGw3, możesz to zrobić za pomocą tego kroku, ponieważ jednostki SKU znajdują się w tej samej rodzinie SKU. W tym celu należy użyć wartości: ```-GatewaySku VpnGw3```

Jeśli używasz tego środowiska w środowisku, jeśli nie musisz zmieniać rozmiaru bramy, nie musisz określać-GatewaySku. Należy zauważyć, że w tym kroku należy ustawić obiekt bramy w programie PowerShell, aby wyzwolić rzeczywistą aktualizację. Ta aktualizacja może potrwać od 30 do 45 minut, nawet jeśli nie zmieniasz rozmiarów bramy.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Zmiana bramy Active-Active na bramę Active-Standby
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

Zastąp następujące parametry używane dla przykładów z ustawieniami, które są wymagane dla własnej konfiguracji, a następnie zadeklaruj te zmienne.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po zadeklarowaniu zmiennych Pobierz nazwę konfiguracji protokołu IP, którą chcesz usunąć.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Usuń konfigurację protokołu IP bramy i Wyłącz tryb aktywny-aktywny.

Użyj tego przykładu, aby usunąć konfigurację adresu IP bramy i wyłączyć tryb aktywny-aktywny. Zwróć uwagę, że musisz ustawić obiekt bramy w programie PowerShell, aby wyzwolić rzeczywistą aktualizację.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ta aktualizacja może potrwać do 30 do 45 minut.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
