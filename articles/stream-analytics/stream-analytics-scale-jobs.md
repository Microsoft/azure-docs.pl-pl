---
title: Skalowanie w górę i w dół w Azure Stream Analytics zadaniach
description: W tym artykule opisano sposób skalowania zadania Stream Analytics przez Partycjonowanie danych wejściowych, dostrajanie zapytania i Ustawianie jednostek przesyłania strumieniowego zadań.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018687"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skalowanie zadania Azure Stream Analytics w celu zwiększenia przepływności
W tym artykule opisano sposób dostrajania zapytania Stream Analytics, aby zwiększyć przepływność zadań analizy przesyłania strumieniowego. Poniższy przewodnik umożliwia skalowanie zadania w celu obsługi wyższego obciążenia i wykorzystanie większej ilości zasobów systemowych (takich jak większa przepustowość, więcej zasobów procesora CPU, więcej pamięci).
Jako warunek wstępny może być konieczne zapoznanie się z następującymi artykułami:
-   [Opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md)
-   [Tworzenie zadań działania równoległego](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Przypadek 1 — zapytanie jest z założenia całkowicie działania równoległego między partycjami wejściowymi
Jeśli zapytanie jest z założenia w pełni działania równoległego między partycjami wejściowymi, można wykonać następujące czynności:
1.  Utwórz zapytanie, aby było zaskakująco równoległe za pomocą słowa kluczowego **Partition by** . Więcej szczegółów znajduje się w sekcji zaskakująco Jobs Jobs [na tej stronie](stream-analytics-parallelization.md).
2.  W zależności od typów danych wyjściowych używanych w zapytaniu niektóre dane wyjściowe mogą nie być działania równoległego lub muszą być zaskakująco równoległe w dalszej konfiguracji. Na przykład dane wyjściowe usługi PowerBI nie są działania równoległego. Dane wyjściowe są zawsze scalane przed wysłaniem do ujścia danych wyjściowych. Obiekty blob, tabele, ADLS, Service Bus i funkcja platformy Azure są automatycznie równoległe. Dane wyjściowe usług SQL i Azure Synapse Analytics mają opcję przetwarzanie równoległe. Centrum zdarzeń musi mieć ustawioną konfigurację PartitionKey, aby pasowała do pola **Partition by** (zazwyczaj PartitionID). W przypadku centrum zdarzeń należy również zwrócić szczególną uwagę na liczbę partycji dla wszystkich danych wejściowych i wszystkich wyjść, aby uniknąć przekroczenia między partycjami. 
3.  Uruchom zapytanie z **6 Su** (to jest pełna pojemność jednego węzła obliczeniowego), aby zmierzyć maksymalną osiągalną przepływność, a jeśli używasz funkcji **Grupuj według**, Zmierz liczbę grup (Kardynalność), które może obsłużyć zadanie. Poniżej znajdują się ogólne objawy działania, które ograniczają zasoby systemowe.
    - Metryka użycia SU% jest ponad 80%. Oznacza to, że użycie pamięci jest wysokie. Czynniki, które przyczyniają się do zwiększenia tej metryki, zostały opisane [tutaj](stream-analytics-streaming-unit-consumption.md). 
    -   Wyjściowa sygnatura czasowa jest uwzględniana w odniesieniu do zegara ściany. W zależności od logiki zapytania wyjściowa sygnatura czasowa może być przesunięta do logiki od czasu zegara ściany. Należy jednak postępować w przybliżeniu z tą samą szybkością. Jeśli sygnatura czasowa wyjściowa jest w dalszej i dalszej stopniu, jest wskaźnikiem, że system jest przesłonięty. Może to wynikać z przepływności wyjściowej wyjściowego ujścia lub wysokie wykorzystanie procesora CPU. W tej chwili nie udostępniamy metryki użycia procesora, więc może być trudne odróżnienie tych dwóch.
        - Jeśli problem jest związany z ograniczaniem ujścia, może być konieczne zwiększenie liczby partycji wyjściowych (a także partycji wejściowych, aby zapewnić całkowite działania równoległego zadania) lub zwiększyć ilość zasobów ujścia (na przykład liczba jednostek żądań dla CosmosDB).
    - Na diagramie zadań istnieje Metryka zdarzenia zaległości dla każdej partycji dla każdego danych wejściowych. Jeśli Metryka zdarzenia zaległości ciągle rośnie, jest również wskaźnikiem, że zasób systemowy jest ograniczony (z powodu ograniczania ujścia danych wyjściowych lub dużego procesora).
4.  Po ustaleniu limitów, które mogą dotrzeć do 6 zadań SU, można wykonać ekstrapolację liniowo pojemności przetwarzania zadania podczas dodawania większej liczby usług SUs, przy założeniu, że nie ma żadnych pochylania danych, które powodują, że niektóre partycje "gorąca".

> [!NOTE]
> Wybierz odpowiednią liczbę jednostek przesyłania strumieniowego: ponieważ Stream Analytics tworzy węzeł przetwarzania dla każdego z 6 dodanych SU, najlepiej jest określić liczbę węzłów jako dzielnik liczby partycji wejściowych, aby partycje mogły być równomiernie rozproszone w węzłach.
> Załóżmy na przykład, że dane zadanie SU można osiągnąć 4 MB/s, a liczba partycji wejściowych to 4. Możesz uruchomić zadanie z 12 SU, aby osiągnąć około 8 MB/s szybkości przetwarzania lub 24 SU do osiągnięcia 16 MB/s. Następnie można określić, kiedy ma zostać zwiększony numer SU dla zadania, do jakiej wartości jako funkcja stawki wejściowej.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Przypadek 2 — Jeśli zapytanie nie jest zaskakująco równolegle.
Jeśli zapytanie nie jest zaskakująco równolegle, możesz wykonać następujące czynności.
1.  Zacznij od zapytania bez **partycji** , aby uniknąć złożoności partycjonowania, a następnie uruchom zapytanie z 6 Su, aby zmierzyć maksymalne obciążenie tak, jak w [przypadku 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  W przypadku osiągnięcia przewidywanego obciążenia w zakresie przepływności można wykonać te czynności. Alternatywnie możesz zdecydować się na zmierzenie tego samego zadania uruchomionego o godzinie 3 i 1 SU, aby dowiedzieć się, jaka jest minimalna liczba identyfikatorów SU, które działają w danym scenariuszu.
3.  Jeśli nie można osiągnąć żądanej przepływności, spróbuj przerwać zapytanie w wielu krokach, jeśli jest to możliwe, jeśli nie ma już wielu kroków i Przydziel do 6 SU dla każdego kroku w zapytaniu. Na przykład jeśli masz 3 kroki, Przydziel 18-SU w opcji "Skaluj".
4.  Podczas uruchamiania takiego zadania Stream Analytics każdy krok jest umieszczany w osobnym węźle z dedykowanym 6 zasobami SU. 
5.  Jeśli nadal nie osiągnięto celu ładowania, możesz spróbować użyć **partycji** , rozpoczynając od kroków zbliżonych do danych wejściowych. Dla operatora **Grupuj według** , który nie może być naturalnie partycjonowany, można użyć wzorca zagregowanego lokalnego/globalnego do wykonywania partycjonowanej **grupy** , po której następuje niepartycjonowana **Grupa według**. Na przykład, jeśli chcesz policzyć liczbę samochodów przechodzących przez każdy z nich, co 3 minuty, a ilość danych przekracza to, co może być obsługiwane przez 6 SU.

Zapytanie:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
W powyższym zapytaniu są zliczane samochody na każdą partycję, a następnie dodając licznik ze wszystkich partycji jednocześnie.

Po podpisaniu na partycje, dla każdej partycji kroku, Przydziel do 6 SU, każda partycja o 6 SU jest maksymalna, więc każda partycja może zostać umieszczona w jego własnym węźle przetwarzania.

> [!Note]
> Jeśli zapytania nie można podzielić na partycje, dodanie dodatkowych wartości SU w zapytaniu wieloetapowym może nie zawsze zwiększyć przepływność. Jednym ze sposobów na uzyskanie wydajności jest zredukowanie ilości początkowej czynności przy użyciu lokalnego/globalnego wzorca agregacji, zgodnie z powyższym opisem w kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Przypadek 3 — w zadaniu jest uruchomionych wiele niezależnych zapytań.
W przypadku niektórych przypadków użycia niezależnego dostawcy oprogramowania, w przypadku których jest bardziej opłacalne przetwarzanie danych z wielu dzierżawców w jednym zadaniu przy użyciu oddzielnych danych wejściowych i wyjściowych dla każdej dzierżawy, może się okazać, że w jednym zadaniu jest uruchomionych bardzo kilka (na przykład 20) niezależnych zapytań. Założenie to każde obciążenie tego podzapytania jest stosunkowo małe. W takim przypadku można wykonać następujące czynności.
1.  W tym przypadku nie należy używać **partycji według** zapytania
2.  Zmniejsz liczbę partycji wejściowych do najmniejszej możliwej wartości 2, jeśli używasz centrum zdarzeń.
3.  Uruchom zapytanie z 6 SU. Przy oczekiwanym obciążeniu dla każdego podzapytania Dodaj możliwie jak najwięcej takich zapytań, aż do momentu, gdy zadanie zakończy limity zasobów systemowych. Jeśli tak się stanie, zapoznaj się z [przypadkiem 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) .
4.  Po osiągnięciu powyższego limitu podzapytania należy rozpocząć dodawanie podzapytania do nowego zadania. Liczba zadań do uruchomienia jako funkcja liczby niezależnych zapytań powinna być dość liniowa, przy założeniu, że nie ma żadnego pochylenia obciążenia. Następnie można prognozować liczbę zadań SU, które należy uruchomić jako funkcję liczby dzierżawców, które chcesz obsłużyć.
5.  W przypadku używania dołączania danych referencyjnych z takimi zapytaniami przed łączeniem z tymi samymi danymi referencyjnymi należy łączyć dane wejściowe. Następnie Podziel zdarzenia w razie potrzeby. W przeciwnym razie każde sprzężenie danych referencyjnych przechowuje kopię danych referencyjnych w pamięci, co sprawia, że nie jest to konieczne.

> [!Note] 
> Ile dzierżawców ma zostać umieszczonych w każdym zadaniu?
> Ten wzorzec zapytania często zawiera dużą liczbę podzapytań i skutkuje bardzo dużą i złożoną topologią. Kontroler zadania może nie być w stanie obsłużyć takiej dużej topologii. Zgodnie z zasadą kciuka można utrzymywać w obszarze 40 dzierżaw za 1 zadanie SU i 60 dzierżawy dla 3 zadań Su i 6. W przypadku przekroczenia pojemności kontrolera zadanie nie zostanie uruchomione pomyślnie.



## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/