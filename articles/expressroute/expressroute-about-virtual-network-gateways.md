---
title: Informacje o bramach sieci wirtualnej ExpressRoute — Azure | Microsoft Docs
description: Informacje o bramach sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU i typach bramy.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: b721a9b8d8bdff3f3aaf05f15857c00347e7abb4
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202382"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Informacje o bramach sieci wirtualnej ExpressRoute

Aby połączyć sieć wirtualną platformy Azure i sieć lokalną za pośrednictwem usługi ExpressRoute, najpierw należy utworzyć bramę sieci wirtualnej. Brama sieci wirtualnej służy do dwóch celów: trasy IP programu Exchange między sieciami i kierowanie ruchu sieciowego. W tym artykule objaśniono typy bram, jednostki SKU bramy i szacowane wydajność według jednostki SKU. W tym artykule opisano również ExpressRoute [FastPath](#fastpath), funkcję, która umożliwia ruch sieciowy z sieci lokalnej w celu obejścia bramy sieci wirtualnej w celu zwiększenia wydajności.

## <a name="gateway-types"></a>Typy bram

Podczas tworzenia bramy sieci wirtualnej należy określić kilka ustawień. Jedno z wymaganych ustawień "-Gatewaytype" określa, czy brama jest używana dla ruchu ExpressRoute, czy sieci VPN. Istnieją dwa typy bram:

* **Sieć VPN** — aby wysłać zaszyfrowany ruch przez publiczny Internet, należy użyć typu bramy "VPN". Jest to również nazywane bramą sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

* **ExpressRoute** — aby wysłać ruch sieciowy w ramach połączenia prywatnego, należy użyć typu bramy "ExpressRoute". Jest to również nazywane bramą ExpressRoute i jest typem bramy używanym podczas konfigurowania ExpressRoute.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić bramę do bardziej zaawansowanej jednostki SKU bramy, w większości przypadków możesz użyć polecenia cmdlet programu PowerShell "Zmień rozmiar AzVirtualNetworkGateway". Będzie to działało w przypadku uaktualnień do wersji Standard i HighPerformance SKU. Jednak w celu uaktualnienia do jednostki SKU UltraPerformance należy ponownie utworzyć bramę. Ponowne utworzenie bramy powoduje przestoje.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Szacowane wyniki według jednostki SKU bramy
W poniższej tabeli przedstawiono typy bram i szacowane wydajności. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak opóźnienie end-to-end i liczba przepływów ruchu, które są otwierane przez aplikację. Liczby w tabeli przedstawiają górny limit, który aplikacja może teoretycznie osiągnąć w idealnym środowisku.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy ExpressRoute należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez maszyny wirtualne i usługi bramy sieci wirtualnej. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy użyciu wymaganych ustawień bramy ExpressRoute. Nigdy nie należy wdrażać żadnych innych (na przykład dodatkowych maszyn wirtualnych) do podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet", aby działała prawidłowo. Nazwa podsieci bramy "GatewaySubnet" pozwala platformie Azure wiedzieć, że jest to podsieć służąca do wdrażania maszyn wirtualnych i usług bramy sieci wirtualnej.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzieleni do maszyn wirtualnych bramy i usług bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. 

Podczas planowania rozmiaru podsieci bramy zapoznaj się z dokumentacją dotyczącą konfiguracji, która ma zostać utworzona. Na przykład Konfiguracja współdzielenia ExpressRoute/VPN Gateway wymaga większej podsieci bramy niż większość innych konfiguracji. Ponadto warto upewnić się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje dodatkowe. Chociaż można utworzyć małą podsieć bramy (/29), zalecamy utworzenie podsieci bramy/27 lub większej (/27,/26 itp.), jeśli masz dostępną przestrzeń adresową. Obejmuje to większość konfiguracji.

Poniższy przykład Menedżer zasobów PowerShell przedstawia podsieć bramy o nazwie GatewaySubnet. W notacji CIDR można zobaczyć wartość/27, która pozwala na wystarczającą liczbę adresów IP dla większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Strefa — nadmiarowe jednostki SKU bramy

Bramy ExpressRoute można także wdrożyć w Strefy dostępności platformy Azure. Fizycznie i logicznie oddziela je do różnych Strefy dostępności, chroniąc lokalną łączność sieciową do platformy Azure przed awariami na poziomie strefy.

![Strefa nadmiarowa ExpressRoute Gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Bramy strefowo nadmiarowe korzystają z określonych nowych jednostek SKU bramy dla bramy ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nowe jednostki SKU bramy obsługują również inne opcje wdrażania, które najlepiej pasują do Twoich potrzeb. Podczas tworzenia bramy sieci wirtualnej przy użyciu nowych jednostek SKU bramy istnieje również możliwość wdrożenia bramy w określonej strefie. Jest to nazywane bramą strefową. Po wdrożeniu bramy zona wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

Brama sieci wirtualnej ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath zaprojektowano w celu poprawienia wydajności ścieżki danych między siecią lokalną i siecią wirtualną. Po włączeniu FastPath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę.

Aby uzyskać więcej informacji na temat FastPath, w tym ograniczeń i wymagań, zobacz [Informacje o FastPath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>Interfejsy API REST i polecenia cmdlet programu PowerShell
Dodatkowe zasoby techniczne i wymagania dotyczące składni w przypadku używania interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej można znaleźć na następujących stronach:

| **Klasyczny** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0#azure) |[Program PowerShell](/powershell/module/az.network#networking) |
| [Interfejs API REST](/previous-versions/azure/reference/jj154113(v=azure.100)) |[Interfejs API REST](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o konfiguracjach dostępnych połączeń, zobacz [ExpressRoute Overview (przegląd](expressroute-introduction.md)).

Aby uzyskać więcej informacji na temat tworzenia bram ExpressRoute, zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Aby uzyskać więcej informacji o konfigurowaniu bram strefowo nadmiarowych, zobacz [Tworzenie strefy nadmiarowej bramy sieci wirtualnej](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Aby uzyskać więcej informacji na temat FastPath, zobacz [Informacje o FastPath](about-fastpath.md).