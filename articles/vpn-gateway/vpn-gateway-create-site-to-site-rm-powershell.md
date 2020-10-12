---
title: 'Łączenie sieci lokalnej z siecią wirtualną platformy Azure: sieci VPN typu lokacja-lokacja: PowerShell'
description: Utwórz połączenie IPsec lokacja-VPN Gateway lokacja z sieci lokalnej do sieci wirtualnej platformy Azure za pośrednictwem publicznego Internetu przy użyciu programu PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 759436477563065d9b03bffa314a4a843ec694b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435953"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell

Ten artykuł pokazuje, jak używać programu PowerShell do tworzenia połączenia bramy sieci VPN lokacja-lokacja z Twojej sieci lokalnej do sieci wirtualnej. Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

![Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Przykładowe wartości

W przykładach w tym artykule są stosowane następujące wartości. Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. Utwórz sieć wirtualną i podsieć bramy

Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją. Podczas tworzenia sieci wirtualnej upewnij się, że określone przestrzenie adresowe nie nakładają się na żadne inne przestrzenie adresowe w obrębie sieci lokalnej. 

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

### <a name="about-the-gateway-subnet"></a>Informacje o podsieci bramy

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>Tworzenie sieci wirtualnej i podsieci bramy

Ten przykład tworzy sieć wirtualną i podsieć bramy. Jeśli masz już sieć wirtualną, do której potrzebujesz dodać podsieć bramy, zobacz [Aby dodać podsieć bramy do utworzonej wcześniej sieci wirtualnej](#gatewaysubnet).

Utwórz grupę zasobów:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Utwórz swoją sieć wirtualną.

1. Ustaw zmienne.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Utwórz sieć wirtualną.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Aby dodać podsieć bramy do utworzonej wcześniej sieci wirtualnej

Wykonaj kroki znajdujące się w tej sekcji, jeśli masz już sieć wirtualną, ale konieczne jest dodanie podsieci bramy.

1. Ustaw zmienne.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Utwórz podsieć bramy.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Ustaw konfigurację.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a> Utwórz bramę sieci lokalnej

Brama sieci lokalnej (LNG) zazwyczaj odnosi się do lokalizacji lokalnej. Nie jest taka sama jak Brama sieci wirtualnej. Nadaj lokacji nazwę, za pomocą której platforma Azure może odwołać się do niej, a następnie określ adres IP lokalnego urządzenia sieci VPN, z którym będzie tworzone połączenie. Określ również prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej. W przypadku zmian w sieci lokalnej prefiksy można łatwo zaktualizować.

Użyj następujących wartości:

* *GatewayIPAddress* to adres IP lokalnego urządzenia sieci VPN.
* *AddressPrefix* oznacza lokalną przestrzeń adresową.

Aby dodać bramę sieci lokalnej z pojedynczym prefiksem adresu:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Aby dodać bramę sieci lokalnej z wieloma prefiksami adresów:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej:

Zdarza się, że prefiksy bramy sieci lokalnej są zmieniane. Kroki, które należy wykonać w celu zmodyfikowania prefiksów adresów IP, zależą od tego, czy utworzono połączenie bramy sieci VPN. Zapoznaj się z sekcją [Modyfikowanie prefiksów adresów IP bramy sieci lokalnej](#modify) tego artykułu.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. Zażądaj publicznego adresu IP

Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. 

Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

Prześlij żądanie przydzielenia publicznego adresu IP, który zostanie przypisany do Twojej bramy sieci VPN sieci wirtualnej.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. Utwórz konfigurację adresowania IP bramy

Konfiguracja bramy definiuje podsieć ("GatewaySubnet") i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie własnej konfiguracji bramy:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN sieci wirtualnej.

Użyj następujących wartości:

* Wartość *-GatewayType* dla konfiguracji lokacja-lokacja to *Vpn*. Typ bramy zawsze zależy od wdrażanej konfiguracji. Na przykład inne konfiguracje bramy mogą wymagać zastosowania wartości -GatewayType ExpressRoute.
* Dla pozycji *-VpnType* określającej typ sieci VPN można wybrać opcję *RouteBased* (oparta na trasach; w dokumentacji używa się czasem określenia „brama dynamiczna”) lub *PolicyBased* (oparta na zasadach; w dokumentacji używa się czasem określenia „brama statyczna”). Więcej informacji o typach bram sieci VPN można znaleźć w artykule [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md).
* Wybierz jednostkę SKU bramy, która ma być używana. Dla niektórych jednostek SKU istnieją ograniczenia konfiguracji. Aby uzyskać więcej informacji, zobacz [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy). Jeśli podczas tworzenia bramy sieci VPN wystąpi błąd dotyczący opcji -GatewaySku, sprawdź, czy zainstalowano najnowszą wersję poleceń cmdlet programu PowerShell.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

Po uruchomieniu tego polecenia konfigurowanie bramy może potrwać do 45 minut.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. Skonfiguruj urządzenie sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne są następujące elementy:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci wirtualnej przy użyciu programu PowerShell, Skorzystaj z następującego przykładu. W tym przykładzie VNet1GWPIP jest nazwą zasobu publicznego adresu IP utworzonego we wcześniejszym kroku.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. Tworzenie połączenia sieci VPN

Następnie należy utworzyć połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej i urządzeniem sieci VPN. Przedstawione na nich wartości należy zastąpić własnymi. Klucz współużytkowany musi odpowiadać wartości użytej podczas konfiguracji urządzenia sieci VPN. Należy pamiętać, że dla połączenia typu lokacja-lokacja wartość parametru „-ConnectionType” to **IPsec**.

1. Ustaw zmienne.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Utwórz połączenie.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

Po chwili zostanie nawiązane połączenie.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. Sprawdź połączenie sieci VPN

Istnieje kilka różnych sposobów sprawdzenia połączenia sieci VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej

Jeśli prefiksy adresów IP, które mają być kierowane do lokalizacji lokalnej, ulegną zmianie, można zmodyfikować bramę sieci lokalnej. Poniżej przedstawiono dwa zestawy instrukcji. Należy wybrać odpowiednie instrukcje w zależności od tego, czy utworzono już połączenie bramy sieci VPN. Korzystając z tych przykładów, zmodyfikuj wartości tak, aby były zgodne ze środowiskiem.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Aby zmodyfikować adres IP bramy dla bramy sieci lokalnej

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>Aby usunąć połączenie bramy

Jeśli nie znasz nazwy połączenia, możesz je znaleźć przy użyciu polecenia cmdlet "Get-AzVirtualNetworkGatewayConnection".

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>Następne kroki

*  Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne).
* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu lokacja-lokacja przy użyciu szablonu Azure Resource Manager, zobacz [Tworzenie połączenia sieci VPN typu lokacja-lokacja](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu sieć wirtualna-sieć wirtualna przy użyciu szablonu Azure Resource Manager, zobacz [wdrażanie replikacji geograficznej HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
