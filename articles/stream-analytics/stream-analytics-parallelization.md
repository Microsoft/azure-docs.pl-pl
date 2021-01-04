---
title: Korzystanie z przetwarzanie równoległe zapytań i skalowanie w Azure Stream Analytics
description: W tym artykule opisano sposób skalowania Stream Analytics zadań przez skonfigurowanie partycji wejściowych, dostrajanie definicji zapytania i ustawienie jednostek przesyłania strumieniowego zadań.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 326af3bc38ce70cc7cb205384bb4302c5ff73d28
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704184"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Korzystanie z przetwarzanie równoległe zapytań w Azure Stream Analytics
W tym artykule pokazano, jak korzystać z przetwarzanie równoległe w Azure Stream Analytics. Dowiesz się, jak skalować Stream Analytics zadania przez skonfigurowanie partycji wejściowych i dostrajanie definicji zapytania analitycznego.
Jako warunek wstępny warto zapoznać się z pojęciem jednostki przesyłania strumieniowego opisanej w temacie [Omówienie i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jakie są części zadania Stream Analytics?
Definicja zadania Stream Analytics obejmuje co najmniej jedno dane wejściowe przesyłania strumieniowego, zapytanie i dane wyjściowe. Dane wejściowe to miejsce, z którego zadanie odczytuje strumień danych. Zapytanie służy do przekształcania strumienia danych wejściowych, a dane wyjściowe to miejsce, w którym zadanie wysyła wyniki zadania do.

## <a name="partitions-in-inputs-and-outputs"></a>Partycje w danych wejściowych i wyjściowych
Partycjonowanie pozwala podzielić dane na podzestawy w oparciu o [klucz partycji](../event-hubs/event-hubs-scalability.md#partitions). Jeśli dane wejściowe (na przykład Event Hubs) są podzielone na partycje za pomocą klucza, zdecydowanie zaleca się określenie tego klucza partycji podczas dodawania danych wejściowych do zadania Stream Analytics. Skalowanie zadania Stream Analytics korzysta z partycji w danych wejściowych i wyjściowych. Zadanie Stream Analytics może zużywać i zapisywać różne partycje równolegle, co zwiększa przepływność. 

### <a name="inputs"></a>Dane wejściowe
Wszystkie Azure Stream Analytics dane wejściowe mogą korzystać z partycjonowania:
-   EventHub (należy jawnie ustawić klucz partycji za pomocą słowa kluczowego PARTITION BY, jeśli jest używany poziom zgodności 1,1 lub niższy)
-   IoT Hub (należy jawnie ustawić klucz partycji za pomocą słowa kluczowego PARTITION BY, jeśli jest używany poziom zgodności 1,1 lub niższy)
-   Blob Storage

### <a name="outputs"></a>Dane wyjściowe

Podczas pracy z Stream Analytics można korzystać z partycjonowania w danych wyjściowych:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabela platformy Azure
-   BLOB Storage (można jawnie ustawić klucz partycji)
-   Cosmos DB (należy jawnie ustawić klucz partycji)
-   Event Hubs (należy jawnie ustawić klucz partycji)
-   IoT Hub (należy jawnie ustawić klucz partycji)
-   Service Bus
- SQL i Azure Synapse Analytics z opcjonalnym partycjonowaniem: więcej informacji można znaleźć na [stronie dane wyjściowe do Azure SQL Database](./stream-analytics-sql-output-perf.md).

Power BI nie obsługuje partycjonowania. Jednak nadal można podzielić na partycje dane wejściowe zgodnie z opisem w [tej sekcji](#multi-step-query-with-different-partition-by-values) . 

Aby uzyskać więcej informacji o partycjach, zobacz następujące artykuły:

* [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](/azure/architecture/best-practices/data-partitioning) (Partycjonowanie danych)


## <a name="embarrassingly-parallel-jobs"></a>Zaskakująco zadania równoległe
Zadanie *równoległe zaskakująco* to najbardziej skalowalny scenariusz w Azure Stream Analytics. Łączy jedną partycję danych wejściowych z jednym wystąpieniem zapytania na jedną partycję danych wyjściowych. Ten równoległy port ma następujące wymagania:

1. Jeśli logika zapytań zależy od tego samego klucza, który jest przetwarzany przez to samo wystąpienie zapytania, należy się upewnić, że zdarzenia przechodzą do tej samej partycji danych wejściowych. W przypadku Event Hubs lub IoT Hub oznacza to, że dane zdarzenia muszą mieć ustawioną wartość **PartitionKey** . Alternatywnie można korzystać z podzielonych nadawców. W przypadku usługi BLOB Storage oznacza to, że zdarzenia są wysyłane do tego samego folderu partycji. Przykładem może być wystąpienie zapytania, które agreguje dane według identyfikatora użytkownika, w którym dane wejściowe centrum zdarzeń jest partycjonowane przy użyciu identyfikatora użytkownika jako klucza partycji. Jeśli jednak logika zapytania nie wymaga tego samego klucza do przetworzenia przez to samo wystąpienie zapytania, można zignorować to wymaganie. Przykładem tej logiki będzie prosta kwerenda SELECT-Project-Filter.  

2. Następnym krokiem jest to, aby zapytanie zostało podzielone na partycje. W przypadku zadań o poziomie zgodności 1,2 lub wyższym (zalecane) kolumna niestandardowa może być określona jako klucz partycji w ustawieniach wejściowych, a zadanie zostanie paralellized automatycznie. Zadania o poziomie zgodności 1,0 lub 1,1 wymagają użycia **partycji o** identyfikatorze PartitionID we wszystkich krokach zapytania. Dozwolone jest wykonanie wielu kroków, ale wszystkie muszą być partycjonowane według tego samego klucza. 

3. Większość danych wyjściowych obsługiwanych w Stream Analytics może korzystać z partycjonowania. W przypadku użycia typu wyjściowego, który nie obsługuje partycjonowania zadania nie będzie *zaskakująco równolegle*. W przypadku danych wyjściowych centrum zdarzeń upewnij się, że **kolumna klucza partycji** jest ustawiona na ten sam klucz partycji, który jest używany w zapytaniu. Więcej informacji można znaleźć w [sekcji Output (dane wyjściowe](#outputs) ).

4. Liczba partycji wejściowych musi być równa liczbie partycji wyjściowych. Dane wyjściowe magazynu obiektów BLOB mogą obsługiwać partycje i dziedziczyć schemat partycjonowania zapytania nadrzędnego. W przypadku określenia klucza partycji dla magazynu obiektów BLOB dane są partycjonowane na partycję wejściową, w związku z czym wynik jest nadal w pełni równoległy. Poniżej przedstawiono przykłady wartości partycji, które zezwalają na w pełni równoległe zadanie:

   * 8 partycji wejściowych centrum zdarzeń i 8 partycji wyjściowych centrum zdarzeń
   * 8 partycji wejściowych centrum zdarzeń i danych wyjściowych magazynu obiektów BLOB
   * 8 partycji wejściowych centrum zdarzeń i danych wyjściowych magazynu obiektów BLOB partycjonowanych przez pole niestandardowe z dowolną kardynalnością
   * 8 partycji wejściowych i danych wyjściowych magazynu obiektów BLOB
   * 8 partycji wejściowych magazynu obiektów blob i 8 partycji wyjściowych centrum zdarzeń

W poniższych sekcjach omówiono niektóre przykładowe scenariusze, które są zaskakująco równolegle.

### <a name="simple-query"></a>Proste zapytanie

* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: centrum zdarzeń z 8 partycjami (kolumna klucza partycji musi mieć ustawioną wartość "PartitionId")

Zapytanie:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

To zapytanie jest prostym filtrem. W związku z tym nie trzeba martwić się o Partycjonowanie danych wejściowych wysyłanych do centrum zdarzeń. Zwróć uwagę, że zadania o poziomie zgodności przed 1,2 muszą zawierać klauzulę **Partition by PartitionID** , dlatego spełnia wymagania #2 ze starszych wersji. W przypadku danych wyjściowych musimy skonfigurować dane wyjściowe centrum zdarzeń w zadaniu, aby klucz partycji miał ustawioną wartość **PartitionID**. Należy upewnić się, że liczba partycji wejściowych jest równa liczbie partycji wyjściowych.

### <a name="query-with-a-grouping-key"></a>Zapytanie z kluczem grupowania

* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Magazyn obiektów BLOB

Zapytanie:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

To zapytanie ma klucz grupowania. W związku z tym zdarzenia zgrupowane razem muszą być wysyłane do tej samej partycji centrum zdarzeń. Ponieważ w tym przykładzie grupujemy według TollBoothID, należy się upewnić, że TollBoothID jest używany jako klucz partycji, gdy zdarzenia są wysyłane do centrum zdarzeń. Następnie w ASA można użyć **partycji według identyfikatora PartitionID** do dziedziczenia z tego schematu partycji i włączenia pełnej przetwarzanie równoległe. Ponieważ dane wyjściowe to magazyn obiektów blob, nie musimy martwić się o skonfigurowanie wartości klucza partycji zgodnie z wymogami #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Przykładowe scenariusze, które *nie* są zaskakująco równoległe

W poprzedniej sekcji wykryto pewne scenariusze równoległe zaskakująco. W tej sekcji omówiono scenariusze, które nie spełniają wszystkich wymagań, które należy zaskakująco równolegle. 

### <a name="mismatched-partition-count"></a>Niezgodna liczba partycji
* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: centrum zdarzeń z partycjami 32

Jeśli liczba partycji wejściowych nie jest zgodna z liczbą partycji wyjściowych, topologia nie jest zaskakująco równolegle niezależnie od zapytania. Jednak nadal możemy uzyskać pewien poziom lub przetwarzanie równoległe.

### <a name="query-using-non-partitioned-output"></a>Zapytanie przy użyciu danych wyjściowych niepodzielonych na partycje
* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Power BI

Dane wyjściowe Power BI nie obsługują obecnie partycjonowania. W związku z tym ten scenariusz nie jest zaskakująco równolegle.

### <a name="multi-step-query-with-different-partition-by-values"></a>Zapytanie wieloetapowe z inną PARTYCJą według wartości
* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: centrum zdarzeń z 8 partycjami
* Poziom zgodności: 1,0 lub 1,1

Zapytanie:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Jak widać, drugi krok używa **TollBoothId** jako klucza partycjonowania. Ten krok nie jest taki sam jak pierwszy krok i w związku z tym wymaga wykonania losowo. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Zapytanie wieloetapowe z inną PARTYCJą według wartości
* Dane wejściowe: centrum zdarzeń z 8 partycjami
* Dane wyjściowe: centrum zdarzeń z 8 partycjami (kolumna klucza partycji musi mieć ustawioną wartość "TollBoothId")
* Poziom zgodności — 1,2 lub nowszy

Zapytanie:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Poziom zgodności 1,2 lub wyższy domyślnie włącza równoległe wykonywanie zapytań. Na przykład zapytanie z poprzedniej sekcji zostanie podzielone na partycje, tak długo, jak kolumna "TollBoothId" jest ustawiona jako klucz partycji wejściowej. Klauzula PARTITION BY PartitionId nie jest wymagana.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Oblicz maksymalną liczbę jednostek przesyłania strumieniowego zadania
Łączna liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie Stream Analytics, zależy od liczby kroków zapytania zdefiniowanego dla zadania oraz liczby partycji dla każdego kroku.

### <a name="steps-in-a-query"></a>Kroki w zapytaniu
Zapytanie może zawierać jeden lub wiele kroków. Każdy krok jest podzapytaniem zdefiniowanym przez słowo kluczowe **with** . Zapytanie, które znajduje się poza słowem kluczowym **with** (tylko jedno zapytanie), jest również zliczane jako krok, taki jak instrukcja **SELECT** w następującej kwerendzie:

Zapytanie:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

To zapytanie zawiera dwa kroki.

> [!NOTE]
> To zapytanie jest omówione bardziej szczegółowo w dalszej części artykułu.
>  

### <a name="partition-a-step"></a>Podziel krok na partycje
Partycjonowanie krok wymaga spełnienia następujących warunków:

* Źródło danych wejściowych musi być podzielone na partycje. 
* Instrukcja **SELECT** zapytania musi odczytywać ze partycjonowanego źródła danych wejściowych.
* Zapytanie w ramach kroku musi zawierać słowo kluczowe **Partition by** .

Podczas partycjonowania zapytania zdarzenia wejściowe są przetwarzane i agregowane w oddzielnych grupach partycji, a zdarzenia wyjściowe są generowane dla każdej grupy. Jeśli chcesz połączyć łączną wartość zagregowaną, musisz utworzyć drugi krok niepodzielony na partycje do agregacji.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Oblicz maksymalną liczbę jednostek przesyłania strumieniowego dla zadania
Wszystkie kroki, które nie są podzielone na partycje, można skalować do sześciu jednostek przesyłania strumieniowego (SUs) dla zadania Stream Analytics. Oprócz tego można dodać 6 usług SUs dla każdej partycji w kroku partycjonowanym.
**Przykłady** można znaleźć w poniższej tabeli.

| Zapytanie                                               | Maksymalna liczba usług SUs dla zadania |
| --------------------------------------------------- | ------------------- |
| <ul><li>Zapytanie zawiera jeden krok.</li><li>Krok nie jest podzielony na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielony na partycje 16.</li><li>Zapytanie zawiera jeden krok.</li><li>Ten krok jest podzielony na partycje.</li></ul> | 96 (6 * 16 partycji) |
| <ul><li>Zapytanie składa się z dwóch kroków.</li><li>Żaden z kroków nie jest podzielony na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielony na 3.</li><li>Zapytanie składa się z dwóch kroków. Krok wejściowy jest podzielony na partycje, a drugi krok nie jest.</li><li>Instrukcja <strong>SELECT</strong> odczytuje z partycjonowanego danych wejściowych.</li></ul> | 24 (18 dla kroków partycjonowanych + 6 dla kroków niepodzielonych na partycje |

### <a name="examples-of-scaling"></a>Przykłady skalowania

Poniższe zapytanie oblicza liczbę samochodów w ramach okna z trzema chwilami przechodzących przez stację opłat, która ma trzy tollbooths. To zapytanie można skalować do sześciu usług SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Aby można było użyć więcej usług SUs dla zapytania, zarówno strumień danych wejściowych, jak i zapytanie muszą zostać podzielona na partycje. Ponieważ partycja strumienia danych jest ustawiona na 3, następujące zmodyfikowane zapytanie można skalować do 18 usług SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

W przypadku partycjonowania zapytania zdarzenia wejściowe są przetwarzane i agregowane w oddzielnych grupach partycji. Zdarzenia wyjściowe są również generowane dla każdej grupy. Partycjonowanie może spowodować pewne nieoczekiwane wyniki, jeśli pole **Grupuj według** nie jest kluczem partycji w strumieniu danych wejściowych. Na przykład pole **TollBoothId** w powyższym zapytaniu nie jest kluczem partycji **Input1**. W efekcie dane z TollBooth #1 mogą być rozłożone w wielu partycjach.

Każda partycja **Input1** zostanie przetworzona osobno Stream Analytics. W związku z tym zostanie utworzony wiele rekordów liczby samochodów dla tego samego Tollbooth w tym samym oknie wirowania. Jeśli nie można zmienić klucza partycji wejściowej, ten problem można rozwiązać, dodając krok niepartycjonowany do wartości zagregowanych w partycjach, jak w poniższym przykładzie:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

To zapytanie można skalować do 24 usług SUs.

> [!NOTE]
> W przypadku sprzęgania dwóch strumieni upewnij się, że strumienie są podzielone na partycje za pomocą klucza partycji kolumny, która jest używana do tworzenia sprzężeń. Upewnij się również, że istnieje taka sama liczba partycji w obu strumieniach.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Osiąganie większej przepływności na dużą skalę

Zadanie [równoległe zaskakująco](#embarrassingly-parallel-jobs) jest niezbędne, ale nie jest wystarczające, aby utrzymać wyższą przepływność na dużą skalę. Każdy system przechowywania i odpowiadający mu Stream Analytics dane wyjściowe mają różne informacje na temat sposobu osiągnięcia najlepszej możliwej przepustowości zapisu. Podobnie jak w przypadku dowolnego scenariusza w skali, istnieją pewne wyzwania, które można rozwiązać za pomocą odpowiednich konfiguracji. W tej sekcji omówiono konfiguracje kilku typowych danych wyjściowych i przedstawiono przykłady dla pozyskania szybkości pozyskiwania, 5 K i 10 000 zdarzeń na sekundę.

Poniższe obserwacje wykorzystują zadanie Stream Analytics z zapytaniem bezstanowym (Passthrough), czyli podstawowym formatem UDF języka JavaScript, który zapisuje w centrum zdarzeń, Azure SQL DB lub Cosmos DB.

#### <a name="event-hub"></a>Centrum zdarzeń

|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|--------|---------|---------|
| 1 tys.     |    1    |  2 JEDNOSTEK PRZEPŁYWNOŚCI   |
| 5 tys.     |    6    |  6 JEDNOSTEK PRZEPŁYWNOŚCI   |
| 10 tys.    |    12   |  10 JEDNOSTEK PRZEPŁYWNOŚCI  |

Rozwiązanie [centrum zdarzeń](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) skaluje się liniowo w zakresie jednostek przesyłania strumieniowego (Su) i przepływność, co sprawia, że jest to najbardziej wydajne i wydajny sposób analizowania i przesyłania strumieniowego danych poza Stream Analytics. Zadania można skalować do 192 SU, co w przybliżeniu tłumaczy na przetwarzanie do 200 MB/s lub 19 000 000 000 000 zdarzeń dziennie.

#### <a name="azure-sql"></a>Azure SQL
|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|---------|------|-------|
|    1 tys.   |   3  |  S3   |
|    5 tys.   |   18 |  P4   |
|    10 tys.  |   36 |  P6   |

[Usługa Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql)  obsługuje pisanie równoległe, zwane dziedziczeniem partycjonowania, ale nie jest domyślnie włączona. Jednak włączenie dziedziczenia partycjonowania oraz w pełni równoległe zapytanie może być niewystarczające do osiągnięcia większej przepływności. Przepływność zapisu SQL zależy znacznie od konfiguracji bazy danych i schematu tabeli. Artykuł dotyczący [wydajności danych wyjściowych SQL](./stream-analytics-sql-output-perf.md) zawiera więcej szczegółów na temat parametrów, które mogą zmaksymalizować przepływność zapisu. Jak wskazano w [Azure Stream Analytics dane wyjściowe do Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artykułu, to rozwiązanie nie jest skalowane liniowo jako w pełni równoległe potoki powyżej 8 partycji i może wymagać ponownego partycjonowania danych wyjściowych SQL (zobacz [do](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Jednostki SKU w warstwie Premium są konieczne w celu utrzymania wysokich stawek we/wy wraz z obciążeniem kopii zapasowych dziennika co kilka minut.

#### <a name="cosmos-db"></a>Cosmos DB
|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|-------|-------|---------|
|  1 tys.   |  3    | 20 000 RU  |
|  5 tys.   |  24   | 60K RU  |
|  10 tys.  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) dane wyjściowe z Stream Analytics zostały zaktualizowane w celu korzystania z natywnej integracji w ramach [poziomu zgodności 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Poziom zgodności 1,2 zapewnia znacznie większą przepływność i zmniejsza zużycie RU w porównaniu do 1,1, który jest domyślnym poziomem zgodności dla nowych zadań. Rozwiązanie używa kontenerów CosmosDB podzielonych na/deviceId, a pozostałe rozwiązanie jest skonfigurowane identycznie.

Wszystkie [przesyłane strumieniowo przykłady platformy Azure](https://github.com/Azure-Samples/streaming-at-scale) umożliwiają użycie centrum zdarzeń jako danych wejściowych, które są karmione przez klientów testowych z symulacją obciążenia. Każde zdarzenie wejściowe jest dokumentem JSON rozmiarze 1 KB, który umożliwia łatwe tłumaczenie skonfigurowanych stawek za pozyskiwanie przepływności (1 MB/s, 5 MB/s i 10 MB/s). Zdarzenia symulowania urządzenia IoT wysyłającego następujące dane JSON (w skróconej formie) dla maksymalnie 1000 urządzeń:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> Konfiguracje mogą ulec zmianie ze względu na różne składniki używane w rozwiązaniu. Aby uzyskać dokładniejsze oszacowanie, Dostosuj przykłady tak, aby pasowały do Twojego scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowanie wąskich gardeł

Użyj okienka metryki w zadaniu Azure Stream Analytics, aby identyfikować wąskie gardła w potoku. Przejrzyj **zdarzenia wejściowe/wyjściowe** dla przepływności i ["opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zaległe zdarzenia** , aby sprawdzić, czy zadanie jest zgodne z szybkością danych wejściowych. W przypadku metryk centrum zdarzeń należy poszukać **żądań z ograniczeniami** i odpowiednio dostosować jednostki progowe. W przypadku metryk Cosmos DB Sprawdź **maksymalną liczbę użytych jednostek ru/s na klucz partycji** w obszarze przepływność, aby upewnić się, że zakresy kluczy partycji są jednolicie używane. W przypadku usługi Azure SQL DB Monitoruj **operacje we/wy dziennika** i **procesora CPU**.

## <a name="get-help"></a>Uzyskiwanie pomocy

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