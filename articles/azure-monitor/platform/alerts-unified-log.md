---
title: Alerty dzienników w Azure Monitor
description: Wyzwalaj wiadomości e-mail, powiadomienia, wywołaj adresy URL witryn sieci Web (webhook) lub automatyzację, gdy określony warunek zapytania dziennika zostanie spełniony
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 8081c60833c3c02d55ae66ca695ba106dba01450
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294142"
---
# <a name="log-alerts-in-azure-monitor"></a>Alerty dzienników w Azure Monitor

## <a name="overview"></a>Omówienie

Alerty dzienników są jednym z typów alertów, które są obsługiwane w [alertach platformy Azure](./alerts-overview.md). Alerty dzienników umożliwiają użytkownikom użycie zapytania [log Analytics](../log-query/get-started-portal.md) w celu obliczenia dzienników zasobów co określoną częstotliwość i wyzwolenia alertu na podstawie wyników. Reguły mogą wyzwalać jedną lub więcej akcji przy użyciu [grup akcji](./action-groups.md).

> [!NOTE]
> Dane dziennika z [obszaru roboczego log Analytics](../log-query/get-started-portal.md) mogą być wysyłane do magazynu metryk Azure monitor. Alerty metryk mają [różne zachowanie](alerts-metric-overview.md), co może być bardziej odpowiednie w zależności od danych, z którymi pracujesz. Aby uzyskać informacje na temat tego, co i jak można kierować dzienniki do metryk, zobacz [alert metryki dla dzienników](alerts-metric-logs.md).

> [!NOTE]
> Obecnie nie ma dodatkowych opłat za wersję interfejsu API `2020-05-01-preview` i alerty dzienników zorientowanych na zasoby.  Cennik funkcji, które są w wersji zapoznawczej, zostanie ogłoszony w przyszłości, a powiadomienie podane przed rozpoczęciem rozliczania. W przypadku wybrania opcji kontynuowania korzystania z nowej wersji interfejsu API i zdarzeń dzienników skoncentrowanych na zasobach po upływie okresu wypowiedzenia zostanie naliczona stawka mająca zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne

Alerty dzienników uruchamiają zapytania dotyczące danych Log Analytics. Najpierw należy rozpocząć [zbieranie danych dziennika](resource-logs.md) i wykonywanie zapytań dotyczących danych dziennika pod kątem problemów. Możesz użyć [przykładowego zapytania dotyczącego alertu](../log-query/saved-queries.md) w log Analytics, aby zrozumieć, co można odkryć lub [zacząć pisać przy tworzeniu własnych zapytań](../log-query/get-started-portal.md).

[Współautor monitorowania platformy Azure](./roles-permissions-security.md) to wspólna rola, która jest wymagana do tworzenia, modyfikowania i aktualizowania alertów dzienników. W przypadku dzienników zasobów należy również uzyskać uprawnienia dostępu & wykonywania zapytań. Częściowe dostęp do dzienników zasobów może kończyć się niepowodzeniem lub zwracać częściowe wyniki. [Dowiedz się więcej o konfigurowaniu alertów dziennika na platformie Azure](./alerts-log.md).

> [!NOTE]
> Alerty dzienników dla Log Analytics używane do zarządzania przy użyciu starszego [interfejsu API log Analytics alertów](api-alerts.md). [Dowiedz się więcej na temat przełączania do bieżącego interfejsu API ScheduledQueryRules](alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definicja oceny zapytania

Definicja warunku reguł przeszukiwania dzienników zaczyna się od:

- Jakie zapytanie należy uruchomić?
- Jak korzystać z wyników?

W poniższych sekcjach opisano różne parametry, których można użyć w celu ustawienia powyższej logiki.

### <a name="log-query"></a>Zapytanie dziennika
Zapytanie [log Analytics](../log-query/get-started-portal.md) używane do obliczania reguły. Wyniki zwrócone przez to zapytanie są używane do określenia, czy alert ma zostać wyzwolony. Do tego zapytania można domieścić zakresy:

- Określony zasób, na przykład maszynę wirtualną.
- Zasób o dużej skali, taki jak subskrypcja lub Grupa zasobów.
- Wiele zasobów przy użyciu [zapytania między zasobami](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Zapytania alertów mają ograniczenia, aby zapewnić optymalną wydajność i znaczenie wyników. [Dowiedz się więcej tutaj](./alerts-log-query.md).

> [!IMPORTANT]
> Zamiar zasobów i [zapytania między zasobami](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) są obsługiwane tylko przy użyciu bieżącego interfejsu API scheduledQueryRules. W przypadku korzystania z starszego [interfejsu API log Analytics alertów](api-alerts.md)należy przełączyć. [Dowiedz się więcej o przełączaniu](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Zakres czasu zapytania

Zakres czasu jest ustawiany w definicji warunku reguły. W obszarach roboczych i Application Insights jest on nazywany **okresem**. We wszystkich innych typach zasobów jest nazywana **przesłonięciem zakresu czasu zapytania**.

Podobnie jak w przypadku usługi log Analytics, zakres czasu ogranicza dane zapytania do określonego zakresu. Nawet jeśli **to** polecenie jest używane w zapytaniu, przedział czasu będzie miał zastosowanie.

Na przykład zapytanie skanuje 60 minut, gdy zakres czasu wynosi 60 minut, nawet jeśli tekst zawiera **temu (1D)**. Zakres czasu i filtrowanie czasu zapytania muszą być zgodne. W przykładzie w przypadku zmiany **okresu**  /  **przesłonięcia zapytania** do jednego dnia program będzie działał zgodnie z oczekiwaniami.

### <a name="measure"></a>Measure

Alerty dziennika umożliwiają logowanie do wartości liczbowych, które można ocenić. Można mierzyć dwie różne rzeczy:

#### <a name="count-of-the-results-table-rows"></a>Liczba wierszy tabeli wyników

Liczba wyników to domyślna miara. Idealny do pracy ze zdarzeniami, takimi jak dzienniki zdarzeń systemu Windows, dziennik systemowy i wyjątki aplikacji. Wyzwalane, gdy rekordy dziennika są wykonywane lub nie występują w przedziale czasu.

Alerty dzienników najlepiej sprawdzają się w przypadku próby wykrycia danych w dzienniku. Działa ono mniej dobrze podczas próby wykrywania braku danych w dziennikach. Na przykład alert dotyczący pulsu maszyny wirtualnej.

W przypadku obszarów roboczych i Application Insights jest on wywoływany **w oparciu** o wybraną **liczbę wyników**. We wszystkich innych typach zasobów jest wywoływana **miara** z **wierszami tabeli**wyboru.

> [!NOTE]
> Ponieważ dzienniki są danymi z częściową strukturą, są one z natury bardziej ukryte niż Metryka, podczas próby wykrywania braku danych w dziennikach mogą wystąpić problemy, które należy wziąć pod uwagę przy użyciu [alertów metryk](alerts-metric-overview.md). Dane można wysyłać do magazynu metryk z dzienników przy użyciu [alertów metryk dla dzienników](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Przykładowy kod tabeli wyników liczba wierszy użycia

Chcesz dowiedzieć się, kiedy aplikacja odpowie z kodem błędu 500 (wewnętrzny błąd serwera). Można utworzyć regułę alertu z następującymi szczegółami:

- **Dotyczących** 

```Kusto
requests
| where resultCode == "500"
```

- **Okres:** 15 minut
- **Częstotliwość alertów:** 15 minut
- **Wartość progowa:** Większe niż 0

Następnie reguły alertów są monitorowane dla wszystkich żądań kończących się na 500 kodzie błędu. Zapytanie jest uruchamiane co 15 minut w ciągu ostatnich 15 minut. Po znalezieniu nawet jednego rekordu uruchamiany jest alert i wyzwalane są skonfigurowane akcje.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Obliczanie miary na podstawie kolumny liczbowej (takiej jak wartość licznika CPU)

W przypadku obszarów roboczych i Application Insights jest ona wywoływana **w oparciu** o **pomiar metryki**wyboru. We wszystkich innych typach zasobów jest wywoływana **miara** z wyborem dowolnej nazwy kolumny liczb.

### <a name="aggregation-type"></a>Typ agregacji

Obliczenia wykonywane na wielu rekordach w celu zagregowania ich do jednej wartości liczbowej. Na przykład:
- **Liczba** zwraca liczbę rekordów w zapytaniu
- Wartość **średnia** zwraca średnią zdefiniowaną [**stopień szczegółowości agregacji**](#aggregation-granularity) kolumny miary.

W obszarach roboczych i Application Insights jest obsługiwana tylko w typie miary **pomiaru metryki** . Wynik zapytania musi zawierać kolumnę o nazwie AggregatedValue, która dostarcza wartość liczbową po agregacji zdefiniowanej przez użytkownika. We wszystkich innych typach zasobów jest wybierany **typ agregacji** z pola tej nazwy.

### <a name="aggregation-granularity"></a>Stopień szczegółowości agregacji

Określa interwał, który jest używany do agregowania wielu rekordów do jednej wartości liczbowej. Na przykład jeśli określono **5 minut**, rekordy byłyby pogrupowane według 5-minutowych interwałów przy użyciu określonego **typu agregacji** .

W obszarach roboczych i Application Insights jest obsługiwana tylko w typie miary **pomiaru metryki** . Wynik zapytania musi zawierać [bin ()](/azure/kusto/query/binfunction) , który ustawia interwał w wynikach zapytania. We wszystkich innych typach zasobów pole kontrolujące to ustawienie jest nazywane **stopnia szczegółowości agregacji**.

> [!NOTE]
> Ponieważ funkcja [bin ()](/azure/kusto/query/binfunction) może powodować nierówne przedziały czasu, usługa alertów automatycznie konwertuje funkcję [bin ()](/azure/kusto/query/binfunction) na funkcję [bin_at ()](/azure/kusto/query/binatfunction) na odpowiednim czasie w czasie wykonywania, aby zapewnić wyniki ze stałym punktem.

### <a name="split-by-alert-dimensions"></a>Podziel według wymiarów alertów

Podziel alerty według liczby lub kolumn ciągów na oddzielne alerty, grupując je na unikatowe kombinacje. Podczas tworzenia alertów skoncentrowanych na zasobach w skali (zakres subskrypcji lub grupy zasobów) można podzielić według kolumny identyfikatora zasobu platformy Azure. Podział w kolumnie Identyfikator zasobu platformy Azure zmieni obiekt docelowy alertu na określony zasób.

W obszarach roboczych i Application Insights jest obsługiwana tylko w typie miary **pomiaru metryki** . Pole jest nazywane **agregacją**. Jest ona ograniczona do trzech kolumn. Posiadanie więcej niż trzech grup według kolumn w zapytaniu może prowadzić do nieoczekiwanych wyników. We wszystkich innych typach zasobów jest on konfigurowany w sekcji **dzielenie według wymiarów** w warunku (ograniczone do sześciu podziałów).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Przykład dzielenia według wymiarów alertów

Na przykład chcesz monitorować błędy wielu maszyn wirtualnych z uruchomioną witryną sieci Web lub aplikacją w określonej grupie zasobów. Można to zrobić za pomocą reguły alertu dziennika w następujący sposób:

- **Dotyczących** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    W przypadku korzystania z obszarów roboczych i Application Insights z logiką alertów **pomiaru metryk** ten wiersz należy dodać do tekstu zapytania:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Kolumna identyfikatora zasobu:** _ResourceId (podział według kolumny identyfikatora zasobu w regułach alertów) jest dostępny tylko dla subskrypcji i grup zasobów.
- **Wymiary/zagregowane w:**
  - Computer = VM1, VM2 (Filtrowanie wartości w definicji reguł alertów nie jest obecnie dostępne dla obszarów roboczych i Application Insights. Filtr w tekście zapytania.)
- **Okres:** 15 minut
- **Częstotliwość alertów:** 15 minut
- **Wartość progowa:** Większe niż 0

Ta reguła monitoruje, czy dowolna maszyna wirtualna miała zdarzenia błędów w ciągu ostatnich 15 minut. Każda maszyna wirtualna jest monitorowana osobno i wyzwala akcje pojedynczo.

> [!NOTE]
> Podział według wymiarów alertów jest dostępny tylko dla bieżącego interfejsu API scheduledQueryRules. W przypadku korzystania z starszego [interfejsu API log Analytics alertów](api-alerts.md)należy przełączyć. [Dowiedz się więcej o przełączaniu](./alerts-log-api-switch.md). Generowanie alertów zasobów na dużą skalę jest obsługiwane tylko w wersji interfejsu API `2020-05-01-preview` i nowszych.

## <a name="alert-logic-definition"></a>Definicja logiki alertu

Po zdefiniowaniu zapytania do uruchamiania i oceny wyników należy zdefiniować logikę alertów i czas działania akcji. W poniższych sekcjach opisano różne parametry, których można użyć:

### <a name="threshold-and-operator"></a>Próg i operator

Wyniki zapytania są przekształcane na liczbę, która jest porównywana z progiem i operatorem.

### <a name="frequency"></a>Częstotliwość

Interwał, w którym jest uruchamiane zapytanie. Można ustawić od 5 minut do jednego dnia. Wartość musi być równa lub mniejsza niż [zakres czasu zapytania](#query-time-range) , aby nie trafiać rekordów dziennika.

Na przykład jeśli ustawisz okres czasu na 30 minut i częstotliwość na 1 godzinę.  Jeśli zapytanie jest uruchamiane o godzinie 00:00, zwraca rekordy z przedziału od 23:30 do 00:00. Przy następnym uruchomieniu zapytania jest 01:00, które zwróci rekordy z zakresu od 00:30 do 01:00. Wszystkie rekordy utworzone w zakresie od 00:00 do 00:30 nigdy nie zostaną ocenione.

### <a name="number-of-violations-to-trigger-alert"></a>Liczba naruszeń alertu wyzwalacza

Możesz określić okres oceny alertu i liczbę błędów, które są potrzebne do wyzwolenia alertu. Umożliwienie lepszego zdefiniowania czasu wpływu na wyzwolenie alertu. 

Na przykład jeśli [**stopień szczegółowości agregacji**](#aggregation-granularity) reguł jest zdefiniowany jako "5 minut", można wyzwolić alert tylko wtedy, gdy wystąpiło trzy błędy (15 minut) od ostatniej godziny. To ustawienie jest definiowane przez zasady biznesowe aplikacji.

## <a name="state-and-resolving-alerts"></a>Stan i rozwiązywanie alertów

Alerty dzienników są bezstanowe. Alerty są wyzwalane za każdym razem, gdy warunek zostanie spełniony, nawet jeśli został wcześniej wywołany. Wyzwolone alerty nie są rozwiązywane. Można [oznaczyć alert jako zamknięty](alerts-managing-alert-states.md). Możesz również wyciszyć akcje, aby uniemożliwić ich wyzwalanie przez okres po wyzwoleniu reguły alertu.

W obszarze roboczym i Application Insights jest on wywoływany **pomijanie alertów**. We wszystkich innych typach zasobów jest wywoływana **Akcja wyciszenia**. 

Zobacz ten przykład oceny alertu:

| Godzina    | Szacowanie warunku dziennika | Wynik 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | Alert nie jest wyzwalany. Nie wywołano żadnych akcji.
| 00:10 | TRUE  | Wyzwolone alerty i grupy akcji o nazwie. Nowy stan alertu jest aktywny.
| 00:15 | TRUE  | Wyzwolone alerty i grupy akcji o nazwie. Nowy stan alertu jest aktywny.
| 00:20 | FALSE | Alert nie jest wyzwalany. Nie wywołano żadnych akcji. Stan alertów poprzedniej pozostaje aktywny.

## <a name="pricing-and-billing-of-log-alerts"></a>Cennik i rozliczanie alertów dziennika

Informacje o cenach znajdują się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Alerty dzienników są wyświetlane w obszarze dostawca zasobów `microsoft.insights/scheduledqueryrules` z:

- Alerty dzienników dla Application Insights wyświetlane z dokładną nazwą zasobu oraz właściwościami grupy zasobów i alertu.
- Alerty dzienników dla Log Analytics wyświetlane z dokładną nazwą zasobu wraz z właściwościami grupy zasobów i alertu; podczas tworzenia przy użyciu [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules).
- Alerty dzienników utworzone ze [starszej wersji interfejsu API log Analytics](./api-alerts.md) nie są śledzone [zasoby platformy Azure](../../azure-resource-manager/management/overview.md) i nie mają wymuszanych unikatowych nazw zasobów. Te alerty są nadal tworzone w `microsoft.insights/scheduledqueryrules` postaci ukrytych zasobów, które mają tę strukturę nazewnictwa zasobów `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Alerty dzienników dla starszego interfejsu API są wyświetlane z podaną powyżej ukrytą nazwą zasobu wraz z właściwościami grupy zasobów i alertu.

> [!NOTE]
> Nieobsługiwane znaki zasobów, takie jak `<, >, %, &, \, ?, /` są zastępowane `_` w ramach ukrytych nazw zasobów, a także odzwierciedlają informacje o rozliczeniach.

> [!NOTE]
> Alerty dzienników dla Log Analytics używane do zarządzania przy użyciu starszego [interfejsu API alertów log Analytics](api-alerts.md) i starszych szablonów [log Analytics zapisanych wyszukiwań i alertów](../insights/solutions.md). [Dowiedz się więcej na temat przełączania do bieżącego interfejsu API ScheduledQueryRules](alerts-log-api-switch.md). Wszelkie zarządzanie regułami alertów należy wykonać przy użyciu [starszej wersji interfejsu API log Analytics](api-alerts.md) , dopóki nie zdecydujesz się na przełączenie i nie będzie można używać ukrytych zasobów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat tworzenia alertów dziennika na platformie Azure](./alerts-log.md).
* Informacje [o elementach webhook w alertach dziennika na platformie Azure](alerts-log-webhook.md).
* Dowiedz się więcej o [alertach platformy Azure](./alerts-overview.md).
* Dowiedz się więcej o [log Analytics](../log-query/log-query-overview.md).

