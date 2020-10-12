---
title: Sposób wybierania odpowiedniej oferty przepływności w Azure Cosmos DB
description: Dowiedz się, w jaki sposób wybrać między standardową (ręczną) i elastyczną skalowalną przepływność dla obciążenia.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605211"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Jak wybrać między standardowym (ręcznym) i automatycznym skalowaniem przepływności 

Azure Cosmos DB obsługuje dwa typy lub oferty z zainicjowaną przepływność: standardowe (ręczne) i automatyczne skalowanie. Oba typy przepływności są odpowiednie dla obciążeń o kluczowym znaczeniu, które wymagają wysokiej wydajności i skali, i są obsługiwane przez te same Azure Cosmos DB umowy SLA na podstawie przepływności, dostępności, opóźnienia i spójności.

W tym artykule opisano sposób wybierania przepustowości standardowej (ręcznej) i automatycznego skalowania dla obciążenia. 

## <a name="overview-of-provisioned-throughput-types"></a>Omówienie typów przepływności aprowizacji
Przed przystąpieniem do różnic między standardowym (ręcznym) i automatycznym skalowaniem należy najpierw zrozumieć, w jaki sposób zainicjowana przepływność działa w Azure Cosmos DB. 

W przypadku korzystania z zainicjowanej przepływności należy ustawić przepływność, mierzoną w jednostkach żądań na sekundę (RU/s) wymagana dla danego obciążenia. Usługa obsługuje wymagania dotyczące wydajności wymagane do obsługi wymagań dotyczących przepływności. Operacje bazy danych dotyczące usługi, takie jak operacje odczytu, zapisu i zapytania, wykorzystują pewną ilość jednostek żądania (jednostek ru). Dowiedz się więcej o [jednostkach żądania](request-units.md).

W poniższej tabeli przedstawiono porównanie wysokiego poziomu między standardowym (ręcznym) i automatycznym skalowaniem.

|Opis|Standard (ręczny)|Automatyczne skalowanie|
|-------------|------|-------|
|Najlepiej dopasowane do|Obciążenia ze stałym lub przewidywalnym ruchem|Obciążenia ze zmiennym lub nieprzewidywalnym ruchem. Zobacz [przypadki użycia funkcji automatycznego skalowania](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Jak to działa|Należy zainicjować określoną liczbę jednostek RU/s `T` , która jest statyczna w czasie, chyba że użytkownik zmieni je ręcznie. W każdej sekundzie można użyć maksymalnie `T` ru/s przepływności. <br/><br/>Na przykład jeśli ustawisz standardowy (ręczny) 400 RU/s, przepływność będzie pozostawać o 400 RU/s.|Należy ustawić najwyższy lub maksymalny RU/s, `Tmax` których system nie powinien przekraczać. System automatycznie skaluje przepływność w `T` taki sposób, że `0.1* Tmax <= T <= Tmax` . <br/><br/>Jeśli na przykład ustawisz opcję maksymalne skalowanie RU/s z 4000 RU/s, system będzie skalowany między 400 4000 RU/s.|
|Zastosowanie|Chcesz ręcznie zarządzać wydajnością przepływności (RU/s) i samodzielnie skalować ją.<br/><br/>Masz duże, spójne użycie aprowizacji RU/s. Z wszystkich godzin w miesiącu, jeśli ustawisz obsługę RU/s `T` i użyjesz pełnej kwoty przez 66% godzin lub więcej, Szacowana zostanie oszczędność przy użyciu standardowej (ręcznej) aprowizacji jednostek ru/s.<br/><br/>Jest to oparte na porównaniu między ustawieniem `T` w standardowym (ręcznym) i tej samej wielkości `Tmax` w automatycznym skalowaniu. |Chcesz, aby Azure Cosmos DB zarządzać wydajnością przepływności (RU/s) i skalować ją na podstawie użycia.<br/><br/>Istnieje użycie RU/s, które jest zmienne lub trudne do przewidywania. Dla wszystkich godzin w miesiącu, jeśli ustawisz automatyczne skalowanie maksymalne RU/s `Tmax` i użyjesz całkowitej kwoty `Tmax` przez 66% godzin lub mniej, szacowane zostanie zapisywanie przy użyciu funkcji automatycznego skalowania.<br/><br/>Jest to oparte na porównaniu między ustawieniem automatycznego skalowania `Tmax` i tą samą ilością `T` w standardowym (ręcznym) przepływności.|
|Model rozliczania|Opłaty są naliczane za godzinę, dla których Zainicjowano obsługę RU, niezależnie od liczby użytych jednostek ru.<br/><br/>Przykład: <li>Inicjowanie obsługi administracyjnej 400 RU/s</li><li>Godzina 1: brak żądań</li><li>Godzina 2:400 RU/s żądań</li><br/><br/>W przypadku godzin 1 i 2 opłaty są naliczane 400 RU/s w obu godzinach według [stawek standardowych (ręcznych)](https://azure.microsoft.com/pricing/details/cosmos-db/).|Opłaty są naliczane za godzinę, w przypadku których najwyższego poziomu RU/s system przeskalowany do godziny. <br/><br/>Przykład: <li>Zainicjuj obsługę automatycznego skalowania RU/s z 4000 RU/s (skaluje się między 400 4000 RU/s)</li><li>Godzina 1: system skalowany w górę do najwyższej wartości 3500 RU/s</li><li>Godzina 2: system skalowany w dół do minimum 400 RU/s (zawsze 10% z `Tmax` ), ze względu na brak użycia</li><br/><br/>Opłaty są naliczane za 3500 RU/s w godzinie 1, a 400 RU/s w godzinie 2 — w przypadku [stawek przepływności o tymczasowym skalowaniu](https://azure.microsoft.com/pricing/details/cosmos-db/). Częstotliwość skalowania automatycznego według RU/s wynosi 1,5 * standardowa (ręczna) częstotliwość.
|Co się stanie w przypadku przekroczenia alokowanej jednostki RU/s|Jednostka RU/s pozostaje statyczna po zainicjowaniu obsługi administracyjnej. Wszystkie żądania, które przekraczają zainicjowaną jednostek RU w drugim, będą ustalane proporcjonalnie do liczby, z odpowiedzią, która zaleca czas oczekiwania przed ponowną próbą. W razie konieczności można ręcznie zwiększyć lub zmniejszyć RU/s.| System ustawi skalowanie RU/s do wartości maksymalnego RU/s skalowania automatycznego. Wszystkie żądania, które zużywają ponad maksymalne skalowanie RU/s w drugim, będą ustalane proporcjonalnie, z odpowiedzią, która zaleca czas oczekiwania przed ponowną próbą.|

## <a name="understand-your-traffic-patterns"></a>Zrozumienie wzorców ruchu

### <a name="new-applications"></a>Nowe aplikacje ###

Jeśli tworzysz nową aplikację i nie znasz jeszcze wzorca ruchu, możesz zacząć od punktu wejścia RU/s (lub minimum RU/s), aby uniknąć nadmiernej aprowizacji na początku. W przypadku małej aplikacji, która nie wymaga dużej skali, warto udostępnić tylko minimalny punkt wejścia RU/s, aby zoptymalizować koszt. W przypadku małych aplikacji o małym oczekiwanym ruchu można także wziąć pod uwagę tryb wydajności [bezserwerowej](throughput-serverless.md) .

Niezależnie od tego, czy planujesz używać standardowego (ręcznego) czy automatycznego skalowania, Oto co należy wziąć pod uwagę:

W przypadku aprowizacji standardowego (ręcznego) RU/s w punkcie wejścia 400 RU/s nie będzie możliwe korzystanie z ponad 400 RU/s, chyba że ręcznie zmienisz przepływność. Zostanie naliczona stawka za 400 RU/s w standardowym (ręcznym) współczynniku przepływności na godzinę.

W przypadku udostępnienia przepływności skalowania automatycznego w punkcie wejścia Max RU/s z 4000 RU/s, zasób będzie skalowany w zakresie od 400 do 4000 RU/s. Ponieważ częstotliwość rozliczeń przepływności automatycznego skalowania na RU/s wynosi 1,5 x standardowej (ręcznej) stawki, w godzinach, w których system został przeskalowany w dół do minimum 400 RU/400 s Jednak dzięki funkcji automatycznego skalowania w dowolnym momencie, jeśli dane są używane przez aplikację, można wykorzystać do 4000 RU/s, nie jest wymagana żadna akcja użytkownika. Ogólnie rzecz biorąc, należy zastanowić się nad korzyścią, że można w dowolnym momencie wykorzystać do maksymalnej wartości RU/s przy użyciu współczynnika 1,5 x skalowania automatycznego.

Skorzystaj z [kalkulatora pojemności](estimate-ru-with-capacity-planner.md) Azure Cosmos DB, aby oszacować wymagania dotyczące przepływności. 

### <a name="existing-applications"></a>Istniejące aplikacje ###

Jeśli masz istniejącą aplikację korzystającą z standardowej (ręcznej) przepływności, możesz użyć [metryk Azure monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) , aby określić, czy wzorzec ruchu jest odpowiedni dla automatycznego skalowania. 

Najpierw Znajdź [znormalizowaną metrykę użycia jednostki żądania](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) dla bazy danych lub kontenera. Znormalizowane użycie to miara, ile aktualnie korzystasz z standardowej (ręcznej) przepływności. Im bliżej liczby jest 100%, tym więcej jest w pełni korzystasz z zainicjowanej usługi RU/s. [Dowiedz się więcej](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) o metryki.

Następnie ustal, w jaki sposób znormalizowane wykorzystanie jest różne w miarę upływu czasu. Jeśli widzisz, że znormalizowane użycie jest zmienne lub nieprzewidywalne, rozważ włączenie automatycznego skalowania dla bazy danych lub kontenera. Natomiast jeśli jest to stała i przewidywalna, weź pod uwagę pozostałą w standardowej (ręcznej) przepływności. 

> [!TIP]
> Przy użyciu standardowej (ręcznej) przepływności można użyć znormalizowanej metryki użycia do oszacowania rzeczywistej wartości RU/s, która może być używana w przypadku przełączenia na automatyczne skalowanie. Pomnóż znormalizowane użycie w danym punkcie w czasie przez aktualnie zainicjowaną Standard (ręczna) RU/s. Jeśli na przykład Zainicjowano obsługę zasobów 5000 RU/s, a znormalizowane wykorzystanie wynosi 90%, użycie RU/s jest 0,9 * 5000 = 4500 RU/s. Jeśli widzisz, że wzorzec ruchu jest zmienny, ale jesteś w trybie przekraczającym lub nieobsługiwanym, możesz włączyć funkcję automatycznego skalowania, a następnie odpowiednio zmienić ustawienie maksymalnego RU/s skalowania automatycznego.

## <a name="measure-and-monitor-your-usage"></a>Mierzenie i monitorowanie użycia
W miarę upływu czasu, po wybraniu typu przepływności należy monitorować aplikację i wprowadzać odpowiednie korekty zgodnie z potrzebami. 

W przypadku korzystania z funkcji automatycznego skalowania należy użyć Azure Monitor, aby zobaczyć, że zainicjowano maksymalną skalowalność skalowania automatycznego/s (**Automatyczne skalowanie w poziomie**) i ru/s system jest obecnie skalowany do (**zainicjowana przepływność**). Poniżej znajduje się przykład zmiennej lub nieprzewidywalnego obciążenia przy użyciu funkcji automatycznego skalowania. Należy pamiętać, że w przypadku braku ruchu, system skaluje RU/s do minimum 10% maksymalnej wartości RU/s, która w tym przypadku jest odpowiednio 5000 RU/s i 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Przykład obciążenia przy użyciu funkcji automatycznego skalowania":::

> [!NOTE]
> W przypadku korzystania z standardowej (ręcznej) przepływności zapewnianej przez zainicjowaną **przepływność** jest określana przez użytkownika. Gdy używana jest przepływność automatycznego skalowania, ta Metryka odnosi się do jednostki RU/s, do której system jest obecnie skalowany.

## <a name="next-steps"></a>Następne kroki
* [Kalkulator ru](https://cosmos.azure.com/capacitycalculator/) umożliwia oszacowanie przepływności dla nowych obciążeń.
* Użyj [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) do monitorowania istniejących obciążeń.
* Dowiedz się, jak [udostępnić przepływność automatycznego skalowania dla bazy danych lub kontenera usługi Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Przejrzyj [często zadawane pytania dotyczące skalowania automatycznego](autoscale-faq.md).