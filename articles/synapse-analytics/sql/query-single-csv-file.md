---
title: Kwerenda plików CSV przy użyciu SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania do pojedynczych plików CSV w różnych formatach plików przy użyciu języka SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431594"
---
# <a name="query-csv-files"></a>Kwerenda plików CSV

W tym artykule dowiesz się, jak zbadać pojedynczy plik CSV przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. Pliki CSV mogą mieć różne formaty: 

- Z wierszem nagłówka i bez niego
- Wartości rozdzielane przecinkami i kartami
- Zakończenia linii w stylu systemu Windows i Uniksa
- Wartości niecytowane i cytowane oraz znaki ucieczki

Wszystkie powyższe odmiany zostaną omówione poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu zapoznaj się z następującymi artykułami:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nowa linia w stylu systemu Windows

W poniższej kwerendzie pokazano, jak odczytać plik CSV bez wiersza nagłówka, z nowym wierszem w stylu systemu Windows i kolumnami rozdzielanych przecinkami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV bez nagłówka, nowy wiersz w stylu Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="unix-style-new-line"></a>Nowa linia w stylu uniksa

W poniższej kwerendzie pokazano sposób odczytywania pliku bez wiersza nagłówka z nowym wierszem w stylu Uniksa i kolumnami rozdzielanych przecinkami. Zwróć uwagę na inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV bez wiersza nagłówka i z nową linią w stylu uniksa.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
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

W poniższej kwerendzie pokazano, jak odczytać plik z wierszem nagłówka, z nowym wierszem w stylu Uniksa i kolumnami rozdzielanych przecinkami. Zwróć uwagę na inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z nową linią w stylu uniksa.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
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

W poniższej kwerendzie pokazano sposób odczytywania pliku z wierszem nagłówka z nowym wierszem w stylu Uniksa, kolumnami rozdzielanym przecinkami i wartościami cytowanymi. Zwróć uwagę na inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z nową linią i wartościami cytowanymi w stylu uniksańskim.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
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
> Ta kwerenda zwróci te same wyniki, jeśli pominięto parametr FIELDQUOTE, ponieważ domyślną wartością dla funkcji FIELDQUOTE jest podwójna wycena.

## <a name="escaping-characters"></a>Ucieczka znaków

W poniższej kwerendzie pokazano sposób odczytywania pliku z wierszem nagłówka, z nowym wierszem w stylu Uniksa, kolumnami rozdzielanym przecinkami i charem ucieczki używanym dla ogranicznika pola (przecinka) w obrębie wartości. Zwróć uwagę na inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z unix-style nowej linii i char escape używane dla ogranicznika pola.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
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
> Ta kwerenda zakończy się niepowodzeniem, jeśli ESCAPECHAR nie jest określony, ponieważ przecinek w "Slov,enia" będzie traktowany jako ogranicznik pola zamiast części nazwy kraju. "Slov,enia" będzie traktowany jako dwie kolumny. W związku z tym określony wiersz będzie miał jedną kolumnę więcej niż inne wiersze i jedną kolumnę więcej niż zdefiniowano w klauzuli WITH.

## <a name="tab-delimited-files"></a>Pliki rozdzielane kartą

W poniższej kwerendzie pokazano sposób odczytywania pliku z wierszem nagłówka z nowym wierszem w stylu Uniksa i kolumnami rozdzielanych kartą. Zwróć uwagę na inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z unix-style nowy wiersz i tab ogranicznik.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
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

## <a name="returning-subset-of-columns"></a>Zwracany podzbiór kolumn

Do tej pory określono schemat pliku CSV przy użyciu with i listę wszystkich kolumn. Można określić tylko kolumny, które są faktycznie potrzebne w zapytaniu przy użyciu numeru porządkowego dla każdej potrzebnej kolumny. Pominięto również kolumny bez zainteresowania.

Następująca kwerenda zwraca liczbę odrębnych nazw krajów w pliku, określając tylko kolumny, które są potrzebne:

> [!NOTE]
> Spójrz na with klauzuli w zapytaniu poniżej i należy pamiętać, że istnieje "2" (bez cudzysłowów) na końcu wiersza, gdzie można zdefiniować *[country_name]* kolumny. Oznacza to, że kolumna *[country_name]* jest drugą kolumną w pliku. Kwerenda zignoruje wszystkie kolumny w pliku z wyjątkiem drugiej.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Następne kroki

W kolejnych artykułach dowiesz się, jak:

- [Kwerenda plików parkietu](query-parquet-files.md)
- [Wykonywanie zapytań o foldery i wiele plików](query-folders-multiple-csv-files.md)
