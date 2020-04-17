---
title: Eksportowanie przy użyciu usługi Stream Analytics z usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Usługa Stream Analytics może stale przekształcać, filtrować i kierować dane eksportowane z usługi Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536883"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Przetwarzanie eksportowanych danych z usługi Application Insights za pomocą usługi Stream Analytics
[Usługa Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) to idealne narzędzie do przetwarzania danych [eksportowanych z usługi Application Insights.](export-telemetry.md) Usługa Stream Analytics może pobierać dane z różnych źródeł. Można przekształcić i filtrować dane, a następnie kierować je do różnych pochłaniacze.

W tym przykładzie utworzymy adapter, który pobiera dane z usługi Application Insights, zmienia nazwy i przetwarza niektóre pola i przesyła je do usługi Power BI.

> [!WARNING]
> Istnieją znacznie lepsze i łatwiejsze [zalecane sposoby wyświetlania danych usługi Application Insights w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ). Ścieżka zilustrowana tutaj jest tylko przykładem, aby zilustrować, jak przetwarzać eksportowane dane.
> 
> 

![Diagram blokowy do eksportu za pośrednictwem usługi SA do PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Ciągły eksport zawsze wyprowadza dane do konta usługi Azure Storage, więc musisz najpierw utworzyć magazyn.

1. Utwórz "klasyczne" konto magazynu w ramach subskrypcji w [witrynie Azure portal](https://portal.azure.com).
   
   ![W witrynie Azure portal wybierz pozycję Nowe, Dane, Magazyn](./media/export-stream-analytics/030.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz pozycję Kontenery, kliknij kafelek Kontenery, a następnie dodaj](./media/export-stream-analytics/040.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Wkrótce będzie potrzebny do skonfigurowania danych wejściowych do usługi analizy strumienia.
   
    ![W magazynie otwórz okno Ustawienia, klawisze i zrób kopię podstawowego klucza dostępu](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpoczynanie ciągłego eksportowania do magazynu platformy Azure
[Ciągły eksport](export-telemetry.md) przenosi dane z usługi Application Insights do magazynu platformy Azure.

1. W witrynie Azure portal przejdź do zasobu usługi Application Insights utworzonego dla aplikacji.
   
    ![Wybierz przeglądanie, wgląd w aplikacje, aplikację](./media/export-stream-analytics/050.png)
2. Utwórz ciągły eksport.
   
    ![Wybierz ustawienia, eksportu ciągłego, dodaj](./media/export-stream-analytics/060.png)

    Wybierz konto magazynu utworzone wcześniej:

    ![Ustawianie miejsca docelowego eksportu](./media/export-stream-analytics/070.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybieranie typów zdarzeń](./media/export-stream-analytics/080.png)

1. Niech niektóre dane gromadzą się. Usiądź wygodnie i pozwól ludziom korzystać z aplikacji na chwilę. Dane telemetryczne pojawią się, a zobaczysz wykresy statystyczne w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md) i poszczególnych zdarzeniach w [wyszukiwaniu diagnostycznym.](../../azure-monitor/app/diagnostic-search.md) 
   
    A także dane zostaną wyeksportowane do magazynu. 
2. Sprawdź wyeksportowane dane. W programie Visual Studio wybierz pozycję **Wyświetl / Cloud Explorer**i otwórz platformę Azure / Storage. (Jeśli nie masz tej opcji menu, musisz zainstalować zestaw SDK platformy Azure: Otwórz okno dialogowe Nowy projekt i otwórz visual c# / chmurę / Pobierz zestaw Microsoft Azure SDK dla platformy .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Zanotuj wspólną część nazwy ścieżki, która pochodzi od nazwy aplikacji i klucza instrumentacji. 

Zdarzenia są zapisywane w plikach obiektów blob w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Dlatego chcielibyśmy odczytać dane zdarzenia i odfiltrować pola, które chcemy. Istnieją różne rzeczy, które możemy zrobić z danymi, ale naszym planem jest dzisiaj użycie usługi Stream Analytics do przesyłania danych do usługi Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia usługi Azure Stream Analytics
Z [witryny Azure Portal](https://portal.azure.com/)wybierz usługę Azure Stream Analytics i utwórz nowe zadanie usługi Stream Analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Po utworzeniu nowego zadania wybierz pozycję **Przejdź do zasobu**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Dodawanie nowego wejścia

![](./media/export-stream-analytics/SA004.png)

Ustaw go do podjęcia danych wejściowych z obiektu blob eksportu ciągłego:

![](./media/export-stream-analytics/SA0005.png)

Teraz będziesz potrzebować podstawowego klucza dostępu z konta magazynu, który został odnotowany wcześniej. Ustaw to jako klucz konta magazynu.

### <a name="set-path-prefix-pattern"></a>Ustawianie wzorca prefiksu ścieżki

**Pamiętaj, aby ustawić format daty na YYYY-MM-DD (z myślnikami).**

Wzorzec prefiksu ścieżki określa, gdzie usługa Stream Analytics znajduje pliki wejściowe w magazynie. Należy ustawić go, aby odpowiadać, jak eksport ciągły przechowuje dane. Ustaw to w ten sposób:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27`to nazwa zasobu usługi Application Insights **.**
* `1234...`jest kluczem instrumentacji zasobu usługi Application Insights, **pomijając myślniki**. 
* `PageViews`to typ danych, które chcesz przeanalizować. Dostępne typy zależą od filtru ustawionego w trybie eksportu ciągłego. Sprawdź wyeksportowane dane, aby wyświetlić inne dostępne typy, i zobacz [model danych eksportu](export-data-model.md).
* `/{date}/{time}`jest wzorem napisanym dosłownie.

> [!NOTE]
> Sprawdź magazyn, aby upewnić się, że ścieżka jest właściwa.
> 

## <a name="add-new-output"></a>Dodawanie nowych danych wyjściowych
Teraz wybierz zadanie > **Wyniki** > **Dodaj**.

![](./media/export-stream-analytics/SA006.png)


![Wybierz nowy kanał, kliknij pozycję Wyjścia, Dodaj, Power BI](./media/export-stream-analytics/SA010.png)

Podaj **swoje konto służbowe,** aby autoryzować analizę stream analytics w celu uzyskania dostępu do zasobu usługi Power BI. Następnie wymyśl nazwę dla danych wyjściowych oraz docelowego zestawu danych i tabeli usługi Power BI.

## <a name="set-the-query"></a>Ustawianie kwerendy
Kwerenda reguluje translację z danych wejściowych na dane wyjściowe.

Użyj funkcji Test, aby sprawdzić, czy masz odpowiednie dane wyjściowe. Nadaj mu przykładowe dane pobrane ze strony danych wejściowych. 

### <a name="query-to-display-counts-of-events"></a>Kwerenda do wyświetlania liczby zdarzeń
Wklej tę kwerendę:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input to alias, który nadajemy wejściu strumienia
* pbi-output jest aliasem wyjściowym, który zdefiniowaliśmy
* Używamy [outer apply GetElements,](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) ponieważ nazwa zdarzenia znajduje się w tablicy zagnieżdżonej JSON. Następnie Select wybiera nazwę zdarzenia, wraz z liczbą wystąpień o tej nazwie w okresie. Klauzula [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) grupuje elementy w okresy jednej minuty.

### <a name="query-to-display-metric-values"></a>Kwerenda do wyświetlania wartości metryk
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Ta kwerenda przechodzi do danych telemetrycznych metryk, aby uzyskać czas zdarzenia i wartość metryki. Wartości metryki znajdują się wewnątrz tablicy, więc używamy zewnętrznego zastosuj GetElements wzorzec wyodrębnić wiersze. "myMetric" to nazwa metryki w tym przypadku. 

### <a name="query-to-include-values-of-dimension-properties"></a>Zapytanie o uwzględnienie wartości właściwości wymiaru
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Ta kwerenda zawiera wartości właściwości wymiaru bez zależności od określonego wymiaru, który znajduje się przy stałym indeksie w tablicy wymiarów.

## <a name="run-the-job"></a>Uruchamianie zadania
Można wybrać datę w przeszłości, aby rozpocząć zadanie od. 

![Zaznacz zadanie i kliknij pozycję Zapytanie. Wklej poniższą próbkę.](./media/export-stream-analytics/SA008.png)

Poczekaj, aż zadanie zostanie uruchomione.

## <a name="see-results-in-power-bi"></a>Wyświetlanie wyników w usłudze Power BI
> [!WARNING]
> Istnieją znacznie lepsze i łatwiejsze [zalecane sposoby wyświetlania danych usługi Application Insights w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ). Ścieżka zilustrowana tutaj jest tylko przykładem, aby zilustrować, jak przetwarzać eksportowane dane.
> 
> 

Otwórz program Power BI na koncie służbowym i wybierz zestaw danych i tabelę zdefiniowaną jako dane wyjściowe zadania usługi Stream Analytics.

![W usłudze Power BI wybierz zestaw danych i pola.](./media/export-stream-analytics/200.png)

Teraz możesz używać tego zestawu danych w raportach i pulpitach nawigacyjnych w [usłudze Power BI](https://powerbi.microsoft.com).

![W usłudze Power BI wybierz zestaw danych i pola.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Brak danych?
* Sprawdź, czy [format daty został](#set-path-prefix-pattern) poprawnie ustawiony na YYYY-MM-DD (z myślnikami).

## <a name="video"></a>Film wideo
Noam Ben Zeev pokazuje, jak przetwarzać eksportowane dane za pomocą usługi Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Eksport ciągły](export-telemetry.md)
* [Szczegółowe odwołanie do modelu danych dla typów właściwości i wartości.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

