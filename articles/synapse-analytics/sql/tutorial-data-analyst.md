---
title: 'Samouczek: używanie bezserwerowej puli SQL (wersja zapoznawcza) do analizowania otwartych zestawów danych platformy Azure w usłudze Azure Synapse Studio (wersja zapoznawcza)'
description: W tym samouczku pokazano, jak łatwo przeprowadzać analizę danych w celu łączenia różnych otwartych zestawów datadataset przy użyciu bezserwerowej puli SQL (wersja zapoznawcza) i wizualizować wyniki w usłudze Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: cec16041edd10a57088df4ae9cfe0587906919eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309284"
---
# <a name="tutorial-use-serverless-sql-pool-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Samouczek: używanie bezserwerowej puli SQL do analizowania otwartych zestawów danych platformy Azure i wizualizacji wyników w usłudze Azure Synapse Studio

W ramach tego samouczka nauczysz się przeprowadzać analizę danych w sposób naukowy, łącząc różne otwarte zestawy datadataset za pomocą puli SQL bezserwerowej, a następnie wizualizując wyniki w usłudze Azure Synapse Studio.

W szczególności analizujemy [zestaw danych o taksówkach w Nowym Jorku (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) , który obejmuje:

- Data i godzina odebrania.
- Wybierz i upuść lokalizacje. 
- Odległość podróży.
- Opłaty za elementy.
- Typy szybkości.
- Typy płatności. 
- Liczba osób zgłaszanych przez sterowniki.

## <a name="automatic-schema-inference"></a>Automatyczne wnioskowanie schematu

Ponieważ dane są przechowywane w formacie pliku Parquet, automatyczne wnioskowanie schematu jest dostępne. Można łatwo wysyłać zapytania o dane bez wyświetlania typów danych wszystkich kolumn w plikach. Można również użyć mechanizmu kolumny wirtualnej i funkcji FilePath do odfiltrowania określonego podzestawu plików.

Najpierw zapoznaj się z danymi z NYC taksówkami, uruchamiając następujące zapytanie:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Poniższy fragment kodu przedstawia wynik dla danych NYC Taksówk:

![Fragment wyniku danych NYC taksówki](./media/tutorial-data-analyst/1.png)

Podobnie można wysyłać zapytania do publicznego zestawu danych dni wolnych za pomocą następującego zapytania:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Poniższy fragment kodu przedstawia wynik dla publicznego zestawu danych dni wolnych:

![Fragment wyniku zestawu wyników publicznych świąt](./media/tutorial-data-analyst/2.png)

Na koniec można także wykonywać zapytania dotyczące zestawu danych pogody przy użyciu następującej kwerendy:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Poniższy fragment kodu przedstawia wynik zestawu danych o pogodzie:

![Fragment wyniku zestawu danych pogody](./media/tutorial-data-analyst/3.png)

Możesz dowiedzieć się więcej na temat znaczenia poszczególnych kolumn w opisach [NYCych](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)samochodów, [świąt publicznych](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)i [danych pogody](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) .

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analiza szeregów czasowych, sezonowości i nieprzestających

Można łatwo podsumowywać roczną liczbę kolarstwuówek, korzystając z następującej kwerendy:

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

Poniższy fragment kodu przedstawia wynik rocznej liczby kolarstwuówek:

![Roczna liczba taksówki kolarstwu fragmentu wyniku](./media/tutorial-data-analyst/4.png)

Dane można wizualizować w programie Synapse Studio, przełączając **tabelę** w widok **wykresu** . Można wybrać różne typy wykresów, takie jak **obszar** , **pasek** , **kolumna** , **linia** , **Wykres kołowy** i **punktowy**. W takim przypadku Wykreśl wykres **kolumnowy** z kolumną **Category** ustawioną na **current_year** :

![Wykres kolumnowy przedstawiający kolarstwu na rok](./media/tutorial-data-analyst/5.png)

W tej wizualizacji można jasno zobaczyć trend zmniejszania liczby kolarstwu przez lata. W efekcie ten spadek jest spowodowany niedawniej zwiększoną popularnością firm partnerskich.

> [!NOTE]
> W momencie pisania tego samouczka dane dla 2019 są niekompletne. W efekcie istnieje ogromny spadek liczby kolarstwu w danym roku.

Następnie należy skoncentrować się na analizie w jednym roku, na przykład 2016. Następujące zapytanie zwraca dzienną liczbę kolarstwu w tym roku:

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

Poniższy fragment kodu przedstawia wynik tego zapytania:

![Dzienna liczba kolarstwu dla fragmentu wyniku 2016](./media/tutorial-data-analyst/6.png)

Ponownie można łatwo wizualizować dane, Wykreśl wykres **kolumnowy** z kolumną **kategorii** ustawioną na **current_day** i kolumnę **Legenda (serie)** ustawioną na **rides_per_day**.

![Wykres kolumnowy przedstawiający dzienną liczbę kolarstwu dla 2016](./media/tutorial-data-analyst/7.png)

Na wykresie grafu można zobaczyć, że istnieje tygodniowy wzorzec, w soboty, jako dzień szczytu. W ciągu roku letniego występuje mniej kolarstwuów z powodu wakacji. Należy również zwrócić uwagę na kilka znaczących porzucanych kolarstwuów bez wyraźnego wzorca, kiedy i Dlaczego występują.

Następnie Zobaczmy, czy porzucanie jest skorelowane z świętami publicznymi, dołączając zestaw danych NYC taksówki kolarstwu do publicznego zestawu danych dni wolnych:

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

![Wizualizacja wyników z NYC taksówkami kolarstwu i publicznymi zestawami danych](./media/tutorial-data-analyst/8.png)

Tym razem chcemy wyróżnić liczbę kolarstwuówek w dni wolne od pracy. W tym celu wybierz pozycję **Brak** dla kolumny **kategoria** , a **rides_per_day** i **dni wolne** jako kolumny **legendy (serie)** .

![Liczba kolarstwuówek w trakcie publicznego wykresu kreślenia](./media/tutorial-data-analyst/9.png)

Na wykresie wykresu można zobaczyć, że w dni wolne od pracy liczba kolarstwuów jest niższa. W dalszym ciągu brakuje jednego z objaśnień dużego dnia 23 stycznia. Sprawdźmy Pogoda w NYC w tym dniu, wykonując zapytania dotyczące zestawu danych pogody:

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

![Wizualizacja wyników zestawu danych pogody](./media/tutorial-data-analyst/10.png)

Wyniki zapytania wskazują, że wystąpił spadek liczby kolarstwu taksówki, ponieważ:

- Blizzard w tym dniu w NYC z ciężkim śniegiem (~ 30 cm).
- Była zimna (temperatura była niższa od 0 ° c).
- Była to uzwojenie (~ 10 m/s).

W tym samouczku pokazano, jak analityk danych może szybko przeprowadzić analizę danych w sposób naukowy, a także łatwo połączyć różne zestawy, używając bezserwerowej puli SQL i wizualizować wyniki przy użyciu usługi Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak połączyć pulę SQL bezserwerową w celu Power BI Desktop i tworzenia raportów, zobacz [łączenie puli SQL bezserwerowej w celu Power BI Desktop i tworzenia raportów](tutorial-connect-power-bi-desktop.md).
 
