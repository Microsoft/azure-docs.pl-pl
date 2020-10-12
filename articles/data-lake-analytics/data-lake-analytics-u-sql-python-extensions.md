---
title: Rozszerzone skrypty U-SQL z językiem Python w Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka Python w skryptach U-SQL przy użyciu Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: b15ab268433e4220d499f3e1fe7cb90ffac2a1be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876021"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozszerzone skrypty U-SQL z kodem języka Python w Azure Data Lake Analytics

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozszerzenia języka Python zostały zainstalowane na koncie Azure Data Lake Analytics.

* Przejdź do konta Data Lake Analytics w Azure Portal
* W menu po lewej stronie w obszarze **wprowadzenie** kliknij pozycję **przykładowe skrypty**
* Kliknij pozycję **Zainstaluj rozszerzenia U-SQL** , a następnie przycisk **OK** .

## <a name="overview"></a>Omówienie

Rozszerzenia Python dla języka U-SQL umożliwiają deweloperom wykonywanie wysoce równoległych wykonań kodu języka Python. Poniższy przykład ilustruje podstawowe kroki:

* Użycie `REFERENCE ASSEMBLY` instrukcji w celu włączenia rozszerzeń języka Python dla skryptu U-SQL
* Używanie `REDUCE` operacji do partycjonowania danych wejściowych klucza
* Rozszerzenia Python dla języka U-SQL zawierają wbudowanego zredukowanego ( `Extension.Python.Reducer` ), który uruchamia kod języka Python na każdym wierzchołku przypisanym do tego ograniczenia
* Skrypt U-SQL zawiera osadzony kod języka Python, który ma funkcję o nazwie `usqlml_main` , która akceptuje Pandas Dataframe jako dane wejściowe i zwraca element Pandas Dataframe jako dane wyjściowe.

```usql
REFERENCE ASSEMBLY [ExtPython];
DECLARE @myScript = @"
def get_mentions(tweet):
    return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )
def usqlml_main(df):
    del df['time']
    del df['author']
    df['mentions'] = df.tweet.apply(get_mentions)
    del df['tweet']
    return df
";
@t  =
    SELECT * FROM
       (VALUES
           ("D1","T1","A1","@foo Hello World @bar"),
           ("D2","T2","A2","@baz Hello World @beer")
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Jak środowisko Python integruje się z językiem U-SQL

### <a name="datatypes"></a>Typy danych

* Ciągi i kolumny liczbowe z języka U-SQL są konwertowane między Pandas i U-SQL
* Wartości null U-SQL są konwertowane na i `NA` z Pandas

### <a name="schemas"></a>Schematy

* Wektory indeksów w Pandas nie są obsługiwane w języku U-SQL. Wszystkie wejściowe ramki danych w funkcji języka Python zawsze mają 64-bitowy indeks liczbowy z przedziału od 0 do liczby wierszy pomniejszonej o 1.
* Zestawy danych U-SQL nie mogą mieć zduplikowanych nazw kolumn
* Nazwy kolumn U-SQL DataSets, które nie są ciągami.

### <a name="python-versions"></a>Wersje języka Python

Obsługiwane jest tylko środowisko Python 3.5.1 (skompilowane dla systemu Windows).

### <a name="standard-python-modules"></a>Standardowe moduły języka Python

Uwzględniane są wszystkie standardowe moduły języka Python.

### <a name="additional-python-modules"></a>Dodatkowe moduły języka Python

Oprócz standardowych bibliotek języka Python są dostępne kilka powszechnie używanych bibliotek języka Python:

* Pandas
* numpy
* numexpr

### <a name="exception-messages"></a>Komunikaty o wyjątkach

Obecnie wyjątek w kodzie języka Python jest wyświetlany jako ogólny błąd wierzchołka. W przyszłości komunikaty o błędach zadania w języku Python będą wyświetlane w komunikatach o wyjątku w programie.

### <a name="input-and-output-size-limitations"></a>Ograniczenia rozmiaru danych wejściowych i wyjściowych

Każdy wierzchołek ma przypisaną ograniczoną ilość pamięci. Obecnie ten limit wynosi 6 GB w przypadku aktualizacji automatycznych. Ponieważ wejściowe i wyjściowe ramki danych muszą istnieć w pamięci w kodzie Python, całkowity rozmiar danych wejściowych i wyjściowych nie może przekroczyć 6 GB.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z funkcji okna języka U-SQL dla zadań Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
