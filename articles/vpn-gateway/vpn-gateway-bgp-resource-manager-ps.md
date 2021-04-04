---
title: 'Azure VPN Gateway: Konfigurowanie protokołu BGP: PowerShell'
description: W tym artykule opisano konfigurowanie protokołu BGP z bramami sieci VPN platformy Azure przy użyciu Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659764"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure przy użyciu programu PowerShell
W tym artykule przedstawiono kroki umożliwiające włączenie protokołu BGP w ramach połączenia sieci VPN między lokacjami (S2S) i połączenia między sieciami wirtualnymi przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell.



## <a name="about-bgp"></a>BGP — informacje
BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP umożliwia bramom sieci VPN na platformie Azure i lokalnym urządzeniom sieci VPN (nazywanym elementami równorzędnymi lub sąsiednimi BGP), przeprowadzaną za pośrednictwem bram lub routerów wymianę „tras” zawierających przeznaczone dla obu bram informacje na temat dostępności i osiągalności tych prefiksów. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP.

Zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure](vpn-gateway-bgp-overview.md) , aby uzyskać więcej informacji na temat zalet protokołu BGP oraz poznać wymagania techniczne i zagadnienia dotyczące korzystania z protokołu BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Wprowadzenie do protokołu BGP w bramach sieci VPN platformy Azure

W tym artykule przedstawiono kroki umożliwiające wykonanie następujących zadań:

* [Część 1 — Włączanie protokołu BGP na bramie sieci VPN platformy Azure](#enablebgp)
* Część 2 — Nawiązywanie połączenia między lokalizacjami przy użyciu protokołu BGP
* [Część 3 — nawiązywanie połączenia między sieciami wirtualnymi przy użyciu protokołu BGP](#v2vbgp)

Każda część instrukcji stanowi podstawowy blok konstrukcyjny umożliwiający włączenie protokołu BGP w łączności sieciowej. Jeśli wszystkie trzy części zostały wykonane, można utworzyć topologię, jak pokazano na poniższym diagramie:

![Topologia protokołu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Możesz połączyć części wspólnie, aby utworzyć bardziej złożoną sieć tranzytową z wieloma przeskokami, która spełnia Twoje potrzeby.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Część 1 — Konfigurowanie protokołu BGP na platformie Azure VPN Gateway
Kroki konfiguracji konfigurowania parametrów protokołu BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![Brama BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Zanim rozpoczniesz
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj Azure Resource Manager polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 — Tworzenie i Konfigurowanie VNet1
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne
W tym ćwiczeniu zaczynamy od zadeklarowania zmiennych. Poniższy przykład deklaruje zmienne przy użyciu wartości dla tego ćwiczenia. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami. Tych zmiennych można użyć, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połącz się z subskrypcją i Utwórz nową grupę zasobów
Aby użyć poleceń cmdlet Menedżer zasobów, upewnij się, że przełączono do trybu programu PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utwórz sieci testvnet1
Poniższy przykład tworzy sieć wirtualną o nazwie sieci testvnet1 i trzy podsieci, jedną o nazwie GatewaySubnet, jedną o nazwie frontonu i jedną nazywaną zapleczem. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2. Tworzenie VPN Gateway dla sieci testvnet1 przy użyciu parametrów protokołu BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Utwórz konfiguracje adresów IP i podsieci
Następnie zostaje przesłane żądanie przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Zdefiniujesz również wymagane konfiguracje podsieci i IP.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Utwórz bramę sieci VPN przy użyciu numeru AS
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Protokół BGP wymaga Route-Based bramy sieci VPN, a także parametru dodawania,-ASN, aby ustawić ASN (numer AS) dla sieci testvnet1. Jeśli parametr ASN nie zostanie ustawiony, zostanie przypisany numer ASN 65515. Tworzenie bramy może potrwać co najmniej 30 minut.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskaj adres IP elementu równorzędnego protokołu BGP platformy Azure
Po utworzeniu bramy należy uzyskać adres IP elementu równorzędnego BGP na VPN Gateway platformy Azure. Ten adres jest wymagany do skonfigurowania VPN Gateway platformy Azure jako elementu równorzędnego protokołu BGP dla lokalnych urządzeń sieci VPN.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Ostatnie polecenie pokazuje odpowiednie konfiguracje protokołu BGP na platformie Azure VPN Gateway; na przykład:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po utworzeniu bramy można użyć tej bramy do ustanowienia połączenia między lokalizacjami lub połączenia między sieciami wirtualnymi przy użyciu protokołu BGP. W poniższych sekcjach opisano kroki, które należy wykonać w celu ukończenia ćwiczenia.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Część 2 — Nawiązywanie połączenia między lokalizacjami przy użyciu protokołu BGP

Aby nawiązać połączenie między różnymi lokalizacjami, należy utworzyć bramę sieci lokalnej, która będzie reprezentować lokalne urządzenie sieci VPN, oraz połączenie z bramą VPN z bramą sieci lokalnej. Istnieją artykuły, które przeprowadzą Cię przez te kroki, tym artykuł zawiera dodatkowe właściwości wymagane do określenia parametrów konfiguracji protokołu BGP.

![Protokół BGP dla wielu lokalizacji](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Przed kontynuowaniem upewnij się, że wykonano [część 1](#enablebgp) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 — Tworzenie i Konfigurowanie bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

To ćwiczenie kontynuuje kompilację konfiguracji pokazanej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Kilka rzeczy, które należy zwrócić uwagę na parametry bramy sieci lokalnej:

* Brama sieci lokalnej może znajdować się w tej samej lub innej lokalizacji i grupie zasobów co Brama sieci VPN. Ten przykład pokazuje je w różnych grupach zasobów w różnych lokalizacjach.
* Prefiks, który należy zadeklarować dla bramy sieci lokalnej, to adres IP elementu równorzędnego BGP na urządzeniu sieci VPN. W tym przypadku jest to prefiks klasy/32 "10.52.255.254/32".
* W razie konieczności należy używać różnych numerów ASN protokołu BGP między sieciami lokalnymi i siecią wirtualną platformy Azure. Jeśli są one takie same, należy zmienić numer ASN sieci wirtualnej, jeśli lokalne urządzenie sieci VPN już używa numeru ASN do komunikacji równorzędnej z innymi sąsiadami BGP.

Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Utwórz bramę sieci lokalnej dla usługi Site5

Należy pamiętać, aby utworzyć grupę zasobów, jeśli nie została utworzona, przed utworzeniem bramy sieci lokalnej. Zwróć uwagę na dwa dodatkowe parametry bramy sieci lokalnej: ASN i BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2. Łączenie bramy sieci wirtualnej i bramy sieć lokalna

#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwie bramy

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz sieci testvnet1 do połączenia site5

W tym kroku utworzysz połączenie od sieci testvnet1 do site5. Aby włączyć protokół BGP dla tego połączenia, należy określić wartość "-EnableBGP $True". Jak wspomniano wcześniej, możliwe jest posiadanie połączeń BGP i innych niż BGP dla tego samego VPN Gateway platformy Azure. Jeśli w właściwości Connection nie włączono protokołu BGP, platforma Azure nie będzie włączać protokołu BGP dla tego połączenia, mimo że parametry protokołu BGP są już skonfigurowane na obu bramach.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Poniższy przykład zawiera listę parametrów wprowadzonych w sekcji Konfiguracja protokołu BGP na lokalnym urządzeniu sieci VPN w ramach tego ćwiczenia:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Połączenie jest nawiązywane po kilku minutach, a sesja komunikacji równorzędnej BGP rozpoczyna się po nawiązaniu połączenia IPsec.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Część 3 — nawiązywanie połączenia między sieciami wirtualnymi przy użyciu protokołu BGP

Ta sekcja dodaje połączenie między sieciami wirtualnymi przy użyciu protokołu BGP, jak pokazano na poniższym diagramie:

![Diagram przedstawiający połączenie sieciowe w języku V do V.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje są kontynuowane w poprzednich krokach. Należy ukończyć [część i](#enablebgp) , aby utworzyć i skonfigurować sieci testvnet1 oraz VPN Gateway przy użyciu protokołu BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1. Tworzenie TestVNet2 i bramy sieci VPN

Ważne jest, aby upewnić się, że przestrzeń adresów IP nowej sieci wirtualnej (TestVNet2) nie pokrywa się z żadnym z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia między sieciami wirtualnymi między różnymi subskrypcjami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia między sieciami wirtualnymi](vpn-gateway-vnet-vnet-rm-ps.md). Pamiętaj o dodaniu "-EnableBgp $True" podczas tworzenia połączeń, aby włączyć protokół BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Utwórz bramę sieci VPN dla usługi TestVNet2 przy użyciu parametrów protokołu BGP

Zażądaj przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej, i zdefiniuj wymaganą konfigurację podsieci i adresu IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Utwórz bramę sieci VPN z numerem AS. Należy zastąpić domyślny numer ASN na bramach sieci VPN platformy Azure. Zawiadomienie ASN dla połączonej sieci wirtualnych musi być inne, aby można było włączyć routing BGP i transport.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2. Łączenie bram sieci testvnet1 i TestVNet2

W tym przykładzie obie bramy znajdują się w tej samej subskrypcji. Ten krok można wykonać w tej samej sesji programu PowerShell.

#### <a name="1-get-both-gateways"></a>1. Pobierz obie bramy

Pamiętaj o zalogowaniu się i połączeniu z subskrypcją 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Utwórz oba połączenia

W tym kroku utworzysz połączenie od sieci testvnet1 do TestVNet2, a połączenie z TestVNet2 do sieci testvnet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Należy pamiętać o włączeniu protokołu BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków połączenie jest nawiązywane po kilku minutach. Sesja komunikacji równorzędnej BGP jest wykonywana po zakończeniu połączenia między sieciami wirtualnymi.

Jeśli wszystkie trzy części tego ćwiczenia zostały wykonane, została ustanowiona następująca topologia sieci:

![Protokół BGP dla połączeń między sieciami wirtualnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).