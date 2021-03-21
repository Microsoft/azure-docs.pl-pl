---
title: Typowe wzorce zapytań w Azure Stream Analytics
description: W tym artykule opisano kilka typowych wzorców zapytań i projektów, które są przydatne w Azure Stream Analytics zadaniach.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019996"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Typowe wzorce zapytań w Azure Stream Analytics

Zapytania w Azure Stream Analytics są wyrażane w języku zapytań przypominającym język SQL. Konstrukcje językowe są udokumentowane w podręczniku [Skorowidz języka zapytań Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) . 

W projekcie zapytania można jasno utworzyć prostą logikę przekazującą, aby przenieść dane zdarzenia z jednego strumienia wejściowego do wyjściowego magazynu danych. może to spowodować rozbudowane dopasowanie wzorców i analizy czasowej w celu obliczenia agregacji w różnych oknach czasu, jak w przypadku [tworzenia rozwiązania IoT za pomocą](stream-analytics-build-an-iot-solution-using-stream-analytics.md) przewodnika Stream Analytics. Można przyłączyć dane z wielu wejść do łączenia zdarzeń przesyłania strumieniowego, a także odszukać statyczne dane referencyjne w celu wzbogacania wartości zdarzeń. Możesz również zapisywać dane w wielu danych wyjściowych.

W tym artykule przedstawiono rozwiązania kilku typowych wzorców zapytań w oparciu o rzeczywiste scenariusze.

## <a name="supported-data-formats"></a>Obsługiwane formaty danych

Azure Stream Analytics obsługuje przetwarzanie zdarzeń w formatach danych CSV, JSON i Avro.

Zarówno kod JSON, jak i Avro mogą zawierać złożone typy, takie jak obiekty zagnieżdżone (rekordy) lub tablice. Aby uzyskać więcej informacji na temat pracy z tymi złożonymi typami danych, zapoznaj się z artykułem [Analizowanie danych JSON i Avro](stream-analytics-parsing-json.md) .

## <a name="send-data-to-multiple-outputs"></a>Wysyłanie danych do wielu wyjść

Można użyć wielu instrukcji **SELECT** do wyprowadzania danych do różnych ujścia danych wyjściowych. Na przykład jeden **wybór** może wyprowadzić alert oparty na progu, podczas gdy inny może generować zdarzenia do magazynu obiektów BLOB.

**Dane wejściowe**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**ArchiveOutput wyjściowy**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**AlertOutput wyjściowy**:

| Marka | Godzina | Liczba |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Zapytanie**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

Klauzula **into** informuje Stream Analytics, do których danych wyjściowych należy zapisywać dane. Pierwszy **wybór** definiuje kwerendę przekazującą, która odbiera dane z danych wejściowych i wysyła je do danych wyjściowych o nazwie **ArchiveOutput**. Drugie zapytanie wykonuje pewne proste agregacje i filtrowanie przed wysłaniem wyników do wyjściowego wyjścia systemu o nazwie **AlertOutput**.

Należy zauważyć, że klauzula **with** może służyć do definiowania wielu bloków podzapytań. W przypadku tej opcji można otworzyć mniejszą liczbę czytelników do źródła danych wejściowych.

**Zapytanie**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Aby uzyskać więcej informacji, zapoznaj się z [klauzulą **with**](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Proste zapytanie przekazywane

Proste zapytanie przekazywane może służyć do kopiowania danych strumienia wejściowego do danych wyjściowych. Na przykład, jeśli strumień danych zawierający informacje o pojeździe w czasie rzeczywistym muszą zostać zapisane w bazie danych SQL na potrzeby analizy liter, zadanie zostanie przetworzone przez proste zapytanie przekazujące.

**Dane wejściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Dane wyjściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Zapytanie**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Kwerenda **SELECT** * tworzy projekty wszystkich pól zdarzenia przychodzącego i wysyła je do danych wyjściowych. W ten sam sposób **Wybierz opcję** można również użyć do tylko pól wymaganych przez projekt z danych wejściowych. W tym przykładzie, jeśli *Marka* i *czas* są jedynymi wymaganymi polami do zapisania, te pola można określić w instrukcji **SELECT** .

**Dane wejściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Dane wyjściowe**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Zapytanie**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Ciąg pasujący do typu LIKE i NOT LIKE

**Podobnie jak** i **nie podoba** się, można użyć do sprawdzenia, czy pole pasuje do określonego wzorca. Na przykład można utworzyć filtr w celu zwrócenia tylko płyt z licencjami, które zaczynają się od litery "A" i kończą się cyfrą 9.

**Dane wejściowe**:

| Marka | License_plate | Godzina |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Dane wyjściowe**:

| Marka | License_plate | Godzina |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Zapytanie**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Użyj instrukcji **like** , aby sprawdzić wartość pola **License_plate** . Powinna zaczynać się literą "A", a następnie zawierać dowolny ciąg składający się z zero lub więcej znaków, kończący się cyfrą 9.

## <a name="calculation-over-past-events"></a>Obliczenia względem przeszłych zdarzeń

Funkcja **lag** może służyć do przeglądania przeszłych zdarzeń w przedziale czasu i porównywania ich z bieżącym zdarzeniem. Na przykład bieżące działanie samochodu może zostać wystawione, jeśli różni się od ostatniego samochodu, za pośrednictwem którego nastąpiło połączenie.

**Dane wejściowe**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Dane wyjściowe**:

| Marka | Godzina |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Zapytanie**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Użyj **opóźnienia** , aby wejść do strumienia wejściowego o jedno zdarzenie z powrotem, pobierając wartość *Utwórz* i porównując ją *z wartością w* bieżącym zdarzeniu i wyjściowym zdarzenia.

Aby uzyskać więcej informacji, zapoznaj się z [**opóźnieniem**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Zwróć ostatnie zdarzenie w oknie

Ponieważ zdarzenia są używane przez system w czasie rzeczywistym, nie istnieje funkcja, która może określić, czy zdarzenie będzie ostatnim, aby dotrzeć do tego okna. Aby to osiągnąć, strumień wejściowy musi być przyłączony do innego, gdzie czas zdarzenia jest maksymalny dla wszystkich zdarzeń w tym oknie.

**Dane wejściowe**:

| License_plate | Marka | Godzina |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Dane wyjściowe**:

| License_plate | Marka | Godzina |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Zapytanie**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Pierwszy krok zapytania znajduje maksymalną sygnaturę czasową w 10-minutowym systemie Windows, czyli sygnaturę czasową ostatniego zdarzenia dla tego okna. Drugi krok sprzęga wyniki pierwszego zapytania z oryginalnym strumieniem, aby znaleźć zdarzenie zgodne z ostatnimi sygnaturami czasowymi w poszczególnych oknach. 

**DateDiff** jest funkcją specyficzną dla daty, która porównuje i zwraca różnicę czasu między dwoma polami DateTime, aby uzyskać więcej informacji, zapoznaj się z [funkcjami daty](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Aby uzyskać więcej informacji na temat sprzęgania strumieni, zapoznaj się z tematem [**Join**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Agregacja danych w czasie

W celu obliczenia informacji w przedziale czasu dane można agregować jednocześnie. W tym przykładzie licznik jest obliczany w ciągu ostatnich 10 sekund czasu dla każdego określonego samochodu.

**Dane wejściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Dane wyjściowe**:

| Marka | Liczba |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Zapytanie**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Ta agregacja grupuje samochody według *marki* i liczy je co 10 sekund. Dane wyjściowe zawierają *Marka* i *liczbę* samochodów, za pomocą których nastąpiło połączenie.

TumblingWindow to funkcja okienkowa służąca do grupowania zdarzeń razem. Agregację można zastosować dla wszystkich pogrupowanych zdarzeń. Aby uzyskać więcej informacji, zobacz [Windowing Functions](stream-analytics-window-functions.md).

Aby uzyskać więcej informacji na temat agregacji, zobacz [funkcje agregujące](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Okresowe wartości wyjściowe

W przypadku nietypowych lub brakujących zdarzeń dane wyjściowe w regularnych odstępach czasu mogą być generowane na podstawie bardziej rozrzedzonych danych wejściowych. Na przykład Wygeneruj zdarzenie co 5 sekund, które raportuje ostatnio widziany punkt danych.

**Dane wejściowe**:

| Godzina | Wartość |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Dane wyjściowe (pierwsze 10 wierszy)**:

| Window_end | Last_event. Pierwszym | Last_event. Wartościami |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Zapytanie**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

To zapytanie generuje zdarzenia co 5 sekund i wyświetla ostatnie zdarzenie, które zostało odebrane wcześniej. Czas trwania **HOPPINGWINDOW** określa, jak daleko odbędzie się zapytanie, aby znaleźć najnowsze zdarzenie.

Aby uzyskać więcej informacji, zapoznaj się z [oknem przeskoku](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Skorelowanie zdarzeń w strumieniu

Zdarzenia skorelowane w tym samym strumieniu mogą być wykonywane przez przeglądanie przeszłych zdarzeń przy użyciu funkcji **lag** . Na przykład dane wyjściowe można generować za każdym razem, gdy dwa kolejne samochody z tego *samego* przechodzą przez połączenie płatne przez ostatnie 90 sekund.

**Dane wejściowe**:

| Marka | License_plate | Godzina |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Dane wyjściowe**:

| Marka | Godzina | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Zapytanie**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

Funkcja **lag** może odszukać strumień wejściowy po jednym zdarzeniu i pobrać wartość *Make* , porównując ją z wartością *Make* bieżącego zdarzenia.  Po spełnieniu warunku dane z poprzedniego zdarzenia mogą być rzutowane przy użyciu **opóźnienia** w instrukcji **SELECT** .

Aby uzyskać więcej informacji, zapoznaj się z [opóźnieniem](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Wykrywanie czasu trwania między zdarzeniami

Czas trwania zdarzenia może być obliczany przez wyszukanie ostatniego zdarzenia uruchomienia po odebraniu zdarzenia końcowego. To zapytanie może być przydatne do określenia czasu poświęcanego użytkownikowi na stronę lub funkcję.

**Dane wejściowe**:  

| Użytkownik | Cecha | Zdarzenie | Godzina |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Rozpocznij |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000 Z |

**Dane wyjściowe**:  

| Użytkownik | Cecha | Czas trwania |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Zapytanie**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**Ostatnia** funkcja może służyć do pobierania ostatniego zdarzenia w określonym warunku. W tym przykładzie warunek to zdarzenie typu Start, partycjonowanie wyszukiwania według **partycji przez** użytkownika i funkcję. W ten sposób każdy użytkownik i funkcja jest traktowana niezależnie podczas wyszukiwania zdarzenia uruchomienia. **Limit czasu trwania** umożliwia przeszukiwanie z powrotem do 1 godziny między zdarzeniami końcowymi i początkowymi.

## <a name="count-unique-values"></a>Zlicz unikatowe wartości

**Liczba** i **różne** mogą służyć do zliczania wartości unikatowych pól, które pojawiają się w strumieniu w przedziale czasu. Zapytanie można utworzyć, aby obliczyć, *ile unikatowych* przepływów samochodów przechodzą przez połączenie płatne w 2-sekundowym oknie.

**Dane wejściowe**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Rozdzielczości**

| Count_make | Godzina |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Zapytanie:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Licznik (różne marki)** zwraca liczbę unikatowych wartości w kolumnie **Utwórz** w przedziale czasu.
Aby uzyskać więcej informacji, zapoznaj się z [funkcją agregującą **Count**](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Pobieranie pierwszego zdarzenia w oknie

Nie można użyć elementu **isfirst** do pobrania pierwszego zdarzenia w przedziale czasu. Na przykład umieszczanie pierwszego samochodu w każdym 10-minutowym przedziale czasu.

**Dane wejściowe**:

| License_plate | Marka | Godzina |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Dane wyjściowe**:

| License_plate | Marka | Godzina |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015 — 07-27T00:12:02.0000000 Z |

**Zapytanie**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**Isfirst** może również podzielić dane na partycje i obliczyć pierwsze zdarzenie do każdego *określonego samochodu,* co jest dostępne co 10 minut.

**Dane wyjściowe**:

| License_plate | Marka | Godzina |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Zapytanie**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Aby uzyskać więcej informacji, zobacz [**isfirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Usuń zduplikowane zdarzenia w oknie

Podczas wykonywania operacji, takich jak Obliczanie średniej wartości dla zdarzeń w danym przedziale czasu, należy filtrować powtarzające się zdarzenia. W poniższym przykładzie drugie zdarzenie jest duplikatem pierwszego.

**Dane wejściowe**:  

| DeviceId | Godzina | Atrybut | Wartość |
| --- | --- | --- | --- |
| 1 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 1 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 2 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |40 |
| 1 |2018 R-07-27T00:00:05.0000000 Z |Temperatura |60 |
| 2 |2018 R-07-27T00:00:05.0000000 Z |Temperatura |50 |
| 1 |2018 R-07-27T00:00:10.0000000 Z |Temperatura |100 |

**Dane wyjściowe**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Zapytanie**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Licznik (DISTINCT Time)** zwraca liczbę unikatowych wartości w kolumnie Time w przedziale czasu. Dane wyjściowe pierwszego kroku można następnie użyć do obliczenia średniej dla każdego urządzenia, przez odrzucenie duplikatów.

Aby uzyskać więcej informacji, zobacz [Count (DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Określanie logiki dla różnych przypadków/wartości (instrukcje CASE)

Instrukcje **Case** mogą udostępniać różne obliczenia dla różnych pól w oparciu o określone kryterium. Na przykład Przypisz Tor "A" do samochodów *Make1* i Lane "B" do innych.

**Dane wejściowe**:

| Marka | Godzina |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Dane wyjściowe**:

| Marka |Dispatch_to_lane | Godzina |
| --- | --- | --- |
| Make1 |Z |2015-01-01T00:00:01.0000000 Z |
| Make2 |B |2015-01-01T00:00:02.0000000 Z |

**Rozwiązanie 2.**

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

Wyrażenie **Case** porównuje wyrażenie z zestawem prostych wyrażeń, aby określić jego wynik. W tym przykładzie pojazdy *Make1* są wysyłane do toru "A", a wszystkie inne marki mają przypisany Tor "B".

Aby uzyskać więcej informacji, zobacz [wyrażenie CASE](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Konwersja danych

Dane mogą być rzutowane w czasie rzeczywistym za pomocą metody **Cast** . Na przykład wagi samochodu mogą być konwertowane z typu **nvarchar (max)** na typ **bigint** i używane w obliczeniach liczbowych.

**Dane wejściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Dane wyjściowe**:

| Marka | Waga |
| --- | --- |
| Make1 |3000 |

**Zapytanie**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Użyj instrukcji **Cast** , aby określić typ danych. Zapoznaj się z listą obsługiwanych typów danych w [typach danych (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Aby uzyskać więcej informacji na temat [funkcji konwersji danych](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Wykrywanie czasu trwania warunku

W przypadku warunków obejmujących wiele zdarzeń funkcja **lag** może służyć do identyfikowania czasu trwania tego warunku. Załóżmy na przykład, że usterka spowodowała, że wszystkie samochody mają niepoprawną wagę (powyżej 20 000 funtów), a czas trwania tego błędu musi być obliczany.

**Dane wejściowe**:

| Marka | Godzina | Waga |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25 000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25 000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25 000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Dane wyjściowe**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Zapytanie**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Pierwsza instrukcja **SELECT** korelacji bieżącym pomiarem wagi z poprzednią pomiarem, projekcję razem z bieżącą pomiar. Druga **opcja** powoduje powracanie do ostatniego zdarzenia, gdzie *previous_weight* jest mniejsza niż 20000, gdzie bieżąca waga jest mniejsza niż 20000, a *previous_weight* bieżącego zdarzenia był większy niż 20000.

End_fault to bieżące niewadliwe zdarzenie, w przypadku którego poprzednie zdarzenie było nieuszkodzone, a Start_fault to ostatnie niewadliwe zdarzenie.

## <a name="process-events-with-independent-time-substreams"></a>Przetwarzaj zdarzenia o niezależnym czasie (w podstrumieniach)

Zdarzenia mogą być opóźnione lub nieaktualne z powodu pochylenia zegara między producentami zdarzeń, nachylenia zegara między partycjami lub opóźnienia sieci.
Na przykład zegar urządzenia dla *TollID* 2 ma pięć sekund w tle *TollID* 1, a zegar urządzenia dla *TollID* 3 to dziesięć sekund za *TollID* 1. Obliczenia mogą być wykonywane niezależnie dla każdego naliczania opłat, biorąc pod uwagę tylko własne dane zegara jako sygnaturę czasową.

**Dane wejściowe**:

| LicensePlate | Marka | Godzina | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Dane wyjściowe**:

| TollID | Liczba |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Zapytanie**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

Klauzula **timestamp over by** sprawdza na każdej osi czasu niezależnie od tego, czy są używane podstrumienie. Zdarzenie wyjściowe dla każdego *TollID* jest generowane w miarę ich obliczania, co oznacza, że zdarzenia są wykonywane w kolejności w odniesieniu do każdego *TollIDu* , a nie w kolejności, w jakiej wszystkie urządzenia były w tym samym zegarze.

Aby uzyskać więcej informacji, zapoznaj się z [sygnaturą czasową](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Okna sesji

Okno sesji to okno, które ciągle rozszerza się po wystąpieniu zdarzeń i jest zamykane w celu obliczenia, jeśli nie otrzymano żadnego zdarzenia po upływie określonego czasu lub gdy okno osiągnie maksymalny czas trwania.
To okno jest szczególnie przydatne podczas obliczania danych interakcji użytkownika. Okno jest uruchamiane, gdy użytkownik rozpoczyna pracę z systemem i zamyka się, gdy nie zaobserwowano więcej zdarzeń, co oznacza, że użytkownik zatrzymał działanie.
Na przykład użytkownik korzysta z strony sieci Web, w której zarejestrowano liczbę kliknięć, a okno sesji może służyć do określenia, jak długo użytkownik współdziała z lokacją.

**Dane wejściowe**:

| User_id | Godzina | Adres URL |
| --- | --- | --- |
| 0 | 2017 — 01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017 — 01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017 — 01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017 — 01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017 — 01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Dane wyjściowe**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017 — 01-26T00:00:00.0000000 Z | 2017 — 01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017 — 01-26T00:00:55.0000000 Z | 2017 — 01-26T00:01:15.0000000 Z | 20 |

**Zapytanie**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**Wybierz** projekty, które są istotne dla interakcji użytkownika, wraz z czasem trwania interakcji. Grupowanie danych według użytkownika i **SessionWindow** , które są zamykane w przypadku braku interakcji w ciągu 1 minuty, o maksymalnym rozmiarze okna wynoszącym 60 minut.

Aby uzyskać więcej informacji na temat **SessionWindow**, zobacz [okno sesji](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Rozszerzalność języka z funkcją zdefiniowaną przez użytkownika w językach JavaScript i C #

Język zapytań Azure Stream Analytics można rozszerzyć za pomocą funkcji niestandardowych zapisanych w języku JavaScript lub C#. Funkcje zdefiniowane przez użytkownika (UDF) to niestandardowe/złożone obliczenia, których nie można łatwo wyrazić przy użyciu języka **SQL** . Te UDF można definiować raz i wielokrotnie używać w ramach zapytania. Na przykład można użyć funkcji UDF do przekonwertowania szesnastkowej wartości *nvarchar (max)* na wartość *bigint* .

**Dane wejściowe**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | 11b |
| 3 | "121" |

**Dane wyjściowe**:

| Device_id | Liczba dziesiętna |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Funkcja zdefiniowana przez użytkownika będzie obliczać wartość *bigint* z HexValue przy każdym wykorzystanym zdarzeniu.

Aby uzyskać więcej informacji, zobacz [JavaScript](./stream-analytics-javascript-user-defined-functions.md) i [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Zaawansowane dopasowywanie do wzorca przy użyciu MATCH_RECOGNIZE

**MATCH_RECOGNIZE** jest zaawansowanym mechanizmem dopasowywania wzorców, który może służyć do dopasowania sekwencji zdarzeń do dobrze zdefiniowanego wzorca wyrażenia regularnego.
Na przykład usługa ATM jest monitorowana w czasie rzeczywistym w przypadku awarii w trakcie działania sieci ATM, jeśli istnieją dwa kolejne komunikaty ostrzegawcze, których administrator musi zostać powiadomiony.

**Dane wejściowe**:

| ATM_id | Operation_id | Return_Code | Godzina |
| --- | --- | --- | --- |
| 1 | "Wprowadzanie numeru PIN" | Prawnego | 2017 — 01-26T00:10:00.0000000 Z |
| 2 | "Otwieranie gniazda pieniężnego" | Prawnego | 2017 — 01-26T00:10:07.0000000 Z |
| 2 | "Zamykające gniazdo pieniężne" | Prawnego | 2017 — 01-26T00:10:11.0000000 Z |
| 1 | "Wprowadzanie wycofania ilości" | Prawnego | 2017 — 01-26T00:10:08.0000000 Z |
| 1 | "Otwieranie gniazda pieniężnego" | Wyświetlania | 2017 — 01-26T00:10:14.0000000 Z |
| 1 | "Drukowanie salda banku" | Wyświetlania | 2017 — 01-26T00:10:19.0000000 Z |

**Dane wyjściowe**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Otwieranie gniazda pieniężnego" | 2017 — 01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

To zapytanie dopasowuje co najmniej dwa kolejne zdarzenia błędów i generuje alarm w przypadku spełnienia warunków.
**Wzorzec** definiuje wyrażenie regularne, które ma być używane na potrzeby dopasowywania, w tym przypadku dowolną liczbę operacji zakończonych powodzeniem, po których następuje co najmniej dwa kolejne błędy.
Pomyślne i Niepowodzenie są definiowane przy użyciu wartości Return_Code i po spełnieniu warunku **miary** są rzutowane z *ATM_id*, pierwszą operacją ostrzegawczą i pierwszym ostrzeżeniem.

Aby uzyskać więcej informacji, zobacz [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geoogrodzenie i zapytania geograficzne

Azure Stream Analytics udostępnia wbudowane funkcje geoprzestrzenne, które mogą służyć do implementowania scenariuszy takich jak zarządzanie flotą, udostępnianie samochodu, samochody połączone i śledzenie zasobów.
Dane geograficzne można pozyskać w formatach GeoJSON lub WKT w ramach strumienia zdarzeń lub danych referencyjnych.
Na przykład firma wyspecjalizowana na maszynach produkcyjnych do drukowania paszportów, wydzierżawiania ich maszynom rządowym i konsulatom. Lokalizacja tych maszyn jest silnie kontrolowana, aby uniknąć nieodpowiedniego umieszczenia i możliwego użycia w celu podrabiania paszportów. Każdy komputer jest wyposażony w moduł śledzący GPS, a informacje są przekazywane z powrotem do zadania Azure Stream Analytics.
Produkcja chce śledzić lokalizację tych maszyn i otrzymywać alerty, jeśli jeden z nich opuszcza autoryzowany obszar, w ten sposób można je zdalnie wyłączyć, urzędy alertów i pobrać sprzęt.

**Dane wejściowe**:

| Equipment_id | Equipment_current_location | Godzina |
| --- | --- | --- |
| 1 | "POINT (-122.13288797982818 47.64082002051315)" | 2017 — 01-26T00:10:00.0000000 Z |
| 1 | "POINT (-122.13307252987875 47.64081350934929)" | 2017 — 01-26T00:11:00.0000000 Z |
| 1 | "POINT (-122.13308862313283 47.6406508603241)" | 2017 — 01-26T00:12:00.0000000 Z |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017 — 01-26T00:13:00.0000000 Z |

**Dane wejściowe odwołania**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "WIELOKĄT ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Dane wyjściowe**:

| Equipment_id | Equipment_alert_location | Godzina |
| --- | --- | --- |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017 — 01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Zapytanie umożliwia producentowi automatyczne monitorowanie lokalizacji maszyn, otrzymywanie alertów, gdy maszyna opuszcza dozwolony ogranicznik. Wbudowana funkcja geoprzestrzenna umożliwia użytkownikom używanie danych GPS w ramach zapytania bez bibliotek innych firm.

Aby uzyskać więcej informacji, zapoznaj się z [scenariuszami geoprzestrzennymi i agregacjami geograficznymi przy użyciu Azure Stream Analytics](geospatial-scenarios.md) artykułu.

## <a name="get-help"></a>Uzyskaj pomoc

Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)