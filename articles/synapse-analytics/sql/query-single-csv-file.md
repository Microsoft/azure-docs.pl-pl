---
title: Wykonywanie zapytań dotyczących plików CSV za pomocą puli SQL bezserwerowej
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące pojedynczych plików CSV z różnymi formatami plików, używając bezserwerowej puli SQL.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462600"
---
# <a name="query-csv-files"></a>Wykonywanie zapytań o pliki CSV

W tym artykule dowiesz się, jak wykonywać zapytania dotyczące pojedynczego pliku CSV przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics. Pliki CSV mogą mieć różne formaty: 

- Z wierszem nagłówka i bez niego
- Wartości rozdzielane przecinkami i tabulatorami
- Zakończenia wierszy stylu systemu Windows i systemu UNIX
- Wartości bez cudzysłowu i cudzysłowy oraz znaki ucieczki

Wszystkie powyższe zmiany zostaną omówione poniżej.

## <a name="quickstart-example"></a>Przykład szybkiego startu

`OPENROWSET` funkcja umożliwia odczytywanie zawartości pliku CSV przez podanie adresu URL pliku.

### <a name="read-a-csv-file"></a>Odczytaj plik CSV

Najprostszym sposobem, aby zobaczyć zawartość `CSV` pliku, jest podanie adresu URL pliku do `OPENROWSET` działania, określenie woluminu csv `FORMAT` i 2,0 `PARSER_VERSION` . Jeśli plik jest publicznie dostępny lub jeśli tożsamość usługi Azure AD ma dostęp do tego pliku, powinna być widoczna zawartość pliku przy użyciu zapytania, jak pokazano w następującym przykładzie:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Opcja `firstrow` służy do pomijania pierwszego wiersza w pliku CSV, który reprezentuje nagłówek w tym przypadku. Upewnij się, że możesz uzyskać dostęp do tego pliku. Jeśli plik jest chroniony za pomocą klucza SAS lub tożsamości niestandardowej, należy skonfigurować [poświadczenia na poziomie serwera dla logowania SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Jeśli plik CSV zawiera znaki UTF-8, upewnij się, że używasz sortowania bazy danych UTF-8 (na przykład `Latin1_General_100_CI_AS_SC_UTF8` ).
> Niezgodność między kodowaniem tekstu w pliku a sortowaniem może spowodować nieoczekiwane błędy konwersji.
> Można łatwo zmienić domyślne sortowanie bieżącej bazy danych, korzystając z następującej instrukcji T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Użycie źródła danych

Poprzedni przykład używa pełnej ścieżki do pliku. Alternatywnie można utworzyć zewnętrzne źródło danych z lokalizacją wskazującą folder główny magazynu:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Po utworzeniu źródła danych możesz użyć tego źródła danych i ścieżki względnej do pliku w `OPENROWSET` funkcji:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Jeśli źródło danych jest chronione za pomocą klucza SAS lub tożsamości niestandardowej, można skonfigurować [Źródło danych z poświadczeniami o zakresie bazy danych](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Jawnie określ schemat

`OPENROWSET` umożliwia jawne Określanie kolumn, które mają być odczytywane z klauzuli File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Liczby po typie danych w `WITH` klauzuli reprezentują indeks kolumny w pliku CSV.

> [!IMPORTANT]
> Jeśli plik CSV zawiera znaki UTF-8, należy się upewnić, że explicilty określono niektóre sortowanie UTF-8 (na przykład `Latin1_General_100_CI_AS_SC_UTF8` ) dla wszystkich kolumn w `WITH` klauzuli lub ustawić niektóre sortowanie UTF-8 na poziomie bazy danych.
> Niezgodność między kodowaniem tekstu w pliku a sortowaniem może spowodować nieoczekiwane błędy konwersji.
> Można łatwo zmienić domyślne sortowanie bieżącej bazy danych, korzystając z następującej instrukcji T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Można łatwo ustawić sortowanie dla typów kolum, korzystając z następującej definicji: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

W poniższych sekcjach można zobaczyć, jak wykonywać zapytania dotyczące różnych typów plików CSV.

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
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

![Pierwsze 10 wierszy pliku CSV bez wiersza nagłówka i z Unix-Style nowym wierszem.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z Unix-Style nowym wierszem.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z Unix-Style nowym wierszem i wartościami w cudzysłowie.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="escape-characters"></a>Znaki ucieczki

Następujące zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka z nowym wierszem systemu UNIX, kolumnami rozdzielonymi przecinkami oraz znakiem ucieczki używanym przez Ogranicznik pola (przecinek) w obrębie wartości. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka oraz z Unix-Style nowym wierszem i znakiem ucieczki używanym do ogranicznika pola.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> To zapytanie nie powiedzie się, jeśli ESCAPECHAR nie zostanie określony, ponieważ przecinek w "slov, enia" byłby traktowany jako ogranicznik pola zamiast części nazwy kraju/regionu. "Slov, enia" byłyby traktowane jak dwie kolumny. W związku z tym, konkretny wiersz może mieć jedną kolumnę więcej niż pozostałe wiersze, a jedna kolumna jest większa niż zdefiniowana w klauzuli WITH.

### <a name="escape-quoting-characters"></a>Znaki Quote ucieczki

Poniższe zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka z nowym wierszem w stylu systemu UNIX, kolumnami rozdzielanymi przecinkami oraz znakami podwójnego cudzysłowu w obrębie wartości. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Poniższe zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka z nowym wierszem w stylu systemu UNIX, kolumnami rozdzielanymi przecinkami oraz znakami podwójnego cudzysłowu w obrębie wartości.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> Znak cudzysłowu musi być zmieniony przy użyciu innego znaku cudzysłowu. Znak quota może pojawić się w wartości kolumny tylko wtedy, gdy wartość jest hermetyzowana przy użyciu znaków cudzysłowu.

## <a name="tab-delimited-files"></a>Pliki rozdzielane tabulatorami

Następujące zapytanie pokazuje, jak odczytywać plik z wierszem nagłówka, z nowym wierszem w stylu systemu UNIX i kolumnami rozdzielanymi tabulatorami. Zanotuj inną lokalizację pliku w porównaniu z innymi przykładami.

Podgląd pliku:

![Pierwsze 10 wierszy pliku CSV z wierszem nagłówka i z Unix-Style nowym ogranicznikiem wiersza i tabulatorem.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="return-a-subset-of-columns"></a>Zwracanie podzbioru kolumn

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
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
