---
title: Użyj wykazu U-SQL w Azure Data Lake Analytics
description: Dowiedz się, jak udostępniać kod i dane przy użyciu wykazu U-SQL. Twórz funkcje z wartościami przechowywanymi w tabeli, twórz widoki, twórz tabele i wysyłaj do nich zapytania.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220078"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Wprowadzenie do wykazu U-SQL w Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Utwórz TVF

W poprzednim skrypcie U-SQL powtórzono użycie WYODRĘBNIAnia do odczytu z tego samego pliku źródłowego. Za pomocą funkcji zwracającej tabelę w języku U-SQL (TVF) można hermetyzować dane do użytku w przyszłości.  

Poniższy skrypt tworzy TVF o nazwie wywoływanej `Searchlog()` w domyślnej bazie danych i schemacie:

```usql
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
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
RETURN;
END;
```

Poniższy skrypt pokazuje, jak używać TVF, który został zdefiniowany w poprzednim skrypcie:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Tworzenie widoków

Jeśli masz pojedyncze wyrażenie zapytania, a nie TVF, możesz użyć widoku U-SQL do hermetyzacji tego wyrażenia.

Poniższy skrypt tworzy widok o nazwie `SearchlogView` w domyślnej bazie danych i schemacie:

```usql
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Poniższy skrypt demonstruje użycie zdefiniowanego widoku:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Tworzenie tabel
Podobnie jak w przypadku tabel relacyjnych baz danych, za pomocą języka U-SQL można utworzyć tabelę ze wstępnie zdefiniowanym schematem lub utworzyć tabelę, która wnioskuje schemat z zapytania, które wypełnia tabelę (nazywaną również CREATE TABLE jako SELECT lub CTAS).

Utwórz bazę danych i dwie tabele przy użyciu następującego skryptu:

```usql
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Wykonywanie zapytań dotyczących tabel
Można wykonywać zapytania dotyczące tabel, takich jak te utworzone w poprzednim skrypcie, w taki sam sposób, jak pliki danych. Zamiast tworzenia zestawu wierszy przy użyciu WYODRĘBNIAnia, możesz teraz odwołać się do nazwy tabeli.

Aby odczytywać z tabel, zmodyfikuj użyty wcześniej skrypt przekształcenia:

```usql
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Obecnie nie można uruchomić SELECT w tabeli w tym samym skrypcie, w której utworzono tabelę.

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
