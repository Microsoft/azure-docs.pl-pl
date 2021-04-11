---
title: Analizowanie wydajności
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582383"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Analizowanie wydajności na platformie Azure Wyszukiwanie poznawcze

W tym artykule opisano narzędzia, zachowania i podejścia do analizowania wydajności zapytań i indeksowania w Wyszukiwanie poznawcze.

## <a name="develop-baseline-numbers"></a>Opracowywanie numerów linii bazowej

W każdej dużej implementacji ważne jest, aby przeprowadzić test porównawczy wydajności usługi Wyszukiwanie poznawcze przed przystąpieniem do produkcji. Należy przetestować oba oczekiwane obciążenie zapytania wyszukiwania, ale również oczekiwane obciążenia pozyskiwania danych (jeśli to możliwe, uruchom je jednocześnie). Numery testów porównawczych pomagają w sprawdzaniu poprawności odpowiedniej [warstwy wyszukiwania](search-sku-tier.md), [konfiguracji usługi](search-capacity-planning.md)i oczekiwanych [opóźnień zapytań](search-performance-analysis.md#average-query-latency).

W celu opracowania testów porównawczych zalecamy korzystanie z narzędzia [Azure-Search-Performance test (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) .

Aby wyizolować skutki architektury usługi rozproszonej, spróbuj przetestować konfiguracje usług jednej repliki i jedną partycję.

> [!NOTE]
> W przypadku warstw zoptymalizowanych pod kątem magazynu (L1 i L2) należy oczekiwać mniejszej przepływności zapytań i wyższych opóźnień niż w przypadku warstw standardowych.
>

## <a name="use-diagnostic-logging"></a>Korzystanie z rejestrowania diagnostycznego

Najważniejszym narzędziem, które administrator diagnozuje potencjalne problemy z wydajnością, jest [rejestrowanie diagnostyczne](search-monitor-logs.md) , które zbiera dane operacyjne i metryki dotyczące usługi wyszukiwania. Rejestrowanie diagnostyczne jest włączane za [Azure monitor](../azure-monitor/overview.md). Istnieją koszty związane z używaniem Azure Monitor i przechowywaniem danych, ale jeśli włączysz je dla usługi, może to być kontrolka do badania problemów z wydajnością.

Istnieje wiele możliwości opóźnienia, zarówno w trakcie transferu sieciowego, jak i podczas przetwarzania zawartości w warstwie usług aplikacji lub usługi wyszukiwania. Kluczową zaletą rejestrowania diagnostycznego jest to, że działania są rejestrowane z perspektywy usługi wyszukiwania, co oznacza, że dziennik może pomóc w ustaleniu, czy problemy z wydajnością są spowodowane problemami związanymi z kwerendą lub indeksem lub innym punktem awarii.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Łańcuch zarejestrowanych zdarzeń" border="true":::

Rejestrowanie diagnostyczne umożliwia przechowywanie zarejestrowanych informacji. Zalecamy używanie [log Analytics](../azure-monitor/logs/log-analytics-overview.md) , aby można było wykonywać zaawansowane zapytania Kusto dotyczące danych w celu uzyskania odpowiedzi na wiele pytań dotyczących użycia i wydajności. 

Na stronach portalu usługi wyszukiwania możesz włączyć rejestrowanie za pomocą **ustawień diagnostycznych**, a następnie wystawić zapytania Kusto w odniesieniu do log Analytics, wybierając pozycję **dzienniki**. Aby uzyskać więcej informacji na temat konfigurowania, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Opcje menu rejestrowania" border="true":::

## <a name="throttling-behaviors"></a>Zachowania ograniczania

Ograniczanie przepustowości występuje, gdy usługa wyszukiwania osiągnęła limit, co może obsłużyć z perspektywy zasobów. Ograniczanie przepustowości może odbywać się podczas wykonywania zapytań lub indeksowania. Po stronie klienta wywołanie interfejsu API powoduje odebranie odpowiedzi HTTP 503, gdy została ograniczona. Podczas indeksowania istnieje również możliwość otrzymania odpowiedzi HTTP o 207, która wskazuje, że co najmniej jeden element nie może być indeksowany. Ten błąd jest wskaźnikiem, że usługa wyszukiwania zbliża się do pojemności. 

Zgodnie z regułą kciuka najlepiej jest określić liczbę wyświetlanych przepływów. Na przykład jeśli jedno zapytanie wyszukiwania z 500 000 jest ograniczone, może to nie być duże wystąpienie problemu. Jeśli jednak w danym okresie jest ograniczany duży odsetek zapytań, będzie to bardziej istotny problem. Dzięki wykorzystaniu funkcji ograniczania przepustowości przez okres, można także identyfikować przedziały czasu, w których może wystąpić nieoczekiwane ograniczenie i pomóc w wyborze najlepszego dopasowania.

Prosta poprawka do większości problemów z ograniczeniami polega na wyrzucaniu większej ilości zasobów w usłudze wyszukiwania (zwykle replik dla ograniczania opartego na zapytaniach lub partycjach do ograniczania przepływności na podstawie indeksowania). Jednak zwiększenie liczby replik lub partycji powoduje dodanie kosztu, dlatego ważne jest, aby znać powód, dla którego występuje ograniczenie. Badanie warunków, które powodują ograniczenie przepustowości, zostanie wyjaśnione w kolejnych sekcjach. 

Poniżej znajduje się przykład zapytania Kusto, które może identyfikować podział odpowiedzi HTTP z usługi wyszukiwania, która została załadowana. Wykonując zapytania w ciągu 7 dni, renderowany wykres słupkowy pokazuje, że stosunkowo duży procent zapytań wyszukiwania został ograniczony, w porównaniu do liczby udanych (200) odpowiedzi.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Wykres słupkowy liczby błędów http" border="true":::

Badanie ograniczeń w określonym przedziale czasu może pomóc w zidentyfikowaniu czasu, w którym ograniczanie przepustowości może odbywać się częściej. W poniższym przykładzie wykres szeregów czasowych jest używany do wyświetlania liczby zapytań z ograniczeniami, które wystąpiły w określonym przedziale czasu. W takim przypadku zapytania z ograniczeniami są skorelowane z przeprowadzonymi wynikami oceny wydajności.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Wykres liniowy ograniczonych zapytań" border="true":::

## <a name="measure-individual-queries"></a>Mierzenie pojedynczych zapytań

W niektórych przypadkach może być przydatne do testowania pojedynczych zapytań, aby zobaczyć, jak są one wykonywane. Aby to zrobić, ważne jest, aby sprawdzić, jak długo usługa wyszukiwania ma wykonać pracę, a także jak długo trwa wykonywanie żądania rundy z klienta i powrót do klienta. Dzienniki diagnostyczne mogą być używane do wyszukiwania poszczególnych operacji, ale łatwiej jest to zrobić przy użyciu narzędzia klienckiego, takiego jak program.

W poniższym przykładzie zapytanie wyszukiwania oparte na usłudze REST zostało wykonane. Wyszukiwanie poznawcze obejmuje w każdej odpowiedzi liczbę milisekund potrzebnych do ukończenia zapytania, widoczna na karcie nagłówki w ciągu "Upłynęło czasu". Obok pozycji stan w górnej części odpowiedzi znajdziesz czas trwania rundy. w tym przypadku 418 milisekund. W sekcji wyniki została wybrana karta "nagłówki". Korzystając z tych dwóch wartości wyróżnionych czerwoną ramką na poniższej ilustracji, zobaczymy, że usługa wyszukiwania potrzebowała 21 MS, aby ukończyć zapytanie wyszukiwania, a całe żądanie rundy klienta zajęło 125 MS. Przez odjęcie tych dwóch liczb możemy określić, że trwało 104 MS czasu na przesłanie zapytania wyszukiwania do usługi Search i przekazanie wyników wyszukiwania z powrotem do klienta.

Może to być niezwykle pomocne w ustaleniu, czy mogą występować opóźnienia sieciowe lub inne czynniki wpływające na wydajność zapytań.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Metryki czasu trwania zapytania" border="true":::

## <a name="query-rates"></a>Stawki zapytania

Jedną z potencjalnych przyczyn usługi wyszukiwania do ograniczania żądań jest spowodowane zawiera liczbą zapytań wykonywanych w przypadku, gdy wolumin jest przechwytywany jako zapytania na sekundę (zapytań) lub zapytania na minutę (QPM). Ponieważ usługa wyszukiwania otrzymuje więcej ZAPYTAŃów, zazwyczaj trwa dłużej i dłużej odpowiada na te zapytania, dopóki nie przestanie ona działać, co spowoduje wysłanie odpowiedzi HTTP z ograniczeniami 503. 

Następujące zapytanie Kusto pokazuje wolumin zapytania jako mierzoną w QPM oraz średni czas trwania zapytania w milisekundach (AvgDurationMS) i średnią liczbę dokumentów (AvgDocCountReturned) zwracanych w każdym z nich.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Wykres przedstawiający zapytania na minutę" border="true":::

> [!TIP]
> Aby odsłonić dane znajdujące się za tym wykresem, Usuń wiersz `| render timechart` i ponownie uruchom zapytanie.

## <a name="impact-of-indexing-on-queries"></a>Wpływ indeksowania na zapytania

Ważnym czynnikiem, które należy wziąć pod uwagę podczas przeglądania wydajności, jest to, że indeksowanie używa tych samych zasobów co w przypadku zapytań wyszukiwania. W przypadku indeksowania dużej ilości zawartości można oczekiwać, że opóźnienie będzie widoczne, gdy usługa próbuje uwzględnić oba obciążenia.

Jeśli zapytania spowalniają działanie, spójrz na chronometraż działania indeksowania, aby sprawdzić, czy jest on zgodny z obniżeniem zapytania. Może to być na przykład, że indeksator uruchamia zadanie codzienne lub godzinowe, które jest skorelowane z obniżoną wydajnością zapytań wyszukiwania. 

Ta sekcja zawiera zestaw zapytań, które mogą pomóc w wizualizacji częstotliwości wyszukiwania i indeksowania. W tych przykładach zakres czasu jest ustawiany w zapytaniu. Upewnij się, że podczas uruchamiania zapytań w Azure Portal jest wskazywany **zestaw w kwerendzie** .

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Ustawianie zakresów czasu w narzędziu zapytania" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Średnie opóźnienie zapytania

W poniższym zapytaniu rozmiar interwału wynoszący 1 minutę jest używany do wyświetlania średniego opóźnienia zapytań wyszukiwania. Na wykresie można zobaczyć, że średnie opóźnienie było niskie do 5:17:45 i ostatnie do 5:53pm.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Wykres przedstawiający Średnie opóźnienie zapytania" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Średnia liczba zapytań na minutę (QPM)

Poniższe zapytanie pozwala nam sprawdzić średnią liczbę zapytań na minutę, aby upewnić się, że nie było żadnego sortowania w żądaniach wyszukiwania, które mogą mieć wpływ na opóźnienie. Na wykresie można zobaczyć, że występuje pewne wariancje, ale nic nie wskazuje na liczbę żądań.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Wykres przedstawiający średnie zapytania na minutę" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Operacje indeksowania na minutę (OPM)

W tym miejscu zostanie wyświetlona liczba operacji indeksowania na minutę. Na wykresie można zobaczyć, że indeksowanie dużej ilości danych zostało rozpoczęte o godzinie 5:42 pm i zakończyło się o 5:50pm. To indeksowanie rozpoczęło się 3 minuty, zanim zapytania wyszukiwania zaczęły być ukryte i zakończyły się 3 minuty, zanim zapytania wyszukiwania nie będą już ukryte.

Od tego czasu możemy zobaczyć, że trwało około 3 minuty, aby usługa wyszukiwania była zbyt mała od indeksowania, aby zacząć mieć wpływ na opóźnienie zapytania wyszukiwania. Możemy również zobaczyć, że po ukończeniu indeksowania zajęło jeszcze trzy minuty, aby usługa wyszukiwania mogła ukończyć całą pracę od nowo indeksowanej zawartości, dopóki zapytania wyszukiwania nie będą już ukryte.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Wykres przedstawiający operacje indeksowania na minutę" border="true":::

## <a name="background-service-processing"></a>Przetwarzanie usługi w tle

Nie jest nietypowym sposobem wyświetlania okresowych skoków w czasie oczekiwania na zapytanie lub indeksowanie. Wartości graniczne mogą wystąpić w reakcji na indeksowanie lub wysokie stawki zapytań, ale mogą również wystąpić podczas operacji scalania. Indeksy wyszukiwania są przechowywane w fragmentach — lub fragmentów. Okresowo system Scala mniejsze fragmentów w duże fragmentów, co może pomóc zoptymalizować wydajność usługi. Ten proces scalania czyści również dokumenty, które zostały wcześniej oznaczone do usunięcia z indeksu, co spowodowało odzyskanie miejsca do magazynowania. 

Scalanie fragmentów jest szybkie, ale również wymaga dużej ilości zasobów i w ten sposób może obniżyć wydajność usługi. Z tego powodu, jeśli widzisz krótkie rozerwania opóźnień zapytań, a te zmiany są zgodne z najnowszymi zmianami w indeksowanej zawartości, najprawdopodobniej postanowisz o tym opóźnieniu do fragmentu operacji scalania. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi dodatkowymi artykułami związanymi z analizowaniem wydajności usługi.

+ [Porady dotyczące wydajności](search-performance-tips.md)
+ [Wybierz warstwę usług](search-sku-tier.md)
+ [Zarządzanie pojemnością](search-capacity-planning.md)
