---
title: 'Azure ExpressRoute — omówienie: łączenie za pośrednictwem połączenia prywatnego'
description: W opisie technicznym usługi ExpressRoute omówiono sposób działania połączenia usługi ExpressRoute podczas rozszerzania sieci lokalnej na platformę Azure przez połączenie prywatne.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206955"
---
# <a name="what-is-azure-expressroute"></a>Co to jest usługa Azure ExpressRoute?
Usługa ExpressRoute umożliwia poszerzanie sieci lokalnych w chmurze firmy Microsoft przez połączenie prywatne z pomocą dostawcy łączności. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure i Microsoft 365.

Łączność może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub wirtualnego połączenia krzyżowego za pośrednictwem dostawcy łączności w ramach funkcji wspólnej lokalizacji. Połączenia ExpressRoute nie przechodzą przez publiczny Internet. Dzięki temu połączenia ExpressRoute zapewniają większą niezawodność, większe szybkości, spójne opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez Internet. Informacje dotyczące łączenia sieci z firmą Microsoft przy użyciu usługi ExpressRoute można znaleźć w temacie [Modele połączeń usługi ExpressRoute](expressroute-connectivity-models.md).

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
Każdy obwód usługi ExpressRoute składa się z dwóch połączeń z dwoma routerami granicznymi Microsoft Enterprise (MSEE) w [lokalizacji ExpressRoute](./expressroute-locations.md#expressroute-locations) od dostawcy połączenia/z granicy sieci. Firma Microsoft wymaga podwójnego połączenia BGP od dostawcy połączenia/brzegu Twojej sieci — po jednym do każdego routera MSEE. Można zdecydować o niewdrażaniu nadmiarowych urządzeń/obwodów sieci Ethernet po swojej stronie. Dostawcy połączenia używają jednak urządzeń nadmiarowych, aby zagwarantować, że połączenia będą przekazywane do firmy Microsoft w sposób nadmiarowy. Konfiguracja łączności nadmiarowej w warstwie 3 jest wymaganiem, które musi być spełnione, aby umowa [SLA](https://azure.microsoft.com/support/legal/sla/) była ważna.

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

Na przykład, jeśli łączysz się z firmą Microsoft w Amsterdamie za poorednictwem ExpressRoute. Będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft obsługiwanych w Europie Północnej i Europa Zachodnia. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute), który zawiera omówienie regionów geopolitycznych, powiązanych regionów chmury Microsoft i odpowiadających im lokalizacji komunikacji równorzędnej usługi ExpressRoute.

### <a name="global-connectivity-with-expressroute-premium"></a>Globalna łączność z usługą ExpressRoute Premium
Możesz włączyć usługę [ExpressRoute Premium](expressroute-faqs.md) , aby zwiększyć łączność w granicach geopolitycznych. Na przykład jeśli łączysz się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute, będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft hostowanych we wszystkich regionach na całym świecie. Możesz również uzyskać dostęp do usług wdrożonych w Ameryce Południowej lub Australii w taki sam sposób, jak w regionach Europy Północnej i Europa Zachodnia. Chmury narodowe są wykluczone.

### <a name="local-connectivity-with-expressroute-local"></a>Lokalna łączność z usługą ExpressRoute Local
Aby efektywnie przesyłać dane, należy włączyć [lokalną jednostkę SKU](expressroute-faqs.md). Za pomocą lokalnej jednostki SKU można przenieść dane do lokalizacji ExpressRoute w najbliższym regionie platformy Azure. W przypadku lokalnego transfer danych jest uwzględniany w opłacie za port ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Łączność między sieciami lokalnymi za pomocą rozwiązania ExpressRoute Global Reach
Za pomocą rozwiązania ExpressRoute Global Reach możesz wymieniać dane między lokacjami lokalnymi, łącząc obwody usługi ExpressRoute. Na przykład jeśli masz prywatne centrum danych w Kalifornii połączone z obwodem usługi ExpressRoute w Dolina krzemu i innym prywatnym centrum danych w sieci Texas Instruments podłączonym do obwodu usługi ExpressRoute w Dallas. Dzięki ExpressRoute Global Reach można połączyć prywatne centra danych ze sobą za pomocą tych dwóch obwodów usługi ExpressRoute. Ruch sieciowy między centrami danych będzie odbywał się za pośrednictwem sieci firmy Microsoft.

Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Bogaty ekosystem partnerów łączności
Usługa ExpressRoute ma nieustannie rosnący ekosystem dostawców połączeń i partnerów będących integratorami systemów. Najnowsze informacje znajdują się w artykule [Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Łączność z chmurami krajowymi
Firma Microsoft obsługuje izolowane środowiska w chmurze dla określonych regionów geopolitycznych i segmentów klientów. Lista krajowych chmur i dostawców znajduje się w artykule [Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md).

### <a name="expressroute-direct"></a>Usługa ExpressRoute Direct
Usługa ExpressRoute Direct daje klientom możliwość łączenia się bezpośrednio z globalną siecią firmy Microsoft w lokalizacjach komunikacji równorzędnej strategicznie rozmieszczonych na całym świecie. Funkcja ExpressRoute Direct zapewnia dwie połączenia z 100 Gb/s, które obsługują łączność aktywną/aktywną w dużej skali.

Najważniejsze funkcje dostarczane przez program ExpressRoute Direct obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Fizyczne oddzielenie w przypadku tych branż, które są regulowane i w których łączność musi być dedykowana oraz oddzielona, na przykład w przypadku usług bankowych, rządowych i handlowych
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

Aby uzyskać więcej informacji, zobacz [About ExpressRoute Direct (Usługa ExpressRoute Direct)](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Opcje przepustowości
Możesz zakupić obwody usługi ExpressRoute do szerokiego zakresu przepustowości. Obsługiwane przepustowość są wyświetlane jako obserwowane. Koniecznie skontaktuj się z dostawcą połączenia, aby określić przepustowości obsługiwane przez niego.

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
Możesz wybrać najlepszy dla siebie model rozliczeń. Wybierz między modelami rozliczeń wymienionymi jako obserwowane. Więcej informacji znajduje się w artykule [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md).

* **Nieograniczona ilość danych**. W ramach rozliczeń jest naliczana miesięczna opłata. Transfer danych przychodzących i wychodzących jest bezpłatny.
* **Mierzone dane**. W ramach rozliczeń jest naliczana miesięczna opłata. Transfer danych przychodzących jest bezpłatny. Transfer danych wychodzących jest obciążany opłatą za GB transferu danych. Stawki transferu danych różnią się zależnie od regionu.
* **Dodatek ExpressRoute Premium**. ExpressRoute Premium jest dodatkiem do obwodu usługi ExpressRoute. Dodatek ExpressRoute Premium zapewnia następujące możliwości: 
  * Zwiększone limity tras dla publicznej i prywatnej komunikacji równorzędnej Azure z 4000 do 10 000 tras.
  * Globalna łączność dla usług. Obwód ExpressRoute utworzony w dowolnym regionie (z wyjątkiem chmur krajowych) będzie miał dostęp do zasobów w każdym innym regionie na świecie. Na przykład dostęp do sieci wirtualnej utworzonej w Europie Zachodniej można uzyskać za pośrednictwem obwodu usługi ExpressRoute zainicjowanego w Dolinie Krzemowej.
  * Zwiększona liczba łączy sieci wirtualnej na obwód usługi ExpressRoute z 10 do większego limitu, w zależności od przepustowości obwodu.

## <a name="faq"></a>Często zadawane pytania
Często zadawane pytania dotyczące usługi ExpressRoute można znaleźć na stronie [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Zasubskrybuj źródło danych RSS i Wyświetl najnowsze aktualizacje funkcji ExpressRoute na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) .

## <a name="next-steps"></a>Następne kroki
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Dowiedz się więcej o [modelach połączeń usługi ExpressRoute](expressroute-connectivity-models.md).
* Znajdź dostawcę usługi. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).