---
title: Eksportowanie za pomocą Stream Analytics z platformy Azure Application Insights | Microsoft Docs
description: Stream Analytics mogą w sposób ciągły przekształcać, filtrować i kierować dane eksportowane z Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: a517bddd8981554b7fb5044d33b6c6777df51e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719801"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Użyj Stream Analytics, aby przetwarzać eksportowane dane z Application Insights

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jest idealnym narzędziem do przetwarzania danych [wyeksportowanych z Application Insights](export-telemetry.md). Stream Analytics może pobierać dane z różnych źródeł. Może on przekształcać i filtrować dane, a następnie kierować je do różnych zlewów.

W tym przykładzie utworzymy adapter, który pobiera dane z Application Insights, zmienia nazwy i przetwarza niektóre pola, a następnie przekazuje je do Power BI.

> [!WARNING]
> Istnieje dużo lepszych i łatwiejszych [sposobów wyświetlania danych Application Insights w Power BI](./export-power-bi.md). Ścieżka zilustrowana poniżej to przykład ilustrujący, jak przetwarzać eksportowane dane.

![Diagram blokowy do eksportowania przez SA do PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze wyprowadza dane do konta usługi Azure Storage, dlatego należy najpierw utworzyć magazyn.

1. Utwórz konto magazynu klasycznego w subskrypcji w [Azure Portal](https://portal.azure.com).
   
   ![W Azure Portal wybierz kolejno pozycje nowy, dane i magazyn](./media/export-stream-analytics/030.png)
2. Tworzenie kontenera
   
    ![W obszarze nowy magazyn wybierz pozycję kontenery, kliknij kafelek kontenery, a następnie pozycję Dodaj.](./media/export-stream-analytics/040.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Będzie to wkrótce potrzebne do skonfigurowania danych wejściowych usługi Stream Analytics.
   
    ![W obszarze magazyn Otwórz pozycję Ustawienia, klucze i wykonaj kopię podstawowego klucza dostępu](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocznij eksport ciągły do usługi Azure Storage

[Eksport ciągły](export-telemetry.md) przenosi dane z Application Insights do usługi Azure Storage.

1. W Azure Portal przejdź do zasobu Application Insights utworzonego dla aplikacji.
   
    ![Wybierz kolejno opcje Przeglądaj, Application Insights, aplikacja](./media/export-stream-analytics/050.png)
2. Utwórz eksport ciągły.
   
    ![Wybieranie ustawień, eksport ciągły, Dodawanie](./media/export-stream-analytics/060.png)

    Wybierz utworzone wcześniej konto magazynu:

    ![Ustaw miejsce docelowe eksportu](./media/export-stream-analytics/070.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybieranie typów zdarzeń](./media/export-stream-analytics/080.png)

1. Umożliwia gromadzenie danych. Powróć i pozwól, aby użytkownicy korzystali z aplikacji przez pewien czas. Dane telemetryczne będą dostępne, a wykresy statystyczne są wyświetlane w [Eksploratorze metryk](../essentials/metrics-charts.md) i w poszczególnych zdarzeniach w [przeszukiwaniu diagnostycznym](./diagnostic-search.md).
   
    Ponadto dane zostaną wyeksportowane do magazynu. 
2. Sprawdź wyeksportowane dane. W programie Visual Studio wybierz pozycję **Widok/Eksplorator chmury** i Otwórz pozycję Azure/Storage. (Jeśli nie masz tej opcji menu, musisz zainstalować zestaw Azure SDK: Otwórz okno dialogowe Nowy projekt i otwórz Visual C#/Cloud/Get Zestaw Microsoft Azure SDK dla platformy .NET).
   
    ![Zrzut ekranu przedstawiający sposób ustawiania typów zdarzeń, które mają być wyświetlane.](./media/export-stream-analytics/04-data.png)
   
    Zanotuj wspólną część nazwy ścieżki, która jest pochodną nazwy aplikacji i klucza Instrumentacji.

Zdarzenia są zapisywane w plikach obiektów BLOB w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Więc chcemy przeczytać dane zdarzenia i odfiltrować pola, które chcemy. Istnieją wszystkie rodzaje rzeczy, które możemy zrobić z danymi, ale naszym planem jest użycie Stream Analytics do potoku danych do Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia Azure Stream Analytics

W [Azure Portal](https://portal.azure.com/)wybierz usługę Azure Stream Analytics i Utwórz nowe zadanie Stream Analytics:

![Zrzut ekranu przedstawiający stronę główną służącą do tworzenia Stream Analytics zadania w Azure Portal.](./media/export-stream-analytics/SA001.png)

![Zrzut ekranu pokazujący szczegóły, które są zbędne podczas tworzenia nowego zadania Stream Analytics.](./media/export-stream-analytics/SA002.png)

Po utworzeniu nowego zadania wybierz pozycję **Przejdź do zasobu**.

![Zrzut ekranu pokazujący komunikat odebrany po pomyślnym wdrożeniu nowego zadania Stream Analytics.](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![Zrzut ekranu pokazujący sposób dodawania danych wejściowych do zadania Stream Analytics.](./media/export-stream-analytics/SA004.png)

Ustaw, aby pobierał dane wejściowe z obiektu BLOB ciągłego eksportu:

![Zrzut ekranu pokazujący Konfigurowanie zadania Stream Analytics, które ma przyjmować dane wejściowe z obiektu BLOB ciągłego eksportu.](./media/export-stream-analytics/SA0005.png)

Teraz potrzebny jest podstawowy klucz dostępu z konta magazynu, który został zanotowany wcześniej. Ustaw tę wartość jako klucz konta magazynu.

### <a name="set-path-prefix-pattern"></a>Ustaw wzorzec prefiksu ścieżki

**Upewnij się, że ustawiono format daty RRRR-MM-DD (z kreskami).**

Wzorzec prefiksu ścieżki Określa, gdzie Stream Analytics znajdować pliki wejściowe w magazynie. Należy ustawić tę wartość, aby odpowiadała, jak eksport ciągły przechowuje dane. Ustaw go następująco:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

W tym przykładzie:

* `webapplication27` to nazwa zasobu Application Insights w **przypadku małych liter**.
* `1234...` jest kluczem Instrumentacji zasobu Application Insights, **pomijając łączniki**. 
* `PageViews` to typ danych, które mają być analizowane. Dostępne typy zależą od filtru ustawionego w ramach eksportu ciągłego. Sprawdź wyeksportowane dane, aby zobaczyć inne dostępne typy i zobacz [Eksportuj model danych](export-data-model.md).
* `/{date}/{time}` jest wzorcem zapisanym dosłownie.

> [!NOTE]
> Sprawdź magazyn, aby upewnić się, że pobrano odpowiednie ścieżki.

## <a name="add-new-output"></a>Dodaj nowe dane wyjściowe

Teraz wybierz zadanie > Dodawanie danych **wyjściowych**  >  .

![Zrzut ekranu pokazujący Wybieranie zadania Stream Analytics, aby dodać nowe dane wyjściowe.](./media/export-stream-analytics/SA006.png)


![Wybierz nowy kanał, kliknij pozycję dane wyjściowe, Dodaj, Power BI](./media/export-stream-analytics/SA010.png)

Podaj swoje **konto służbowe** , aby autoryzować Stream Analytics dostępu do zasobu Power BI. Następnie wynalazek nazwę danych wyjściowych i dla docelowego zestawu danych Power BI i tabeli.

## <a name="set-the-query"></a>Ustaw zapytanie

Zapytanie reguluje tłumaczenie danych wyjściowych na dane wyjściowe.

Użyj funkcji testowej, aby sprawdzić, czy otrzymujesz odpowiednie dane wyjściowe. Nadaj mu przykładowe dane, które zostały wykonane ze strony danych wejściowych.

### <a name="query-to-display-counts-of-events"></a>Zapytanie w celu wyświetlenia liczby zdarzeń

Wklej to zapytanie:

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

* Eksport — dane wejściowe to alias przekazany do danych wejściowych strumienia
* PBI-Output to zdefiniowany przez nas alias wyjściowy
* Użyjemy klasy [OUTER APPLY](/stream-analytics-query/apply-azure-stream-analytics) , ponieważ nazwa zdarzenia znajduje się w zagnieżdżonej tablicy JSON. Następnie wybierz pozycję wybiera nazwę zdarzenia wraz z liczbą wystąpień o tej nazwie w danym okresie. Klauzula [Group by](/stream-analytics-query/group-by-azure-stream-analytics) Grupuje elementy w okresach o pojedynczej minucie.

### <a name="query-to-display-metric-values"></a>Zapytanie w celu wyświetlenia wartości metryk

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

* To zapytanie służy do przechodzenia do danych telemetrycznych metryk w celu uzyskania czasu zdarzenia i wartości metryki. Wartości metryk znajdują się wewnątrz tablicy, dlatego w celu wyodrębnienia wierszy używamy wzorca APPLY GetElements. "fotometryczne" to nazwa metryki w tym przypadku.

### <a name="query-to-include-values-of-dimension-properties"></a>Zapytanie w celu uwzględnienia wartości właściwości wymiaru

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

* To zapytanie zawiera wartości właściwości wymiaru bez w zależności od określonego wymiaru w stałym indeksie macierzy wymiarowej.

## <a name="run-the-job"></a>Uruchamianie zadania

Możesz wybrać datę z przeszłości, aby uruchomić zadanie z.

![Wybierz zadanie, a następnie kliknij pozycję zapytanie. Wklej poniższy przykład.](./media/export-stream-analytics/SA008.png)

Poczekaj, aż zadanie zostanie uruchomione.

## <a name="see-results-in-power-bi"></a>Zobacz wyniki w Power BI

> [!WARNING]
> Istnieje dużo lepszych i łatwiejszych [sposobów wyświetlania danych Application Insights w Power BI](./export-power-bi.md). Ścieżka zilustrowana poniżej to przykład ilustrujący, jak przetwarzać eksportowane dane.


Otwórz Power BI przy użyciu konta służbowego i wybierz zestaw danych i tabelę zdefiniowane jako dane wyjściowe zadania Stream Analytics.

![W Power BI wybierz zestaw danych i pola.](./media/export-stream-analytics/200.png)

Teraz można użyć tego zestawu danych w raportach i pulpitach nawigacyjnych w programie [Power BI](https://powerbi.microsoft.com).

![Zrzut ekranu przedstawia przykład raportu z zestawu danych w Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Brak danych?

* Sprawdź, czy [Format daty](#set-path-prefix-pattern) jest prawidłowo ustawiony na rrrr-mm-dd (z kreskami).

## <a name="next-steps"></a>Następne kroki
* [Eksport ciągły](export-telemetry.md)
* [Szczegółowe informacje o modelu danych dla typów i wartości właściwości.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)
