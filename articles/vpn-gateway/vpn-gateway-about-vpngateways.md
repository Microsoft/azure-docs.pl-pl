---
title: Informacje o usłudze Azure VPN Gateway
description: Dowiedz się, co to jest VPN Gateway i jak używać VPN Gateway do nawiązywania połączenia z sieciami wirtualnymi protokołu IPsec typu lokacja-lokacja, Sieć wirtualna-sieć VNet i połączenie sieci VPN punkt-lokacja.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom:
- contperf-fy21q1
- e2e-hybrid
ms.openlocfilehash: f143ed3a8e739f4a79367694e933b28d717230dd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640874"
---
# <a name="what-is-vpn-gateway"></a>Co to jest usługa VPN Gateway?

Brama sieci VPN to specyficzny typ bramy sieci wirtualnej, który służy do wysyłania zaszyfrowanego ruchu sieciowego między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu. Za pomocą bramy sieci VPN można także wysyłać zaszyfrowany ruch sieciowy między sieciami wirtualnymi platformy Azure za pośrednictwem sieci firmy Microsoft. Każda sieć wirtualna może mieć tylko jedną bramę sieci VPN. Możesz jednak utworzyć wiele połączeń do tej samej bramy sieci VPN. W przypadku utworzenia wielu połączeń do tej samej bramy sieci VPN wszystkie tunele VPN współdzielą dostępną przepustowość bramy.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Co to jest brama sieci wirtualnej?

Brama sieci wirtualnej składa się z co najmniej dwóch maszyn wirtualnych wdrożonych w określonej podsieci, która jest tworzona w ramach *podsieci bramy*. Maszyny wirtualne bramy sieci wirtualnej zawierają tabele routingu i uruchamiają konkretne usługi bramy. Te maszyny wirtualne są tworzone podczas tworzenia bramy sieci wirtualnej. Nie można bezpośrednio skonfigurować maszyn wirtualnych, które są częścią bramy sieci wirtualnej.

Podczas konfigurowania bramy sieci wirtualnej należy skonfigurować ustawienie określające typ bramy. Typ bramy określa sposób użycia bramy sieci wirtualnej oraz akcje podejmowane przez bramę. Typ bramy "VPN" określa, że typ tworzonej bramy sieci wirtualnej to "Brama sieci VPN". Odróżnia je od bramy ExpressRoute, która używa innego typu bramy. Sieć wirtualna może mieć dwie bramy sieci wirtualnej; jedna Brama sieci VPN i jedna brama ExpressRoute. Aby uzyskać więcej informacji, zobacz [Gateway types](vpn-gateway-about-vpn-gateway-settings.md#gwtype) (Typy bram).

Tworzenie bramy sieci wirtualnej może potrwać do 45 minut. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy użyciu określonych przez Ciebie ustawień. Po utworzeniu bramy sieci VPN możesz utworzyć połączenie tunelu VPN IPsec/IKE między bramą sieci VPN a inną bramą sieci VPN (sieć wirtualna-sieć wirtualna) lub utworzyć połączenie tunelu VPN IPsec/IKE obejmujące wiele lokalizacji między bramą sieci VPN a lokalnym urządzeniem sieci VPN (lokacja-lokacja). Możesz również utworzyć połączenie sieci VPN typu punkt-lokacja (VPN over OpenVPN, IKEv2 lub SSTP), które umożliwia łączenie się z siecią wirtualną z lokalizacji zdalnej, np. z konferencji lub z domu.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Konfigurowanie bramy VPN Gateway

Połączenie bramy sieci VPN bazuje na wielu zasobach konfigurowanych przy użyciu konkretnych ustawień. Większość zasobów można skonfigurować osobno, choć niektóre z nich należy skonfigurować w określonej kolejności.

### <a name="design"></a><a name="diagrams"></a>Projekt

Dostępne są różne konfiguracje połączeń bramy sieci VPN. Należy określić, która konfiguracja najlepiej odpowiada Twoim wymaganiom. Na przykład połączenie punkt-lokacja, lokacja-lokacja i współistniejące połączenia ExpressRoute/lokacja-lokacja mają różne instrukcje i wymagania dotyczące konfiguracji. Aby uzyskać informacje na temat projektowania i wyświetlania diagramów topologii połączeń, zobacz [Design](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Tabela planowania

W poniższej tabeli znajdują się informacje pomocne podczas podejmowania decyzji co do najlepszej opcji łączności dla rozwiązania.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Ustawienia

Ustawienia wybrane dla każdego zasobu mają kluczowe znaczenie dla utworzenia prawidłowego połączenia. Aby uzyskać informacje na temat poszczególnych zasobów i ustawień dla bramy sieci VPN, zobacz [Ustawienia bramy sieci VPN — informacje](vpn-gateway-about-vpn-gateway-settings.md). Ten artykuł zawiera informacje ułatwiające poznanie typów bram, jednostek SKU bram typów sieci VPN, typów połączeń, podsieci bram, bram sieci lokalnych i innych ustawień zasobów, które warto wziąć pod uwagę.

### <a name="deployment-tools"></a><a name="tools"></a>Narzędzia wdrażania

Możesz rozpocząć tworzenie i konfigurowanie zasobów za pomocą jednego narzędzia konfiguracji, takiego jak witryna Azure Portal. Później możesz zdecydować się zmienić narzędzie na inne, np. program PowerShell, w celu skonfigurowania dodatkowych zasobów lub zmodyfikowania istniejących zasobów, jeśli jest to wymagane. Obecnie nie wszystkie zasoby i ustawienia zasobów można skonfigurować w witrynie Azure Portal. Instrukcje w artykułach dotyczących poszczególnych topologii połączeń określają, kiedy potrzebne jest konkretne narzędzie konfiguracji.

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy

Podczas tworzenia bramy sieci wirtualnej określa się jednostkę SKU bramy do użycia. Wybierz jednostkę SKU spełniającą Twoje wymagania na podstawie typów obciążeń, przepustowości, funkcji i umów SLA.

* Aby uzyskać więcej informacji o jednostkach SKU bramy, w tym obsługiwanych funkcjach, środowisku produkcyjnym i procesie tworzenia i konfigurowania, zapoznaj się z artykułem [VPN Gateway Settings-Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) .
* Aby uzyskać informacje o starszych jednostkach SKU, zobacz [Praca ze starszymi wersjami SKU](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Jednostki SKU bramy według tunelowania, połączenia i przepływności

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Strefy dostępności

Bramy sieci VPN można wdrożyć w Strefy dostępności platformy Azure. Zapewni to elastyczność, skalowalność i większą dostępność bram sieci wirtualnej. Wdrażanie bram w strefach dostępności platformy Azure fizycznie i logicznie dzieli bramy w danym regionie, chroniąc jednocześnie lokalną łączność sieci z platformą Azure przed błędami na poziomie strefy. Zobacz [temat strefy — nadmiarowe bramy sieci wirtualnej w strefy dostępności platformy Azure](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Ceny

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Więcej informacji o jednostkach SKU bramy dla usługi VPN Gateway zawiera artykuł [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

Aby zapoznać się z często zadawanymi pytaniami dotyczącymi bramy sieci VPN, zobacz [Brama VPN Gateway — często zadawane pytania](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Zasubskrybuj źródło danych RSS i zapoznaj się z najnowszymi aktualizacjami funkcji VPN Gateway na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) .

## <a name="next-steps"></a>Następne kroki

- Więcej informacji można znaleźć w temacie [Brama VPN Gateway — często zadawane pytania](vpn-gateway-vpn-faq.md).
- Wyświetl [limity usług i subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
