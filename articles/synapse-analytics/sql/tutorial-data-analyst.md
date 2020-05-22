---
title: Samouczek analityków danych — używanie usługi SQL na żądanie (wersja zapoznawcza) do analizowania otwartych zestawów danych platformy Azure w usłudze Azure Synapse Studio (wersja zapoznawcza)
description: W ramach tego samouczka dowiesz się, jak łatwo przeprowadzać analizę danych w nauce łączącej różne otwarte zestawy datadataset przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) i wizualizować wyniki w usłudze Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b2fe4dea27564b96c5ef1734dc16ca4525011d17
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745643"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Korzystanie z SQL na żądanie (wersja zapoznawcza) w celu analizowania otwartych zestawów danych platformy Azure i wizualizacji wyników w usłudze Azure Synapse Studio (wersja zapoznawcza)

W ramach tego samouczka nauczysz się przeprowadzać analizę danych w postaci poznawczej, łącząc różne usługi Azure Open DataSets przy użyciu funkcji SQL na żądanie, a następnie wizualizując wyniki w usłudze Azure Synapse Studio.

W szczególności przeanalizuje [zestaw danych o taksówkach w Nowym Jorku (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) , który obejmuje daty i godziny pobierania i wyłączania, lokalizacje pobierania i upuszczania, odległości podróży, opłaty za elementy, typy stawek, typy płatności oraz liczby osób zgłaszanych przez sterowniki.

Celem analizy jest znalezienie trendów w zakresie zmian liczby kolarstwuówek w czasie. Analizujesz dwa inne otwarte zestawy danych platformy Azure ([dni wolne](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) i [dane pogodowe](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)), aby zrozumieć, jakie wartości odstają się w liczbie taksówkówek kolarstwu.

## <a name="create-data-source"></a>Utwórz źródło danych

Obiekt źródła danych służy do odwoływania się do konta usługi Azure Storage, na którym należy analizować dane. Publicznie dostępny magazyn nie potrzebuje niektórych poświadczeń w celu uzyskania dostępu do magazynu.

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a credential.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="automatic-schema-inference"></a>Automatyczne wnioskowanie schematu

Ponieważ dane są przechowywane w formacie pliku Parquet, automatyczne wnioskowanie schematu jest dostępne, dzięki czemu jeden może łatwo wysyłać zapytania o dane bez konieczności wyświetlania listy typów danych wszystkich kolumn w plikach. Ponadto jedna z nich może korzystać z funkcji Virtual Column i FilePath, aby odfiltrować określony podzbiór plików.

Najpierw zapoznaj się z danymi z NYC taksówkami, uruchamiając następujące zapytanie:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Poniżej przedstawiono fragment wyniku dla danych NYC Taksówki:

![fragment wyniku](./media/tutorial-data-analyst/1.png)

Podobnie możemy zbadać zestaw danych dni urlopu publicznego przy użyciu następującej kwerendy:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'holidaydatacontainer/Processed/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Poniżej przedstawiono fragment wyniku dla publicznego zestawu danych dni wolnych:

![fragment wyniku 2](./media/tutorial-data-analyst/2.png)

Wreszcie możemy również badać zestaw danych pogody przy użyciu następującej kwerendy:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
```

Poniżej przedstawiono fragment wyniku dla zestawu danych pogody:

![fragment wyniku 3](./media/tutorial-data-analyst/3.png)

Możesz dowiedzieć się więcej na temat znaczenia poszczególnych kolumn w opisach [NYCych](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)samochodów, [świąt publicznych](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)i [danych pogody](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) .

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analiza szeregów czasowych, sezonowości i nieprzestających

Można łatwo podsumowywać roczną liczbę kolarstwuówek, korzystając z następującej kwerendy:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Poniżej przedstawiono fragment wyniku dla corocznej liczby kolarstwuówek:

![fragment wyniku 4](./media/tutorial-data-analyst/4.png)

Dane można wizualizować w programie Synapse Studio, przełączając się z tabeli do widoku wykresu. Można wybierać między różnymi typami wykresów (obszar, pasek, kolumna, linia, wykres kołowy i punktowy). W tym przypadku przykreślmy wykres kolumnowy z kolumną Category ustawioną na wartość "current_year":

![Wizualizacja wyników 5](./media/tutorial-data-analyst/5.png)

W tej wizualizacji trend zmniejszania liczby kolarstwu przez lata może być wyraźnie widoczny, najprawdopodobniej z powodu niedawnej zwiększonej popularności firm partnerskich udostępniania.

> [!NOTE]
> W momencie pisania tego samouczka dane dla 2019 są niekompletne, więc w danym roku istnieje ogromna liczba kolarstwu.

Następnie skupmy nasze analizy w jednym roku, na przykład 2016. Następujące zapytanie zwraca dzienną liczbę kolarstwu w tym roku:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Poniżej przedstawiono fragment wyniku dla tego zapytania:

![fragment wyniku 6](./media/tutorial-data-analyst/6.png)

Ponownie można łatwo wizualizować dane, Wykreśl wykres kolumnowy z kolumną kategorii "current_day" i kolumną Legenda (serie) "rides_per_day".

![Wizualizacja wyników 7](./media/tutorial-data-analyst/7.png)

Z wykresu można zauważyć, że istnieje tygodniowy wzorzec, z szczytem w sobotę. W trakcie roku letniego występuje mniej kolarstwuów z powodu okresu urlopowego. Jednak istnieją również pewne znaczące przerwy w liczbie taksówki kolarstwu bez jasnego wzorca, kiedy i dlaczego się pojawiają.

Następnie Zobaczmy, czy te porzucania są potencjalnie skorelowane z świętami, dołączając NYC taksówki kolarstwu do publicznego zestawu danych dni wolnych:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
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
            BULK 'holidaydatacontainer/Processed/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
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

![Wizualizacja wyników 8](./media/tutorial-data-analyst/8.png)

Tym razem chcemy wyróżnić liczbę kolarstwuówek w dni wolne od pracy. W tym celu wybierzesz opcję "Brak" dla kolumny kategorii i "rides_per_day" oraz "święta" jako kolumny legendy (serie).

![Wizualizacja wyników 9](./media/tutorial-data-analyst/9.png)

Z wykresu można jasno zauważyć, że w przypadku dni wolnych liczba kolarstwu jest niższa. Jednak w dalszym ciągu nie są dostępne żadne niewyjaśnione ogromne dane. Sprawdźmy Pogoda w NYC w tym dniu, wykonując zapytania dotyczące zestawu danych pogody:

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
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Wizualizacja wyników 10](./media/tutorial-data-analyst/10.png)

Wyniki zapytania wskazują, że porzucanie w wielu taksówkach kolarstwu było spowodowane:

- Blizzard w tym dniu w NYC, ponieważ trwało śnieg (~ 30 cm)
- była zimna (temperatura poniżej 0 stopni Celsjusza)
- i wiatr (~ 10 mln/s)

W tym samouczku pokazano, jak analityk danych może szybko przeprowadzić analizę danych poznawczej, a także łatwo łączyć z innymi zestawami, korzystając z SQL na żądanie, i wizualizować wyniki przy użyciu usługi Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [łączenie SQL na żądanie Power BI Desktop & tworzenia raportu](tutorial-connect-power-bi-desktop.md) , aby dowiedzieć się, jak nawiązać połączenie z usługą SQL na żądanie w celu Power BI Desktop i tworzenia raportów.
 
