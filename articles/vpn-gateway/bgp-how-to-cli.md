---
title: 'Konfigurowanie protokołu BGP na platformie Azure VPN Gateway: interfejs wiersza polecenia'
description: W tym artykule omówiono konfigurowanie protokołu BGP z bramą sieci VPN platformy Azure przy użyciu Azure Resource Manager i interfejsu wiersza polecenia.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393602"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Jak skonfigurować protokół BGP na bramie sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia

W tym artykule opisano włączenie protokołu BGP w przypadku połączenia sieci VPN między lokalizacjami (S2S) i połączenia między sieciami wirtualnymi (czyli połączenia między nimi) przy użyciu modelu wdrażania Azure Resource Manager i interfejsu wiersza polecenia platformy Azure.

## <a name="about-bgp"></a>BGP — informacje

Protokół BGP jest standardowym protokołem routingu używanym powszechnie w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP umożliwia bramom sieci VPN i lokalnym urządzeniom sieci VPN, nazywanymi równorzędnymi BGP lub sąsiadami, wymianę tras. Trasy informują obie bramy o dostępności i osiągalności dla prefiksów, aby przejść przez bramę lub routery. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami przez propagowanie tras, które Brama BGP uzyskuje od jednego elementu równorzędnego BGP, do wszystkich innych elementów równorzędnych protokołu BGP.

Aby uzyskać więcej informacji na temat zalet protokołu BGP oraz poznać wymagania techniczne i zagadnienia dotyczące korzystania z protokołu BGP, zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure](vpn-gateway-bgp-overview.md).

Ten artykuł ułatwia wykonywanie następujących zadań:

* [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) (wymagane)

  Następnie można wykonać jedną z następujących sekcji lub obie:

* [Nawiązywanie połączenia między lokalizacjami przy użyciu protokołu BGP](#crossprembgp)
* [Nawiązywanie połączenia między sieciami wirtualnymi przy użyciu protokołu BGP](#v2vbgp)

Każda z tych trzech sekcji stanowi podstawowy blok konstrukcyjny do włączenia protokołu BGP w łączności sieciowej. Jeśli wszystkie trzy sekcje zostały wykonane, można utworzyć topologię, jak pokazano na poniższym diagramie:

![Topologia protokołu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można połączyć te sekcje w celu utworzenia bardziej złożonej sieci tranzytowej wieloskokowych, która spełnia Twoje potrzeby.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Włączanie protokołu BGP dla bramy sieci VPN

Ta sekcja jest wymagana przed wykonaniem któregokolwiek z kroków w innych sekcjach konfiguracji. Poniższe kroki konfiguracji ustawiają parametry protokołu BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![Brama BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2,0 lub nowszej). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1. Tworzenie i Konfigurowanie sieci testvnet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Nawiązywanie połączenia z subskrypcją

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Tworzenie grupy zasobów

Poniższy przykład tworzy grupę zasobów o nazwie TestRG1 w lokalizacji "Wschodnie". Jeśli masz już grupę zasobów w regionie, w której chcesz utworzyć sieć wirtualną, możesz użyć tej grupy.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Utwórz sieci testvnet1

Poniższy przykład umożliwia utworzenie sieci wirtualnej o nazwie sieci testvnet1 i trzech podsieci: GatewaySubnet, frontonu i zaplecza. Podczas podstawiania wartości ważne jest, aby zawsze nazwać podsieć bramy GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

Pierwsze polecenie tworzy przestrzeń adresową frontonu i podsieć frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresową dla podsieci zaplecza. Trzecie i czwarte polecenia tworzą podsieć zaplecza i GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2. Tworzenie bramy sieci VPN dla usługi sieci testvnet1 przy użyciu parametrów protokołu BGP

#### <a name="1-create-the-public-ip-address"></a>1. Utwórz publiczny adres IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Utwórz bramę sieci VPN przy użyciu numeru AS

Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Protokół BGP wymaga Route-Based bramy sieci VPN. Wymagany jest również dodatkowy parametr, `-Asn` Aby ustawić numer systemu autonomicznego (ASN) dla sieci testvnet1. Utworzenie bramy może potrwać trochę czasu (45 minut lub więcej). 

Jeśli uruchomisz to polecenie przy użyciu `--no-wait` parametru, nie zobaczysz żadnych informacji zwrotnych ani danych wyjściowych. `--no-wait`Parametr umożliwia utworzenie bramy w tle. Nie oznacza to, że Brama sieci VPN jest tworzona natychmiast.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskaj adres IP elementu równorzędnego protokołu BGP platformy Azure

Po utworzeniu bramy należy uzyskać adres IP elementu równorzędnego BGP w bramie sieci VPN platformy Azure. Ten adres jest wymagany do skonfigurowania bramy sieci VPN jako elementu równorzędnego protokołu BGP dla lokalnych urządzeń sieci VPN.

Uruchom następujące polecenie i zapoznaj się z `bgpSettings` sekcją w górnej części danych wyjściowych:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po utworzeniu bramy można użyć tej bramy do ustanowienia połączenia między lokalizacjami lub połączenia między sieciami wirtualnymi przy użyciu protokołu BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Nawiązywanie połączenia między lokalizacjami przy użyciu protokołu BGP

Aby nawiązać połączenie między różnymi lokalizacjami, należy utworzyć bramę sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN. Następnie należy połączyć bramę sieci VPN platformy Azure z bramą sieci lokalnej. Mimo że te kroki są podobne do tworzenia innych połączeń, obejmują dodatkowe właściwości wymagane do określenia parametrów konfiguracji protokołu BGP.

![Protokół BGP dla wielu lokalizacji](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1. Tworzenie i Konfigurowanie bramy sieci lokalnej

To ćwiczenie kontynuuje kompilację konfiguracji pokazanej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji. Podczas pracy z bramami sieci lokalnej należy pamiętać o następujących kwestiach:

* Brama sieci lokalnej może znajdować się w tej samej lokalizacji i grupie zasobów co Brama sieci VPN lub może znajdować się w innej lokalizacji i grupie zasobów. Ten przykład przedstawia bramy w różnych grupach zasobów w różnych lokalizacjach.
* Minimalny prefiks, który należy zadeklarować dla bramy sieci lokalnej, to adres IP elementu równorzędnego BGP na urządzeniu sieci VPN. W tym przypadku jest to prefiks klasy/32 10.51.255.254/32.
* W razie konieczności należy używać różnych numerów ASN protokołu BGP między sieciami lokalnymi i siecią wirtualną platformy Azure. Jeśli są takie same, należy zmienić numer ASN sieci wirtualnej, jeśli lokalne urządzenia sieci VPN już używają numeru ASN do komunikacji równorzędnej z innymi sąsiadami BGP.

Przed kontynuowaniem upewnij się, że została ukończona sekcja [Włączanie protokołu BGP dla bramy sieci VPN](#enablebgp) w tym ćwiczeniu i że nadal masz połączenie z subskrypcją 1. Zwróć uwagę, że w tym przykładzie utworzysz nową grupę zasobów. Zwróć również uwagę na dwa dodatkowe parametry bramy sieci lokalnej: `Asn` i `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2. Łączenie bramy sieci wirtualnej i bramy sieć lokalna

W tym kroku utworzysz połączenie od sieci testvnet1 do site5. Należy określić parametr, `--enable-bgp` Aby włączyć protokół BGP dla tego połączenia. 

W tym przykładzie Brama sieci wirtualnej i Brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić cały identyfikator zasobu dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator zasobu VNet1GW

Użyj danych wyjściowych z następującego polecenia, aby pobrać identyfikator zasobu dla VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Znajdź wiersz w danych wyjściowych `"id":` . Potrzebujesz wartości w cudzysłowie, aby utworzyć połączenie w następnej sekcji.

Przykładowe dane wyjściowe:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Skopiuj wartości `"id":` z edytora tekstu, takiego jak Notatnik, aby można je było łatwo wkleić podczas tworzenia połączenia. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Pobierz identyfikator zasobu site5

Użyj poniższego polecenia, aby pobrać identyfikator zasobu Site5 z danych wyjściowych:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Utwórz połączenie sieci testvnet1-to-site5

W tym kroku utworzysz połączenie od sieci testvnet1 do site5. Jak wspomniano wcześniej, możliwe jest posiadanie połączeń BGP i innych niż BGP dla tej samej bramy sieci VPN platformy Azure. Jeśli w właściwości Connection nie włączono protokołu BGP, platforma Azure nie będzie włączać protokołu BGP dla tego połączenia, mimo że parametry protokołu BGP są już skonfigurowane na obu bramach. Zastąp identyfikatory subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

W tym ćwiczeniu Poniższy przykład zawiera listę parametrów do wprowadzenia w sekcji Konfiguracja protokołu BGP lokalnego urządzenia sieci VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po kilku minutach połączenie powinno zostać ustanowione. Sesja komunikacji równorzędnej BGP rozpoczyna się po nawiązaniu połączenia IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Nawiązywanie połączenia między sieciami wirtualnymi przy użyciu protokołu BGP

Ta sekcja dodaje połączenie między sieciami wirtualnymi przy użyciu protokołu BGP, jak pokazano na poniższym diagramie: 

![Protokół BGP dla połączeń między sieciami wirtualnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje wykonują kroki opisane w poprzednich sekcjach. Aby utworzyć i skonfigurować sieci testvnet1 i bramę sieci VPN z protokołem BGP, należy ukończyć sekcję [Włączanie protokołu BGP dla bramy sieci VPN](#enablebgp) .

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1. Tworzenie TestVNet2 i bramy sieci VPN

Ważne jest, aby upewnić się, że przestrzeń adresów IP nowej sieci wirtualnej (TestVNet2) nie nakłada się na żadne z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia między sieciami wirtualnymi między różnymi subskrypcjami. Aby dowiedzieć się więcej, zobacz [Konfigurowanie połączenia między sieciami wirtualnymi](vpn-gateway-howto-vnet-vnet-cli.md). Upewnij się, że dodajesz `-EnableBgp $True` podczas tworzenia połączeń, aby włączyć protokół BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Utwórz nową grupę zasobów

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

Pierwsze polecenie tworzy przestrzeń adresową frontonu i podsieć frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresową dla podsieci zaplecza. Trzecie i czwarte polecenia tworzą podsieć zaplecza i GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Utwórz publiczny adres IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Utwórz bramę sieci VPN przy użyciu numeru AS

Utwórz bramę sieci wirtualnej dla usługi TestVNet2. Należy zastąpić domyślny numer ASN na bramach sieci VPN platformy Azure. Numery ASN dla połączonych sieci wirtualnych muszą być inne, aby można było włączyć routing BGP i transport.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2. Łączenie bram sieci testvnet1 i TestVNet2

W tym kroku utworzysz połączenie od sieci testvnet1 do site5. Aby włączyć protokół BGP dla tego połączenia, należy określić `--enable-bgp` parametr.

W poniższym przykładzie Brama sieci wirtualnej i Brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić cały identyfikator zasobu dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator zasobu VNet1GW 

Pobierz identyfikator zasobu VNet1GW z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Pobierz identyfikator zasobu VNet2GW

Pobierz identyfikator zasobu VNet2GW z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Utwórz połączenia

Utwórz połączenie od sieci testvnet1 do TestVNet2 i połączenie z TestVNet2 do sieci testvnet1. Zastąp identyfikatory subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Włącz protokół BGP dla *obu* połączeń.
> 
> 

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut. Sesja komunikacji równorzędnej BGP zostanie zakończona po zakończeniu połączenia między sieciami wirtualnymi.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu połączenia możesz dodać maszyny wirtualne do sieci wirtualnych. Aby uzyskać instrukcje, zobacz [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).
