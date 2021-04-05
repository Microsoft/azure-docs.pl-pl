---
title: 'Azure ExpressRoute: przenoszenie publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft'
description: W tym artykule przedstawiono procedurę przenoszenia publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft w witrynie ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206292"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Move a public peering to Microsoft peering (Przechodzenie z publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft)

Ten artykuł pomaga w przenoszeniu konfiguracji publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft bez przestojów. Usługa ExpressRoute obsługuje komunikację równorzędną firmy Microsoft z filtrami tras na potrzeby usług typu PaaS na platformie Azure, takich jak Azure Storage i Azure SQL Database. Teraz potrzebujesz tylko jednej domeny routingu, aby mieć dostęp do usług PaaS i SaaS firmy Microsoft. Filtry tras umożliwiają selektywne anonsowanie prefiksów usług PaaS dla regionów świadczenia usługi Azure, z których chcesz korzystać.

Publiczna Komunikacja równorzędna Azure ma 1 adres IP translatora adresów sieciowych skojarzony z każdą sesją BGP. Komunikacja równorzędna firmy Microsoft umożliwia skonfigurowanie własnych alokacji NAT, a także Używanie filtrów tras dla selektywnych anonsów prefiksów. Publiczna Komunikacja równorzędna to jednokierunkowa usługa, za pomocą której połączenie jest zawsze inicjowane z sieci WAN do Microsoft Azure usług. Usługi Microsoft Azure nie będą mogły inicjować połączeń do sieci za poorednictwem tej domeny routingu.

Po włączeniu publicznej komunikacji równorzędnej można nawiązać połączenie ze wszystkimi usługami platformy Azure. Nie zezwalamy na selektywne Wybieranie usług, do których anonsuje trasy. Podczas gdy Komunikacja równorzędna firmy Microsoft jest łącznością dwukierunkową, w której można inicjować połączenie z usługi Microsoft Azure Service wraz z siecią WAN. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [obwody usługi ExpressRoute i domeny routingu](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Aby nawiązać połączenie z usługą komunikacji równorzędnej firmy Microsoft, należy skonfigurować translator adresów sieciowych i zarządzać nim. Dostawca połączenia może skonfigurować i zarządzać translatorem adresów sieciowych jako usługą zarządzaną. Jeśli planujesz uzyskiwanie dostępu do usług Azure PaaS i Azure SaaS na potrzeby komunikacji równorzędnej firmy Microsoft, ważne jest, aby poprawnie zmienić rozmiar puli adresów IP NAT. Aby uzyskać więcej informacji na temat translatora adresów sieciowych dla usługi ExpressRoute, zobacz [wymagania NAT dotyczące komunikacji równorzędnej firmy Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Po nawiązaniu połączenia z firmą Microsoft za pomocą usługi Azure ExpressRoute (komunikacji równorzędnej firmy Microsoft) masz wiele linków do firmy Microsoft. Jedno połączenie to istniejące połączenie internetowe, a drugie to usługa ExpressRoute. Część ruchu do firmy Microsoft może wychodzić przez Internet ale wracać przez usługę ExpressRoute lub odwrotnie.

![Łączność dwukierunkowa](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Pula adresów IP translatora adresów sieciowych anonsowana do firmy Microsoft nie może być anonsowana do Internetu. Spowodowałoby to przerwanie łączności z innymi usługami firmy Microsoft.

Zapoznaj się z [routingiem asymetrycznym z wieloma ścieżkami sieciowymi](./expressroute-asymmetric-routing.md) , aby zapamiętać Routing asymetryczny przed skonfigurowaniem komunikacji równorzędnej firmy Microsoft.

* Jeśli używasz publicznej komunikacji równorzędnej i masz obecnie reguły sieci IP dla publicznych adresów IP, które są używane do uzyskiwania dostępu do [usługi Azure Storage](../storage/common/storage-network-security.md) lub [Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md), musisz upewnić się, że pula adresów IP translatora adresów sieciowych skonfigurowana za pomocą komunikacji równorzędnej firmy Microsoft jest uwzględniona na liście publicznych adresów IP konta usługi Azure Storage lub konta usługi Azure SQL.<br>
* Aby przejść do komunikacji równorzędnej firmy Microsoft bez przestojów, wykonaj kroki opisane w tym artykule w kolejności, w jakiej zostały przedstawione.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Utwórz komunikację równorzędną firmy Microsoft

Jeśli nie utworzono komunikacji równorzędnej firmy Microsoft, użyj dowolnego z poniższych artykułów, aby utworzyć komunikację równorzędną firmy Microsoft. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć komunikację równorzędną firmy Microsoft dla obwodu.

Jeśli warstwa 3 jest zarządzana przez następujące informacje są wymagane przed kontynuowaniem:

* Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.<br>
* Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.<br>
* Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. W przypadku łączy podstawowych i pomocniczych należy użyć tego samego identyfikatora sieci VLAN.<br>
* Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.<br>
* Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.<br>
* Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.

* **Opcjonalne** — ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić numer AS, do którego są rejestrowane.<br>
* **Opcjonalnie** — skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

Szczegółowe instrukcje dotyczące włączania komunikacji równorzędnej firmy Microsoft można znaleźć w następujących artykułach:

* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure PowerShell](expressroute-howto-routing-arm.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Sprawdź poprawność komunikacji równorzędnej firmy Microsoft

Upewnij się, że Komunikacja równorzędna firmy Microsoft jest włączona, a anonsowane publiczne prefiksy są w skonfigurowanym stanie.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Skonfiguruj i Dołącz filtr tras do obwodu

Domyślnie Nowa Komunikacja równorzędna firmy Microsoft nie anonsuje żadnych prefiksów do momentu dołączenia do obwodu filtru tras. Podczas tworzenia reguły filtru trasy można określić listę społeczności usługi dla regionów platformy Azure, które mają być używane dla usług Azure PaaS Services. Zapewnia to elastyczność filtrowania tras zgodnie z wymaganiami, jak pokazano na poniższym zrzucie ekranu:

![Scal publiczną komunikację równorzędną](./media/how-to-move-peering/routefilter.jpg)

Skonfiguruj filtry tras przy użyciu dowolnego z następujących artykułów:

* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal](how-to-routefilter-portal.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Usuń publiczną komunikację równorzędną

Po sprawdzeniu, czy skonfigurowano komunikację równorzędną firmy Microsoft i prefiksy, które mają być używane, są prawidłowo anonsowane w komunikacji równorzędnej firmy Microsoft, można następnie usunąć publiczną komunikację równorzędną. Aby usunąć publiczną komunikację równorzędną, należy użyć dowolnego z następujących artykułów:

* [Usuwanie publicznej komunikacji równorzędnej Azure przy użyciu Azure PowerShell](about-public-peering.md#powershell)
* [Usuwanie publicznej komunikacji równorzędnej Azure przy użyciu interfejsu wiersza polecenia](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Wyświetlanie komunikacji równorzędnej
  
Można wyświetlić listę wszystkich obwodów usługi ExpressRoute i komunikacji równorzędnej w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie szczegółów komunikacji równorzędnej firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.