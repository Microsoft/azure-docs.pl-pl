---
title: Wprowadzenie do języka U-SQL w Azure Data Lake Analytics
description: Poznaj podstawy języka U-SQL w Azure Data Lake Analytics. Napisz pierwsze zapytanie przy użyciu zmiennych, aby uzyskać dodatkowe dane z plików, Przekształć zestaw wierszy i agregować dane.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 6f2af2eed8aa01f35e1813bcd910f3ea22e2a335
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87128849"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Wprowadzenie do języka U-SQL w Azure Data Lake Analytics

Skrypt U-SQL to język, który łączy deklaratywne SQL z niestandardowym kodem C#, aby umożliwić przetwarzanie danych w dowolnej skali. Dzięki skalowalnej i rozproszonej funkcji zapytania U-SQL można efektywnie analizować dane w magazynach relacyjnych, takich jak Azure SQL Database. Za pomocą języka U-SQL można przetwarzać dane bez struktury, stosując schemat przy odczytywaniu i wstawianiu niestandardowych logiki i UDF. Ponadto skrypt U-SQL zawiera rozszerzalność, która zapewnia precyzyjną kontrolę nad wykonywaniem w odpowiedniej skali.

## <a name="learning-resources"></a>Zasoby szkoleniowe

* [Samouczek u-SQL](https://aka.ms/usqltutorial) zawiera Przewodnik dotyczący większości języka u-SQL. Ten dokument jest zalecany do czytania dla wszystkich deweloperów, którzy chcą poznać język U-SQL.
* Aby uzyskać szczegółowe informacje na temat **składni języka u-SQL**, zobacz [Dokumentacja języka u-SQL](https://docs.microsoft.com/u-sql/).
* Aby zrozumieć zasady **projektowania u-SQL**, zapoznaj się z wpisem w blogu programu Visual Studio, [wprowadzając polecenie U-SQL — język, który ułatwia przetwarzanie danych Big Data](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem do przykładów U-SQL w tym dokumencie Przeczytaj i kompletny [Samouczek: Tworzenie skryptów u-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Ten samouczek wyjaśnia Mechanics przy użyciu języka U-SQL z Azure Data Lake Tools for Visual Studio.

## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy skrypt U-SQL jest prosty i umożliwia nam Eksplorowanie wielu aspektów języka U-SQL.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Ten skrypt nie zawiera żadnych kroków transformacji. Odczytuje z pliku źródłowego o nazwie `SearchLog.tsv` , schematizes go i zapisuje zestaw wierszy z powrotem do pliku o nazwie SearchLog-first-u-sql.csv.

Zwróć uwagę na znak zapytania obok typu danych w `Duration` polu. Oznacza to, że `Duration` pole może mieć wartość null.

### <a name="key-concepts"></a>Kluczowe pojęcia

* **Zmienne zestawu wierszy**: każde wyrażenie zapytania, które generuje zestaw wierszy, można przypisać do zmiennej. Język U-SQL jest zgodny ze wzorcem nazewnictwa zmiennych T-SQL ( `@searchlog` na przykład) w skrypcie.
* Słowo kluczowe **Extract** odczytuje dane z pliku i definiuje schemat podczas odczytu. `Extractors.Tsv` jest wbudowanym ekstraktorem U-SQL dla plików wartości rozdzielanych znakami tabulacji. Można opracowywać niestandardowe wyodrębniania.
* Dane **wyjściowe** są zapisywane z zestawu wierszy do pliku. `Outputters.Csv()` jest wbudowanym wypełnieniem U-SQL w celu utworzenia pliku z wartościami rozdzielanymi przecinkami. Możesz opracowywać niestandardowe wyprowadzenia.

### <a name="file-paths"></a>Ścieżki plików

Instrukcje EXTRACT i OUTPUT używają ścieżek plików. Ścieżki plików mogą być bezwzględne lub względne:

Ta bezwzględna ścieżka do pliku odnosi się do pliku w Data Lake Store o nazwie `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Następująca ścieżka pliku rozpoczyna się od `"/"` . Odnosi się do pliku w domyślnym koncie Data Lake Store:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Użyj zmiennych skalarnych

Możesz również używać zmiennych skalarnych, aby ułatwić konserwację skryptów. Poprzedni skrypt U-SQL może być również zapisany jako:

```usql
DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv();
OUTPUT @searchlog
    TO @out
    USING Outputters.Csv();
```

## <a name="transform-rowsets"></a>Przekształć zestawy wierszy

Użyj **opcji wybierz** do przekształcenia zestawów wierszy:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
OUTPUT @rs1
    TO "/output/SearchLog-transform-rowsets.csv"
    USING Outputters.Csv();
```

Klauzula WHERE używa [wyrażenia logicznego języka C#](/dotnet/csharp/language-reference/operators/index). Możesz użyć języka wyrażeń języka C# do wykonywania własnych wyrażeń i funkcji. Można nawet wykonywać bardziej skomplikowane filtrowanie, łącząc je ze sobą koniunkcji logiczne (ANDs) i rozłączeń (ORs).

Poniższy skrypt używa metody DateTime. Parse () i połączenia.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
OUTPUT @rs1
    TO "/output/SearchLog-transform-datetime.csv"
    USING Outputters.Csv();
```

 >[!NOTE]
 >Drugie zapytanie działa w wyniku pierwszego zestawu wierszy, który tworzy złożone z dwóch filtrów. Możesz również ponownie użyć nazwy zmiennej, a nazwy są w określonym zakresie.

## <a name="aggregate-rowsets"></a>Agregacja zestawów wierszy

Język U-SQL zapewnia znane, UPORZĄDKOWANe według, grupowanie według i agregacje.

Poniższe zapytanie znajduje łączny czas trwania na region, a następnie wyświetla pięć pierwszych czasów trwania w kolejności.

Zestawy wierszy U-SQL nie zachowują kolejności dla następnego zapytania. W tym celu należy dodać polecenie ORDER BY do WYJŚCIOWEj instrukcji:

```usql
DECLARE @outpref string = "/output/Searchlog-aggregation";
DECLARE @out1    string = @outpref+"_agg.csv";
DECLARE @out2    string = @outpref+"_top5agg.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
GROUP BY Region;
@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
OUTPUT @rs1
    TO @out1
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
OUTPUT @res
    TO @out2
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Klauzula ORDER BY języka U-SQL wymaga użycia klauzuli FETCH w wyrażeniu SELECT.

Klauzula HAVING U-SQL może służyć do ograniczania danych wyjściowych do grup, które spełniają warunek HAVING:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
OUTPUT @res
    TO "/output/Searchlog-having.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Aby uzyskać zaawansowane scenariusze agregacji, zobacz dokumentację referencyjną języka U-SQL dla [funkcji agregujących, analitycznych i referencyjnych](/u-sql/built-in-functions)

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
