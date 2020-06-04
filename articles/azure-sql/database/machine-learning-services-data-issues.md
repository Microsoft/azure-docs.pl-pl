---
title: Współpraca z typami danych R i SQL oraz obiektami
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak korzystać z typów danych i obiektów danych w języku R z Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza), w tym typowych problemów, które mogą wystąpić.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d7f33dd782b46afa1aa3845d2acb5712a76ea420
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324202"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Pracuj z danymi języka R i SQL w Azure SQL Database Machine Learning Services (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule omówiono niektóre typowe problemy, które mogą wystąpić podczas przemieszczania danych między językiem R i Azure SQL Database w [Machine Learning Services (z językiem r) w Azure SQL Database](machine-learning-services-overview.md). Środowisko zdobyte w tym ćwiczeniu zapewnia podstawowe tło podczas pracy z danymi w swoim skrypcie.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Typowe problemy, które mogą wystąpić, to m.in.:

- Typy danych czasami nie są zgodne
- Niejawne konwersje mogą mieć miejsce
- Operacje Cast i Convert są czasami wymagane
- Język R i SQL używają różnych obiektów danych

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeniach, musisz najpierw mieć [Azure SQL Database z włączoną Machine Learning Services (z R)](machine-learning-services-overview.md) .

- Upewnij się, że zainstalowano najnowszą [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przewodniku szybki start będziesz używać programu SSMS.

## <a name="working-with-a-data-frame"></a>Praca z ramką danych

Gdy skrypt zwróci wyniki z języka R do SQL, musi zwrócić dane jako **dane. Frame**. Każdy inny typ obiektu, który został wygenerowany w skrypcie — czy jest to lista, współczynnik, wektor lub dane binarne — muszą zostać przekonwertowane na ramkę danych, jeśli chcesz, aby dane wyjściowe były wyprowadzane jako część wyników procedury składowanej. Na szczęście istnieje wiele funkcji R do obsługi zmieniania innych obiektów na ramkę danych. Można nawet serializować model binarny i zwrócić go w ramce danych, którą wykonasz w dalszej części tego artykułu.

Najpierw Poeksperymentuj z niektórymi podstawowymi obiektami R — wektorami, macierzami i listami — i zobacz, jak konwersja na ramkę danych zmienia dane wyjściowe przesłane do SQL.

Porównaj te dwa skrypty "Hello world" w języku R. Skrypty wyglądają niemal identycznie, ale pierwszy zwraca jedną kolumnę trzech wartości, a druga zwraca trzy kolumny z pojedynczą wartością.

**Przykład 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Przykład 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Dlaczego wyniki są różne?

Odpowiedź można zwykle znaleźć przy użyciu `str()` polecenia R. Dodaj funkcję `str(object_name)` gdziekolwiek w skrypcie języka r, aby schemat danych określonego obiektu R został zwrócony jako komunikat informacyjny. Komunikaty można przeglądać na karcie **wiadomości** w programie SSMS.

Aby ustalić, dlaczego przykład 1 i przykład 2 mają takie różne wyniki, Wstaw wiersz `str(OutputDataSet)` na końcu `@script` definicji zmiennej w każdej instrukcji, jak to:

**Przykład 1 z dodaną funkcją str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Przykład 2 z dodaną funkcją str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Teraz Przejrzyj tekst w **komunikatach** , aby zobaczyć, dlaczego dane wyjściowe różnią się od siebie.

**Wyniki — przykład 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Wyniki — przykład 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak widać, niewielka zmiana składni języka R miała duży wpływ na schemat wyników. Ze względu na wszystkie szczegóły różnice w typach danych R są objaśnione szczegółowo w sekcji *struktury danych* w artykule ["Advanced R" przez Hadley Wickham](http://adv-r.had.co.nz).

Na razie Wystarczy pamiętać, że po przeniesieniu obiektów R do ramek danych należy sprawdzić oczekiwane wyniki.

> [!TIP]
> Można również użyć funkcji tożsamości języka R, takich jak `is.matrix` , `is.vector` , aby zwrócić informacje o wewnętrznej strukturze danych.

## <a name="implicit-conversion-of-data-objects"></a>Niejawna konwersja obiektów danych

Każdy obiekt danych R ma własne reguły dotyczące sposobu obsługi wartości w połączeniu z innymi obiektami danych, jeśli dwa obiekty danych mają tę samą liczbę wymiarów lub jeśli którykolwiek obiekt danych zawiera heterogeniczne typy danych.

Załóżmy na przykład, że chcesz przeprowadzić mnożenie macierzy przy użyciu języka R. Chcesz pomnożyć macierz jednokolumnową o trzy wartości przez tablicę z czterema wartościami i spodziewać się, że w wyniku 4x3 macierz.

Najpierw utwórz małą tabelę danych testowych.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Teraz uruchom następujący skrypt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

W obszarze okładek kolumna trzech wartości jest konwertowana na tablicę z jedną kolumną. Ponieważ macierz jest tylko szczególnym przypadkiem tablicy w języku R, tablica `y` jest niejawnie przenikająca do macierzy jednokolumnowej, aby oba argumenty były zgodne.

**Uzyskane**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Należy jednak zwrócić uwagę na to, co się dzieje w przypadku zmiany rozmiaru tablicy `y` .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Teraz R zwraca pojedynczą wartość jako wynik.

**Uzyskane**
    
|Col1|
|---|
|1542|

Dlaczego? W tym przypadku, ponieważ dwa argumenty mogą być obsługiwane jako wektory o tej samej długości, R zwraca wewnętrzny produkt jako macierz.  Jest to oczekiwane zachowanie zgodnie z regułami algebry liniowych. Jednak może to spowodować problemy, jeśli aplikacja wyjściowa oczekuje, że schemat wyjściowy nie zmieni się.

## <a name="merge-or-multiply-columns-of-different-length"></a>Scalanie lub mnożenie kolumn o różnej długości

Język R zapewnia dużą elastyczność pracy z wektorami o różnych rozmiarach, a także do łączenia tych struktur takich jak te struktury kolumn z ramkami danych. Listy wektorów mogą wyglądać jak tabela, ale nie przestrzegają wszystkich reguł rządzących tabelami baz danych.

Na przykład poniższy skrypt definiuje tablicę liczbową o długości 6 i zapisuje ją w zmiennej języka R `df1` . Tablica liczbowa jest następnie połączona z liczbami całkowitymi tabeli RTestData (utworzona powyżej), która zawiera trzy (3) wartości, aby utworzyć nową ramkę danych `df2` .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Aby wypełnić ramkę danych, R powtarza elementy pobierane z RTestData tyle razy, ile jest to konieczne, aby dopasować liczbę elementów w tablicy `df1` .

**Uzyskane**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Należy pamiętać, że ramka danych wygląda tylko na tabelę, ale jest w rzeczywistości listą wektorów.

## <a name="cast-or-convert-sql-data"></a>Rzutowanie lub konwertowanie danych SQL

Język r i SQL nie używają tych samych typów danych, więc w przypadku uruchamiania zapytania w języku SQL w celu pobrania danych, a następnie przekazania ich do środowiska uruchomieniowego języka R, niektóre typy niejawnej konwersji zwykle odbywają się. Po powrocie danych z języka R do SQL są wykonywane inne zestawy konwersji.

- SQL wypychanie danych z zapytania do procesu R i konwertuje je na wewnętrzną reprezentację w celu zwiększenia wydajności.
- Środowisko uruchomieniowe języka R ładuje dane do zmiennej danych. Frame i wykonuje własne operacje na danych.
- Aparat bazy danych zwraca dane do bazy danych SQL przy użyciu zabezpieczonego połączenia wewnętrznego i przedstawia dane pod kątem typów danych SQL.
- Dane można uzyskać, łącząc się z SQL za pomocą klienta lub biblioteki sieciowej, która może wydawać zapytania SQL i obsługiwać zestawy danych tabelarycznych. Ta aplikacja kliencka może potencjalnie wpływać na dane na inne sposoby.

Aby zobaczyć, jak to działa, uruchom zapytanie, takie jak ten, w magazynie danych [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) . Ten widok zwraca dane sprzedaży używane podczas tworzenia prognoz.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Możesz użyć dowolnej wersji usługi AdventureWorks lub utworzyć inne zapytanie przy użyciu własnej bazy danych. Punkt jest próbować obsługiwać niektóre dane zawierające tekst, DateTime i wartości liczbowe.

Teraz spróbuj użyć tego zapytania jako danych wejściowych procedury składowanej.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Jeśli wystąpi błąd, prawdopodobnie trzeba będzie wprowadzić pewne zmiany do tekstu zapytania. Na przykład predykat ciągu w klauzuli WHERE musi być ujęty w dwa zestawy znaków pojedynczego cudzysłowu.

Po otrzymaniu zapytania Sprawdź wyniki `str` funkcji, aby zobaczyć, jak R traktuje dane wejściowe.

**Uzyskane**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Kolumna DateTime została przetworzona przy użyciu typu danych języka R, **POSIXct**.
- Kolumna tekstowa "ProductSeries" została zidentyfikowana jako **współczynnik**, co oznacza zmienną kategorii. Wartości ciągów są domyślnie obsługiwane jako czynniki. Jeśli przekażesz ciąg do języka R, zostanie on przekonwertowany na liczbę całkowitą do użytku wewnętrznego, a następnie zmapowany z powrotem do ciągu w danych wyjściowych.

## <a name="summary"></a>Podsumowanie

Nawet z tych krótkich przykładów można zobaczyć, że zachodzi potrzeba sprawdzenia efektów konwersji danych podczas przekazywania zapytań SQL jako danych wejściowych. Ponieważ niektóre typy danych SQL nie są obsługiwane przez język R, należy wziąć pod uwagę następujące sposoby uniknięcia błędów:

- Przetestuj swoje dane z wyprzedzeniem i Sprawdź kolumny lub wartości w schemacie, które mogą być problemem podczas przekazywania do kodu języka R.
- Określ kolumny w wejściowym źródle danych, a nie za pomocą `SELECT *` i Dowiedz się, w jaki sposób każda kolumna będzie obsługiwana.
- W razie potrzeby należy wykonać Jawne rzutowania w czasie przygotowywania danych wejściowych, aby uniknąć niepotrzebnych.
- Unikaj przekazywania kolumn danych (takich jak identyfikatory GUID lub ROWGUID), które powodują błędy i nie są przydatne do modelowania.

Aby uzyskać więcej informacji na temat obsługiwanych i nieobsługiwanych typów danych języka R, zobacz sekcję [biblioteki i typy danych języka r](/sql/advanced-analytics/r/r-libraries-and-data-types).
