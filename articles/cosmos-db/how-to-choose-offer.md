---
title: Jak wybrać między ręczne i automatyczne skalowanie na Azure Cosmos DB
description: Dowiedz się, jak wybrać między standardową (ręczną) elastyczną przepływność i elastyczną skalowalną przepływność dla obciążenia.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089644"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Jak wybrać między standardowym (ręcznym) i automatycznym skalowaniem przepływności 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

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

Następnie ustal, w jaki sposób znormalizowane wykorzystanie jest różne w miarę upływu czasu. Znajdź największe znormalizowane użycie dla każdej godziny. Następnie Oblicz średnie znormalizowane użycie przez wszystkie godziny. Jeśli średnie użycie wynosi mniej niż 66%, rozważ włączenie automatycznego skalowania w bazie danych lub kontenerze. Jeśli natomiast średnie użycie wynosi ponad 66%, zalecane jest pozostanie przy standardowej (ręcznej) aprowizacji przepływności.

> [!TIP]
> Jeśli Twoje konto jest skonfigurowane do korzystania z zapisów w wielu regionach i ma więcej niż jeden region, stawka za 100 RU/s jest taka sama dla ręcznego i automatycznego skalowania. Oznacza to, że włączenie automatycznego skalowania nie wiąże się z żadnymi dodatkowymi kosztami, niezależnie od użycia. W związku z tym zawsze zaleca się używanie funkcji automatycznego skalowania przy użyciu zapisu w wielu regionach, gdy masz więcej niż jeden region, aby skorzystać z oszczędności wynikających z płacenia tylko za jednostki RU/s, do których aplikacja jest skalowana. Jeśli masz zapisy w wielu regionach i jeden region, użyj średniego wykorzystania, aby określić, czy automatyczne skalowanie spowoduje oszczędności kosztów. 

#### <a name="examples"></a>Przykłady

Przyjrzyjmy się dwóm różnym przykładowym obciążom i przeanalizować je, jeśli są odpowiednie dla przepływności ręcznej lub automatycznego skalowania. Aby zilustrować ogólne podejście, przeanalizujemy trzy godziny historii, aby określić różnicę kosztów między ręcznym i automatycznym skalowaniem. W przypadku obciążeń produkcyjnych zalecane jest użycie od 7 do 30 dni historii (lub dłużej, jeśli jest to możliwe) do ustanowienia wzorca użycia RU/s.

> [!NOTE]
> Wszystkie przykłady przedstawione w tym dokumencie są oparte na cenie konta usługi Azure Cosmos, które zostało wdrożone w regionie nierządowym w Stanach Zjednoczonych. Ceny i obliczenia różnią się w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB.

Założenia:
- Załóżmy, że mamy obecnie ręczną przepływność 30 000 RU/s. 
- W naszym regionie jest konfigurowana jednoregionowa zapis z jednym regionem. W przypadku wielu regionów będziemy mnożyć koszt godzinowy według liczby regionów.
- Użyj stawek cennika publicznego dla ręcznego ($0,008 USD za 100 RU/s na godzinę) i przepływności skalowania automatycznego ($0,012 USD za 100 RU/s na godzinę) w ramach kont zapisu z jednym regionem. Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Przykład 1: obciążenie zmienne (zalecane automatyczne skalowanie)

Najpierw Spójrzmy na znormalizowane użycie RU. To obciążenie ma zmienny ruch, z znormalizowanym zużyciem RU w zakresie od 6% do 100%. Istnieją sporadyczne gwałtowne przewidywalność do 100%, ale wiele godzin o niskim obciążeniu. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Obciążenie z zmiennym ruchem — znormalizowane użycie RU między 6% a 100% przez wszystkie godziny":::

Porównujemy koszty aprowizacji 30 000 RU/s, a w przeciwieństwie do ustawiania automatycznego skalowania RU/s do 30 000 (skaluje się między 3000-30 000 RU/s). 

Teraz Przeanalizujmy historię. Załóżmy, że użycie zostało opisane w poniższej tabeli. Średnie użycie w tych trzech godzinach wynosi 39%. Ze względu na to, że znormalizowana wartość zużycia RU jest mniejsza niż 66%, oszczędzamy przy użyciu funkcji automatycznego skalowania. 

Należy pamiętać, że w godzinie 1, gdy istnieje 6% użycia, funkcja automatycznego skalowania będzie rozliczać RU/s dla 10% maksymalnej wartości RU/s, czyli minimum na godzinę. Chociaż koszt automatycznego skalowania może być wyższa niż ręczna przepływność w określonych godzinach, pod warunkiem, że średnie wykorzystanie jest mniejsze niż 66% przez cały czas, Skalowanie automatyczne będzie tańsze.

|Okres  | Wykorzystanie |Rozliczane automatyczne skalowanie RU/s  |Opcja 1: ręczne 30 000 RU/s  | Opcja 2: automatyczne skalowanie w zakresie od 3000 do 30 000 RU/s |
|---------|---------|---------|---------|---------|
|Godzina 1  | 6%  |     3000  |  30 000 * 0,008/100 = $2,40        |   3000 * 0,012/100 = $0,36      |
|Godzina 2  | 100%  |     30 000    |  30 000 * 0,008/100 = $2,40       |  30 000 * 0,012/100 = $3,60      |
|Godzina 3 |  11  |     3300    |  30 000 * 0,008/100 = $2,40       |    3300 * 0,012/100 = $0,40     |
|**Łącznie**   |  |        |  $7,20       |    $4,36 (39% oszczędności)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Przykład 2: stałe obciążenie (zalecane ręczne przepływność)

To obciążenie ma stały ruch z znormalizowanym zużyciem RU od 72% do 100%. W przypadku 30 000 RU/s zainicjowano, oznacza to, że zużywamy od 21 600 do 30 000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Obciążenie ze stałym ruchem — znormalizowane użycie RU między 72% a 100% przez wszystkie godziny":::

Porównujemy koszty aprowizacji 30 000 RU/s, a w przeciwieństwie do ustawiania automatycznego skalowania RU/s do 30 000 (skaluje się między 3000-30 000 RU/s).

Załóżmy, że mamy historię użycia zgodnie z opisem w tabeli. Nasze średnie użycie w tych trzech godzinach wynosi 88%. Ze względu na to, że znormalizowana wartość zużycia RU jest większa niż 66%, oszczędzamy przy użyciu ręcznej przepływności.

Ogólnie rzecz biorąc, jeśli średnie wykorzystanie przez wszystkie 730 godzin w ciągu miesiąca jest większe niż 66%, zostanie ono zapisane za pomocą ręcznej przepływności. 

| Okres | Wykorzystanie |Rozliczane automatyczne skalowanie RU/s  |Opcja 1: ręczne 30 000 RU/s  | Opcja 2: automatyczne skalowanie w zakresie od 3000 do 30 000 RU/s |
|---------|---------|---------|---------|---------|
|Godzina 1  | 72%  |     21 600   |  30 000 * 0,008/100 = $2,40        |   21600 * 0,012/100 = $2,59      |
|Godzina 2  | 93%  |     28 000    |  30 000 * 0,008/100 = $2,40       |  28 000 * 0,012/100 = $3,36       |
|Godzina 3 |  100%  |     30 000    |  30 000 * 0,008/100 = $2,40       |    30 000 * 0,012/100 = $3,60     |
|**Łącznie**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Przy użyciu standardowej (ręcznej) przepływności można użyć znormalizowanej metryki użycia do oszacowania rzeczywistej wartości RU/s, która może być używana w przypadku przełączenia na automatyczne skalowanie. Pomnóż znormalizowane użycie w danym punkcie w czasie przez aktualnie zainicjowaną Standard (ręczna) RU/s. Jeśli na przykład Zainicjowano obsługę zasobów 5000 RU/s, a znormalizowane wykorzystanie wynosi 90%, użycie RU/s jest 0,9 * 5000 = 4500 RU/s. Jeśli widzisz, że wzorzec ruchu jest zmienny, ale jesteś w trybie przekraczającym lub nieobsługiwanym, możesz włączyć funkcję automatycznego skalowania, a następnie odpowiednio zmienić ustawienie maksymalnego RU/s skalowania automatycznego.

#### <a name="how-to-calculate-average-utilization"></a>Jak obliczyć średnie wykorzystanie
Automatycznie Skaluj rachunki dla najwyższego RU/s skalowane do godziny. Podczas analizowania znormalizowanego zużycia RU w miarę upływu czasu ważne jest, aby przy obliczaniu średniej użyć najwyższego użycia na godzinę. 

Aby obliczyć średnią o największym wykorzystaniu przez wszystkie godziny:
1. Ustaw **agregację** dla metryki użycia Noramlized ru na **wartość Max**.
1. Wybierz poziom **szczegółowości czasu** do 1 godziny.
1. Przejdź do **opcji wykresu**.
1. Wybierz opcję wykres słupkowy. 
1. W obszarze **udostępnianie** wybierz opcję **Pobierz do programu Excel** . W wygenerowanym arkuszu kalkulacyjnym Oblicz średnie wykorzystanie przez wszystkie godziny. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Aby zobaczyć znormalizowane użycie RU według godziny, 1) wybierz stopień szczegółowości czasu do 1 godziny. 2) Edytowanie ustawień wykresu; 3) wybierz opcję wykres słupkowy; 4) w obszarze udział wybierz opcję Pobierz do programu Excel, aby obliczyć średnią dla wszystkich godzin. ":::

## <a name="measure-and-monitor-your-usage"></a>Mierzenie i monitorowanie użycia
W miarę upływu czasu, po wybraniu typu przepływności należy monitorować aplikację i wprowadzać odpowiednie korekty zgodnie z potrzebami. 

W przypadku korzystania z funkcji automatycznego skalowania należy użyć Azure Monitor, aby zobaczyć, że zainicjowano maksymalną skalowalność skalowania automatycznego/s (**Automatyczne skalowanie w poziomie**) i ru/s system jest obecnie skalowany do (**zainicjowana przepływność**). Poniżej znajduje się przykład zmiennej lub nieprzewidywalnego obciążenia przy użyciu funkcji automatycznego skalowania. Należy pamiętać, że w przypadku braku ruchu, system skaluje RU/s do minimum 10% maksymalnej wartości RU/s, która w tym przypadku jest odpowiednio 5000 RU/s i 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Przykład obciążenia przy użyciu funkcji automatycznego skalowania z automatycznym skalą RU/s z 50 000 RU/s i przepływność od 5000 do 50 000 RU/s":::

> [!NOTE]
> W przypadku korzystania z standardowej (ręcznej) przepływności zapewnianej przez zainicjowaną **przepływność** jest określana przez użytkownika. Gdy używana jest przepływność automatycznego skalowania, ta Metryka odnosi się do jednostki RU/s, do której system jest obecnie skalowany.

## <a name="next-steps"></a>Następne kroki
* [Kalkulator ru](https://cosmos.azure.com/capacitycalculator/) umożliwia oszacowanie przepływności dla nowych obciążeń.
* Użyj [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) do monitorowania istniejących obciążeń.
* Dowiedz się, jak [udostępnić przepływność automatycznego skalowania dla bazy danych lub kontenera usługi Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Przejrzyj [często zadawane pytania dotyczące skalowania automatycznego](autoscale-faq.md).