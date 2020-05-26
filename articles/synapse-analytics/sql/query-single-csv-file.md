---
title: Wykonywanie zapytań dotyczących plików CSV za pomocą SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące pojedynczych plików CSV z różnymi formatami plików przy użyciu funkcji SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 41c4a8940cc49a3859a2511f0de65d0019817078
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836553"
---
# <a name="query-csv-files"></a>Pliki CSV zapytania

W tym artykule dowiesz się, jak wysyłać zapytania do pojedynczego pliku CSV przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. Pliki CSV mogą mieć różne formaty: 

- Z wierszem nagłówka i bez niego
- Wartości rozdzielane przecinkami i tabulatorami
- Zakończenia wierszy stylu systemu Windows i systemu UNIX
- Wartości bez cudzysłowu i cudzysłowy oraz znaki ucieczki

Wszystkie powyższe zmiany zostaną omówione poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** , w której zostaną utworzone tabele. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

## <a name="windows-style-new-line"></a>Nowy wiersz w stylu systemu Windows

Poniższe zapytanie pokazuje, jak odczytać plik CSV bez wiersza nagłówka, z nowym wierszem w stylu systemu Windows i kolumnami rozdzielonymi przecinkami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV bez nagłówka, nowy wiersz stylu systemu Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nowy wiersz w stylu systemu UNIX

Następujące zapytanie pokazuje, jak odczytać plik bez wiersza nagłówka, z nowym wierszem w stylu systemu UNIX i kolumnami rozdzielanymi przecinkami. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV bez wiersza nagłówka i w stylu systemu UNIX nowy wiersz.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Wiersz nagłówka

Następujące zapytanie pokazuje, jak wczytać plik z wierszem nagłówka, z nowym wierszem w stylu systemu UNIX i kolumnami rozdzielanymi przecinkami. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z nowym wierszem w stylu systemu UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Niestandardowy znak cudzysłowu

Następujące zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka z nowym wierszem systemu UNIX, kolumnami rozdzielonymi przecinkami i wartościami z cudzysłowu. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i w stylu systemu UNIX nowy wiersz i ujęte w cudzysłów wartości.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> To zapytanie zwróci te same wyniki, jeśli pominięto parametr FIELDQUOTE, ponieważ wartość domyślna dla FIELDQUOTE jest podwójnym cudzysłowem.

## <a name="escaping-characters"></a>Znaki ucieczki

Następujące zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka z nowym wierszem systemu UNIX, kolumnami rozdzielonymi przecinkami oraz znakiem ucieczki używanym przez Ogranicznik pola (przecinek) w obrębie wartości. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka oraz z nowym wierszem w stylu systemu UNIX i znakiem ucieczki używanym do ogranicznika pola.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> To zapytanie nie powiedzie się, jeśli ESCAPECHAR nie zostanie określony, ponieważ przecinek w "slov, enia" byłby traktowany jako ogranicznik pola zamiast części nazwy kraju/regionu. "Slov, enia" byłyby traktowane jak dwie kolumny. W związku z tym, konkretny wiersz może mieć jedną kolumnę więcej niż pozostałe wiersze, a jedna kolumna jest większa niż zdefiniowana w klauzuli WITH.

## <a name="tab-delimited-files"></a>Pliki rozdzielane tabulatorami

Następujące zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka, z nowym wierszem w stylu systemu UNIX i kolumnami rozdzielanymi tabulatorami. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z separatorem nowej linii i tabulatora w stylu systemu UNIX.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Zwracanie podzbioru kolumn

Do tej pory określono schemat plików CSV za pomocą polecenia WITH i lista wszystkich kolumn. W zapytaniu można określić tylko kolumny, które są w rzeczywistości potrzebne, używając numeru porządkowego dla każdej kolumny. Pomijasz również kolumny bez zainteresowania.

Następujące zapytanie zwraca liczbę różnych nazw krajów/regionów w pliku, określając tylko te kolumny, które są zbędne:

> [!NOTE]
> Zapoznaj się z klauzulą WITH w zapytaniach poniżej i zwróć uwagę na to, że w kolumnie *[country_name]* znajduje się wartość "2" (bez cudzysłowów). Oznacza to, że kolumna *[country_name]* jest drugą kolumną w pliku. Zapytanie zignoruje wszystkie kolumny w pliku poza drugim.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Następne kroki

W następnych artykułach pokazano, jak:

- [Wykonywanie zapytania dotyczącego plików Parquet](query-parquet-files.md)
- [Wykonywanie zapytań dotyczących folderów i wielu plików](query-folders-multiple-csv-files.md)
