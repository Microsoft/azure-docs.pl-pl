---
title: Samouczek analityka danych — analizowanie zestawów danych azure open w usłudze Azure Synapse Studio za pomocą funkcji SQL na żądanie (wersja zapoznawcza)
description: W tym samouczku dowiesz się, jak łatwo przeprowadzić analizę danych odkrywczych łącząc różne zestawy danych Azure Open przy użyciu sql na żądanie (wersja zapoznawcza) i wizualizować wyniki w usłudze Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423229"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Analiza otwartych zestawów danych platformy Azure Open i wizualizacja wyników w usłudze Azure Synapse Studio (wersja zapoznawcza) umożliwia korzystanie z języka SQL na żądanie (wersja zapoznawcza)

W tym samouczku dowiesz się, jak przeprowadzić analizę danych odkrywczych, łącząc różne zestawy danych azure open przy użyciu języka SQL na żądanie, a następnie wizualizując wyniki w usłudze Azure Synapse Studio.

W szczególności analizujesz [zestaw danych Taksówki w Nowym Jorku (NYC),](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) który zawiera daty/godziny odbioru i nadania, lokalizacje odbioru i nadania, odległości podróży, wyszczególnione taryfy, typy stawek, typy płatności i liczbę pasażerów zgłoszonych przez kierowcę.

Celem analizy jest znalezienie trendów w zmianach liczby przejazdów taksówką w czasie. Analizuj dwa inne zestawy danych platformy Azure Open[(święta i](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) [dane pogodowe),](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)aby zrozumieć odstające liczby przejazdów taksówką.

## <a name="create-credentials"></a>Tworzenie poświadczeń

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Automatyczne wnioskowanie schematu

Ponieważ dane są przechowywane w formacie pliku Parkietu, dostępne jest automatyczne wnioskowanie schematu, dzięki czemu można łatwo wysyłać zapytania do danych bez konieczności wystawiania typów danych wszystkich kolumn w plikach. Ponadto można wykorzystać mechanizm kolumny wirtualnej i funkcję ścieżki pliku, aby odfiltrować określony podzbiór plików.

Najpierw zapoznajmy się z danymi taksówki NYC, uruchamiając następującą kwerendę:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Poniżej przedstawiono fragment wyników dla danych taksówki NYC:

![fragment kodu wyników](./media/tutorial-data-analyst/1.png)

Podobnie możemy zbadać zestaw danych dni wolnych od pracy przy użyciu następującej kwerendy:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Poniżej przedstawiono fragment kodu wyników dla zestawu danych dni wolnych od pracy:

![fragment wyników 2](./media/tutorial-data-analyst/2.png)

Wreszcie możemy również zbadać zestaw danych pogodowych za pomocą następującej kwerendy:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Poniżej przedstawiono fragment wyników dla zestawu danych pogodowych:

![fragment wyników 3](./media/tutorial-data-analyst/3.png)

Więcej informacji na temat znaczenia poszczególnych kolumn można znaleźć w opisach zestawów danych [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Public Holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)i [Weather Data.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Szeregi czasowe, sezonowość i analiza odstający

Możesz łatwo podsumować roczną liczbę przejazdów taksówką za pomocą następującej kwerendy:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Poniżej przedstawiono fragment wyników dla rocznej liczby przejazdów taksówką:

![fragment wyników 4](./media/tutorial-data-analyst/4.png)

Dane można wizualizować w Synapse Studio, przełączając się z widoku Tabela na Wykres. Można wybierać spośród różnych typów wykresów (obszar, słupk, kolumna, linia, kołowy i punktowy). W takim przypadku wykreślmy wykres kolumnowy z kolumną Kategoria ustawioną na "current_year":

![wizualizacja wyników 5](./media/tutorial-data-analyst/5.png)

Z tej wizualizacji widać wyraźnie tendencję zmniejszającą się liczbę przejazdów na przestrzeni lat, prawdopodobnie ze względu na niedawną zwiększoną popularność firm współdzielonych.

> [!NOTE]
> W momencie pisania tego samouczka dane za 2019 są niekompletne, więc na ten rok napada ogromny spadek liczby przejazdów.

Następnie skupmy naszą analizę na jednym roku, na przykład 2016. Następująca kwerenda zwraca dzienną liczbę przejazdów w ciągu tego roku:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Poniżej przedstawiono fragment wyników dla tej kwerendy:

![fragment wyników 6](./media/tutorial-data-analyst/6.png)

Ponownie możemy łatwo wizualizować dane, kreśląc wykres kolumnowy z kolumną Kategoria "current_day" i Legenda (seria) "rides_per_day".

![wizualizacja wyników 7](./media/tutorial-data-analyst/7.png)

Z fabuły można zaobserwować, że istnieje tygodniowy wzór, z sobotnim szczytem. W miesiącach letnich, istnieje mniej przejazdów taksówką ze względu na okres wakacji. Jednak istnieją również pewne znaczne spadki liczby przejazdów taksówką bez wyraźnego wzorca, kiedy i dlaczego występują.

Następnie sprawdźmy, czy te krople są potencjalnie skorelowane z dniami ustawowo wolnych od pracy, dołączając do przejazdów taksówką w Nowym Jorku z zestawem danych dni wolnych od pracy:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![wizualizacja wyników 8](./media/tutorial-data-analyst/8.png)

Tym razem chcemy podkreślić liczbę przejazdów taksówką w czasie świąt państwowych. W tym celu wybierzemy kolumny "brak" dla kolumny Kategoria i "rides_per_day" oraz "holiday" jako kolumny Legend (seria).

![wizualizacja wyników 9](./media/tutorial-data-analyst/9.png)

Z działki wyraźnie widać, że w czasie świąt państwowych liczba przejazdów taksówką jest niższa. Jednak nadal jest jeden niewyjaśniony ogromny spadek w dniu 23 stycznia. Sprawdźmy pogodę w Nowym Jorku w tym dniu, odpytując zestaw danych pogodowych:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![wizualizacja wyników 10](./media/tutorial-data-analyst/10.png)

Wyniki zapytania wskazują, że spadek liczby przejazdów taksówką był spowodowany:

- zamieć w tym dniu w Nowym Jorku, jak nie było ciężki śnieg (~ 30 cm)
- było zimno (temperatura poniżej zera stopni Celsjusza)
- i wietrznie (~10m/s)

W tym samouczku pokazano, jak analityk danych może szybko przeprowadzić analizę danych odkrywczych, łatwo połączyć różne zestawy danych przy użyciu języka SQL na żądanie i wizualizować wyniki przy użyciu usługi Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

Zapoznaj [się z artykułem Połącz sql na żądanie z programem Power BI Desktop & utworzyć artykuł o raporcie,](tutorial-connect-power-bi-desktop.md) aby dowiedzieć się, jak połączyć sql na żądanie z programem Power BI Desktop i utworzyć raporty.
 
