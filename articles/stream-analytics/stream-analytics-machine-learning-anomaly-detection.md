---
title: Wykrywanie anomalii w Azure Stream Analytics
description: W tym artykule opisano sposób używania Azure Stream Analytics i Azure Machine Learning ze sobą w celu wykrywania anomalii.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: c57a3920dac3e18e248109fafdf61fdfa871c54d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023401"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w Azure Stream Analytics

Dostępne zarówno w chmurze, jak i w Azure IoT Edge, Azure Stream Analytics oferuje wbudowane funkcje wykrywania anomalii oparte na uczeniu maszynowym, które mogą być używane do monitorowania dwóch najczęściej występujących anomalii: tymczasowych i trwałych. Za pomocą funkcji **AnomalyDetection_SpikeAndDip** i **AnomalyDetection_ChangePoint** można przeprowadzić wykrywanie anomalii bezpośrednio w zadaniu Stream Analytics.

Modele uczenia maszynowego zakładają jednolite próbkowanie szeregów czasowych. Jeśli serie czasowe nie są jednolite, można wstawić krok agregacji z oknem wirowania przed wywołaniem wykrywania anomalii.

Operacje uczenia maszynowego nie obsługują w tym momencie trendów sezonowości ani korelacji z variate.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Wykrywanie anomalii przy użyciu uczenia maszynowego w Azure Stream Analytics

Poniższy film wideo demonstruje sposób wykrywania anomalii w czasie rzeczywistym za pomocą funkcji uczenia maszynowego w Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Zachowanie modelu

Ogólnie dokładność modelu zwiększa się z większą ilością danych w oknie przesuwania. Dane w określonym oknie przesuwania są traktowane jako część jego normalnego zakresu wartości dla tego przedziału czasu. Model uwzględnia tylko historię zdarzeń w oknie przesuwania, aby sprawdzić, czy bieżące zdarzenie jest anomalią. W miarę przenoszenia okna przesuwania stare wartości są wykluczane z szkolenia modelu.

Funkcje te działają przez ustanowienie pewnego normalnego, w oparciu o to, co się stało. Elementy odstające są identyfikowane przez porównanie z ustaloną normalną, na poziomie ufności. Rozmiar okna powinien opierać się na minimalnych zdarzeniach wymaganych do uczenia modelu do normalnego zachowania, dzięki czemu w przypadku wystąpienia anomalii będzie on mógł go rozpoznać.

Czas odpowiedzi modelu zwiększa się wraz z rozmiarem historii, ponieważ musi on być porównywany z większą liczbą przeszłych zdarzeń. Zaleca się uwzględnienie tylko niezbędnej liczby zdarzeń w celu zapewnienia lepszej wydajności.

Przerwy w szeregach czasowych mogą być wynikiem modelu, który nie otrzymuje zdarzeń w określonych punktach w czasie. Ta sytuacja jest obsługiwana przez Stream Analytics przy użyciu logiki do przypisywania. Rozmiar historii, a także czas trwania dla tego samego przesuwanego okna jest używany do obliczania średniej szybkości, z jaką zdarzenia są oczekiwane.

W [tym miejscu](https://aka.ms/asaanomalygenerator) jest dostępny Generator anomalii, który umożliwia strumieniowe korzystanie z danych z różnymi wzorcami anomalii. Zadanie ASA można skonfigurować przy użyciu tych funkcji wykrywania anomalii do odczytu z tego Centrum IoT Hub i wykrywania anomalii.

## <a name="spike-and-dip"></a>Wartości skokowe i DIP

Tymczasowe anomalie w strumieniu zdarzeń szeregów czasowych są nazywane skokami i DIP. Wartości graniczne i wartości DIP można monitorować przy użyciu operatora Machine Learning, [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Przykład wzrostu i anomalii DIP](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

W tym samym przedziale, jeśli drugi skok jest mniejszy niż pierwszy, obliczona Ocena dla mniejszego stopnia jest prawdopodobnie nieistotna w porównaniu z wynikiem pierwszego wzrostu w określonym poziomie ufności. Możesz spróbować zmniejszyć poziom ufności modelu, aby wykryć takie anomalie. Jeśli jednak zaczniesz otrzymywać zbyt wiele alertów, możesz użyć wyższego interwału ufności.

Poniższe przykładowe zapytanie przyjmuje jednolitą stawkę danych wejściowych jednego zdarzenia na sekundę w 2-minutowym przedziale czasowym z historią 120 zdarzeń. Końcowa instrukcja SELECT wyodrębnia i wyprowadza wynik i stan anomalii z poziomem ufności równym 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Punkt zmiany

Trwałe anomalie w strumieniu zdarzeń szeregów czasowych są zmianami w dystrybucji wartości w strumieniu zdarzeń, takich jak zmiany poziomów i trendy. W Stream Analytics takie anomalie są wykrywane za pomocą operatora [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) opartego na Machine Learning.

Trwałe zmiany ostatnie znacznie dłużej niż skoki i DIP mogą wskazywać na katastrofalne zdarzenia. Trwałe zmiany nie są zwykle widoczne dla gołym okiem, ale mogą być wykrywane za pomocą operatora **AnomalyDetection_ChangePoint** .

Na poniższym obrazie przedstawiono przykład zmiany poziomu:

![Przykład anomalii zmiany poziomu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Na poniższej ilustracji przedstawiono przykład zmiany trendu:

![Przykład anomalii zmiany trendu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Poniższe przykładowe zapytanie przyjmuje jednolitą stawkę danych wejściowych jednego zdarzenia na sekundę w oknie przesuwania 20 minut z rozmiarem historii 1200 zdarzeń. Końcowa instrukcja SELECT wyodrębnia i wyprowadza wynik i stan anomalii z poziomem ufności równym 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Charakterystyki wydajności

Wydajność tych modeli zależy od rozmiaru historii, czasu trwania okna, obciążenia zdarzenia oraz tego, czy jest używane partycjonowanie poziomu funkcji. W tej sekcji omówiono te konfiguracje i przedstawiono przykłady, w których można utrzymywać stawki za pozyskiwanie, 5 K i 10 000 zdarzeń na sekundę.

* **Rozmiar historii** — te modele są wykonywane liniowo z **rozmiarem historii**. Im dłuższy rozmiar historii, tym dłużej modele trwają do oceny nowego zdarzenia. Wynika to z faktu, że modele porównują nowe zdarzenie z każdym z ostatnich zdarzeń w buforze historii.
* **Czas trwania okna** — **czas trwania okna** powinien odzwierciedlać, jak długo trwa odbieranie jak wielu zdarzeń określonych przez rozmiar historii. Bez tego, czy w oknie nie ma wielu zdarzeń, Azure Stream Analytics będzie mieć przypisane wartości. W związku z tym użycie procesora CPU jest funkcją rozmiaru historii.
* **Obciążenie zdarzeniami** — im większa **obciążenie zdarzeń**, tym większa jest szybkość działania wykonywanego przez modele, która ma wpływ na użycie procesora CPU. Zadanie można skalować w poziomie, dzięki czemu zaskakująco się równolegle, zakładając, że logika biznesowa będzie używać większej liczby partycji wejściowych.
* **Partycjonowanie**  -  poziomu funkcji **Partycjonowanie poziomu funkcji** odbywa się przy użyciu ```PARTITION BY``` w ramach wywołania funkcji wykrywania anomalii. Ten typ partycjonowania dodaje obciążenie, ponieważ stan musi być utrzymywany jednocześnie dla wielu modeli. Partycjonowanie na poziomie funkcji jest używane w scenariuszach takich jak partycjonowanie na poziomie urządzenia.

### <a name="relationship"></a>Relacja
Rozmiar historii, czas trwania okna oraz całkowite obciążenie zdarzeniami są powiązane w następujący sposób:

windowDuration (w MS) = 1000 * historySize/(całkowite zdarzenia wejściowe na sekundę/liczba partycji wejściowych)

Podczas partycjonowania funkcji za pomocą deviceId Dodaj "PARTITION BY deviceId" do wywołania funkcji wykrywania anomalii.

### <a name="observations"></a>Obserwacje
Poniższa tabela zawiera obserwacje przepływności dla pojedynczego węzła (6 SU) dla przypadku niepartycjonowanego:

| Rozmiar historii (zdarzenia) | Czas trwania okna (MS) | Całkowita liczba zdarzeń wejściowych na sekundę |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1 650 |
| 6000 | 10 910 | 1,100 |

Poniższa tabela zawiera obserwacje przepływności dla pojedynczego węzła (6 SU) dla podzielonego przypadku partycjonowania:

| Rozmiar historii (zdarzenia) | Czas trwania okna (MS) | Całkowita liczba zdarzeń wejściowych na sekundę | Liczba urządzeń |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1 091 | 1,100 | 10 |
| 600 | 10 910 | 1,100 | 10 |
| 6000 | 218 182 | <550 | 10 |
| 60 | 21 819 | 550 | 100 |
| 600 | 218 182 | 550 | 100 |
| 6000 | 2 181 819 | <550 | 100 |

Przykładowy kod do uruchamiania powyższej konfiguracji, która nie jest partycjonowana, znajduje się w [repozytorium przesyłania strumieniowego na](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) platformie Azure. Kod tworzy zadanie usługi Stream Analytics bez partycjonowania poziomu funkcji, które używa centrum zdarzeń jako danych wejściowych i wyjściowych. Ładowanie danych wejściowych jest generowane przy użyciu klientów testowych. Każde zdarzenie wejściowe jest dokumentem JSON rozmiarze 1 KB. Zdarzenia symulują wysyłanie danych JSON przez urządzenie IoT (dla maksymalnie 1000 urządzeń). Rozmiar historii, czas trwania okna i całkowite obciążenie zdarzeń są różne dla dwóch partycji wejściowych.

> [!Note]
> Aby uzyskać dokładniejsze oszacowanie, Dostosuj przykłady tak, aby pasowały do Twojego scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowanie wąskich gardeł
Użyj okienka metryki w zadaniu Azure Stream Analytics, aby identyfikować wąskie gardła w potoku. Przejrzyj **zdarzenia wejściowe/wyjściowe** dla przepływności i ["opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zaległe zdarzenia** , aby sprawdzić, czy zadanie jest zgodne z szybkością danych wejściowych. W przypadku metryk centrum zdarzeń należy poszukać **żądań z ograniczeniami** i odpowiednio dostosować jednostki progowe. W przypadku metryk Cosmos DB Sprawdź **maksymalną liczbę użytych jednostek ru/s na klucz partycji** w obszarze przepływność, aby upewnić się, że zakresy kluczy partycji są jednolicie używane. W przypadku usługi Azure SQL DB Monitoruj **operacje we/wy dziennika** i **procesora CPU**.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)