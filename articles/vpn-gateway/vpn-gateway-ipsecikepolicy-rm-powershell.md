---
title: Zasady protokołu IPsec/IKE dla sieci VPN S2S & połączeń typu sieć wirtualna-sieć wirtualna
titleSuffix: Azure VPN Gateway
description: Skonfiguruj zasady protokołu IPsec/IKE dla połączeń S2S lub VNet-to-VNet z bramami sieci VPN platformy Azure przy użyciu Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94649266"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurowanie zasad protokołu IPsec/IKE dla połączeń międzylokacyjnej sieci VPN lub połączeń typu sieć wirtualna-sieć wirtualna

W tym artykule przedstawiono procedurę konfigurowania zasad protokołu IPsec/IKE dla połączeń sieci VPN typu lokacja-lokacja lub połączenia między siecią wirtualną przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Informacje o parametrach zasad protokołu IPsec i IKE dla bram sieci VPN platformy Azure
Standard protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Zapoznaj się z [informacjami o wymaganiach kryptograficznych i bramami sieci VPN platformy Azure](vpn-gateway-about-compliance-crypto.md) , aby dowiedzieć się, jak to może pomóc w zapewnieniu łączności między lokalizacjami i połączeniem między sieciami wirtualnymi.

Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania zasad protokołu IPsec/IKE i stosowania ich do nowego lub istniejącego połączenia:

* [Część 1 — przepływ pracy do tworzenia i ustawiania zasad protokołu IPsec/IKE](#workflow)
* [Część 2 — Obsługiwane algorytmy kryptograficzne i siły klucza](#params)
* [Część 3 — Tworzenie nowego połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE](#crossprem)
* [Część 4 — Tworzenie nowego połączenia między sieciami wirtualnymi przy użyciu zasad protokołu IPsec/IKE](#vnet2vnet)
* [Część 5 — Zarządzanie (tworzenie, Dodawanie, usuwanie) zasady protokołu IPsec/IKE dla połączenia](#managepolicy)

> [!IMPORTANT]
> 1. Należy pamiętać, że zasady protokołu IPsec/IKE działają tylko w następujących jednostkach SKU bramy:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (oparte na trasach)
>    * ***Standard** _ i _ *_HighPerformance_** (oparte na trasach)
> 2. Można określić tylko ***jedną*** kombinację zasad dla danego połączenia.
> 3. Należy określić wszystkie algorytmy i parametry dla IKE (tryb główny) i IPsec (tryb szybki). Określenie zasad częściowych nie jest dozwolone.
> 4. Zapoznaj się z wymaganiami dostawcy urządzeń sieci VPN, aby upewnić się, że zasady są obsługiwane na lokalnych urządzeniach sieci VPN. Połączenia S2S lub VNet-Sieć wirtualna nie mogą ustalić, czy zasady są niezgodne.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Część 1 — przepływ pracy do tworzenia i ustawiania zasad protokołu IPsec/IKE
Ta sekcja zawiera opis przepływu pracy w celu utworzenia i zaktualizowania zasad protokołu IPsec/IKE w połączeniu sieci VPN S2S lub połączenia między siecią wirtualną:
1. Tworzenie sieci wirtualnej i bramy VPN Gateway
2. Utwórz bramę sieci lokalnej dla połączenia między lokalizacjami lub inną sieć wirtualną i bramę dla połączenia Sieć wirtualna-sieć wirtualna
3. Tworzenie zasad protokołu IPsec/IKE z wybranymi algorytmami i parametrami
4. Tworzenie połączenia (IPsec lub VNet2VNet) przy użyciu zasad protokołu IPsec/IKE
5. Dodawanie/aktualizowanie/usuwanie zasad protokołu IPsec/IKE dla istniejącego połączenia

Instrukcje zawarte w tym artykule ułatwiają konfigurowanie i Konfigurowanie zasad protokołu IPsec/IKE, jak pokazano na diagramie:

![IPSec — zasady IKE](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Część 2 — Obsługiwane algorytmy kryptograficzne & mocnych kluczy

W poniższej tabeli wymieniono obsługiwane algorytmy kryptograficzne i siły klucza konfigurowalne przez klientów:

| **IPsec/IKEv2**  | **Opcje**    |
| ---  | --- 
| Szyfrowanie IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integralność IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupa DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, brak |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak    |
| Integralność IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupa PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak 
| Okres istnienia skojarzeń zabezpieczeń QM   | (**Opcjonalnie**: wartości domyślne są używane, jeśli nie zostaną określone)<br>Sekundy (liczba całkowita; **min. 300**/wartość domyślna 27 000 sekund)<br>KB (liczba całkowita; **min. 1024**/wartość domyślna to 102 400 000 KB)   |
| Selektor ruchu | UsePolicyBasedTrafficSelectors * * ($True/$False; **Opcjonalna**, domyślna $false, jeśli nie zostanie określona)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfiguracja lokalnego urządzenia sieci VPN musi być zgodna z następującymi algorytmami (lub je zawierać) oraz z następującymi parametrami określonymi w zasadach protokołu IPsec/IKE platformy Azure (lub je zawierać):**
>    * Algorytm szyfrowania IKE (tryb główny/faza 1)
>    * Algorytm integralności IKE (tryb główny/faza 1)
>    * Grupa DH (tryb główny/faza 1)
>    * Algorytm szyfrowania IPsec (tryb szybki/faza 2)
>    * Algorytm integralności protokołu IPsec (tryb szybki/faza 2)
>    * Grupa PFS (tryb szybki/faza 2)
>    * Wybór ruchu (jeśli jest używany UsePolicyBasedTrafficSelectors)
>    * Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.
>
> 2. **Jeśli GCMAES jest używany jako algorytm szyfrowania IPsec, należy wybrać ten sam algorytm GCMAES i długość klucza dla integralności protokołu IPsec. na przykład przy użyciu GCMAES128 dla obu**
> 3. W powyższej tabeli:
>    * Protokół IKEv2 odpowiada trybowi głównemu lub fazie 1
>    * Protokół IPsec odnosi się do trybu szybkiego lub fazy 2
>    * Grupa DH określa grupę Diffie-Hellmen używaną w trybie głównym lub w fazie 1
>    * Grupa PFS określiła grupę Diffie-Hellmen używaną w trybie szybkim lub fazie 2
> 4. Okres istnienia skojarzenia zabezpieczeń trybu głównego protokołu IKEv2 jest ustalony na 28 800 sekund na bramach sieci VPN platformy Azure
> 5. Ustawienie opcji "UsePolicyBasedTrafficSelectors" na $True połączenia spowoduje skonfigurowanie bramy sieci VPN platformy Azure w celu połączenia z zaporą sieci VPN opartej na zasadach lokalnych. W przypadku włączenia PolicyBasedTrafficSelectors należy upewnić się, że urządzenie sieci VPN ma pasujące selektory ruchu zdefiniowane ze wszystkimi kombinacjami sieci lokalnych (Brama sieci lokalnej) prefiksami do/z prefiksów sieci wirtualnej platformy Azure, a nie z dowolnego miejsca. Na przykład jeśli prefiksy sieci lokalnej to 10.1.0.0/16 i 10.2.0.0/16, a prefiksy sieci wirtualnej to 192.168.0.0/16 i 172.16.0.0/16, trzeba określić następujące selektory ruchu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Aby uzyskać więcej informacji na temat selektorów ruchu opartych na zasadach, zobacz [łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Poniższa tabela zawiera listę odpowiednich grup Diffie-Hellman obsługiwanych przez zasady niestandardowe:

| **Grupa Diffie’ego-Hellmana**  | **DHGroup**              | **PFSGroup** | **Długość klucza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP, 768-bitowy   |
| 2                         | DHGroup2                 | PFS2         | MODP, 1024-bitowy  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP, 2048-bitowy  |
| 19                        | ECP256                   | ECP256       | ECP, 256-bitowy    |
| 20                        | ECP384                   | ECP384       | ECP, 384-bitowy    |
| 24                        | DHGroup24                | PFS24        | MODP, 2048-bitowy  |

Więcej informacji można znaleźć w artykułach [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Część 3 — Tworzenie nowego połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

Ta sekcja przeprowadzi Cię przez kroki tworzenia połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE. Poniższe kroki tworzą połączenie, jak pokazano na diagramie:

![S2S — zasady](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zobacz [Tworzenie połączenia S2S VPN](vpn-gateway-create-site-to-site-rm-powershell.md) , aby uzyskać bardziej szczegółowe instrukcje krok po kroku dotyczące tworzenia połączenia sieci VPN S2S.

### <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj Azure Resource Manager polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [omówienie Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

W tym ćwiczeniu zaczynamy od zadeklarowania zmiennych. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami.

```powershell
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połącz się z subskrypcją i Utwórz nową grupę zasobów

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Utwórz sieć wirtualną, bramę sieci VPN i bramę sieci lokalnej.

Poniższy przykład tworzy sieć wirtualną, sieci testvnet1 z trzema podsieciami i bramę sieci VPN. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Krok 2. Tworzenie połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy zasady protokołu IPsec/IKE z następującymi algorytmami i parametrami:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, okres istnienia skojarzenia zabezpieczeń 14400 s & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Jeśli używasz GCMAES dla protokołu IPsec, należy użyć tego samego algorytmu GCMAES i długości klucza dla szyfrowania i integralności protokołu IPsec. Na przykład, odpowiednie parametry będą "-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256" podczas korzystania z GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Utwórz połączenie sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

Utwórz połączenie sieci VPN S2S i Zastosuj utworzone wcześniej zasady protokołu IPsec/IKE.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcjonalnie można dodać "-UsePolicyBasedTrafficSelectors $True" do polecenia cmdlet Create Connection, aby umożliwić bramie sieci VPN platformy Azure Łączenie się z urządzeniami sieci VPN opartymi na zasadach, zgodnie z powyższym opisem.

> [!IMPORTANT]
> Po określeniu zasad protokołu IPsec/IKE w ramach połączenia Brama sieci VPN platformy Azure będzie wysyłać i akceptować tylko propozycje protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi kluczami w tym konkretnym połączeniu. Upewnij się, że lokalne urządzenie sieci VPN do połączenia używa lub akceptuje dokładną kombinację zasad, w przeciwnym razie tunel VPN S2S nie zostanie nawiązane.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Część 4 — Tworzenie nowego połączenia między sieciami wirtualnymi przy użyciu zasad protokołu IPsec/IKE

Kroki tworzenia połączenia między sieciami wirtualnymi za pomocą zasad protokołu IPsec/IKE są podobne do połączeń sieci VPN S2S. Następujące przykładowe skrypty tworzą połączenie, jak pokazano na diagramie:

![V2V — zasady](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zobacz [Tworzenie połączenia między sieciami wirtualnymi,](vpn-gateway-vnet-vnet-rm-ps.md) Aby uzyskać bardziej szczegółowe instrukcje tworzenia połączenia między sieciami wirtualnymi. Aby utworzyć i skonfigurować sieci testvnet1 oraz VPN Gateway, należy wykonać [część 3](#crossprem) .

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Krok 1 — Tworzenie drugiej sieci wirtualnej i bramy sieci VPN

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj zmienne

Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Utwórz drugą sieć wirtualną i bramę sieci VPN w nowej grupie zasobów

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2. Tworzenie połączenia sieci wirtualnej toVNet za pomocą zasad protokołu IPsec/IKE

Podobnie jak połączenie sieci VPN S2S, Utwórz zasady protokołu IPsec/IKE, a następnie zastosuj je do nowego połączenia.

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy inne zasady IPsec/IKE z następującymi algorytmami i parametrami:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, okres istnienia SA 14400 sekund & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Tworzenie połączeń między sieciami wirtualnymi za pomocą zasad protokołu IPsec/IKE

Utwórz połączenie między sieciami wirtualnymi i Zastosuj utworzone zasady protokołu IPsec/IKE. W tym przykładzie obie bramy znajdują się w tej samej subskrypcji. Dlatego można utworzyć i skonfigurować oba połączenia z tymi samymi zasadami protokołu IPsec/IKE w tej samej sesji programu PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Po określeniu zasad protokołu IPsec/IKE w ramach połączenia Brama sieci VPN platformy Azure będzie wysyłać i akceptować tylko propozycje protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi kluczami w tym konkretnym połączeniu. Upewnij się, że zasady protokołu IPsec dla obu połączeń są takie same, w przeciwnym razie połączenie między sieciami wirtualnymi nie zostanie nawiązane.

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut i będzie dostępna następująca topologia sieci, jak pokazano na początku:

![IPSec — zasady IKE](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Część 5 — aktualizowanie zasad protokołu IPsec/IKE dla połączenia

W ostatniej sekcji pokazano, jak zarządzać zasadami IPsec/IKE dla istniejącego połączenia S2S lub połączeń między sieciami wirtualnymi. Poniższe instrukcje przeprowadzą Cię przez następujące operacje dotyczące połączenia:

1. Pokaż zasady protokołu IPsec/IKE połączenia
2. Dodawanie lub aktualizowanie zasad protokołu IPsec/IKE do połączenia
3. Usuwanie zasad protokołu IPsec/IKE z połączenia

Te same kroki dotyczą połączeń S2S i VNet-to-VNet.

> [!IMPORTANT]
> Zasady protokołu IPsec/IKE są obsługiwane tylko w przypadku bram sieci VPN opartych na trasach *standardowych* i *HighPerformance* . Nie działa w przypadku podstawowej jednostki SKU bramy ani bramy sieci VPN opartej na zasadach.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Pokaż zasady protokołu IPsec/IKE połączenia

Poniższy przykład przedstawia sposób pobierania zasad protokołu IPsec/IKE skonfigurowanych w ramach połączenia. Ponadto skrypty kontynuują pracę z powyższych ćwiczeń.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Ostatnie polecenie wyświetla listę bieżących zasad protokołu IPsec/IKE skonfigurowanych dla połączenia, jeśli istnieją. Poniżej przedstawiono przykładowe dane wyjściowe dla połączenia:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Jeśli nie skonfigurowano zasad protokołu IPsec/IKE, polecenie (PS> $connection 6. IpsecPolicies) Pobiera pusty zwrot. Nie oznacza to, że protokół IPsec/IKE nie jest skonfigurowany w ramach połączenia, ale nie ma niestandardowych zasad protokołu IPsec/IKE. Rzeczywiste połączenie używa zasad domyślnych negocjowanych między lokalnym urządzeniem sieci VPN i bramą sieci VPN platformy Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Dodaj lub zaktualizuj zasady protokołu IPsec/IKE dla połączenia

Kroki umożliwiające dodanie nowych zasad lub zaktualizowanie istniejących zasad w połączeniu są takie same: Utwórz nowe zasady, a następnie Zastosuj nowe zasady do połączenia.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Aby włączyć "UsePolicyBasedTrafficSelectors" podczas nawiązywania połączenia z lokalnym urządzeniem sieci VPN opartym na zasadach, należy dodać do polecenia cmdlet parametr "-UsePolicyBaseTrafficSelectors" lub ustawić $False, aby wyłączyć opcję:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Możesz ponownie uzyskać połączenie, aby sprawdzić, czy zasady zostały zaktualizowane.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Dane wyjściowe powinny być widoczne z ostatniego wiersza, jak pokazano w następującym przykładzie:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Usuń zasady protokołu IPsec/IKE z połączenia

Po usunięciu zasad niestandardowych z połączenia usługa Azure VPN Gateway wraca do [domyślnej listy propozycji protokołu IPSec/IKE](vpn-gateway-about-vpn-devices.md) i ponownie negocjuje ją z lokalnym urządzeniem sieci VPN.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Możesz użyć tego samego skryptu, aby sprawdzić, czy zasady zostały usunięte z połączenia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat selektorów ruchu opartych na zasadach, zobacz [łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).