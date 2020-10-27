---
title: 'Łączenie sieci lokalnych z siecią wirtualną: sieci VPN typu lokacja-lokacja: interfejs wiersza polecenia'
description: Utwórz połączenie IPsec lokacja-VPN Gateway lokacja z sieci lokalnej do sieci wirtualnej platformy Azure za pośrednictwem publicznego Internetu przy użyciu interfejsu wiersza polecenia.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/23/2020
ms.author: cherylmc
ms.openlocfilehash: 83ae4185d22a6578130ca96c06ac1e5d0c25b375
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541370"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Tworzenie sieci wirtualnej z wykorzystaniem połączenia sieci VPN typu lokacja-lokacja przy użyciu interfejsu wiersza polecenia

Ten artykuł pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia połączenia bramy sieci VPN lokacja-lokacja z Twojej sieci lokalnej do sieci wirtualnej. Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:<br>

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


![Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie.
* Azure Cloud Shell można użyć do uruchomienia poleceń interfejsu wiersza polecenia (instrukcje poniżej). Jeśli jednak wolisz uruchamiać polecenia lokalnie, upewnij się, że zainstalowano najnowszą wersję poleceń interfejsu wiersza polecenia (2,0 lub nowsze). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli). 
 
  [!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="example-values"></a><a name="example"></a>Przykładowe wartości

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Nawiązywanie połączenia z subskrypcją

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, Połącz się z subskrypcją. Jeśli używasz Azure Cloud Shell w przeglądarce, nie musisz nawiązać połączenia z subskrypcją. Nastąpi automatyczne połączenie w Azure Cloud Shell. Jednak przed nawiązaniem połączenia warto sprawdzić, czy jest używana prawidłowa subskrypcja.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a><a name="rg"></a>2. Tworzenie grupy zasobów

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie „TestRG1” w lokalizacji „eastus”. Jeśli masz już grupę zasobów w regionie, w którym chcesz utworzyć swoją sieć wirtualną, możesz jej użyć zamiast tej.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="3-create-a-virtual-network"></a><a name="VNet"></a>3. Tworzenie sieci wirtualnej

Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Podczas tworzenia sieci wirtualnej upewnij się, że określone przestrzenie adresowe nie nakładają się na żadne inne przestrzenie adresowe w obrębie sieci lokalnej.

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie „TestVNet1” i podsieci „Subnet1”.

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## <a name="4-create-the-gateway-subnet"></a>4. <a name="gwsub"></a> Utwórz podsieć bramy


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Użyj polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet), aby utworzyć podsieć bramy.

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="5-create-the-local-network-gateway"></a><a name="localnet"></a>5. Tworzenie bramy sieci lokalnej

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. Nadaj lokacji nazwę, za pomocą której platforma Azure może odwołać się do niej, a następnie określ adres IP lokalnego urządzenia sieci VPN, z którym będzie tworzone połączenie. Określ również prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej. W przypadku zmian w sieci lokalnej prefiksy można łatwo zaktualizować.

Użyj następujących wartości:

* *--Gateway-IP-Address* jest adresem IP lokalnego urządzenia sieci VPN.
* *--local-address-prefixes* to Twoje lokalne przestrzenie adresowe.

Użyj polecenia [az network local-gateway create](/cli/azure/network/local-gateway), aby dodać bramę sieci lokalnej z wieloma prefiksami adresów:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="6-request-a-public-ip-address"></a><a name="PublicIP"></a>6. Zażądaj publicznego adresu IP

Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

Użyj polecenia [az network public-ip create](/cli/azure/network/public-ip), aby zażądać dynamicznego publicznego adresu IP.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="7-create-the-vpn-gateway"></a><a name="CreateGateway"></a>7. Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN sieci wirtualnej. Tworzenie bramy sieci VPN może potrwać 45 minut lub więcej.

Użyj następujących wartości:

* *--Gateway-Type* dla konfiguracji lokacja-lokacja to *VPN* . Typ bramy zawsze zależy od wdrażanej konfiguracji. Aby uzyskać więcej informacji, zobacz [Gateway types](vpn-gateway-about-vpn-gateway-settings.md#gwtype) (Typy bram).
* *Typ--VPN-Type* może być *RouteBased* (określany jako brama dynamiczna w pewnej dokumentacji) lub *PolicyBased* (nazywany bramą statyczną w pewnej dokumentacji). To ustawienie zależy od wymagań urządzenia, z którym nawiązujesz połączenie. Aby uzyskać więcej informacji o typach bram sieci VPN, zobacz [About VPN Gateway configuration settings](vpn-gateway-about-vpn-gateway-settings.md#vpntype) (Informacje o ustawieniach konfiguracji bramy sieci VPN).
* Wybierz jednostkę SKU bramy, która ma być używana. Dla niektórych jednostek SKU istnieją ograniczenia konfiguracji. Aby uzyskać więcej informacji, zobacz [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

Utwórz bramę sieci VPN za pomocą polecenia [az network vnet-gateway create](/cli/azure/network/vnet-gateway). Jeśli to polecenie zostanie uruchomione z parametrem „--no-wait”, nie będą widoczne żadne informacje zwrotne ani dane wyjściowe. Ten parametr umożliwia utworzenie bramy w tle. Utworzenie bramy trwa około 45 minut.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="8-configure-your-vpn-device"></a><a name="VPNDevice"></a>8. Skonfiguruj urządzenie sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne będą:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP swojej bramy sieci wirtualnej, użyj polecenia [az network public-ip list](/cli/azure/network/public-ip). W celu poprawienia czytelności dane wyjściowe są sformatowane do wyświetlania listy publicznych adresów IP w formacie tabeli.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="9-create-the-vpn-connection"></a><a name="CreateConnection"></a>9. Tworzenie połączenia sieci VPN

Utwórz połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej a lokalnym urządzeniem sieci VPN. Zwróć szczególną uwagę na wartość udostępnionego klucza, która musi być zgodna ze skonfigurowaną wartością klucza współużytkowanego dla Twojego urządzenia sieci VPN.

Utwórz połączenie za pomocą polecenia [az network vpn-connection create](/cli/azure/network/vpn-connection).

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Po chwili zostanie nawiązane połączenie.

## <a name="10-verify-the-vpn-connection"></a><a name="toverify"></a>10. Sprawdź połączenie sieci VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Jeśli chcesz użyć innej metody, aby sprawdzić swoje połączenie, zobacz [Weryfikowanie połączenia bramy sieci VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

Ta sekcja zawiera typowe polecenia, które są przydatne przy pracy z konfiguracjami lokacja-lokacja. Aby uzyskać pełną listę poleceń sieciowych interfejsu wiersza polecenia, zobacz [Interfejs wiersza polecenia platformy Azure — sieć](/cli/azure/network).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne).
* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-forced-tunneling-rm.md).
* Aby uzyskać informacje o połączeniach o wysokiej dostępności typu aktywne-aktywne, zobacz [Połączenia obejmujące wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej dostępności](vpn-gateway-highlyavailable.md).
* Aby zapoznać się z listą poleceń interfejsu wiersza polecenia platformy Azure dotyczących sieci, zobacz [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu lokacja-lokacja przy użyciu szablonu Azure Resource Manager, zobacz [Tworzenie połączenia sieci VPN typu lokacja-lokacja](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu sieć wirtualna-sieć wirtualna przy użyciu szablonu Azure Resource Manager, zobacz [wdrażanie replikacji geograficznej HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
