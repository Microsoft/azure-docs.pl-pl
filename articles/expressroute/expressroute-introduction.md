---
title: 'Azure ExpressRoute — omówienie: łączenie za pośrednictwem połączenia prywatnego'
description: W opisie technicznym usługi ExpressRoute omówiono sposób działania połączenia usługi ExpressRoute podczas rozszerzania sieci lokalnej na platformę Azure przez połączenie prywatne.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: 40d84a4196a3cc104a29b4b9511bd627f3f8d40e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89566878"
---
# <a name="what-is-azure-expressroute"></a>Co to jest usługa Azure ExpressRoute?
Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnych na chmurę firmy Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure i Microsoft 365.

Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu połączenia ExpressRoute zapewniają większą niezawodność, większe szybkości, spójne opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez Internet. Informacje dotyczące łączenia sieci z firmą Microsoft przy użyciu usługi ExpressRoute można znaleźć w temacie [Modele połączeń usługi ExpressRoute](expressroute-connectivity-models.md).

![Omówienie połączeń ExpressRoute](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

* Łączność w warstwie 3 między siecią lokalną a usługą Microsoft Cloud za pośrednictwem dostawcy połączenia. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych za pośrednictwem wymiany sieci Ethernet.
* Łączność z usługami w chmurze firmy Microsoft we wszystkich regionach w regionie geopolitycznym.
* Łączność globalna z usługami Microsoft we wszystkich regionach dzięki dodatkowi ExpressRoute Premium.
* Routing dynamiczny między Twoją siecią i siecią firmy Microsoft za pośrednictwem protokołu BGP.
* Wbudowana nadmiarowość w każdej lokalizacji komunikacji równorzędnej w celu uzyskania większej niezawodności.
* Czas sprawnego działania połączenia zgodnie z umową [SLA](https://azure.microsoft.com/support/legal/sla/).
* Obsługa QoS dla programu Skype dla firm.

Więcej informacji znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

## <a name="features"></a>Funkcje

### <a name="layer-3-connectivity"></a>Łączność w warstwie 3
Firma Microsoft używa protokołu BGP, standardowego protokołu routingu dynamicznego, do wymiany tras między siecią lokalną, wystąpieniami na platformie Azure i publicznymi adresami Microsoft. Ustanawiamy wiele sesji BGP z siecią dla różnych profilów ruchu. Więcej szczegółowych informacji można znaleźć w artykule [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (Obwód i domeny routingu usługi ExpressRoute).

### <a name="redundancy"></a>Nadmiarowość
Każdy obwód usługi ExpressRoute składa się z dwóch połączeń z dwoma routerami granicznymi Microsoft Enterprise (MSEE) w [lokalizacji ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-locations#expressroute-locations) od dostawcy połączenia/z granicy sieci. Firma Microsoft wymaga podwójnego połączenia BGP od dostawcy połączenia/brzegu Twojej sieci — po jednym do każdego routera MSEE. Można zdecydować o niewdrażaniu nadmiarowych urządzeń/obwodów sieci Ethernet po swojej stronie. Dostawcy połączenia używają jednak urządzeń nadmiarowych, aby zagwarantować, że połączenia będą przekazywane do firmy Microsoft w sposób nadmiarowy. Konfiguracja łączności nadmiarowej w warstwie 3 jest wymaganiem, które musi być spełnione, aby umowa [SLA](https://azure.microsoft.com/support/legal/sla/) była ważna.

### <a name="connectivity-to-microsoft-cloud-services"></a>Łączność z usługami w chmurze firmy Microsoft
Połączenia ExpressRoute umożliwiają dostęp do następujących usług:
* Usługi Microsoft Azure
* Usługi platformy Microsoft 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Aby uzyskać szczegółową listę usług obsługiwanych za pośrednictwem usługi ExpressRoute, odwiedź stronę [często zadawanych pytań](expressroute-faqs.md) i odpowiedzi na ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Łączność z wszystkimi regionami w regionie geopolitycznym
Z firmą Microsoft możesz się połączyć w jednej z naszych [lokalizacji komunikacji równorzędnej](expressroute-locations.md) i mieć dostęp do regionów w ramach regionu geopolitycznego.

Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute uzyskasz dostęp do wszystkich usług w chmurze Microsoft hostowanych w Europie Północnej i Europie Zachodniej. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute), który zawiera omówienie regionów geopolitycznych, powiązanych regionów chmury Microsoft i odpowiadających im lokalizacji komunikacji równorzędnej usługi ExpressRoute.

### <a name="global-connectivity-with-expressroute-premium"></a>Globalna łączność z usługą ExpressRoute Premium
Możesz włączyć usługę [ExpressRoute Premium](expressroute-faqs.md) , aby zwiększyć łączność w granicach geopolitycznych. Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute będziesz mieć dostęp do wszystkich usług w chmurze Microsoft hostowanych we wszystkich regionach na świecie (z wyjątkiem chmur krajowych). Możesz uzyskać dostęp do usług wdrożonych w Ameryce Południowej lub Australii w taki sam sposób, w jaki uzyskujesz dostęp do regionów Europa Północna i Europa Zachodnia.

### <a name="local-connectivity-with-expressroute-local"></a>Lokalna łączność z usługą ExpressRoute Local
Możesz efektywnie transferować dane, włączając [lokalną jednostkę SKU](expressroute-faqs.md) , jeśli możesz przenieść dane do lokalizacji ExpressRoute w najbliższym regionie świadczenia usługi Azure. W przypadku lokalnego transfer danych jest uwzględniany w opłacie za port ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Łączność między sieciami lokalnymi za pomocą rozwiązania ExpressRoute Global Reach
Za pomocą rozwiązania ExpressRoute Global Reach możesz wymieniać dane między lokacjami lokalnymi, łącząc obwody usługi ExpressRoute. Jeśli na przykład masz prywatne centrum danych w Kalifornii połączone z usługą ExpressRoute w Dolinie Krzemowej i kolejne prywatne centrum danych w Teksasie połączone z usługą ExpressRoute w Dallas, dzięki rozwiązaniu ExpressRoute Global Reach możesz połączyć te prywatne centra danych za pośrednictwem dwóch obwodów usługi ExpressRoute. Ruch sieciowy między centrami danych będzie odbywał się za pośrednictwem sieci firmy Microsoft.

Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Bogaty ekosystem partnerów łączności
Usługa ExpressRoute ma nieustannie rosnący ekosystem dostawców połączeń i partnerów będących integratorami systemów. Najnowsze informacje znajdują się w artykule [Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Łączność z chmurami krajowymi
Firma Microsoft obsługuje izolowane środowiska w chmurze dla określonych regionów geopolitycznych i segmentów klientów. Lista krajowych chmur i dostawców znajduje się w artykule [Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md).

### <a name="expressroute-direct"></a>Usługa ExpressRoute Direct
Usługa ExpressRoute Direct daje klientom możliwość łączenia się bezpośrednio z globalną siecią firmy Microsoft w lokalizacjach komunikacji równorzędnej strategicznie rozmieszczonych na całym świecie. Usługa ExpressRoute Direct zapewnia dwustronną łączność z przepustowością 100 Gb/s, co umożliwia obsługę łączności w trybie aktywny/aktywny na dużą skalę.

Oto kilka najważniejszych funkcji, które zapewnia usługa ExpressRoute Direct:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Fizyczne oddzielenie w przypadku tych branż, które są regulowane i w których łączność musi być dedykowana oraz oddzielona, na przykład w przypadku usług bankowych, rządowych i handlowych
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

Aby uzyskać więcej informacji, zobacz [About ExpressRoute Direct (Usługa ExpressRoute Direct)](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Opcje przepustowości
Możesz zakupić obwody usługi ExpressRoute do szerokiego zakresu przepustowości. Lista obsługiwanych przepustowości znajduje się poniżej. Koniecznie skontaktuj się z dostawcą połączenia, aby określić przepustowości obsługiwane przez niego.

* 50 Mb/s
* 100 Mb/s
* 200 Mb/s
* 500 Mb/s
* 1 Gb/s
* 2 Gb/s
* 5 Gb/s
* 10 Gb/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamiczne skalowanie przepustowości
Możesz zwiększać przepustowość obwodu usługi ExpressRoute (w oparciu o optymalne rozwiązanie) bez konieczności zatrzymywania połączeń. Aby uzyskać więcej informacji, zobacz [Modyfikowanie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Elastyczne modele rozliczeń
Możesz wybrać najlepszy dla siebie model rozliczeń. Wybierz z modeli rozliczeń wymienionych poniżej. Więcej informacji znajduje się w artykule [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md).

* **Nieograniczona ilość danych**. W ramach rozliczeń jest naliczana miesięczna opłata. Transfer danych przychodzących i wychodzących jest bezpłatny.
* **Mierzone dane**. W ramach rozliczeń jest naliczana miesięczna opłata. Transfer danych przychodzących jest bezpłatny. Transfer danych wychodzących jest obciążany opłatą za GB transferu danych. Stawki transferu danych różnią się zależnie od regionu.
* **Dodatek ExpressRoute Premium**. ExpressRoute Premium jest dodatkiem do obwodu usługi ExpressRoute. Dodatek ExpressRoute Premium zapewnia następujące możliwości: 
  * Zwiększone limity tras dla publicznej i prywatnej komunikacji równorzędnej Azure z 4000 do 10 000 tras.
  * Globalna łączność dla usług. Obwód usługi ExpressRoute utworzony w dowolnym regionie (z wyłączeniem chmur krajowych) będzie miał dostęp do zasobów we wszystkich innych regionach na świecie. Na przykład dostęp do sieci wirtualnej utworzonej w Europie Zachodniej można uzyskać za pośrednictwem obwodu usługi ExpressRoute zainicjowanego w Dolinie Krzemowej.
  * Zwiększona liczba łączy sieci wirtualnej na obwód usługi ExpressRoute z 10 do większego limitu, w zależności od przepustowości obwodu.

## <a name="faq"></a>Często zadawane pytania
Często zadawane pytania dotyczące usługi ExpressRoute można znaleźć na stronie [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Zasubskrybuj źródło danych RSS i Wyświetl najnowsze aktualizacje funkcji ExpressRoute na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) .

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [modelach połączeń usługi ExpressRoute](expressroute-connectivity-models.md).
* Więcej informacji na temat połączeń ExpressRoute i domen routingu. Zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Znajdź dostawcę usługi. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md), [NAT](expressroute-nat.md) i [QoS](expressroute-qos.md).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie i modyfikowanie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
