---
title: Konfiguracje urządzeń partnerskiej sieci VPN do łączenia się z bramami sieci VPN platformy Azure
description: Ten artykuł zawiera omówienie konfiguracji urządzeń partnerskiej sieci VPN w celu nawiązania połączenia z bramami sieci VPN platformy Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 01ceb067f589ef04b04ff0bf05e2971ee30f407c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400164"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Omówienie konfiguracji urządzeń partnerskiej sieci VPN
Ten artykuł zawiera omówienie konfigurowania lokalnych urządzeń sieci VPN w celu nawiązania połączenia z bramami sieci VPN platformy Azure. Przykładowa konfiguracja sieci wirtualnej platformy Azure i bramy sieci VPN służy do pokazywania sposobu nawiązywania połączenia z różnymi konfiguracjami lokalnych urządzeń sieci VPN przy użyciu tych samych parametrów.



## <a name="device-requirements"></a>Wymagania dotyczące urządzeń
Bramy sieci VPN platformy Azure używają standardowych pakietów protokołu IPsec/IKE dla tuneli VPN typu lokacja-lokacja (S2S). Aby uzyskać listę parametrów protokołu IPsec/IKE i algorytmów kryptograficznych dla bram sieci VPN platformy Azure, zobacz [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md). Można również określić dokładne algorytmy i siły klucza dla określonego połączenia, zgodnie z opisem w [temacie Informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Pojedynczy tunel VPN
Pierwsza konfiguracja w przykładzie składa się z pojedynczego tunelu sieci VPN S2S między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN. Opcjonalnie można skonfigurować [Border Gateway Protocol (BGP) przez tunel VPN](#bgp).

![Diagram pojedynczego tunelu sieci VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pojedynczego tunelu sieci VPN, zobacz [Konfigurowanie połączenia lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W poniższych sekcjach opisano parametry połączenia dla konfiguracji przykładowej oraz podano skrypt programu PowerShell, który pomoże Ci rozpocząć pracę.

### <a name="connection-parameters"></a>Parametry połączenia
Ta sekcja zawiera listę parametrów, które zostały opisane w poprzednich sekcjach.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Adres IP VPN Gateway platformy Azure         |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Adres IP lokalnego urządzenia sieci VPN    | Adres IP lokalnego urządzenia sieci VPN    |
| * Numer ASN protokołu BGP sieci wirtualnej                | 65010                        |
| * Adres IP elementu równorzędnego protokołu BGP platformy Azure           | 10.12.255.30                 |
| * Lokalne ASN protokołu BGP         | 65050                        |
| * Lokalny adres IP elementu równorzędnego BGP     | 10.52.255.254                |

\* Opcjonalny parametr tylko dla protokołu BGP.

### <a name="sample-powershell-script"></a>Przykładowy skrypt programu PowerShell
Ta sekcja zawiera przykładowy skrypt pozwalający rozpocząć pracę. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie połączenia S2S VPN przy użyciu programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
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
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>Obowiązkowe Używanie niestandardowych zasad protokołu IPsec/IKE z usługą UsePolicyBasedTrafficSelectors
Jeśli urządzenia sieci VPN nie obsługują żadnych selektorów ruchu, na przykład konfiguracji opartych na trasach lub VTI, Utwórz niestandardowe zasady protokołu IPsec/IKE przy użyciu opcji [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

> [!IMPORTANT]
> Należy utworzyć zasady protokołu IPsec/IKE, aby włączyć opcję **UsePolicyBasedTrafficSelectors** w połączeniu.


Przykładowy skrypt tworzy zasady protokołu IPsec/IKE z następującymi algorytmami i parametrami:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, okres istnienia SA 7 200 sekund i 20 480 000 KB (20 GB)

Skrypt stosuje zasady protokołu IPsec/IKE i włącza opcję **UsePolicyBasedTrafficSelectors** dla połączenia.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>Obowiązkowe Użyj protokołu BGP dla połączenia S2S VPN
Podczas tworzenia połączenia sieci VPN S2S można opcjonalnie użyć [protokołu BGP dla bramy sieci VPN](vpn-gateway-bgp-resource-manager-ps.md). Takie podejście ma dwie różnice:

* Prefiksy adresów lokalnych mogą być pojedynczym adresem hosta. Adres IP lokalnego elementu równorzędnego protokołu BGP jest określony w następujący sposób:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Podczas tworzenia połączenia należy ustawić opcję **-EnableBGP** na $true:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bram sieci VPN w usłudze Active-Active, zobacz [Konfigurowanie aktywnych i aktywnych bram sieci VPN dla połączeń między różnymi lokalizacjami i połączeniami typu sieć wirtualna-sieć wirtualna](vpn-gateway-activeactive-rm-powershell.md).

